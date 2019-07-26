---
title: 'java.lang.ClassNotFoundException:"org.apache.http.ProtocolVersion"'
tags:
  - Android
  - Exception
categories:
  - Android
  - Exception
abbrlink: c7a54c5f
date: 2019-07-26 11:38:39
---

### 运行项目遇到以下问题：


> Caused by: java.lang.ClassNotFoundException: Didn't find class "org.apache.http.ProtocolVersion" on path: DexPathList[[zip file "/data/user_de/0/com.google.android.gms/app_chimera/m/00000043/MapsDynamite.apk"],nativeLibraryDirectories=[/data/user_de/0/com.google.android.gms/app_chimera/m/00000043/MapsDynamite.apk!/lib/arm64-v8a, /system/lib64, /product/lib64]]


### 解决方案：

**1.在清单文件增加代码:**

```xml
 <application
      android:usesCleartextTraffic="true">
```

**2.在清单文件清单再加一句代码：**

```xml
 <application
      android:usesCleartextTraffic="true">
   <uses-library
                android:name="org.apache.http.legacy"
                android:required="false" />
</application>
```

好了，重新运行解决了.
最根本的做法是使用https进行接口访问，毕竟涉及数据的安全性。当然了，这需要服务器的支持。还有第三方sdk，也需要使用https。

<!--more-->