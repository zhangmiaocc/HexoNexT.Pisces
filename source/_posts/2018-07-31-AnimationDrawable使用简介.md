---
title: AnimationDrawable使用简介
tags:
  - Android
  - Animation
categories:
  - Android
  - Animation
abbrlink: d6df4d7b
date: 2018-07-31 14:46:16
---

Drawable animation 可以加载 Drawable 资源实现帧动画。AnimationDrawable 是实现 Drawable animations 的基本类。推荐用 XML 文件的方法实现 Drawable 动画，不推荐在代码中实现。这种 XML 文件存放在工程中 res/drawable/目录下。XML 文件的指令(即属性)为动画播放的顺序和时间间隔。

​ 在 XML 文件中<animation-list>元素为根节点，<item>节点定义了每一帧，表示一个 drawable 资源的帧和帧间隔。下面是一个 XML 文件的实例:

```html
<animation-list
  xmlns:android="http://schemas.android.com/apk/res/android"
  android:oneshot="false"
>
  <item android:drawable="@drawable/loading1" android:duration="100" />

  <item android:drawable="@drawable/loading2" android:duration="100" />

  <item android:drawable="@drawable/loading3" android:duration="100" />

  <item android:drawable="@drawable/loading4" android:duration="100" />

  <item android:drawable="@drawable/loading5" android:duration="100" />
</animation-list>
```

 <!--more-->

设置[Android](http://lib.csdn.net/base/android):oneshot 属性为 true,表示此次动画只执行一次，最后停留在最后一帧。设置为 false 则动画循环播放。文件可以添加为 Image 背景，触发的时候播放。

下面简单通过一个例子，来给 ImageView 设置次动画效果，具体实现方法为

通过 View. setBackgroundResource(resID). animation.start().

```java
private AnimationDrawable animationDrawable;
	    img = (ImageView) findViewById(R.id.img);
	    img.setImageResource(R.drawable.anim_loading);
	    animationDrawable = ((AnimationDrawable) img.getDrawable());
	    animationDrawable.start();
```

Demo 地址：https://github.com/zhangmiaocc/AnimationDrawable

Blog 地址：https://zhangmiao.space/
