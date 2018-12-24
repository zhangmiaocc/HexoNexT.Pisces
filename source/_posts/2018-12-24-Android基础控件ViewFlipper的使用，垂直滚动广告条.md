---
title: Android基础控件ViewFlipper的使用，垂直滚动广告条
date: 2018-12-24 17:05:11
tags:
- Android 
- ViewFlipper
categories:
- Android 
- View
---

> 学习，学习，学以致用
ViewFlipper是安卓自带的控件，很多人可能很少知道这个控件，这个控件很简单，也很好理解。

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fyhz70e763g30b70500tv.gif)

从源码可以看出，其实ViewFlipper间接的继承了FrameLayout，也可以说ViewFlipper其实就是个FrameLayout，只不过在内部封装了动画实现和Handler实现一个循环而已。

[ViewFlipperDemo](https://github.com/zhangmiaocc/ViewFlipperDemo)

<!--more-->

###一、ViewFlipper的布局实现
布局的编写很简单，跟普通布局一样的

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <ViewFlipper
        android:id="@+id/marqueeView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_marginTop="10dip"
        android:layout_marginBottom="30dip"
        android:autoStart="true"
        android:background="#fff"
        android:flipInterval="3000"
        android:inAnimation="@anim/anim_marquee_in"
        android:outAnimation="@anim/anim_marquee_out"
        android:paddingLeft="30dp"/>

</LinearLayout>
```
这里介绍ViewFlipper用到的属性，这些属性其实都可以使用代码实现，只不过这里为了代码看上去美观，才放在布局里的

- android:autoStart：设置自动加载下一个View
- android:flipInterval：设置View之间切换的时间间隔
- android:inAnimation：设置切换View的进入动画
- android:outAnimation：设置切换View的退出动画

下面是ViewFlipper常用的方法介绍，除了可以设置上面的属性之外，还提供了其他方法

- isFlipping： 判断View切换是否正在进行
- setFilpInterval：设置View之间切换的时间间隔
- startFlipping：开始View的切换，而且会循环进行
- stopFlipping：停止View的切换
- setOutAnimation：设置切换View的退出动画
- setInAnimation：设置切换View的进入动画
- showNext： 显示ViewFlipper里的下一个View
- showPrevious：显示ViewFlipper里的上一个View

这里还涉及到两个动画其实就是一个平移的动画，它们都保存在anim文件夹中

**anim_marquee_in.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate
        android:duration="1500"
        android:fromYDelta="100%p"
        android:toYDelta="0"/>
</set>
```
**anim_marquee_out.xml**

```xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">
    <translate
        android:duration="1500"
        android:fromYDelta="0"
        android:toYDelta="-100%p"/>
</set>
```
当然，如果你对动画xml比较熟悉，自己可以实现更多好看的效果

###二、自定义ViewFlipper的广告条
当我们准备好了ViewFlipper之后，就应该在ViewFlipper里面添加我们的广告条了，下面是其中一个广告条的布局文件，另外两个雷同，只不过改了文字而已

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="wrap_content"
              android:layout_height="25dip"
              android:orientation="horizontal">

    <ImageView
        android:id="@+id/iv_image"
        android:layout_width="25dip"
        android:layout_height="25dip"
        android:src="@mipmap/ic_launcher"/>

    <TextView
        android:id="@+id/tv_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:layout_marginLeft="10dip"
        android:text="华为王牌亮相，5G+4000万徕卡"/>

</LinearLayout>
```


###三、代码为ViewFlipper添加广告条
所有的准备条件都准备好了，该开始使用代码将准备好的东西黏在一起了，代码很简单，这里就不多解释了

```java
package com.zm.viewflipperdemo;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.LayoutInflater;
import android.view.View;
import android.widget.TextView;
import android.widget.ViewFlipper;

public class MainActivity extends AppCompatActivity {

    private ViewFlipper marqueeView;
    private String[] textArray = {"华为王牌亮相，5G+4000万徕卡", "圣诞来袭，扫码关注领取大礼！", "2018即将过去，说说您的心里话"};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
        initData();
    }

    private void initView() {
        marqueeView = findViewById(R.id.marqueeView);
    }

    private void initData() {
        LayoutInflater inflater = LayoutInflater.from(this);

        for (int i = 0; i < textArray.length; i++) {
            View view = inflater.inflate(R.layout.marquee_scroll_content, null);
            TextView text = view.findViewById(R.id.tv_text);
            text.setText(textArray[i]);
            marqueeView.addView(view);
        }
    }
}

```
