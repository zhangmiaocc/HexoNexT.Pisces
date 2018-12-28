---
title: Android的.so文件、ABI和CPU的关系
tags:
  - Android
  - Android Tips
  - ABI
  - CPU
  - .so文件
categories:
  - Android
  - Android Tips
abbrlink: b9d5fd68
date: 2018-12-11 18:10:31
---

早期的Android系统几乎只支持ARMv5的CPU架构，你知道现在它支持多少种吗？

Android系统目前支持以下七种不同的CPU架构：ARMv5，ARMv7 (从2010年起)，x86 (从2011年起)，MIPS (从2012年起)，ARMv8，MIPS64和x86_64 (从2014年起)，每一种都关联着一个相应的ABI。

应用程序二进制接口ABI（Application Binary Interface）定义了二进制文件（尤其是.so文件）如何运行在相应的系统平台上，从使用的指令集，内存对齐到可用的系统函数库。

### 为什么你需要重点关注.so文件
- 项目中使用到了NDK，它将会生成.so文件。

- 如果只使用Java语言进行编码，你可能在想不需要关注.so文件了吧，因为Java是跨平台的。但你可能并没有意识到项目中依赖的函数库或者引擎库里面已经嵌入了.so文件，并依赖于不同的ABI。

Android应用支持的ABI取决于APK中位于lib/ABI目录中的.so文件，其中ABI可能是上面说过的七种ABI中的一种。

<!--more-->

### 本地库监视器

[Native Libs Monitor](https://play.google.com/store/apps/details?id=com.xh.nativelibsmonitor.app)这个应用可以帮助我们理解手机上安装的APK用到了哪些.so文件，以及.so文件来源于哪些函数库或者框架。
![](https://ws1.sinaimg.cn/large/006tNbRwgy1fy2z17ti0sj30yg0ljjty.jpg)

### ABI和CPU的关系
很多设备都支持多于一种的ABI。
当一个应用安装在设备上，只有该设备支持的CPU架构对应的.so文件会被安装。
但最好是针对特定平台提供相应平台的二进制包，这种情况下运行时就少了一个模拟层（例如x86设备上模拟arm的虚拟层），从而得到更好的性能（归功于最近的架构更新，例如硬件fpu，更多的寄存器，更好的向量化等）。

我们可以通过Build.SUPPORTED_ABIS得到根据偏好排序的设备支持的ABI列表。但你不应该从你的应用程序中读取它，因为Android包管理器安装APK时，会自动选择APK包中为对应系统ABI预编译好的.so文件

| ABI目录（横向）和cpu（纵向） | armeabi | armeabi-v7a | arm64-v8a | mips | mips64 | x86  | x86_64 |
| ---------------------------- | ------- | ----------- | --------- | ---- | ------ | ---- | ------ |
| ARMv5                        | 支持    |             |           |      |        |      |        |
| ARMv7                        | 支持    | 支持        |           |      |        |      |        |
| ARMv8                        | 支持    | 支持        | 支持      |      |        |      |        |
| MIPS                         |         |             |           | 支持 |        |      |        |
| MIPS64                       |         |             |           | 支持 | 支持   |      |        |
| x86                          | 支持    | 支持        |           |      |        | 支持 |        |
| x86_64                       | 支持    |             |           |      |        | 支持 | 支持   |

**不同的ABI，针对不同的cpu架构有不同的优先权**

例如： x86设备上，libs/x86目录中如果存在.so文件的话，会被安装，如果不存在，则会选择armeabi-v7a中的.so文件，如果也不存在，则选择armeabi目录中的.so文件。

x86设备能够很好的运行ARM类型函数库，但并不保证100%不发生crash，特别是对旧设备。

64位设备（arm64-v8a, x86_64, mips64）能够运行32位的函数库，但是以32位模式运行，在64位平台上运行32位版本的ART和Android组件，将丢失专为64位优化过的性能（ART，webview，media等等）。

### .so文件重要法则
处理.so文件时有一条简单却并不知名的重要法则。

你应该尽可能的提供专为每个ABI优化过的.so文件，你不应该混合着使用（不能就装对不同cpu架构的so文件，放在同一个ABI目录下）。你应该为每个ABI目录提供对应的.so文件。

### NDK兼容性
使用NDK时，你可能会倾向于使用最新的编译平台，但事实上这是错误的，因为NDK平台不是后向兼容（兼容过去的版本）的，而是前向兼容（兼容将来的版本）的。推荐使用app的minSdkVersion对应的编译平台。

这也意味着当你引入一个预编译好的.so文件时，你需要检查它被编译所用的平台版本。

### 混合使用不同C++运行时编译的.so文件
.so文件可以依赖于不同的C++运行时，静态编译或者动态加载。 
混合使用不同版本的C++运行时可能导致很多奇怪的crash，是应该避免的。

### 一个经验法则
- 当只有一个.so文件时，静态编译C++运行时是没问题的，
- 当存在多个.so文件时，应该让所有的.so文件都动态链接相同的C++运行时。
- 这意味着当引入一个新的预编译.so文件，而且项目中还存在其他的.so文件时，我们需要首先确认新引入的.so文件使用的C++运行时是否和已经存在的.so文件一致。

###关于.so文件的错误示例
**问题： **
你的app目前只支持armeabi-v7a和x86架构，你想让app支持更多的cpu类型，新增了一个函数库依赖，这个函数库包含.so文件并支持更多的CPU架构。

发布我们的app后，会发现它在某些设备上会发生Crash，例如Galaxy S6，最终可以发现只有64位目录下的.so文件被安装进手机。

**解决方案：**

重新编译我们的.so文件使其支持缺失的ABIs

也可以设置ndk.abiFilters显示指定支持的ABIs

### 在IDE中的路径
- Android Studio工程放在jniLibs/ABI目录中（当然也可以通过在build.gradle文件中的设置jniLibs.srcDir属性自己指定）
- Eclipse工程放在libs/ABI目录中（这也是ndk-build命令默认生成.so文件的目录）

### 在AAR压缩包中的路径
AAR压缩包中位于jni/ABI目录中（.so文件会自动包含到引用AAR压缩包的APK中）

### 在APK中的路径
最终APK文件中的lib/ABI目录中

### 通过PackageManager安装后，.so文件路径
通过PackageManager安装后，在小于Android 5.0的系统中，.so文件位于app的nativeLibraryPath目录中；在大于等于Android 5.0的系统中，.so文件位于app的nativeLibraryRootDir/CPU_ARCH目录中。

### 生成不同ABI版本的APK
以减少APK包大小为由是一个错误的借口，因为你也可以选择在应用市场上传指定ABI版本的APK，生成不同ABI版本的APK可以在build.gradle中如下配置：
```js
android {
    ... 
    splits {
        abi {
            enable true
            reset()
            include 'x86', 'x86_64', 'armeabi-v7a', 'arm64-v8a' //select ABIs to build APKs for
            universalApk true //generate an additional APK that contains all the ABIs
        }
    }

    // map for the version code
    project.ext.versionCodes = ['armeabi': 1, 'armeabi-v7a': 2, 'arm64-v8a': 3, 'mips': 5, 'mips64': 6, 'x86': 8, 'x86_64': 9]

    android.applicationVariants.all { variant ->
        // assign different version code for each output
        variant.outputs.each { output ->
            output.versionCodeOverride =
                    project.ext.versionCodes.get(output.getFilter(com.android.build.OutputFile.ABI), 0) * 1000000 + android.defaultConfig.versionCode
        }
    }
 }
```