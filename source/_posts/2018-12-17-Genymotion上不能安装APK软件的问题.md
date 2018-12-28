---
title: Genymotion上不能安装APK软件的问题
tags:
  - Android
  - Android Tips
  - Genymotion
categories:
  - Android
  - Android Tips
abbrlink: f1cdd64c
date: 2018-12-17 16:28:52
---

> Genymotion模拟器不能安装APK的原因
> 官网给出的解释：Genymotion模拟器使用的是x86架构，在第三方市场上的应用有部分不采用x86这么一种架构，所以在编译的时候不通过，报“APP not installed”，可以下载Genymotion提供的ARM转换工具包，将应用市场中的ARM架构的apk转换成Genymotion可以编译的x86架构；

### 直接安装报错如下图：

An error occured while deploying the file.
This probably means that the app contains ARM native code and your Genymotion device cannot run ARM instructions. You should either build your native code to x86 or install an ARM translation tool in your device.
部署文件时出错。
这可能意味着应用程序包含本地ARM代码和你的genymotion设备无法运行ARM指令。你可以建立你的原生代码的x86或在您的设备上安装一个臂的翻译工具。

<!--more-->

![](https://ws4.sinaimg.cn/large/006tNbRwgy1fy9tpcgr68j30s00fu0uv.jpg)

### 解决方法
1.用Android Studio 创建一个ARM的虚拟机；（当然这个不是你想要的）
2.下载Genymotion-ARM-Translation-Librarities工具转换包；下载路径：

链接:https://pan.baidu.com/s/1OOj72JqNnTtSZJnCXCoFzA  密码:p8c4

将下载号的工具包直接拖拽到Genymotion中，然后提示重启模拟器；


![](https://ws1.sinaimg.cn/large/006tNbRwgy1fy9tp61icbj30nc08ct9j.jpg)