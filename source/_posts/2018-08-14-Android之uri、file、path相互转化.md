---
title: Android之uri、file、path相互转化
tags:
  - Android
categories:
  - Android
  - 代码片段
abbrlink: 6f91fcbd
date: 2018-08-14 17:22:56
---

#### uri & file 互转
```java
File file = new File(new URI(uri.toString()));  
```
```java
URI uri = file.toURI();  
```

#### uri & path 互转
```java
private String getPath(Uri uri) {  
       String[] projection = {MediaStore.Video.Media.DATA};  
       Cursor cursor = managedQuery(uri, projection, null, null, null);  
       int column_index = cursor.getColumnIndexOrThrow(MediaStore.Audio.Media.DATA);  
       cursor.moveToFirst();  
       return cursor.getString(column_index);  
   }  
```
```java
Uri uri = Uri.parse(path);  
```

#### file & path 互转
```java
String path = file.getPath()  
```
```java
File file = new File(path)  
```

<!--more-->