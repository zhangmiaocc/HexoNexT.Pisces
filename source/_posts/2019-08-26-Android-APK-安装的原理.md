---
title: Android APK 安装的原理
tags:
  - Android版本
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: 2da5bed1
date: 2019-08-26 10:15:08
---

### 前言

大家有没有想过一个应用的APK是怎么被安装到安卓手机上的，安装的本质是什么？我们知道，Windows应用程序的安装包是一个可执行的压缩包，安装的过程是把压缩包中的数据和依赖库拷贝到安装目录，再在桌面或启动栏创建快捷方式，那么安卓的APK安装过程是不是也是这样的呢？本文将为大家解答这些问题。

### APK包的构成

首先，我看一下APK包的构成，Android的APK包和Windows应用程序安装包是不同的，它只是个简单的压缩包，没有可执行的能力，我们还可以用zip工具直接解压它。

一个APK包含以下这些文件：

- META-INF目录：包含两个签名文件（CERT.SF和CERT.RSA），以及一个manifest文件（MANIFEST.MF）
- assets目录：包含工程中的asset目录下的文件，可以使用AssetManager获取
- res目录：包含那些没有被编译到resources.arsc的资源
- lib目录：包含适用于不同处理器的第三方依赖库，这里边可以有多个子目录，比如armeabi, armeabi-v7a, arm64-v8a, x86, x86_64, 以及mips
- resources.arsc文件：存储编译好的资源，包括项目工程中的res/values目录里的xml文件，它们都被编译成二进制格式，也包括一些路径，指向那些没有被编译的资源，比如layout文件和图片
- classes.dex文件：项目中的java类都被编译到该dex文件，这个文件可以被Android的Dalvik/ART虚拟机解析。
- AndroidManifest.xml：二进制格式的manifest文件，这个文件是必须的。

这些文件是Android系统运行一个应用程序时会用到的数据和代码，下面介绍系统如何安装一个APK包。

<!--more-->

### 安装APK

我们安装应用程序，最常用的方法就是在PC上运行命令adb install 加APK的文件路径，回车等待Android设备安装完成，安装成功命令行会显示Success。那么其内部是怎样的一个过程呢？

#### 1. 将APK包push到手机

首先，adb会将PC端的APK文件push到Android设备的/data/local/tmp目录下，一些手机会将拷贝的进度反馈给adb客户端，于是PC上的命令行会展示拷贝的进度。

#### 2. 执行pm命令

PC端的adb程序会向Android端的adbd发送shell:pm命令，于是adbd会向系统的PackageManagerService（PMS）进程发送消息，通知其安装apk包。这里我有两个理解：
理解一：我们平常也可以在adb shell上执行pm命令与PMS交互，那么pm应该是个独立的进程，adbd执行pm命令，是启动了pm这个进程，这个进程再向PMS进程发送消息，通知其安装APK。
理解二：adbd直接向PMS进程发送消息，通知其安装APK。
不知哪个正确，这个有待继续研究，如果有童鞋了解这个，希望能不吝赐教。

#### 3. 触发安装过程

PMS首先将APK包拷贝到另外一个目录/data/app，这个目录是非系统应用的apk存放的目录，与之相对应的，系统应用的apk存放的目录是/system/frameworks、/system/app和/vendor/app。
PMS内部有个AppDirObserver类，其监听着/data/app目录的变化，当apk被复制到/data/app目录之后，该类随即触发PMS对APK进行解析。

#### 4. APK的解析

我们可以先想想，Android系统是如何启动一个APP的？比如点击屏幕上的应用图标，然后一个Activity就被启动了。这个过程中，桌面程序Launcher先是向ActivityManagerService（AMS）进程发送了一个Intent，AMS随即会将这个Intent扔给PMS，PMS则解析这个Intent得到Activity的信息给到AMS，然后AMS会启动一个空进程，并通知该进程创建该Activity。那么PMS为什么会有这个Activity的信息呢？

这就是PMS解析APK要做的事情了，而解析APK的时机又要分成两种场景：

**1. 系统启动时解析APK**
Android系统在启动的时候，会启动一个system_server进程，这个进程驻留着系统多个重要的服务，其中便包含了与APK最相关的PackageManagerService服务，这个服务在启动的时候，会扫描Android系统中几个目标文件夹中的APK，对每个APK进行解析。

**2. 安装过程中解析APK**
安装一个apk的过程，PMS也会对这个APK进行解析，其调用的是PackageManagerService.java的scanPackageLI()方法，其实在系统启动时扫描全部apk的过程也是调用该方法。

可以这样理解，系统启动的时候，是解析已经安装的所有APK，而安装单个APK时，则是用同样的方法解析这个APK，过程是一样的。

**那么解析APK具体做的是什么事情呢？**

其中主要的过程就是解析APK中的AndroidManifest.xml文件，将APK的关键信息四大组件信息、权限信息等存储在内存中的PackageParser对象中，PackageParser对象的结构如图所示：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190826101647.png)

这个PackageParser包含了IntentFilter的信息，使得PMS可以根据Intent来获取一个Activity的信息。那么，PMS在得到PackageParser对象之后，接着会将这个APK的信息加入到PMS自身管理中去，比如将Activity的数据保存在mActivities对象中，将Provider的数据保存在mProviders对象中等，PKMS提供了好几个重要数据结构来保存这些数据，这些数据结构的相关信息如图所示：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190826101712.png)

除了解析和保存APK的核心数据，PMS还会创建应用程序目录：/data/data/包名，同时提取apk中的dex文件并保存到/data/dalvik-cache中，如果该APK包含了native动态库，则需要将它们从APK文件中解压并复制到对应目录中，以及对APK进行dex优化，还有其它一些细节比如APK签名的校验，杀死APK所在进程（覆盖安装的情况）等，安装过程的最后，会发送ACTION_PACKAGE_ADDED广播，通知所有其它应用有新应用安装了。

### 总结

至此，APK安装过程就结束了。纵观整个过程，apk安装的关键就是解析AndroidManifest.xml，将重要的信息保存在PMS进程的内存中，以保证后续启动这个应用程序的组件时，可以在PMS中找到这个组件的信息。我们本来认为的更重要的代码只是进行dex优化后简单地提取到一个目录中而已，另外，APK中的资源并没有被处理，而是在启动应用进程的时候，动态去从APK包中加载而已。