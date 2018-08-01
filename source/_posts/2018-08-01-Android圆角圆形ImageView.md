---
title: Android 圆角圆形ImageView
date: 2018-08-01 16:31:05
tags:
- blog
- markdown
- Android 
categories:
- Android 
---

#### 效果预览
![](https://ws4.sinaimg.cn/large/006tKfTcly1ftua9ykgs7j30u01hcdit.jpg)

### 特点

- 基于AppCompatImageView扩展
- 支持圆角、圆形显示
- 可绘制边框，圆形时可绘制内外两层边框
- 支持边框不覆盖图片
- 可绘制遮罩
- ......

### 支持的属性、方法

| 属性名                     | 含义                                    | 默认值             | 对应方法                     |
| -------------------------- | --------------------------------------- | ------------------ | ---------------------------- |
| is_circle                  | 是否显示为圆形（默认为矩形）            | false              | isCircle()                   |
| corner_top_left_radius     | 左上角圆角半径                          | 0dp                | setCornerTopLeftRadius()     |
| corner_top_right_radius    | 右上角圆角半径                          | 0dp                | setCornerTopRightRadius()    |
| corner_bottom_left_radius  | 左下角圆角半径                          | 0dp                | setCornerBottomLeftRadius()  |
| corner_bottom_right_radius | 右下角圆角半径                          | 0dp                | setCornerBottomRightRadius() |
| corner_radius              | 统一设置四个角的圆角半径                | 0dp                | setCornerRadius()            |
| border_width               | 边框宽度                                | 0dp                | setBorderWidth()             |
| border_color               | 边框颜色                                | #ffffff            | setBorderColor()             |
| inner_border_width         | 相当于内层边框（is_circle为true时支持） | 0dp                | setInnerBorderWidth()        |
| inner_border_color         | 内边框颜色                              | #ffffff            | setInnerBorderColor()        |
| is_cover_src               | border、inner_border是否覆盖图片内容    | false              | isCoverSrc()                 |
| mask_color                 | 图片上绘制的遮罩颜色                    | 不设置颜色则不绘制 | setMaskColor()               |

### 基本用法

**Step 1. 添加JitPack仓库** 在项目根目录下的 `build.gradle` 中添加仓库:

```java
allprojects {
    repositories {
        ...
        maven { url "https://jitpack.io" }
    }
}
```

**Step 2. 添加项目依赖**

```java
dependencies {
    implementation 'com.github.xiaofeng0325:CircularImageView:1.0.0'
}
```

**Step 3. 在布局文件中添加CornerLabelView**

```html
  <com.zm.library.CircularImageView
        android:layout_width="100dp"
        android:layout_height="100dp"
        android:src="@drawable/rabbit"
        app:border_color="#008B45"
        app:border_width="2dp"
        app:corner_radius="20dp"
        app:inner_border_color="#FF7F24"
        app:inner_border_width="4dp"
        app:is_circle="true"
        />
```



源码：[`CircularImageView`](https://github.com/xiaofeng0325/CircularImageView)

