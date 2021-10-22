---
title: Flutter如何开发web端
tags:
  - Android
  - Flutter
categories:
  - Android
  - Flutter
abbrlink: b683bca5
date: 2021-10-16 10:46:21
---


flutter开发移动端与开发web端有些区别，开发移动端会涉及到各自原生系统里特有的一些内容，iOS端与Android通过插件的形式引入的项目当中，但不需要考虑响应式布局。而web端开发需要考虑到窗口的大小变化，需要考虑响应式布局。

开启对web开发的支持
flutter开发要支持web，需要在命令行中输入以下命令打开支持的平台（以下列举了各个平台支持的命令行）：

> flutter config —enable-web-desktop 
flutter config —enable-windows-desktop 
flutter config —enable-macos-desktop 
flutter config —enable-linux-desktop 

之后再次输入 `flutter config`检测开启的情况，如果检测到如下图所示则表示开启成功。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211022110841.png)

这时候可以创建项目了，创建的时候勾选Web选项即可。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211022111535.png)


<!--more-->

## web开发的注意事项

### 支持响应式布局的控件

A. MediaQuery
响应式布局的本质是监听浏览器宽高的变化进而修改UI的样式，所以需要能够监听宽高的变化，此时可以使用MediaQuery控件。MediaQuery控件继承自InheritedWidget，通过`MediaQuery.of(context).size`的方式实时获取到浏览器宽高的变化，进而对全局的UI进行调整。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211022111054.png)

B.  LayoutBuilder
LayoutBuilder是MediaQuery的简化版本，可以实时监控父控件尺寸的变化（不是浏览器宽高的变化），进而对当前控件的UI进行调整。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211022111103.png)

C. AspectRatio
这是一个指定宽高比例的控件，会随着浏览器宽度变化进行等比例缩小或者放大。如果要保证某个控件的宽高保持一致，则需要使用AspectRatio。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211022111112.png)

D. Flexible 与 Expanded
Flexible与Expanded是用在Row或者Column中的控件，前者可以用来控制控件在Row或者Column中占用的比例，后者则用来填充Row或者Column中剩余的空间。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211022111121.png)

E. FractionallySizedBox
这是一个设置占位比例的控件，跟AspectRatio类似，可以设置占有父控件多大比例。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211022111132.png)

### 响应式布局支持的开源库
A. responsive_framework
这个库支持屏幕尺寸变化时对所用控件进行缩放控制或者进行实时UI调整，支持手机、平板、电脑尺寸的设置，使用方便。支持缩放控制是其最大亮点。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211022111159.png)

B. responsive_builder
这个库支持屏幕尺寸的变化时对所有控件进行实时UI调整，并能检测移动端横竖屏变化。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/dd792057-6327-497f-98e1-b06144334e10.gif)

### 引用开源库的注意点
如果要做到开发web的时候也需要支持移动端，在引入开源库时要注意其支持的平台种类。如下图中支持的平台就包含了安卓、iOS、Linux、MacOS、Web以及windows。最好引入的开源库支持全平台。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211022111227.png)

参考资料：
https://betterprogramming.pub/how-to-build-responsive-apps-with-flutter-widgets-review-b22c6dec6904
https://medium.com/flutter-community/seven-things-you-should-know-before-starting-with-flutter-web-8e48555d819e
