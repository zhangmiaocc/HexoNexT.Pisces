---
title: Viewpager+Fragment动态处理（添加、删除）
date: 2018-12-25 15:26:35
tags:
- Android 
- Viewpager
- Fragment
categories:
- Android 
- View
---

### 问题
在进行Fragment的添加和删除时，适配器刷新之后发现并没有什么变化，这是为什么呢？

### 理解
FragmentPagerAdapter

适合少量的页面显示。该类每一个生成的Fragment对象都会储存在内存中，所以适合相对静态、页面少的情况，如果是页面多，且Fragment的处理相对动态（添加、删除等）时，使用FragmentStatePagerAdapter较为适合。

FragmentStatePagerAdapter

适合大量的页面显示，当页面处于不可见时，可能会被销毁，只保留该片段的保存状态。与FragmentPagerAdapter切换页面产生的大量开销对比，这允许了适配器保持与每个被访问页面相关联的更少的存储器。

### 分析
在切换页面时，FragmentPagerAdapter与FragmentStatePagerAdapter对于上上页（预加载默认1，所以取上上页）的处理是不相同的，FragmentPagerAdapter只是销毁对应Fragment的视图，而FragmentStatePagerAdapter则是把Fragment的实例和视图都销毁了。

当我们对页面进行动态处理时，添加（或删除）是对适配器所持有的list对象进行长度的变化，操作完之后就进行适配器的刷新，也就是notifyDataSetChanged方法，先看看该方法：

<!--more-->

```java
//PagerAdapter.class
public void notifyDataSetChanged() {
        synchronized (this) {
            if (mViewPagerObserver != null) {
                //根据源码可知mViewPagerObserver的对象是ViewPager里面PagerObserver类的实例
                mViewPagerObserver.onChanged();
            }
        }
        mObservable.notifyChanged();
    }
```
```java
//ViewPager.class
private class PagerObserver extends DataSetObserver {
        PagerObserver() {
        }

        @Override
        public void onChanged() {
            //调用的是该方法
            dataSetChanged();
        }
        @Override
        public void onInvalidated() {
            dataSetChanged();
        }
    }
```
对FragmentPagerAdapter（或FragmentStatePagerAdapter）执行的方法大概进行注释一下，方便理解，
```java
//ViewPager.class
void dataSetChanged() {
        // This method only gets called if our observer is attached, so mAdapter is non-null.

        final int adapterCount = mAdapter.getCount();
        mExpectedAdapterCount = adapterCount;
        boolean needPopulate = mItems.size() < mOffscreenPageLimit * 2 + 1
                && mItems.size() < adapterCount;
        int newCurrItem = mCurItem;
    
        boolean isUpdating = false;
        //遍历所有item
        for (int i = 0; i < mItems.size(); i++) {
            final ItemInfo ii = mItems.get(i);
            //先调用adapter的getItemPosition方法，获得newPos值
            final int newPos = mAdapter.getItemPosition(ii.object);
    
            if (newPos == PagerAdapter.POSITION_UNCHANGED) {
                continue;
            }
    
            if (newPos == PagerAdapter.POSITION_NONE) {
                mItems.remove(i);
                i--;
    
                if (!isUpdating) {
                    mAdapter.startUpdate(this);
                    isUpdating = true;
                }
                //newPos值为PagerAdapter.POSITION_NONE的时候才会执行destroyItem方法
                mAdapter.destroyItem(this, ii.position, ii.object);
                needPopulate = true;
    
                if (mCurItem == ii.position) {
                    // Keep the current item in the valid range
                    newCurrItem = Math.max(0, Math.min(mCurItem, adapterCount - 1));
                    needPopulate = true;
                }
                continue;
            }
    
            if (ii.position != newPos) {
                if (ii.position == mCurItem) {
                    // Our current item changed position. Follow it.
                    newCurrItem = newPos;
                }
    
                ii.position = newPos;
                needPopulate = true;
            }
        }
    
        if (isUpdating) {
            //finishUpdate方法主要是对事务的操作进行commit
            mAdapter.finishUpdate(this);
        }
    
        Collections.sort(mItems, COMPARATOR);
    
        if (needPopulate) {
            // Reset our known page widths; populate will recompute them.
            final int childCount = getChildCount();
            for (int i = 0; i < childCount; i++) {
                final View child = getChildAt(i);
                final LayoutParams lp = (LayoutParams) child.getLayoutParams();
                if (!lp.isDecor) {
                    lp.widthFactor = 0.f;
                }
            }
            //
            setCurrentItemInternal(newCurrItem, false, true);
            requestLayout();
        }
    }
```
Adapter.getItemPosition方法默认返回的是PagerAdapter.POSITION_UNCHANGED值，如果我们不重写getItemPosition方法，使其返回PagerAdapter.POSITION_NONE的话，那么默认是不操作destroyItem方法的，而在destroyItem方法中，FragmentPagerAdapter和FragmentStatePagerAdapter 对Fragment对象的操作也不一样，上面有说过，FragmentPagerAdapter是只销毁视图，FragmentStatePagerAdapter 是把实例和视图都销毁，就是在destroyItem方法实现的，贴代码：
```java
//FragmentPagerAdapter
@Override
public void destroyItem(ViewGroup container, int position, Object object) {
        if (mCurTransaction == null) {
            mCurTransaction = mFragmentManager.beginTransaction();
        }
        if (DEBUG) Log.v(TAG, "Detaching item #" + getItemId(position) + ": f=" + object
                + " v=" + ((Fragment)object).getView());
        //这里是对fragment进行detach操作，fragmentManager中还保存该实例
        mCurTransaction.detach((Fragment)object);
    }
//FragmentStatePagerAdapter 
@Override
public void destroyItem(ViewGroup container, int position, Object object) {
        Fragment fragment = (Fragment) object;

        if (mCurTransaction == null) {
            mCurTransaction = mFragmentManager.beginTransaction();
        }
        if (DEBUG) Log.v(TAG, "Removing item #" + position + ": f=" + object
                + " v=" + ((Fragment)object).getView());
        while (mSavedState.size() <= position) {
            mSavedState.add(null);
        }
        mSavedState.set(position, fragment.isAdded()
                ? mFragmentManager.saveFragmentInstanceState(fragment) : null);
        mFragments.set(position, null);
        //而这里是对fragment进行remove，直接在fragmentManager中移除掉
        mCurTransaction.remove(fragment);
    }
```
### 解决
根据上面的分析，在进行添加删除的时候，我采用了FragmentStatePagerAdapter的子类，进行方法的重写，主要是对该类的两个方法（instantiateItem和destroyItem）进行重写，替换父类的实现，代码如下：

```java
package com.voctex.adapter;

import android.os.Parcelable;
import android.support.v4.app.Fragment;
import android.support.v4.app.FragmentManager;
import android.support.v4.app.FragmentStatePagerAdapter;
import android.support.v4.view.PagerAdapter;
import android.view.ViewGroup;

import java.util.ArrayList;
import java.util.List;


public class DynamicFragmentAdapter extends FragmentStatePagerAdapter {
    private FragmentManager mFragmentManager;
    private List<Fragment> mFragments = new ArrayList<>();

    public DynamicFragmentAdapter(FragmentManager fm, List<Fragment> list) {
        super(fm);
        this.mFragmentManager = fm;
        if (list == null) return;
        this.mFragments.addAll(list);
    }

    public void updateData(List<Fragment> mlist) {
        if (mlist == null) return;
        this.mFragments.clear();
        this.mFragments.addAll(mlist);
        notifyDataSetChanged();
    }

    @Override
    public Fragment getItem(int arg0) {
        return mFragments.get(arg0);//
    }

    @Override
    public int getCount() {
        return mFragments.size();//
    }

    @Override
    public Parcelable saveState() {
        return null;
    }

    @Override
    public int getItemPosition(Object object) {
        if (!((Fragment) object).isAdded() || !mFragments.contains(object)) {
            return PagerAdapter.POSITION_NONE;
        }
        return mFragments.indexOf(object);
    }

    @Override
    public Object instantiateItem(ViewGroup container, int position) {

        Fragment instantiateItem = ((Fragment) super.instantiateItem(container, position));
        Fragment item = mFragments.get(position);
        if (instantiateItem == item) {
            return instantiateItem;
        } else {
            //如果集合中对应下标的fragment和fragmentManager中的对应下标的fragment对象不一致，那么就是新添加的，所以自己add进入；这里为什么不直接调用super方法呢，因为fragment的mIndex搞的鬼，以后有机会再补一补。
            mFragmentManager.beginTransaction().add(container.getId(), item).commitNowAllowingStateLoss();
            return item;
        }
    }

    @Override
    public void destroyItem(ViewGroup container, int position, Object object) {
        Fragment fragment = (Fragment) object;
        //如果getItemPosition中的值为PagerAdapter.POSITION_NONE，就执行该方法。
        if (mFragments.contains(fragment)) {
            super.destroyItem(container, position, fragment);
            return;
        }
        //自己执行移除。因为mFragments在删除的时候就把某个fragment对象移除了，所以一般都得自己移除在fragmentManager中的该对象。
        mFragmentManager.beginTransaction().remove(fragment).commitNowAllowingStateLoss();

    }
    }
```
### 结束语
>在不断的看源码，查资料，调试程序中，终于是把该问题解决了，网上的资料都说得模棱两可，很多时候都得自己操刀，理解了才是自己的，特别是Fragment在FragmentManager中的mIndex值，有点坑，这里没拿出来说，以后有机会再补补。
