---
title: 完美解决Genymotion无法使用adb命令的问题
date: 2018-12-17 16:11:52
tags:
- Android 
- Android Tips
- Genymotion
categories:
- Android
- Android Tips
---


我在运行Genymotion虚拟机进行android应用调试的时候，无法用Powershell(cmd)进入adb shell，显示的界面是这样的：

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fy9ta2n2f1j30ih06iju9.jpg)

导致无法正常进行adb调试，找了很多方法都没用，后来修改了genymotion中的settings 中的ADB选项中的SDK路径，保持跟你当前应用的eclipse或者android studio中的SDK库一致，然后问题就解决了；

![](https://ws2.sinaimg.cn/large/006tNbRwgy1fy9tbilv40j31490u0tly.jpg)

<!--more-->

![](https://ws3.sinaimg.cn/large/006tNbRwgy1fy9tdfksmoj30ig08aq6e.jpg)