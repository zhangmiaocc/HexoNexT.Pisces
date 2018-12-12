---
title: gradle.properties文件使用
date: 2018-12-05 11:09:00
tags:
- Android 
- Android Tips
- Gradle
categories:
- Android
- Android Tips
---

> 在一些项目中会分拆app 和 lib , 这时候引用support的时候,一旦更改版本会出现需要同步更改两个地方的问题.这种情况,可以通过配置gradle.properties实现替换.
在项目编译过程中,gradle.properties配置的值会被编译解析,其作为配置文件使用是很有必要的.

### 1. 概述
在Android Studio 创建一个项目的时候，Project下面会生成gradle.properties和local.properties文件，如下图:
![](https://ws3.sinaimg.cn/large/006tNbRwgy1fxvpia7sqzj308w09b0t7.jpg)

### 2. properties的数据格式
properties里面的数据格式采用键值对的方式，大概有以下几种写法: 
```
1. key=value 
2. key:value 
3. key :value 
4. # 作为注释 
```
这里主要参考以下链接： 
https://docs.oracle.com/javase/8/docs/api/java/util/Properties.html 
**注意: **在Android Studio 中最好使用第一种写法，要不会有警告
<!--more-->

### 3. 如何使用

#### 3.1 在项目根目录的gradle.properties文件配置:

```groovy
# Project-wide Gradle settings.
#添加ndk支持(按需添加)
android.useDeprecatedNdk=true
# 应用版本名称
VERSION_NAME=1.0.0
# 应用版本号
VERSION_CODE=100
# 支持库版本
SUPPORT_LIBRARY=24.2.1
# MIN_SDK_VERSION
ANDROID_BUILD_MIN_SDK_VERSION=14
# TARGET_SDK_VERSION
ANDROID_BUILD_TARGET_SDK_VERSION=24
# BUILD_SDK_VERSION
ANDROID_BUILD_SDK_VERSION=24
# BUILD_TOOLS_VERSION
ANDROID_BUILD_TOOLS_VERSION=24.0.3
```

#### 3.2 这时候配置app和lib的build.gradle可以这样写:

```groovy
android {
    compileSdkVersion project.ANDROID_BUILD_SDK_VERSION as int
    buildToolsVersion project.ANDROID_BUILD_TOOLS_VERSION

    defaultConfig {
        applicationId project.APPLICATION_ID // lib项目不需要配置这一项
        versionCode project.VERSION_CODE as int
        versionName project.VERSION_NAME
        minSdkVersion project.ANDROID_BUILD_MIN_SDK_VERSION as int
        targetSdkVersion project.ANDROID_BUILD_TARGET_SDK_VERSION as int
    }
}

dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    //这里注意是双引号
    compile "com.android.support:appcompat-v7:${SUPPORT_LIBRARY}"
    compile "com.android.support:design:${SUPPORT_LIBRARY}"
    compile "com.android.support:recyclerview-v7:${SUPPORT_LIBRARY}"
    compile "com.android.support:support-annotations:${SUPPORT_LIBRARY}"
    compile "com.android.support:cardview-v7:${SUPPORT_LIBRARY}"
    compile "com.android.support:support-v4:${SUPPORT_LIBRARY}"
}
```

**这样配置后,当你需要升级你的编译版本,版本号,支持库等的时候,仅需要修改项目根目录的gradle.properties文件即可,是不是又方便了一点点?**



