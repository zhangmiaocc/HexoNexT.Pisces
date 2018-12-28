---
title: Android Studio自定义注释模板
tags:
  - Android
  - 注释模板
categories:
  - Android
  - 注释模板
abbrlink: d339abc3
date: 2018-09-26 17:24:33
---

代码的注释是我们平时必须面对的问题，今天我们就来看看如何自定义属于自己的注释模板，提高我们的开发效率。

1.新建的类自动生成的注释； 

2.自定义注释模板。

## 新建类自动生成的注释

1.打开相应的设置： 
​     File–>Settings–>Editor–>File and code Template。

选择Files中的Class

在上面添加你想要添加的注释：

![image-20180926173840698](https://ws3.sinaimg.cn/large/006tNc79ly1fvn2vsyla0j31kw0q5go5.jpg)

<!--more-->

下面有一些变量可以选择：

{USER} ：表示你系统名字； 
{DATE}： 表示当前时间； 
{NAME}:表示类名。 
而且后面都有注释，相信大家也都能看得懂。

这是设置后的结果画面：

![image-20180926172806712](https://ws3.sinaimg.cn/large/006tNc79ly1fvn2ktdyadj31kw0tqju7.jpg)

这边有许多变量可以引用，想要哪些变量，或者想自定义成什么样的注释，就看你自己的想象力了。 

## 万能注释模板 Java篇

1.打开相应位置： 
 File–>Setting–>Editor–>LiveTemplate：

2.新建一个Live Group: 
 点击右边的+号，选择Template Group,命名自己的一个注释包。我自己命名为Zm Template Group。

 ![image-20180926173317831](https://ws3.sinaimg.cn/large/006tNc79ly1fvn2q7fu6aj31kw0yijzw.jpg)

3.新建一个LIve Template：  在你刚刚新建的group下点击+号，新建一个Live Template:

![image-20180926173353858](https://ws4.sinaimg.cn/large/006tNc79ly1fvn2qu51x4j31kw0yaqdf.jpg)

Abbraviation:是你设置的快捷键，我的快捷键是z。

Expand with :补全你的注释的快捷键，默认为TAB,我改为了Enter。 

4.添加你的注释： 

在下面自定义你想要的注释，这边的注释有点不同了，这边可以自定义变量名，格式和我的一样，用双$包起来。

```java
/**
 * @author   $user$
 * @email    zm@zhangmiao.cc
 * @date     $date$ $time$
 * @describe $desc$
 */
```

5.点击Edit Variables，在Expression选择你需要方法，相当于给你的变量赋值:

![image-20180926173610970](https://ws4.sinaimg.cn/large/006tNc79ly1fvn2t7wkq9j31kw101qcv.jpg)

6.选择你要运用的地方： 

![image-20180926173728634](https://ws4.sinaimg.cn/large/006tNc79ly1fvn2uki5m1j31kw101wof.jpg)

你可以选择Java，C++ 等等。  点击Apply。就成功了，下面让我们来看看效果：

![image-20180926173803405](https://ws1.sinaimg.cn/large/006tNc79ly1fvn2v5k6pgj31kw0umdio.jpg)