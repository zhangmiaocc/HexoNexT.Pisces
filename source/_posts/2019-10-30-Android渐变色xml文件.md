---
title: Android渐变色xml文件
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: 2851801a
date: 2019-10-30 14:05:35
---

在drawable目录下新建drawable resource file，修改xml代码

```xml
<?xml version="1.0" encoding="utf-8"?>

<shape xmlns:android="http://schemas.android.com/apk/res/android" >
    <!--
    android:startColor="#aa000000"  渐变起始色值
    android:centerColor=""      渐变中间色值
    android:endColor="#ffffffff"    渐变结束颜色
    android:angle="45"      渐变的方向 默认为0 从做向右 ，90时从下向上 必须为45的整数倍
    android:type="radial"       渐变类型 有三种 线性linear 放射渐变radial 扫描线性渐变sweep
    android:centerX="0.5"       渐变中心相对X坐标只有渐变类型为放射渐变时有效
    android:centerY="0.5"       渐变中心相对Y坐标只有渐变类型为放射渐变时有效
    android:gradientRadius="100"    渐变半径 非线性放射有效
     -->
    <gradient
        android:startColor="#b7bbd9"
        android:endColor="#5CACEE"
        android:angle="90"
        />

</shape>
```

<!--more-->