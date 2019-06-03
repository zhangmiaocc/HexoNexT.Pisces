---
title: Tangram与VirtualView
tags:
  - Android
  - Tangram
categories:
  - Android
  - Tangram
abbrlink: b1fd08a9
date: 2018-11-08 17:13:19
---

### VirtualView

Tangram中已经实现了页面布局的动态化，我们可以通过配置json文件自由的布局；但还有一个局限性，json中使用的卡片或者组件的type，必须是注册OK的（也就是在客户端已经实现好的）才能使用；如果想要在原来的版本中新增一个type类型的组件，这是没有办法做到的，还是只能通过升级客户端来实现。
 于是，VirtualView出现了。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190603171427.png)

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190603171447.png)

<!--more-->

基布局描术文件是一个xml文件，并附带一个json数据文件:
描述布局文件示例：（其中的相关数据来源都可以从数据json文件中使用表达式获取）

```xml
 <RatioLayout
    orientation="H"
    layoutWidth="match_parent"
    layoutHeight="wrap_content"
    background="#f8f8f8">
    <VHLayout
        layoutRatio="1"
        orientation="V"
        layoutWidth="0"
        layoutHeight="match_parent"
        background="#f8f8f8">
        <VImage
            layoutWidth="match_parent"
            layoutHeight="120.5"
            scaleType="fit_xy"
            src="${picture_2}"/>
        <VLine
            layoutWidth="match_parent"
            layoutHeight="0.5"
            paintWidth="0.5"
            orientation="H"
            color="#e8ecef" />
        <VImage
            layoutWidth="match_parent"
            layoutHeight="60"
            scaleType="fit_xy"
            src="${picture_5}"/>
    </VHLayout>
    <VLine
        layoutWidth="0.5"
        layoutHeight="match_parent"
        paintWidth="0.5"
        orientation="V"
        color="#e8ecef" />
    <VHLayout
        layoutRatio="1"
        orientation="V"
        layoutWidth="0"
        layoutHeight="match_parent"
        background="#f8f8f8">
        <VImage
            layoutWidth="match_parent"
            layoutHeight="60"
            scaleType="fit_xy"
            src="${picture_3}"/>
        <VLine
            layoutWidth="match_parent"
            layoutHeight="0.5"
            paintWidth="0.5"
            orientation="H"
            color="#e8ecef" />
        <VImage
            layoutWidth="match_parent"
            layoutHeight="60"
            scaleType="fit_xy"
            src="${picture_4}"/>
        <VLine
            layoutWidth="match_parent"
            layoutHeight="0.5"
            paintWidth="0.5"
            orientation="H"
            color="#e8ecef" />
        <VImage
            layoutWidth="match_parent"
            layoutHeight="60"
            scaleType="fit_xy"
            src="${picture_6}"/>
    </VHLayout>
</RatioLayout>
```

数据json文件示例：

```json
{
  "picture_2": "http://xxxx/images/59afb155c769f.png",
  "picture_3": "http://xxxx/images/59afb303042ca.png",
  "picture_4": "http://xxxx/images/59afb303042cb.png",
  "picture_5": "http://xxxx/images/59afb36cf1eb8.png",
  "picture_6": "http://xxxx/images/59afb303042cd.png"
}
```

xml文件需要经过编绎生成目标文件才可加载使用，如果用于项目中一般下发的都是经过编绎后的文件，也可以在客户端实时编绎。

> 一个xml就是一个组件，Tangram通过加载这个xml文件即可使用该xml文件所描术的组件，从而实现了动态新增组件类型的功能。

