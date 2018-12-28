---
title: 跑马灯MarqueeView（支持任何View）
tags:
  - Android
  - 代码片段
categories:
  - Android
  - 代码片段
abbrlink: 553b92de
date: 2018-07-24 10:43:31
---

类似TextView的android:ellipsize="marquee"的跑马灯效果 任意View都可实现

源码：[MarqueeView](https://github.com/zhangmiaocc/MarqueeView)
```java
package com.zm.marqueeview;

import android.content.Context;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.view.MotionEvent;
import android.view.View;
import android.view.WindowManager;
import android.widget.HorizontalScrollView;
import android.widget.LinearLayout;

/**
 * 跑马灯MarqueeView
 */
public class MarqueeView extends HorizontalScrollView implements Runnable {

    private Context context;
    private LinearLayout mainLayout;//跑马灯滚动部分
    private int scrollSpeed = 5;//滚动速度
    private int scrollDirection = LEFT_TO_RIGHT;//滚动方向
    private int currentX;//当前x坐标
    private int viewMargin = 100;//View间距
    private int viewWidth;//View总宽度
    private int screenWidth;//屏幕宽度

    public static final int LEFT_TO_RIGHT = 1;
    public static final int RIGHT_TO_LEFT = 2;

    public MarqueeView(Context context) {
        this(context, null);
    }

    public MarqueeView(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public MarqueeView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        this.context = context;
        initView();
    }

    void initView() {
        WindowManager wm = (WindowManager) getContext().getSystemService(Context.WINDOW_SERVICE);
        screenWidth = wm.getDefaultDisplay().getWidth();
        mainLayout = (LinearLayout) LayoutInflater.from(context).inflate(R.layout.scroll_content, null);
        this.addView(mainLayout);
    }

    public void addViewInQueue(View view) {
        LinearLayout.LayoutParams lp = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.WRAP_CONTENT,
                LinearLayout.LayoutParams.WRAP_CONTENT);
        lp.setMargins(viewMargin, 0, 0, 0);
        view.setLayoutParams(lp);
        mainLayout.addView(view);
        view.measure(0, 0);//测量view
        viewWidth = viewWidth + view.getMeasuredWidth() + viewMargin;
    }

    //开始滚动
    public void startScroll() {
        removeCallbacks(this);
        currentX = (scrollDirection == LEFT_TO_RIGHT ? viewWidth : -screenWidth);
        post(this);
    }

    //停止滚动
    public void stopScroll() {
        removeCallbacks(this);
    }

    //设置View间距
    public void setViewMargin(int viewMargin) {
        this.viewMargin = viewMargin;
    }

    //设置滚动速度
    public void setScrollSpeed(int scrollSpeed) {
        this.scrollSpeed = scrollSpeed;
    }

    //设置滚动方向 默认从左向右
    public void setScrollDirection(int scrollDirection) {
        this.scrollDirection = scrollDirection;
    }

    @Override
    public void run() {
        switch (scrollDirection) {
            case LEFT_TO_RIGHT:
                mainLayout.scrollTo(currentX, 0);
                currentX--;

                if (-currentX >= screenWidth) {
                    mainLayout.scrollTo(viewWidth, 0);
                    currentX = viewWidth;
                }
                break;
            case RIGHT_TO_LEFT:
                mainLayout.scrollTo(currentX, 0);
                currentX++;

                if (currentX >= viewWidth) {
                    mainLayout.scrollTo(-screenWidth, 0);
                    currentX = -screenWidth;
                }
                break;
            default:
                break;
        }

        postDelayed(this, 50 / scrollSpeed);
    }

    @Override
    public boolean onTouchEvent(MotionEvent ev) {
        return false;
    }
}
```
<!--more-->

[![效果图](https://github.com/GITbiubiubiu/MarqueeView/raw/master/screenshot/MarqueeView.gif)](https://github.com/GITbiubiubiu/MarqueeView/raw/master/screenshot/MarqueeView.gif)