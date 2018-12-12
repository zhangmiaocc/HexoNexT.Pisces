---
title: debug.keystore的SHA1和MD5
date: 2018-07-20 16:22:14
tags:
- Android 
- AndroidStudio
- Android Tips
categories:
- Android
- Android Tips
---

### 切换到debug.keystore目录

```cmd
cd ~/.android/
```
### 查看debug.keystore的SHA1和MD5

接着输入如下命令
```cmd
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

### 结果如下图
![](https://ws4.sinaimg.cn/large/006tNbRwly1fx6h7i8nvjj30jy0ff0v8.jpg)

<!--more-->