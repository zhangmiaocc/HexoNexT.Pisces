---
title: Android_Studio_3.5：稳步推进ProjectMarble计划
tags:
  - Android Studio
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: 4fc3338f
date: 2019-09-10 11:39:37
---

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190910115202.png)

我们在 Android Studio 3.5 中引入了许多质量变更，请参阅《[Android Studio 3.5 Beta 现已发布](https://developer.android.google.cn/studio/releases#3-5-0)》或者 [Android Studio 版本说明](https://android-developers.googleblog.com/2019/05/android-studio-35-beta.html)，查看完整版变更列表。当然，您也可以先阅读一下这篇文章或收看下方视频，快速了解一下其中的若干重要变更:

- **腾讯视频链接:** [v.qq.com/x/page/w091…](https://link.juejin.im?target=https%3A%2F%2Fv.qq.com%2Fx%2Fpage%2Fw0919w56970.html)
- **Bilibili 视频链接:** [www.bilibili.com/video/av657…](https://link.juejin.im?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2Fav65716536%2F)

## 系统健康

Project Marble 计划中系统健康方面的改进包括: 内存性能、输入与用户界面冻结、构建速度、CPU 使用以及 I/O 性能。我们针对这五点分别设计了新的监测机制，以便在开发过程中更准确地识别问题，此外，流程上的优化也让团队得以更好地分析用户反馈，从开发者自愿分享的统计数据和错误报告中获取更多洞见。

尽管系统健康的许多优化项可能并不为大家所熟知，不过其中还是有几个比较明显的变更，其中包括:

**自动推荐内存设置**

在 Android Studio 3.5 中，IDE 会识别出一个应用项目在 RAM 容量更高的机器上何时需要更多的 RAM，并在通知开发者增加内存堆大小；或者您也可以在 Appearance & Behavior → Memory Settings 下自行调整设置。

<!--more-->

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190910114337.png)

**用户界面冻结**

在 Project Marble 计划开发期间，我们在产品分析数据中发现 IDE 中的 XML 代码编辑速度明显较慢。我们基于这个数据点优化了 XML 输入，使得 Android Studio 3.5 的性能表现有了极大的提升。从以下两张图中您可以发现，得益于输入延迟的改进，使用 XML 编辑数据绑定表达式的速度明显加快了。

![img](https://user-gold-cdn.xitu.io/2019/8/29/16cdb222b4adbb4e?imageslim)改进前: 在 Android Studio 3.4 中编辑代码



![img](https://user-gold-cdn.xitu.io/2019/8/29/16cdb228e68e5701?imageslim)改进后: 在 Android Studio 3.5 中编辑代码



**构建速度**

为了提高 Android Studio 3.5 的构建速度，我们采取了许多措施，其中最为重要的一项变更是为[顶级注释处理器](https://developer.android.google.cn/studio/build/optimize-your-build.html#annotation_processors)添加增量构建支持，这些处理器包括 Glide、AndroidX data binding、Dagger、Realm 和 Kotlin (KAPT)。增量支持能够显著提高构建速度。更多内容，请阅读[《在 Android Studio 中加快构建速度](https://mp.weixin.qq.com/s/AyBkfNL_vodQVLgaZOD6kQ)》。

**磁盘 I/O 文件访问速度**

Android Studio 的许多用户都在使用微软旗下的 Windows 系统。我们发现与其他平台相比，Windows 的磁盘 I/O 文件访问耗时明显更久。深度分析数据后，我们发现在一些杀毒程序在默认设置下，并未将 Android Studio 的构建输出文件夹 (build output folder) 排除在扫描范围之外。在 Android Studio 3.5 中，一旦系统监测到这个情况，Studio 将通过弹窗引导您进行最优设置。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190910114351.png)

## 特性优化

除改善系统健康之外，我们还重新检查了一些关键用户流程， 修复了一些错误以及若干导致不良用户体验的问题，涉及领域包括: 数据绑定、布局、Chrome OS 支持和项目升级，而应用部署流则是其中较为关键的一项改进。

**Apply Changes**

在 Project Marble 计划期间，我们移除了 Instant Run，然后在 Android Studio 3.5 中重新构建并实现了一个更加实用的替代方案，即 [Apply Changes](https://developer.android.google.cn/studio/run#apply-changes)。Apply Changes 使用 Android Oreo 及以上版本中的平台特定 API 来确保可靠且一致的系统行为。与 Instant Run 的机制不同，更改系统配置并不会重写您的 APK 文件。为了支持此项变更，我们重构了整个部署管道，以此提升部署速度；与此同时，我们还微调了工具栏中的运行与部署按钮，希望借此为您提供更为精简的开发体验。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190910114405.png)

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190910114418.png)

总结一下，Android Studio 3.5 共修复几百个错误，并针对以下核心领域引入了若干关键变更:

**系统健康**

- 内存设置
- 内存使用报告
- 减少异常
- 用户界面冻结
- 构建速度
- IDE 速度
- Lint 代码分析
- I/O 文件访问
- 模拟器 CPU 使用

**特性优化**

- Apply Changes
- Gradle 同步
- 项目更新
- 布局编辑器
- 数据绑定
- 应用部署
- C++ 改进
- Intellij 2019 平台升级
- 动态特性支持之条件交付
- 模拟器对可折叠设备及 Google Pixel 设备的支持
- Chrome OS 支持

更多内容，请参阅[ Android Studio 版本说明](https://developer.android.google.cn/studio/releases#3-5-0)，或阅读下列与 Project Marble 计划相关的深度学习专栏或收看 Google I/O 专题分享会:

- Project Marble 计划: Apply Changes： [medium.com/androiddeve…](https://link.juejin.im?target=https%3A%2F%2Fmedium.com%2Fandroiddevelopers%2Fandroid-studio-project-marble-apply-changes-e3048662e8cd)
- 在 Android Studio 中加快构建速度： [medium.com/androiddeve…](https://link.juejin.im?target=https%3A%2F%2Fmedium.com%2Fandroiddevelopers%2Fimproving-build-speed-in-android-studio-3e1425274837)
- Android 模拟器: Project Marble 计划改进项： [medium.com/androiddeve…](https://link.juejin.im?target=https%3A%2F%2Fmedium.com%2Fandroiddevelopers%2Fandroid-emulator-project-marble-improvements-1175a934941e)
- Android Studio Project Marble 计划: Lint 性能： [medium.com/androiddeve…](https://link.juejin.im?target=https%3A%2F%2Fmedium.com%2Fandroiddevelopers%2Fandroid-studio-project-marble-lint-performance-8baedbff2521)
- Android Studio Project Marble 计划: 布局编辑器： [medium.com/androiddeve…](https://link.juejin.im?target=https%3A%2F%2Fmedium.com%2Fandroiddevelopers%2Fandroid-studio-project-marble-layout-editor-608b6704957a)
- Google I/O: Marble 计划 — Android 开发工具有哪些更新? [www.youtube.com/watch?v=8rf…](https://link.juejin.im?target=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3D8rfvfojtRss)

## 自愿数据分享与反馈

我们基于开发者提交的反馈与指标数据，判断 Android Studio 中有哪些内容适用于 Project Marble 计划，并决定具体的优化项目和实现手段。开发者可自愿在 Android Studio 内勾选数据分享，收集上来的数据将帮助团队判定产品是否含有波及全体用户的问题，接着在此基础上，调整功能开发工作的顺序，优先解决最令用户头疼的问题。为了获取最优洞见，我们在产品整合了多种不同的反馈渠道，指标数据分享是其中最基本的一款反馈工具，您可通过以下路径在 Android Studio 中启用该功能 Preferences /Settings → Appearance & Behavior → Data Sharing。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190910114430.png)

 不知道您今年是否留意到 IDE 右下角的用户心情标志。Android Studio 通过这个小小的心情标志，了解用户的使用感受，并获取与实际用例相关的反馈。这是用户向团队提交错误报告最快的途径。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190910114442.png)

## 即刻体验

**下载**

请前往[下载页面](https://developer.android.google.cn/studio)，获取 Android Studio 3.5。如果您当前正在使用旧版本的 Android Studio，请直接进行升级操作即可。

如需使用上述 Android 模拟器特性，请确保您正在运行通过 Android Studio SDK 管理器下载的 Android 模拟器 v29.1.9 或更高版本。

非常感谢大家继续踊跃反馈，与我们分享您的所感所想，建议与意见，或者任何您期望看到的新特性。如果您遇到任何错误或问题，请[提交错误报告](https://source.android.google.cn/source/report-bugs#developer-tools)，或在评论区留言。

[点击这里]([http://services.google.cn/fb/forms/yourquestions/](http://services.google.cn/fb/forms/yourquestions/))提交产品反馈建议


