---
title: Google推荐的图片加载库Glide介绍
date: 2018-07-20 18:59:51
tags:
- blog
- markdown
- Android 
- 图片加载库
categories:
- Android
- 图片加载库
---

英文原文  [ Introduction to Glide, Image Loader Library for Android, recommended by Google](https://inthecheesefactory.com/blog/get-to-know-glide-recommended-by-google/en)

在泰国举行的谷歌开发者论坛上，谷歌为我们介绍了一个名叫 [`Glide`](https://github.com/bumptech/glide) 的图片加载库，作者是bumptech。这个库被广泛的运用在google的开源项目中，包括2014年google I/O大会上发布的官方app。
它的成功让我非常感兴趣。我花了一整晚的时间把玩，决定分享一些自己的经验。在开始之前我想说， [`Glide`](https://github.com/bumptech/glide) 和 [`Picasso`](https://github.com/square/picasso) 有90%的相似度，准确的说，就是Picasso的克隆版本。但是在细节上还是有不少区别的。

### 导入库
 [`Glide`](https://github.com/bumptech/glide) 和 [`Picasso`](https://github.com/square/picasso) 都在jcenter上。在项目中添加依赖非常简单：

##### Glide
```java
dependencies {
    compile 'com.github.bumptech.glide:glide:3.5.2'
    compile 'com.android.support:support-v4:22.0.0'
}
```

##### Picasso
```java
dependencies {
     compile 'com.squareup.picasso:picasso:2.5.1'
}
```
<!-- more -->
