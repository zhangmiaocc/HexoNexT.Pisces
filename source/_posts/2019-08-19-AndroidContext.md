---
title: Android Context细节解析
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: b902250c
date: 2019-08-19 17:37:48
---

## Context到底是啥？

Context 本身是一个抽象类，它的实现类为 ContextImpl。

另外有子类 ContextWrapper 和 ContextThemeWrapper，这两个子类都是 Context 的代理类，主要区别是 ContextThemeWrapper 有自己的主题资源。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190819173910.png)

一个 Context 意味着一个**场景**，一个场景就是我们和软件进行**交互的一个过程**。

从安卓程序的角度来看，其实一个 Activity 就是一个 Context ，一个 Service 也是一个 Context。

<!--more-->

## Context有啥作用？

有啥用？要看它能做啥，看看主要提供了哪些接口了。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190819174053.png)

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190819174212.png)

还挺多的，看起来管得挺多，四大组件都管着，像个 Application 大管家。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190819174304.png)

## 一个app里有多少个Context？

前面说啦，一个Activity就是一个场景（Context），一个Service也是一个场景，所以，应用程序中有多少个Activity或者Service就会有多少个Context对象，也就是有多少个场景。

## ContextImpl和ContextWrapper有啥区别？

看下ContextWrapper：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190819174343.png)

再看下ContextImpl：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190819174949.png)

比较下：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190819174503.png)

不同组件创建ContextImpl的方式：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190819174546.png)

## 总结

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190819174608.png)

Context相当于Application的大管家；

ContextWrapper、ContextThemWrapper都是Context的代理类，ContextImpl是Context的主要实现类，是个实力派！



参考:

https://juejin.im/post/5c1fab7d5188254eb05fbe48

https://juejin.im/post/5865bfa1128fe10057e57c63