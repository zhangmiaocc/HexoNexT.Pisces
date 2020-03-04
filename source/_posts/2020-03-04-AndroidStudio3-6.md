---
title: Android Studio 3.6 正式版终于发布了
tags:
  - Android Studio
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: aaf10946
date: 2020-03-04 10:54:25
---

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304110901.png)

如题，Android Studio 3.6 正式版终于发布了，值得兴奋呀，毕竟 3.5 大版本更新也已经差不多半年了，撒花撒花！这次更新又更新了什么呢？

包括有设计、开发、构建、测试、优化等多方面，下面我们来看看 Release Notes 写了些什么吧！

## Release Notes

我们很高兴宣布 Android Studio 3.6 发布稳定版本了，该版本内有一些针对性的新特性，主要解决了在代码编辑和调试用例中的质量问题。这是我们在 Project Marble 结束之后的第一个版本，其重点是构建强大的集成开发环境（IDE）的基本功能和流。我们从 Project Marble 中学到了很多，在 Android Studio 3.6 中，我们引入了一小部分功能，完善的现有功能，并花费了很大的精力来解决错误并改善基础性能，以确保我们达到去年设定的高质量标准。

Android Studio 3.6 的一些亮点包括一种使用 XML 快速设计、开发和预览应用布局的新方法，在设计编辑器中提供了新的拆分视图。此外，您不再需要手动键入 GPS 坐标来测试应用的位置，因为我们现在将 Google 地图直接嵌入到 Android 模拟器扩展控制面板中。最后，通过针对片段和活动的自动内存泄漏检测，我们简化了应用并查找 Bug。我们希望所有这些功能可以帮助您在 Android 上开发时更快乐、更高效。

感谢在预览版中提供早期反馈的用户。您的反馈帮助我们迭代和改进 Android Studio 3.6 中的功能。如果您已准备好迎接下一个稳定版本，并且想要使用一组新的生产力功能，Android Studio 3.6 已准备好下载，以便您入门。

以下是 Android Studio 3.6 中由主要开发人员流组织的全部新功能列表。

<!--more-->

### 设计

#### 在设计编辑器中拆分视图

设计编辑器（如布局编辑器和导航编辑器）现在提供"拆分"视图，使您能够同时查看 UI 的"设计和代码"视图。拆分视图将替换和改进较早的"预览"窗口，并可以逐个文件进行配置，以保留上下文信息（如缩放因子和设计视图选项），因此您可以选择最适合每个用例的视图。要启用拆分视图，请单击编辑器窗口右上角的"拆分"图标。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304111247.png)

#### 颜色选取器资源选项卡

在此版本中，我们希望更轻松地应用已定义为颜色资源的颜色。在 Android Studio 3.6 中，颜色选取器将填充应用中的颜色资源，以便快速选择和替换颜色资源值。颜色选取器可在设计工具和 XML 编辑器中访问。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304111308.png)

### 开发

#### 视图绑定

视图绑定是一项功能，允许您在引用代码中的视图时提供编译时安全性，从而更轻松地编写与视图交互的代码。启用后，视图绑定将为每个模块中存在的 XML 布局文件生成一个绑定类。在大多数情况下，视图绑定将替换 findViewById。您可以引用具有 ID 的所有视图，这些视图没有空指针或类强制转换异常的风险。这些差异意味着布局和代码之间的不兼容将导致生成在编译时失败，而不是在运行时。要在项目中启用视图绑定，请在每个模块的生成中包括以下内容。

```java
android {
    viewBinding.enabled = true
}
```

#### Android NDK 修改

Android Studio 中的以下 Android NDK 功能以前在 Java 中支持，现在 Kotlin 也支持：

- 从 JNI 声明导航到 C/C++ 中的相应实现函数。通过将鼠标悬停在托管源代码文件中行号附近的 C 或C++项标记上，查看此映射。
- 自动为 JNI 声明创建存根实现函数。首先定义 JNI 声明，然后在要激活的 C/C++ 文件中键入"jni"或方法名称。

### IntelliJ 平台更改

Android Studio 3.6 包括 IntelliJ 2019.2 平台版本。此 IntelliJ 版本包括许多改进，从新的服务工具窗口到大大缩短的启动时间。

### 应用更改

现在，您可以通过单击"应用代码更改"或"应用更改并重新启动活动"来添加类，然后将该代码更改部署到正在运行的应用。

### 构建

#### Android Gradle Plugin (AGP) updates

Android Gradle 插件 3.6 及更高版本包括对 Maven 发布 Gradle 插件的支持，该插件允许您将构建项目发布到 Apache Maven 存储库。Android Gradle 插件为应用或库模块中的每个生成变体项目创建一个组件，您可以使用该组件将出版物自定义到 Maven 存储库。此更改将更轻松地管理各种目标的发布生命周期。

此外，Android Gradle 插件在大型项目的注释处理/KAPT 方面取得了显著的性能改进。这是由 AGP 现在直接生成 R 类字节码，而不是 .java 文件引起的。

#### 新的打包工具

Android 构建团队不断进行更改以提高生成性能，在此版本中，我们将默认打包工具更改为 zipflinger 以进行调试生成。用户应该看到生成速度的提高，但您也可以通过设置 `android.useNewApkCreator_false` 在您的分级中恢复使用旧的打包工具。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304111433.png)

### 测试

#### Android 模拟器 - Google 地图

Android 模拟器 29.2.12 为应用开发人员提供了一种与模拟设备位置进行接口的新方式。我们在扩展控件菜单中嵌入了 Google 地图用户界面，以便更轻松地指定位置，并构建来自位置对的路由。可以保存单个点并将其重新发送到设备作为虚拟位置，而路由可以通过键入地址或单击两个点来生成。当路线上的位置发送到来宾 OS 时，可以实时重播这些路由。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304111448.png)

#### 多屏支持

模拟器 29.1.10 包括对多个虚拟显示器的初步支持。由于有更多的设备具有多个显示器，因此在各种多显示器配置上测试应用非常重要。用户可以通过设置菜单（扩展控件和设置）配置多个显示器。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304111510.png)



![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304111526.png)

#### SDK 断点续传

当使用 Android Studio SDK 管理器下载 Android SDK 组件和工具时，Android Studio 现在允许您恢复中断的下载（例如，由于网络问题），而不是从一开始就重新启动下载。当互联网连接不可靠时，此增强功能对于大型下载（如 Android 模拟器或系统映像）特别有用。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304111543.png)

#### 导入的 APK 可以自动更新

Android Studio 允许您导入外部构建的 APK 来调试和分析它们。以前，当对这些 APK 进行更改时，您必须再次手动导入它们并重新附加符号和源。Android Studio 3.6 现在会自动检测对导入的 APK 文件所做的更改，并为您提供就地重新导入该文件的选项。

### 优化

#### 内存探查器中的泄漏检测

根据反馈，我们在内存探查器中添加了检测可能泄漏的活动和片段实例的能力。要开始使用，请在内存探查器中捕获或导入堆转储文件，并选中"活动/碎片泄漏"复选框以生成结果。有关 Android Studio 如何检测泄漏的详细信息，请参阅我们的文档。



![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304111557.png)

#### 在 APK 分析器中去解类和方法字节码

使用 APK 分析器检查 DEX 文件时，现在可以取消分类和方法字节码。在 DEX 文件查看器中，加载要分析的 APK 的 ProGuard 映射文件。加载后，您将能够通过选择"显示字节码"右键单击要检查的类或方法。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200304111611.png)

## 概括地说，Android Studio 3.6 包括这些新的增强功能和功能

### 设计

- 设计编辑器中的拆分视图
- 颜色选取器资源选项卡已

### 开发

- 视图绑定
- NDK 修改
- Intelli J平台更改
- Add classes with Apply Changes

## 构建

- Android Gradle Plugin (AGP) 升级
- 新的打包工具

## 测试

- Android模拟器Google Maps UI
- 多显示器支持
- 可恢复的SDK下载
- 导入的APK的就地更新

## 优化

内存探查器中的泄漏检测
 在APK分析器中反混淆类和方法字节码
 将Kotlin来源附加到导入的APK

## 下载

从下载页面下载 Android Studio 3.6。如果您使用的是早期版本的 Android Studio，则只需将其更新为最新版本的 Android Studio。要使用上述 Android Emulator 功能，请确保您至少运行通过 Android Studio SDK 管理器下载的 Android Emulator v29.2.12。

- [Google 下载地址](https://developer.android.com/studio)