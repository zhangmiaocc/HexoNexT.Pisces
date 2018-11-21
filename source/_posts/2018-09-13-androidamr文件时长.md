---
title: android 音频amr文件时长
date: 2018-09-13 10:18:43
tags:
- blog
- markdown
- Android 
categories:
- Android 
- 代码片段
---

**一、文件时长获取**

```java
public int getDurations(){
    String curAudioFile = “XXX.amr”;

    MediaPlayer mediaPlayer = new MediaPlayer();

    mediaPlayer.setDataSource(curAudioFile);

    mediaPlayer.prepare();

    return mediaPlayer.getDuration();// 单位毫秒
}
```

<!--more-->

二、文件时长转换**

```java
private static String getAudioDuration(int nDuration0) {

    DecimalFormat df = new DecimalFormat("#.00");

    String fileSizeString = "";

    String wrongSize = "0ms";

    if (nDuration0 == 0) {

        return wrongSize;

    }

    if (nDuration0 < 1000) {

        fileSizeString = df.format((double) nDuration0) + "ms";

    } else if (nDuration0 < 60000) {

        fileSizeString = df.format((double) nDuration0 / 1000) + "s";

    } else if (nDuration0 < 3600000) {

        fileSizeString = df.format((double) nDuration0 / 60000) + "min";

    } else {

        fileSizeString = df.format((double) nDuration0 / 3600000) + "h";

    }

    return fileSizeString;

}
```