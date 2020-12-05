---
title: Android Jetpack
tags:
  - Kotlin
  - Jetpack
categories:
  - Android
  - Jetpack
abbrlink: f1035a2c
date: 2020-10-19 15:36:23
---

Jetpack是一套库、工具和指南，可帮助开发者更轻松地编写优质应用。这些组件可帮助您遵循最佳做法、让您摆脱编写样板代码地工作并简化复杂任务，以便将精力集中放在所需代码上。

Jetpack包含与平台API解除捆绑地`androidx.*`软件包库。这意味着，它可以提供向后兼容性，且比Android平台地更新频率更高，一次确保您始终可以获取最新且最好地Jetpack组件版本。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20201019153720.png)

# Android Jetpack组件

Android Jetpack组件是库的集合，这些库是为协同工作而构建的，不过也可以单独采用，同时利用Kotlin语言功能帮助您提高工作效率，可全部使用，也可混合搭配！
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20201019153732.png)

<!--more-->

## 基础（Foundation）

基础组件可提供横向功能，例入向后兼容性、测试和Kotlin语言支持。

- **Android KTX**
  编写更简洁、惯用的kotlin代码
- **AppCompat**
  在较低版本的Android系统上恰当地降级
- **Auto**
  有助于开发Android Auto应用地组件
- **检测**
  从Android Studio中快速检测基于Kotlin或Java的代码
- **多Dex处理**
  为具有多个dex文件的应用提供支持
- **安全**
  按照安全最佳做法读写加密文件和共享偏好设置
- **测试**
  用于单元和运行时界面测试的Android测试框架
- **TV**
  有助于开发Android TV应用的组件
- **Wear OS by Google谷歌**
  有助于开发Wear应用的组件

## 架构（Architecture）

**架构组件**可帮助您设计文件、可测试且易维护的应用。

- **数据绑定**
  以声明方法将可观察数据绑定到界面元素
- **Lifecycles**
  管理您的Activity和Fragment生命周期
- **LiveData**
  在底层数据库更改时通知视图
- **Navigaton**
  处理应用内导航所需的一切
- **Paging**
  逐步从您的数据源按需加载信息
- **Room**
  流畅地访问SQLite数据库
- [**ViewModel**](https://blog.csdn.net/qq_40833790/article/details/105054936)
  以注重生命周期地方式管理界面相关的数据
- **WorkManager**

管理您的Android后台作业

## 行为（Behavior）

行为组件可帮助您的应用与标准Android服务（如通知、权限、分享和Google助理）相集成。

- **CameraX**
  轻松地向应用中添加相机功能
- **下载管理器**
  安排和管理大量下载任务
- **媒体和播放**
  用于媒体播放和路由（包括Google Cast）地向后兼容API
- **通知**
  提供向后兼容的通知API，支持Wear和Auto
- **权限**
  用于检查和请求应用权限的兼容性API
- **偏好设置**
  创建交互式设置屏幕
- **共享**
  提供适合应用操作栏的共享操作
- **切片**
  创建可在应用外部显示应用数据的灵活界面元素

## 界面（UI）

界面组件可提供微件和辅助程序，让您的应用不仅简单易用，还能带来愉悦体验。了解有助于简化界面开发的**Jetpack Compose**。

- **动画和过渡**
  移动微件和在屏幕之间过渡
- **表情符号**
  在旧版平台上启用最新的表情符号字体
- **Fragment**
  组件化界面的基本单位
- **布局**
  使用不同的算法布置微件
- **调色板**
  从调色板中提取有用的信息





- 