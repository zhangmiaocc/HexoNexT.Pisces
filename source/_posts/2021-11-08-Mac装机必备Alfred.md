---
title: Mac装机必备Alfred
tags:
  - Mac
  - Alfred
categories:
  - Mac
  - Alfred
abbrlink: d083e6c8
date: 2021-11-08 16:04:43
---

### 安装[Alfred](https://www.alfredapp.com/)

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108160814.png)

<!--more-->

### 快捷键设置
为了快速打开`Alfred`，我们需要为它设置一个快捷键，打开`Alfred`偏好设置的`General`选项卡，选中`Alfred Hotkey`输入框，直接使用键盘键入你喜欢的快捷键即可。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108160920.png)

我使用的`alt+空格键`，当初设置时，由于这个快捷键被Mac系统的Spotlight占用了，无法设置成功。如遇到相同的情况，需要先到`系统偏好设置-键盘-快捷键-聚焦`中取消勾选`alt+空格键`打开“聚焦”搜索的设置，然后再返回到`Alfred`中设置即可。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108161042.png)

### 自定义外观
进入`Appearance`选项卡，Alfred为我们提供了几种外观样式，如果你都不喜欢，也可以自定义外观。点击左下角的“+”，创建你的专属样式。通过双击相应的组件，即可打开系统的调色器，可以自由的搭配自己喜欢的颜色。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108161114.png)

进入`Features`选项卡，在左侧列表中，罗列的是`Alfred`的基础功能，包括默认`搜索`、`文件搜索`、`网页搜索`、`网页书签`、`计算器`、`字典`、`联系人`、`剪贴板`、`iTunes`、`系统操作`、`终端`等。

### 默认搜索（Default Results）

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108161251.png)

Essentials和Extras设置我们的搜索类型，可以设置搜索应用程序、联系人、偏好设置、文件夹、文本文件、压缩文件、文档、图片、脚本等。

Unintelligent建议不要勾选，会影响我们的搜索速度以及搜索结果。

Search Scope可以设置Alfred的搜索范围，点击右上角的"+"，可以添加其他的搜索范围；或者选中某项，按`Delete`键移除该搜索范围。

### 文件搜索（File Search）
默认搜索会搜索出所有类型的内容，包括邮件、联系人等其他内容，都是我根本不想搜索到的文件类型，这时就可以使用文件搜索，把一些不需要的类型过滤掉。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108161334.png)

- 空格键 + 关键字：文件搜索
- find + 关键字：打开文件所在的Finder
- in + 关键字：搜索文件内容中带有该关键字的文件

### 网页搜索（Web Search）
这是小编使用频率最高的功能，有了它，再也不怕记不住网址了。Alfred中已经默认设置了很多国外的网站，但大多数都是用不上的，不需要的只要取消勾选就行。点击右下角的`“Add Custom Search”`，即可添加新的网站搜索。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108161453.png)

比如添加马可菠萝网站，只要到马可菠萝的搜索页面随便搜索一个内容，然后复制结果页面的网址，把具体的内容改成`{query}`即可，关键字填写macbl，然后保存。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108161529.png)

- 马可菠萝：https://www.macbl.com/search/{query}
- 百度：https://www.baidu.com/s?ie=utf-8&f=8&wd={query}
- Dribbble：https://dribbble.com/search?q={query}

使用时，在Alfred中输入 macbl Alfred，按回车键即可打开马可菠萝搜索页面，找到Alfred啦！

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108161804.png)

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108161833.png)

### 剪贴板（Clipboard）
剪贴板功能是我选择Alfred的主要原因，可以查看Alfred的所有剪贴历史记录，节省了重复操作的时间，非常强大

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108161903.png)

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/1557323280715908.gif)

### 系统操作（System）
用简单的命令，来控制系统操作，比如最常用的清空垃圾桶(enptytrash)，休眠(sleep)，强制退出应用程序(forcequit)等，快捷键都可以自由设置。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211108162116.png)










