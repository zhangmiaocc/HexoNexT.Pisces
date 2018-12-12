---
title: Style Guide For 前端
date: 2018-12-12 11:11:19
tags:
- 前端
categories:
- 前端
- 代码规范
---

#  命名

- 文件夹、*.JS、*.CSS: `小驼峰`*(little camel-case)*
- *.vue 组件: `大驼峰`*(big camel-case)*
- css的class命名遵循 [BEM](http://www.w3cplus.com/css/bem-definitions.html)

# 重构

## 组件

### 组件存放位置

只有 **一个** 页面内的组件，放在页面文件夹下的 `components` 文件夹下；

**两个** 页面共用的组件，放在第一个页面文件夹下的 `components` 文件夹下；

**三个以上** 页面的共用组件，放在项目文件夹下的 `components` 文件夹下。

> - 组件存放的位置会在开发过程中不断的调整重构。
> - 添加项目级公用组件需将组件参数及用法描述添加到 `README.md` 。

<!--more-->

# Vue

- 引用组件一律使用 `大驼峰`*(big camel-case)* 命名。

如遇 `Footer` 组件则可将组件名定义为 `VFooter` ，**不要使用** **MyFooter** 。


# weex

页面文件夹命名使用 `小驼峰`*(little camel-case)* ，文件夹由 **至少两个单词组成** 。

# git 

### tag 命名规范

- 分支+版本号+日期。版本号前3位逢9进1，最后一位极限逢99进1。例如：`dev-v0.3.3.01-20180110` `pre-v0.3.9.05-20181101`  `master-v0.3.9.05-20180101` 
- master的tag根据pre的最后一个版本。

# 更多

- [Vue 风格指南](https://cn.vuejs.org/v2/style-guide/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)