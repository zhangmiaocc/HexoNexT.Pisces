---
title: Alfred进阶教程，自定义Workflows
tags:
  - Mac
  - Alfred
categories:
  - Mac
  - Alfred
abbrlink: fa126efb
date: 2021-11-08 16:25:42
---

在上一篇[《Mac装机必备-Alfred的基础使用教程》](https://zhangmiao.cc/posts/d083e6c8.html)中，已为大家介绍了Alfred的基础功能。其实除了Alfred已有的功能外，Alfred还支持用户自定义工作流。

通过设置好触发器、输入、操作、实用程序、输出，就可以自由搭建工作流。在本教程中，我将创建一个简单的热键工作流，用来一键启动我每天多次使用的一些应用程序和网页。

进入Alfred的偏好设置中的*workflows*标签页，点击左下角的“*+*”，然后选择*Templates > Files and Apps > Launch file group from hotkey*，创建一个用热键打开的工作流。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108163119.png)

<!--more-->

然后为你的工作流编辑名称、图标等信息，便于识别。（图标可以直接拖入）

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108165131.png)

编辑完成后，点击右下角的Save即可创建出一个名为work的工作流。左边图标是热键，右边图标是你要创建的动作。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108165131.png)

双击左边图标，打开热键设置窗口，选中输入框，直接在键盘上键入你想要设置的热键，然后保存。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108165216.png)

双击右侧图标，打开动作设置窗口，你可以选择一次启动多个Mac软件或文件夹，比如我想要一次打开AndroidStudio、Safari、Sourcetree三个应用程序，直接将它们拖入此窗口即可。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108170007.png)

Alfred除了能设置打开多个Mac软件外，还可以设置打开多个网页。比如我们想同时打开马可菠萝网站，可以在窗口任意位置右键，选择Actions > Open URL。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108170352.png)

在弹窗中将马可菠萝的网址 https://zhangmiao.cc/ 复制粘贴进去，并选好默认浏览器，保存即可。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108170432.png)

最后再把这个新的动作链接到热键的后面，即完成打开马可菠萝网站的设置了。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108170809.png)

了解Alfred的工作流程，能够帮助你轻松完成各种重复任务，让你以前所未有的方式在Mac上提高效率！