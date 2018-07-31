---
title: AnimationDrawable使用简介
date: 2018-07-31 14:46:16
tags:
- blog
- markdown
- Android 
categories:
- Android 
---

Drawable animation可以加载Drawable资源实现帧动画。AnimationDrawable是实现Drawable animations的基本类。推荐用XML文件的方法实现Drawable动画，不推荐在代码中实现。这种XML文件存放在工程中res/drawable/目录下。XML文件的指令(即属性)为动画播放的顺序和时间间隔。

​     在XML文件中<animation-list>元素为根节点，<item>节点定义了每一帧，表示一个drawable资源的帧和帧间隔。下面是一个XML文件的实例:

```html
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">
    
    <item
        android:drawable="@drawable/loading1"
        android:duration="100" />

    <item
        android:drawable="@drawable/loading2"
        android:duration="100" />

    <item
        android:drawable="@drawable/loading3"
        android:duration="100" />

    <item
        android:drawable="@drawable/loading4"
        android:duration="100" />
        
    <item
        android:drawable="@drawable/loading5"
        android:duration="100" />

</animation-list>
```

 设置[Android](http://lib.csdn.net/base/android):oneshot属性为true,表示此次动画只执行一次，最后停留在最后一帧。设置为false则动画循环播放。文件可以添加为Image背景，触发的时候播放。

下面简单通过一个例子，来给ImageView设置次动画效果，具体实现方法为

通过View. setBackgroundResource(resID).    animation.start().

```java
private AnimationDrawable animationDrawable;
	    img = (ImageView) findViewById(R.id.img);
	    img.setImageResource(R.drawable.anim_loading);
	    animationDrawable = ((AnimationDrawable) img.getDrawable());
	    animationDrawable.start();
```

 

 