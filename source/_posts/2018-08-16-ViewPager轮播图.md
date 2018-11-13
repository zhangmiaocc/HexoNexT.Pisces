---
title: ViewPager轮播图（文字&图片）
date: 2018-08-16 19:30:32
tags:
- blog
- markdown
- Android 
- ViewPager
categories:
- Android 
- View
---

ViewPager常用来实现图片的轮播，比如淘宝首页，会把一些促销的商品的图片和描述信息来回的播放，这就是典型的使用ViewPager实现的。

ViewPager属于布局管理器，允许用户通过页面翻转查看左右的数据，下面通过一个实例来讲解ViewPager实现图片轮播和手势滑动。

#### 效果图

|                                                              |                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![](https://ws2.sinaimg.cn/large/0069RVTdly1fubrstjvpxj30f00qo3ze.jpg) | ![](https://ws3.sinaimg.cn/large/0069RVTdly1fubru8skhlj30f00qojru.jpg) | ![](https://ws3.sinaimg.cn/large/0069RVTdly1fubrurmx05j30f00qoaau.jpg) |


#### 布局文件 activity_main.xml

``` html
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="180dp">

    <android.support.v4.view.ViewPager
        android:id="@+id/vp"
        android:layout_width="match_parent"
        android:layout_height="180dp"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:background="#6000"
        android:gravity="center_horizontal"
        android:orientation="vertical"
        android:padding="5dp">

        <TextView
            android:id="@+id/tv_desc"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:textColor="#fff"/>

        <LinearLayout
            android:id="@+id/ll_point"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="5dp"
            android:orientation="horizontal">
        </LinearLayout>

    </LinearLayout>
</RelativeLayout>
```


#### 小圆点选择器

point_enable.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
       android:shape="oval">
    <corners android:radius="8dp"/>
    <solid android:color="#fff"/>
</shape>

```

point_disable.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android"
       android:shape="oval">
    <corners android:radius="8dp"/>
    <solid android:color="@android:color/darker_gray"/>
</shape>

```

point_selector.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_enabled="true"  android:drawable="@drawable/point_enable"/>
    <item android:state_enabled="false"  android:drawable="@drawable/point_disable"/>
</selector>

```


#### MainActivity
MianActivity的代码如下，下面的代码主要是实现图片轮播和手势滑动，同时也提供里一个解决图片轮播到最后一个或滑动到最后一个（或第一个时）停了下来的问题，这个问题对用户体验来说是很糟糕的，所以要解决。同时提供了温习了一下MVC开发模型，这种模型能够让代码显得结构清晰。为了保证代码的连贯性，把代码写在了一个类中。

```java
package com.zm.myviewpager;

import android.os.Bundle;
import android.support.v4.view.PagerAdapter;
import android.support.v4.view.ViewPager;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ImageView;
import android.widget.LinearLayout;
import android.widget.TextView;

import java.util.ArrayList;

public class MainActivity extends AppCompatActivity implements ViewPager.OnPageChangeListener {

    private ViewPager vp;
    private LinearLayout ll_point;
    private TextView tv_desc;
    private int[] imageResIds; //存放图片资源id的数组
    private ArrayList<ImageView> imageViews; //存放图片的集合
    private String[] contentDescs; //图片内容描述
    private int lastPosition;
    private boolean isRunning = false; //viewpager是否在自动轮询

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        //使用M-V-C模型
        //V--view视图
        initViews();
        //M--model数据
        initData();
        //C--control控制器(即适配器)
        initAdapter();
        //开启图片的自动轮询
        new Thread() {
            @Override
            public void run() {
                isRunning = true;
                while (isRunning) {
                    try {
                        Thread.sleep(3000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() { //在子线程中开启子线程
                            //往下翻一页（setCurrentItem方法用来设置ViewPager的当前页）
                            vp.setCurrentItem(vp.getCurrentItem() + 1);
                        }
                    });
                }
            }
        }.start();
    }

    /*
        初始化视图
     */
    private void initViews() {
        //初始化放小圆点的控件
        ll_point = (LinearLayout) findViewById(R.id.ll_point);
        //初始化ViewPager控件
        vp = (ViewPager) findViewById(R.id.vp);
        //设置ViewPager的滚动监听
        vp.setOnPageChangeListener(this);
        //显示图片描述信息的控件
        tv_desc = (TextView) findViewById(R.id.tv_desc);
    }

    /*
      初始化数据
     */
    private void initData() {
        //初始化填充ViewPager的图片资源
        imageResIds = new int[]{R.mipmap.aa, R.mipmap.bb, R.mipmap.cc, R.mipmap.dd, R.mipmap.ee};
        //图片的描述信息
        contentDescs = new String[]{
                "忙碌的生活疏于彼此联系，但却无法冲淡对你的思念。相信我们的心电感应，会把我每一次祈祷和祝福悄悄传送。",
                "你会因为一首歌喜欢上一个人，因为一个人喜欢一个城市，因为一个城市喜欢上一种生活，然后成为一首歌，想念某个人。",
                "我们对亲人的思念是永不会停止的，而思念却是多种的，阿婆的伤痛，妈妈的文字，我的小女儿情绪，不管怎样，已故的亲人永远活在我们的心里。",
                "每一天醒来，你的清影就在我眼前转。不管手里干什么事，一会儿，准走神儿了，呆呆的只想你，算着你什么时候回来。",
                "在一年的每个日子，在一天每个小时，在一小时的每一分钟，在一分钟的每一秒，我都在想你。"
        };
        //保存图片资源的集合
        imageViews = new ArrayList<>();
        ImageView imageView;
        View pointView;
        //循环遍历图片资源，然后保存到集合中
        for (int i = 0; i < imageResIds.length; i++) {
            //添加图片到集合中
            imageView = new ImageView(this);
            imageView.setBackgroundResource(imageResIds[i]);
            imageViews.add(imageView);

            //加小白点，指示器（这里的小圆点定义在了drawable下的选择器中了，也可以用小图片代替）
            pointView = new View(this);
            pointView.setBackgroundResource(R.drawable.point_selector); //使用选择器设置背景
            LinearLayout.LayoutParams layoutParams = new LinearLayout.LayoutParams(8, 8);
            if (i != 0) {
                //如果不是第一个点，则设置点的左边距
                layoutParams.leftMargin = 10;
            }
            pointView.setEnabled(false); //默认都是暗色的
            ll_point.addView(pointView, layoutParams);
        }
    }

    /*
      初始化适配器
     */
    private void initAdapter() {
        ll_point.getChildAt(0).setEnabled(true); //初始化控件时，设置第一个小圆点为亮色
        tv_desc.setText(contentDescs[0]); //设置第一个图片对应的文字
        lastPosition = 0; //设置之前的位置为第一个
        vp.setAdapter(new MyPagerAdapter());
        //设置默认显示中间的某个位置（这样可以左右滑动），这个数只有在整数范围内，可以随便设置
        vp.setCurrentItem(5000000); //显示5000000这个位置的图片
    }

    //界面销毁时，停止viewpager的轮询
    @Override
    protected void onDestroy() {
        super.onDestroy();
        isRunning = false;
    }


    //--------------以下是设置ViewPager的滚动监听所需实现的方法--------
    //页面滑动
    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

    }

    //新的页面被选中
    @Override
    public void onPageSelected(int position) {
        //当前的位置可能很大，为了防止下标越界，对要显示的图片的总数进行取余
        int newPosition = position % 5;
        //设置描述信息
        tv_desc.setText(contentDescs[newPosition]);
        //设置小圆点为高亮或暗色
        ll_point.getChildAt(lastPosition).setEnabled(false);
        ll_point.getChildAt(newPosition).setEnabled(true);
        lastPosition = newPosition; //记录之前的点
    }

    //页面滑动状态发生改变
    @Override
    public void onPageScrollStateChanged(int state) {

    }

    /**
     * 自定义适配器，继承自PagerAdapter
     */
    class MyPagerAdapter extends PagerAdapter {

        //返回显示数据的总条数，为了实现无限循环，把返回的值设置为最大整数
        @Override
        public int getCount() {
            return Integer.MAX_VALUE;
        }

        //指定复用的判断逻辑，固定写法：view == object
        @Override
        public boolean isViewFromObject(View view, Object object) {
            //当创建新的条目，又反回来，判断view是否可以被复用(即是否存在)
            return view == object;
        }

        //返回要显示的条目内容
        @Override
        public Object instantiateItem(ViewGroup container, int position) {
            //container  容器  相当于用来存放imageView
            //从集合中获得图片
            int newPosition = position % 5; //数组中总共有5张图片，超过数组长度时，取摸，防止下标越界
            ImageView imageView = imageViews.get(newPosition);
            //把图片添加到container中
            container.addView(imageView);
            //把图片返回给框架，用来缓存
            return imageView;
        }

        //销毁条目
        @Override
        public void destroyItem(ViewGroup container, int position, Object object) {
            //object:刚才创建的对象，即要销毁的对象
            container.removeView((View) object);
        }
    }

}

```

