---
title: ListView中添加的HeadView隐藏时仍然占用空间的解决方法
date: 2018-09-06 16:59:04
tags:
- Android 
categories:
- Android 
- View
---

今天在开发的时候遇到了一个ListView中添加的HeadView隐藏时仍然占用空间的解决方法；

具体问题如下：listView.addHeadView(headView);

但是在执行headView.setVisibility(View.GONE);后headView虽然隐藏了，但是仍然占用了空间；

解决方法：

在添加HeadView之前首先创建一个父布局parentView,即：
```java
LinearLayout parentView=new LinearLayout (Context context);

parentView.addView(headView);

listView.addHeadView(parentView);
```
之后再进行隐藏：
```java
//就可以实现以上所说的效果了。
headView.setVisibility(View.GONE);
```

<!--more-->