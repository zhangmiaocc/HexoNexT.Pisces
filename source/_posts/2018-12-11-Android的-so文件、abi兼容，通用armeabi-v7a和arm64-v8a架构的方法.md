---
title: Android的.so文件、abi兼容，通用armeabi-v7a和arm64-v8a架构的方法
date: 2018-12-11 18:26:37
tags:
- Android 
- Android Tips
- armeabi-v7a
- arm64-v8a
- abi兼容
- .so文件
categories:
- Android
- Android Tips
---

>了解完 armeabi、armeabi-v7a、arm64-v8a、mips、mips64、x86、x86_64等abi的原理后，很久以前一般都只是用armeabi在做兼容。

现在其实市面上主流的手机都支持armeabi-v7a和arm64-v8a。请看如下简介：
各版本的分析如下所示：
- mips / mips64: 极少用于手机可以忽略，有兴趣的可以百度一下。
- x86 / x86_64: x86 架构的手机都会包含由 Intel 提供的称为 Houdini 的指令集动态转码工具，实现 对 arm .so 的兼容，再考虑 x86 1% 以下的市场占有率，x86 相关的两个 .so 也是可以忽略的
- armeabi: ARM v5 这是相当老旧的一个版本，缺少对浮点数计算的硬件支持，在需要大量计算时有性能瓶颈
- armeabi-v7a: ARM v7 目前主流版本，一般市面上的骁龙系列或者麒麟系列的处理器绝大部分都是这种架构
- arm64-v8a: 64位支持
所谓的ARMv8架构，就是在MIPS64架构上增加了ARMv7架构中已经拥有的的TrustZone技术、虚拟化技术及NEON advanced SIMD技术等特性，研发成的。

综上所述建议大家兼容armeabi-v7a和arm64-v8a这两个，其他架构少之又少，armeabi基本淘汰所以现在就不怎么考虑了。对于一般项目来说，足够了。

在build.gradle的android里的defaultConfig内添加如下内容:
```js
defaultConfig {  
    ndk {
        abiFilters "armeabi-v7a"
        abiFilters "arm64-v8a"
    }
```

<!--more-->

然后在项目中集成so文件的时候

只把armeabi-v7a和arm64-v8a这两个的so文件夹copy到libs里面，具体细节第三方平台的教程里面都写得很详细

如果报错:
```js
Error:(15, 1) A problem occurred evaluating project ':app'.
> Error: NDK integration is deprecated in the current plugin.  Consider trying the new experimental plugin.  For details, see http://tools.android.com/tech-docs/new-build-system/gradle-experimental.  Set "android.useDeprecatedNdk=true" in gradle.properties to continue using the current NDK integration.
```

请在 **gradle.properties** 中 添加 
```js
android.useDeprecatedNdk=true
```

对于新手Android开发者来说，像集成百度地图SDK、JPush等再出现找不到.so文件的问题直接只使用armeabi-v7a和arm64-v8a就足以。