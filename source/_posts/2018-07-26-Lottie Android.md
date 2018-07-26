---
title: Lottie Android For Animation
date: 2018-07-26 17:44:43
- blog
- markdown
- Android 
- Animation
categories:
- Android 
---

Lottie是一个支持[Android](https://link.jianshu.com?t=https://github.com/airbnb/lottie-android)、[iOS](https://link.jianshu.com?t=https://github.com/airbnb/lottie-ios)、[React Native](https://link.jianshu.com?t=https://github.com/airbnb/lottie-react-native)，并由 [Adobe After Effects](https://link.jianshu.com?t=http://www.adobe.com/cn/products/aftereffects.html)制作aep格式的动画，然后经由[bodymovin](https://link.jianshu.com?t=https://github.com/bodymovin/bodymovin)插件转化渲染为json格式可被移动端本地识别解析的[Airbnb](https://link.jianshu.com?t=https://github.com/airbnb)开源库。
 Lottie实时呈现After Effects动画效果，让应用程序可以像使用静态图片一样轻松地使用动画。
 Lottie支持API 14及以上。

#### 一、预览

[![Example1](https://github.com/airbnb/lottie-android/raw/master/gifs/Example1.gif)](https://github.com/airbnb/lottie-android/blob/master/gifs/Example1.gif)

[![Example2](https://github.com/airbnb/lottie-android/raw/master/gifs/Example2.gif)](https://github.com/airbnb/lottie-android/blob/master/gifs/Example2.gif)

[![Example3](https://github.com/airbnb/lottie-android/raw/master/gifs/Example3.gif)](https://github.com/airbnb/lottie-android/blob/master/gifs/Example3.gif)

[![Community](https://github.com/airbnb/lottie-android/raw/master/gifs/Community%202_3.gif)](https://github.com/airbnb/lottie-android/blob/master/gifs/Community%202_3.gif)

[![Example4](https://github.com/airbnb/lottie-android/raw/master/gifs/Example4.gif)](https://github.com/airbnb/lottie-android/blob/master/gifs/Example4.gif)

#### 二 、基本使用

在自己项目module的build.gradle文件中添加如下代码：

```java
dependencies {  
  compile 'com.airbnb.android:lottie:2.0.0-beta4'
}
```

LottieAnimationView使用最简单的方法是:

```html
<com.airbnb.lottie.LottieAnimationView
    android:id="@+id/animation_view"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:lottie_fileName="hello-world.json"
    app:lottie_loop="true"
    app:lottie_autoPlay="true" />
```

其中lottie_loop属性为是否重复无限期动画，当为true时，动画无限次数播放，为false时，播放一次。

或者把json资源放在app/src/main/assets下，也可以这样使用它:

```
LottieAnimationView animationView = (LottieAnimationView) findViewById(R.id.animation_view);
animationView.setAnimation("hello-world.json");
animationView.loop(true);
animationView.playAnimation();
```

该方法将加载文件并在后台解析动画，在完成后异步开始呈现。

如果您希望重用一个动画，例如在列表的每个项目中，或者从一个网络请求JSONObject中加载它:

```java
 LottieAnimationView animationView = (LottieAnimationView) findViewById(R.id.animation_view);
 ...
 Cancellable compositionCancellable = LottieComposition.Factory.fromJson(getResources(), jsonObject, (composition) -> {
     animationView.setComposition(composition);
     animationView.playAnimation();
 });

 // 取消异步加载
 // compositionCancellable.cancel();
```

你也可以控制动画添加监听：

```java
animationView.addAnimatorUpdateListener((animation) -> {
    // Do something.
});
animationView.playAnimation();
...
if (animationView.isAnimating()) {
    // Do something.
}
...
animationView.setProgress(0.5f);
...
// 自定义动画速度和时长
ValueAnimator animator = ValueAnimator.ofFloat(0f, 1f)
    .setDuration(500);
animator.addUpdateListener(animation -> {
    animationView.setProgress(animation.getAnimatedValue());
});
animator.start();
...
animationView.cancelAnimation();
```

你可以给整个动画，一个特定的图层，或者一个图层的特定内容添加一个颜色过滤器。

```java
// 任何符合颜色过滤界面的类
final PorterDuffColorFilter colorFilter = new PorterDuffColorFilter(Color.RED, PorterDuff.Mode.LIGHTEN);

// 在整个视图中添加一个颜色过滤器
animationView.addColorFilter(colorFilter);

//在特定的图层中添加一个颜色滤镜
animationView.addColorFilterToLayer("hello_layer", colorFilter);

// 添加一个彩色过滤器特效“hello_layer”上的内容
animationView.addColorFilterToContent("hello_layer", "hello", colorFilter);

// 清除所有的颜色滤镜
animationView.clearColorFilters();
```

注意:颜色过滤器只适用于图层，如图像层和实层，以及包含填充、描边或组内容的内容。

在引擎盖下,LottieAnimationView使用LottieDrawable呈现其动画。如果需要，您可以直接使用可绘制的表单:

```java
LottieDrawable drawable = new LottieDrawable();
LottieComposition.Factory.fromAssetFileName(getContext(), "hello-world.json", (composition) -> {
    drawable.setComposition(composition);
});
```

如果你的动画会经常重用,LottieAnimationView内置了一个可选的缓存策略。使用LottieAnimationView .setAnimation(String,CacheStrategy)。CacheStrategy可以Strong, Weak, 或者None。LottieAnimationView对加载和解析的动画持有强或弱的参考。弱或强表示缓存中组合的GC参考强度。

#### 三、Image 支持

如果您的动画是从assets中加载的，并且您的图像文件位于assets 的子目录中，那么您可以对图像进行动画。你可以用LottieAnimationView或者LottieDrawable对象调用setImageAssetsFolder(String)方法，明确assets相对文件夹内的路径,确保图像bodymovin出口与他们的名字不变,文件夹应该img_ 开头。如果直接使用LottieDrawable,当你完成时您必须调用recycleBitmaps。
 如果你需要提供你自己的位图，如果你从网络或其他地方下载，你可以提供一个委托来做这个:

```java
animationView.setImageAssetDelegate(new ImageAssetDelegate() {
         @Override public Bitmap fetchBitmap(LottieImageAsset asset) {
           getBitmap(asset);
         }
       });
```

#### 四、性能和内存

如果该组合没有遮罩或mattes，那么性能和内存开销应该相当不错。没有创建任何位图，大多数操作都是简单的画布绘制操作。
 如果这个组合有遮罩或mattes，就会使用屏幕外的缓冲区，并且会有一个性能打击。
 如果在你的动画列表中使用,推荐使用CacheStrategy，在调用LottieAnimationView.setAnimation(String, CacheStrategy)的时候，所以动画不需要每次都反序列化。

#### 五、Lottie官方Demo下载

[https://fir.im/lottiedemo](https://link.jianshu.com?t=https://fir.im/lottiedemo)

#### 六、参考资料

[http://airbnb.design/lottie/](https://link.jianshu.com?t=http://airbnb.design/lottie/)
 [http://www.lottiefiles.com/](https://link.jianshu.com?t=http://www.lottiefiles.com/)
 [https://github.com/airbnb/lottie-android](https://link.jianshu.com?t=https://github.com/airbnb/lottie-android)
 [http://www.adobe.com/cn/products/aftereffects.html](https://link.jianshu.com?t=http://www.adobe.com/cn/products/aftereffects.html)
 [https://github.com/bodymovin/bodymovin](https://link.jianshu.com?t=https://github.com/bodymovin/bodymovin)
 [https://github.com/airbnb/lottie-react-native](https://link.jianshu.com?t=https://github.com/airbnb/lottie-react-native)
 [https://github.com/airbnb/lottie-ios](https://link.jianshu.com?t=https://github.com/airbnb/lottie-ios)
 [https://github.com/airbnb](https://link.jianshu.com?t=https://github.com/airbnb)

 

 

 

 

 