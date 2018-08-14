---
title: Android之uri、file、path相互转化
date: 2018-08-14 17:22:56
tags:
- blog
- markdown
- Android 
categories:
- Android 
---

#### uri & file 互转

uri转file:

```
file = new File(new URI(uri.toString()));  
```

file转uri:

```
URI uri = file.toURI();  
```

#### uri & path 互转
uri转path:

```
private String getPath(Uri uri) {  
       String[] projection = {MediaStore.Video.Media.DATA};  
       Cursor cursor = managedQuery(uri, projection, null, null, null);  
       int column_index = cursor.getColumnIndexOrThrow(MediaStore.Audio.Media.DATA);  
       cursor.moveToFirst();  
       return cursor.getString(column_index);  
   }  
```

path转uri:

```
Uri uri = Uri.parse(path);  
```

#### file & path 互转
file转path:

```
String path = file.getPath()  
```

path转file:

```
File file = new File(path)  
```