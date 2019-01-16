---
title: AndroidStudioGit设置忽略文件不生效
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: 40a53d12
date: 2018-11-21 11:29:50
---

在Android中git提交想忽略某些不想提交的文件，可以在项目目录中新建一个.gitignore，如果没有这个文件，可以手动建一个。里面匹配一下你不想提交的文件。

![](https://ws1.sinaimg.cn/large/006tNbRwly1fxfj2qqj57j307w058aa8.jpg)

<!--more-->

下面这是Android Studio的忽略规则

```properties
# OSX

*.DS_Store

# Gradle files
build/
.gradle/
*/build/

# IDEA
*.iml
.idea/.name
.idea/encodings.xml
.idea/inspectionProfiles/Project_Default.xml
.idea/inspectionProfiles/profiles_settings.xml
.idea/misc.xml
.idea/modules.xml
.idea/scopes/scope_settings.xml
.idea/vcs.xml
.idea/workspace.xml
.idea/libraries

# Built application files
*.apk
*.ap_

# Files for the Dalvik VM
*.dex

# Java class files
*.class

# Generated files
antLauncher/bin
antLauncher/gen

# Local configuration file (sdk path, etc)
local.properties

# Log Files
*.log
```
规则网上很多，可以自己搜下，或者自己写一个也行。但是当我们提交的时候，却发现这些规则并没有失效，原因就是因为.gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。解决方法就是先把本地缓存删除（改变成未track状态），然后再提交：
```properties
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```