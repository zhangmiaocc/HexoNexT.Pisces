---
title: ViewPager要被废弃？官方ViewPager2升级版来临
tags:
  - Android
  - ViewPager
categories:
  - Android
  - View
abbrlink: abf696ca
date: 2019-02-28 16:59:40
---

从文档注释来看ViewPager2确实是用来替代ViewPager 的，顺带解决之前ViewPager的一些问题，并且加入了 RTL,竖向滚动支持，下面一起来详细看下吧。
> ViewPager2 replaces ViewPager, addressing most of its predecessor’s pain-points, including right-to-left layout support, vertical orientation, modifiable Fragment collections, etc.

### 概述

这两天浏览安卓开发者官网的时候，发现google悄然推出了一个新的控件：ViewPager2，一看名称就知道这是一个和我们常用的ViewPager功能相似的控件，算是ViewPager的升级版吧。目前还只是推出了第一个预览版，我们可以直接引入来使用了：

```properties
implementation 'androidx.viewpager2:viewpager2:1.0.0-alpha01'
```

*https://developer.android.google.cn/reference/androidx/viewpager2/widget/ViewPager2*

<!--more-->
我们先来看看有哪些功能和使用上的变化：

新功能：
- 支持RTL布局
- 支持竖向滚动
- 完整支持notifyDataSetChanged

API的变动：
- FragmentStateAdapter替换了原来的 FragmentStatePagerAdapter
- RecyclerView.Adapter替换了原来的 PagerAdapter
- registerOnPageChangeCallback替换了原来的 addPageChangeListener

看了上面这些介绍，有一点比较吸引人的就是支持竖向滚动了，这是怎么实现的呢？ViewPager2的源码不长，我们来简单分析一下。

### 简单解析

通过查看源码得知，ViewPager2是直接继承ViewGroup的，意味着和ViewPager不兼容，类注释上也写了它的作用是取代ViewPager，不过短时间内ViewPager应该还不会被废弃掉。

继续查看源码，发现了两个比较重要的成员变量：

```java
private RecyclerView mRecyclerView;
private LinearLayoutManager mLayoutManager;
```

所以很清楚得知，ViewPager2的核心实现就是RecyclerView+LinearLayoutManager了，因为LinearLayoutManager本身就支持竖向和横向两种布局方式，所以ViewPager2也能很容易地支持这两种滚动方向了，而几乎不需要添加任何多余的代码。

其实在此之前也不乏有大神采用RecyclerView来实现轮播图效果的，具体实现发生略有不同，但大体思想是一致的。这次ViewPager2的推出意味着这种方法终于被扶正了。

为了让RecyclerView变得像原来的ViewPager，需要设置下SnapHelper：

```java
 new PagerSnapHelper().attachToRecyclerView(mRecyclerView);
```

熟悉RecyclerView的同学都知道，SnapHelper用于辅助RecyclerView在滚动结束时将Item对齐到某个位置。PagerSnapHelper的作用让滑动结束时使当前Item居中显示，并且 限制一次只能滑动一页，不能快速滑动，这样就和viewpager的交互很像了。

另外和viewpager一样，viewpager2可以承载fragment，我们需要继承实现它提供的FragmentStateAdapter：

```java
public abstract class FragmentStateAdapter extends
        RecyclerView.Adapter<FragmentViewHolder> implements StatefulAdapter
```

这是一个包含FragmentManager和数据状态恢复功能的RecyclerView.Adapter，具体实现可以参看源码。所以大家也可以用TabLayout+ViewPager2+Fragment来实现联动展示效果。



### 使用

通过android:orientation来指定滚动方向

```xml
<androidx.viewpager2.widget.ViewPager2
        android:id="@+id/viewpager2"
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:orientation="vertical" />
```

在代码中设置一个普通的RecyclerView.adapter：

```java
ViewPager2 viewPager2=findViewById(R.id.viewpager2);
RecyclerviewAdapter adapter = new RecyclerviewAdapter(this);
viewPager2.setAdapter(adapter);
```

这样竖直轮播图就大功告成了。

### 小结

viewpager2利用recyclerview来实现viewpager的功能，无疑使使其可扩展性大大提升，代码也变得更优雅简洁，使用起来也更灵活。不过目前viewpager2只是第一个预览版，还存在稳定性方面的问题，不建议大家引入到正式项目中来，尝尝鲜就好。

PS：如果有谁愿意深入解析，示例对比演示等，欢迎火速联系我，投稿分享给大家。