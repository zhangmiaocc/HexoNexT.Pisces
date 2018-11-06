---
title: Android - 图片处理之Glide4.0版本
date: 2018-09-14 17:40:16
tags:
- blog
- markdown
- Android 
- Glide
categories:
- Android 
- Glide
---

# 前言

一般项目我都会使用Glide作为我的图片加载框架，他和Picasso ,真的很像，郭大神早就分析过了，很详细，这里也就简单做个记录。小白白一枚，学习路上

[Android图片加载框架最全解析（一），Glide的基本用法](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fguolin_blog%2Farticle%2Fdetails%2F53759439)

[Android图片加载框架最全解析（二），从源码的角度理解Glide的执行流程](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fguolin_blog%2Farticle%2Fdetails%2F53939176)

[Android图片加载框架最全解析（三），深入探究Glide的缓存机制 ](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fguolin_blog%2Farticle%2Fdetails%2F54895665)

[Android图片加载框架最全解析（四），玩转Glide的回调与监听](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fguolin_blog%2Farticle%2Fdetails%2F70215985)

[Android图片加载框架最全解析（五），Glide强大的图片变换功能](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fguolin_blog%2Farticle%2Fdetails%2F71524668)

[Android图片加载框架最全解析（六），探究Glide的自定义模块功能](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fguolin_blog%2Farticle%2Fdetails%2F78179422)

[Android图片加载框架最全解析（七），实现带进度的Glide图片加载功能](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fguolin_blog%2Farticle%2Fdetails%2F78357251)

[Android图片加载框架最全解析（八），带你全面了解Glide 4的用法](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fguolin_blog%2Farticle%2Fdetails%2F78582548)



# 一：GitHub

##  [bumptech](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fbumptech)/**glide** 

# 二：[下载使用](https://link.jianshu.com?t=https%3A%2F%2Fmuyangmin.github.io%2Fglide-docs-cn%2Fdoc%2Fdownload-setup.html) 

点击，跳转到官网，介绍很详细

## 1. Gradle

```java
repositories {
  mavenCentral()
  maven { url 'https://maven.google.com' }
}
```

```java
dependencies {
    compile 'com.github.bumptech.glide:glide:4.4.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.4.0'
}
```

## 2.  Android SDK 要求

- Min Sdk Version - 使用 Glide 需要 min SDK 版本 API 14 (Ice Cream Sandwich) 或更高。
- Compile Sdk Version - Glide 必须使用 API 26 (Oreo) 或更高版本的 SDK 来编译。
- Support Library Version - Glide 使用的支持库版本为 27。

否则会出现异常

### 解决方案

```java
dependencies {
    implementation 'com.android.support:appcompat-v7:27.0.2' //这个版本的就可以了
    implementation 'com.github.bumptech.glide:glide:4.4.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.4.0'
}
```

## 3.权限

```xml
 <uses-permission android:name="android.permission.INTERNET"/>
 <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
 <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

# 二：apply && RequestOptions

4.0之前最常用的方式，当然现在也是哈

```java
Glide.with(this)
     .load(url)
     .into(img);
```

4.0之后，有一个新的东西

```java
   Glide.with(this)
                .load(url)
//                .apply(RequestOptions options)
                .into(img);
```

比如 我们加载占位图和错误图

```java
RequestOptions options = new RequestOptions()
        .error(R.drawable.error)
        .placeholder(R.drawable.loading);
Glide.with(this)
     .load(url)
     .apply(options)
     .into(imageView);
```

小伙伴们没看错，这样的方式，摆脱了，以前链式写法中，Glide很长很长，现在的话，我们可以传入一个RequestOptions，对象，就有小伙伴问了，有啥用，我觉得，比较容易封装，

例如

```java
public class GlideUtil {

    public static void load(Context context,
                            String url,
                            ImageView imageView,
                            RequestOptions options) {
        Glide.with(context)
             .load(url)
             .apply(options)
             .into(imageView);
    }
}
```

# 三：API 介绍

| API                                                          | 介绍                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [占位符(Placeholder)](https://link.jianshu.com?t=https%3A%2F%2Fmuyangmin.github.io%2Fglide-docs-cn%2Fdoc%2Fplaceholders.html%23%25E5%258D%25A0%25E4%25BD%258D%25E7%25AC%25A6placeholder) | 当请求正在执行时被展示的 Drawable                            |
| [错误符(Error)](https://link.jianshu.com?t=https%3A%2F%2Fmuyangmin.github.io%2Fglide-docs-cn%2Fdoc%2Fplaceholders.html%23%25E9%2594%2599%25E8%25AF%25AF%25E7%25AC%25A6error) | 请求永久性失败时展示                                         |
| [后备回调符(Fallback)](https://link.jianshu.com?t=https%3A%2F%2Fmuyangmin.github.io%2Fglide-docs-cn%2Fdoc%2Fplaceholders.html%23%25E5%2590%258E%25E5%25A4%2587%25E5%259B%259E%25E8%25B0%2583%25E7%25AC%25A6fallback) | 在请求的url/model为 null 时展示                              |
| override                                                     | 指定了一个图片的尺寸,`Target.SIZE_ORIGINAL`加载图片的原始尺寸 |
| skipMemoryCache(true)                                        | 禁用内存缓存功能                                             |
| diskCacheStrategy(DiskCacheStrategy.NONE)                    | 禁用硬盘缓存功能,参数列表如下 四（1）                        |
| asBitmap()                                                   | 只允许加载静态图片,。如果传入的是GIF图,会展示GIF图的第一帧   |
| asFile()                                                     | 指定文件格式 注意事项 如下 四（2）                           |
| asDrawable()                                                 | 指定Drawable格式                                             |
| submit()                                                     | 使用如下四（3）                                              |
| transforms                                                   | 图片变换，Glide 默认有3个，如下四（4）                       |

------

# 四： 补充说明

## 1. diskCacheStrategy参数补充

| 参数                        | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| DiskCacheStrategy.NONE      | 表示不缓存任何内容。                                         |
| DiskCacheStrategy.DATA      | 表示只缓存原始图片。                                         |
| DiskCacheStrategy.RESOURCE  | 表示只缓存转换过后的图片。                                   |
| DiskCacheStrategy.ALL       | 表示既缓存原始图片，也缓存转换过后的图片。                   |
| DiskCacheStrategy.AUTOMATIC | 表示让Glide根据图片资源智能地选择使用哪一种缓存策略（默认选项）。 |

## 2. asBitmap()注意坑

熟悉Glide 3的朋友对asBitmap()方法肯定不会陌生对吧？但是千万不要觉得这里就没有陷阱了，在Glide 3中的语法是先load()再asBitmap()的，而在Glide 4中是先asBitmap()再load()的。乍一看可能分辨不出来有什么区别，但如果你写错了顺序就肯定会报错了

## 3. submit()

通过如下代码，可以获取到，下载好的图片放在哪，可以看到 都在`cache`下

```java
   new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    File file = Glide.with(MainActivity.this)
                            .asFile()
                            .load(url)
                            .submit()
                            .get();
                    Log.e("Tag", "path-->" + file.getPath());
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (ExecutionException e) {
                    e.printStackTrace();
                }
            }
        }).start();


 E/Tag: path-->/data/user/0/com.allens.glidedemo/cache/image_manager_disk_cache/309df01e6362ddc8939a4e3c549e8276dffb0446a89f2facee371909301fe76a.0
```

## 4. transforms

内置有这3个

```java
RequestOptions options = new RequestOptions()
        .centerCrop();

RequestOptions options = new RequestOptions()
        .fitCenter();

RequestOptions options = new RequestOptions()
        .circleCrop();//圆形
```

一般我们还会自己去定义，以下是常用的3种转换

使用起来也很简单

```java
   RequestOptions options = new RequestOptions()
                .skipMemoryCache(true)
                .diskCacheStrategy(DiskCacheStrategy.NONE);
                //圆形
               .transforms(new CircleTransform(mContext,2, Color.DKGRAY))//外圈宽度，外圈颜色
                //黑白
               .transforms(new BlackWhiteTransformation());
               //高斯模糊 范围在 0 -- 25 越大模糊程度越高
               .transforms(new BlurTransformation(mContext, 25)); // (0 < r <= 25)
                //可以使用多种
               .transforms(new BlurTransformation(mContext, 25),new CircleTransform(mContext,2, Color.DKGRAY))
```

### （1） 转成黑白

```java
package com.allens.lib_glide.Transformation;

import android.graphics.Bitmap;
import android.media.ThumbnailUtils;
import android.support.annotation.NonNull;
import android.view.animation.Transformation;

import com.bumptech.glide.load.engine.bitmap_recycle.BitmapPool;
import com.bumptech.glide.load.resource.bitmap.BitmapTransformation;

import java.security.MessageDigest;

/**
 * 描述:
 * <p> 黑白
 * Created by allens on 2018/1/8.
 */

public class BlackWhiteTransformation extends BitmapTransformation {
    @Override
    protected Bitmap transform(@NonNull BitmapPool pool, @NonNull Bitmap toTransform, int outWidth, int outHeight) {
        return convertToBlackWhite(toTransform);
    }

    @Override
    public void updateDiskCacheKey(MessageDigest messageDigest) {

    }

    private Bitmap convertToBlackWhite(Bitmap bmp) {
        int width = bmp.getWidth(); // 获取位图的宽
        int height = bmp.getHeight(); // 获取位图的高
        int[] pixels = new int[width * height]; // 通过位图的大小创建像素点数组

        bmp.getPixels(pixels, 0, width, 0, 0, width, height);
        int alpha = 0xFF << 24;
        for (int i = 0; i < height; i++) {
            for (int j = 0; j < width; j++) {
                int grey = pixels[width * i + j];

                //分离三原色
                int red = ((grey & 0x00FF0000) >> 16);
                int green = ((grey & 0x0000FF00) >> 8);
                int blue = (grey & 0x000000FF);

                //转化成灰度像素
                grey = (int) (red * 0.3 + green * 0.59 + blue * 0.11);
                grey = alpha | (grey << 16) | (grey << 8) | grey;
                pixels[width * i + j] = grey;
            }
        }
        //新建图片
        Bitmap newBmp = Bitmap.createBitmap(width, height, Bitmap.Config.RGB_565);
        //设置图片数据
        newBmp.setPixels(pixels, 0, width, 0, 0, width, height);

        Bitmap resizeBmp = ThumbnailUtils.extractThumbnail(newBmp, 380, 460);
        return resizeBmp;
    }
}
```

### （2）高斯模糊

```java
package com.allens.lib_glide.Transformation;

import android.annotation.TargetApi;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Paint;
import android.os.Build;
import android.renderscript.Allocation;
import android.renderscript.Element;
import android.renderscript.RSRuntimeException;
import android.renderscript.RenderScript;
import android.renderscript.ScriptIntrinsicBlur;
import android.support.annotation.NonNull;

import com.bumptech.glide.load.engine.bitmap_recycle.BitmapPool;
import com.bumptech.glide.load.resource.bitmap.BitmapTransformation;

import java.security.MessageDigest;

/**
 * 描述: 高斯模糊
 * <p>
 * Created by allens on 2018/1/8.
 */

public class BlurTransformation extends BitmapTransformation {


    private Context context;

    private float blurRadius;

    public BlurTransformation(Context context, float blurRadius) {
        this.context = context;
        this.blurRadius = blurRadius;
    }

    @Override
    protected Bitmap transform(@NonNull BitmapPool pool, @NonNull Bitmap toTransform, int outWidth, int outHeight) {
        return blurBitmap(context, toTransform, blurRadius, outWidth, outHeight);
    }

    @Override
    public void updateDiskCacheKey(MessageDigest messageDigest) {

    }

    /**
     * @param context   上下文对象
     * @param image     需要模糊的图片
     * @param outWidth  输入出的宽度
     * @param outHeight 输出的高度
     * @return 模糊处理后的Bitmap
     */
    @TargetApi(Build.VERSION_CODES.JELLY_BEAN_MR1)
    public Bitmap blurBitmap(Context context, Bitmap image, float blurRadius, int outWidth, int outHeight) {
        // 将缩小后的图片做为预渲染的图片
        Bitmap inputBitmap = Bitmap.createScaledBitmap(image, outWidth, outHeight, false);
        // 创建一张渲染后的输出图片
        Bitmap outputBitmap = Bitmap.createBitmap(inputBitmap);
        // 创建RenderScript内核对象
        RenderScript rs = RenderScript.create(context);
        // 创建一个模糊效果的RenderScript的工具对象
        ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.create(rs, Element.U8_4(rs));
        // 由于RenderScript并没有使用VM来分配内存,所以需要使用Allocation类来创建和分配内存空间
        // 创建Allocation对象的时候其实内存是空的,需要使用copyTo()将数据填充进去
        Allocation tmpIn = Allocation.createFromBitmap(rs, inputBitmap);
        Allocation tmpOut = Allocation.createFromBitmap(rs, outputBitmap);
        // 设置渲染的模糊程度, 25f是最大模糊度
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.JELLY_BEAN_MR1) {
            blurScript.setRadius(blurRadius);
        }
        // 设置blurScript对象的输入内存
        blurScript.setInput(tmpIn);
        // 将输出数据保存到输出内存中
        blurScript.forEach(tmpOut);
        // 将数据填充到Allocation中
        tmpOut.copyTo(outputBitmap);
        return outputBitmap;
    }

}
```

### （3） 圆形

```java
package com.allens.lib_glide.Transformation;

import android.content.Context;
import android.content.res.Resources;
import android.graphics.Bitmap;
import android.graphics.BitmapShader;
import android.graphics.Canvas;
import android.graphics.Paint;
import android.support.annotation.NonNull;

import com.bumptech.glide.load.engine.bitmap_recycle.BitmapPool;
import com.bumptech.glide.load.resource.bitmap.BitmapTransformation;

import java.security.MessageDigest;

/**
 * 描述:
 * <p> 圆形
 * Created by allens on 2018/1/8.
 */

public class CircleTransform extends BitmapTransformation {
    private Paint mBorderPaint;
    private float mBorderWidth;

    public CircleTransform(Context context) {
        super(context);
    }

    public CircleTransform(Context context, int borderWidth, int borderColor) {
        super(context);
        mBorderWidth = Resources.getSystem().getDisplayMetrics().density * borderWidth;
        mBorderPaint = new Paint();
        mBorderPaint.setDither(true);
        mBorderPaint.setAntiAlias(true);
        mBorderPaint.setColor(borderColor);
        mBorderPaint.setStyle(Paint.Style.STROKE);
        mBorderPaint.setStrokeWidth(mBorderWidth);
    }

    @Override
    protected Bitmap transform(@NonNull BitmapPool pool, @NonNull Bitmap toTransform, int outWidth, int outHeight) {
        return circleCrop(pool, toTransform);
    }

    @Override
    public void updateDiskCacheKey(MessageDigest messageDigest) {

    }

    private Bitmap circleCrop(BitmapPool pool, Bitmap source) {
        if (source == null) {
            return null;
        }
        int size = (int) (Math.min(source.getWidth(), source.getHeight()) - (mBorderWidth / 2));
        int x = (source.getWidth() - size) / 2;
        int y = (source.getHeight() - size) / 2;
        Bitmap squared = Bitmap.createBitmap(source, x, y, size, size);
        Bitmap result = pool.get(size, size, Bitmap.Config.ARGB_8888);
        if (result == null) {
            result = Bitmap.createBitmap(size, size, Bitmap.Config.ARGB_8888);
        }
        Canvas canvas = new Canvas(result);
        Paint paint = new Paint();
        paint.setShader(new BitmapShader(squared, BitmapShader.TileMode.CLAMP, BitmapShader.TileMode.CLAMP));
        paint.setAntiAlias(true);
        float r = size / 2f;
        canvas.drawCircle(r, r, r, paint);
        if (mBorderPaint != null) {
            float borderRadius = r - mBorderWidth / 2;
            canvas.drawCircle(r, r, borderRadius, mBorderPaint);
        }
        return result;
    }
}
```

### 4.0 圆角

```java
package com.starot.spark.transformation;


import android.content.Context;
import android.content.res.Resources;
import android.graphics.Bitmap;
import android.graphics.BitmapShader;
import android.graphics.Canvas;
import android.graphics.Paint;
import android.support.annotation.NonNull;

import com.bumptech.glide.load.engine.bitmap_recycle.BitmapPool;
import com.bumptech.glide.load.resource.bitmap.BitmapTransformation;

import java.security.MessageDigest;

/**
 * 描述:
 * <p> 圆形
 *
 * @author allens
 * @date 2018/1/8
 */

public class CircleTransform extends BitmapTransformation {
    private Paint mBorderPaint;
    private float mBorderWidth;

    public CircleTransform(Context context) {
        super(context);
    }

    public CircleTransform(Context context, int borderWidth, int borderColor) {
        super(context);
        mBorderWidth = Resources.getSystem().getDisplayMetrics().density * borderWidth;
        mBorderPaint = new Paint();
        mBorderPaint.setDither(true);
        mBorderPaint.setAntiAlias(true);
        mBorderPaint.setColor(borderColor);
        mBorderPaint.setStyle(Paint.Style.STROKE);
        mBorderPaint.setStrokeWidth(mBorderWidth);
    }

    @Override
    protected Bitmap transform(@NonNull BitmapPool pool, @NonNull Bitmap toTransform, int outWidth, int outHeight) {
        return circleCrop(pool, toTransform);
    }

    @Override
    public void updateDiskCacheKey(MessageDigest messageDigest) {

    }

    private Bitmap circleCrop(BitmapPool pool, Bitmap source) {
        if (source == null) {
            return null;
        }
        int size = (int) (Math.min(source.getWidth(), source.getHeight()) - (mBorderWidth / 2));
        int x = (source.getWidth() - size) / 2;
        int y = (source.getHeight() - size) / 2;
        Bitmap squared = Bitmap.createBitmap(source, x, y, size, size);
        Bitmap result = pool.get(size, size, Bitmap.Config.ARGB_8888);
        if (result == null) {
            result = Bitmap.createBitmap(size, size, Bitmap.Config.ARGB_8888);
        }
        Canvas canvas = new Canvas(result);
        Paint paint = new Paint();
        paint.setShader(new BitmapShader(squared, BitmapShader.TileMode.CLAMP, BitmapShader.TileMode.CLAMP));
        paint.setAntiAlias(true);
        float r = size / 2f;
        canvas.drawCircle(r, r, r, paint);
        if (mBorderPaint != null) {
            float borderRadius = r - mBorderWidth / 2;
            canvas.drawCircle(r, r, borderRadius, mBorderPaint);
        }
        return result;
    }
}
```

## 5.Generated API

如果4.0用的不爽，就想使用3.0版本的那种链式写法,将Glide 关键字改成
 GlideApp即可

```java
GlideApp.with(this)
        .load(url)
        .placeholder(R.drawable.loading)
        .error(R.drawable.error)
        .skipMemoryCache(true)
        .diskCacheStrategy(DiskCacheStrategy.NONE)
        .override(Target.SIZE_ORIGINAL)
        .circleCrop()
        .into(imageView);
```

 

 

 

 

  

 

 