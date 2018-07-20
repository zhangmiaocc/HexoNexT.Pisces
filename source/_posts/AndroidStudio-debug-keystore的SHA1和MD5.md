---
title: debug.keystore的SHA1和MD5
date: 2018-07-20 16:22:14
tags:
- blog
- markdown
- Android 
- AndroidStudio
categories:
- Android
- AndroidStudio
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
![img](http://m.qpic.cn/psb?/V11BgY9M4abG72/yN2G49C4*PYlDiQPFshF3fXoEmAzM5jwb04B43RmAKE!/b/dC8BAAAAAAAA&bo=zgIrAgAAAAADB8c!&rf=viewer_4)

