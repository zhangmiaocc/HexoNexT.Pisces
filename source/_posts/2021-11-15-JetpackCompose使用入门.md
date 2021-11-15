---
title: Jetpack Compose 入门学习
tags:
  - Kotlin
  - Jetpack
categories:
  - Android
  - Jetpack
abbrlink: 7b2baf2a
date: 2021-11-15 11:40:42
---

## Compose简介

- Jetpack Compose：利用声明式编程构建Android原生界面（UI）的 工具包

## 优势

- 更少的代码、代码量锐减
- 强大的工具/组件支持
- 直观的 Kotlin API
- 简单易用

## Compose 编程思想
- **声明性编程范式**：声明性的函数构建一个简单的界面组件，无需修改任何 XML 布局，也不需要使用布局编辑器，只需要调用 Jetpack Compose 函数来声明想要的元素，Compose 编译器即会完成后面的所有工作。

- **简单的组合函数**

  ```kotlin
  @Composable
  fun Greeting(name: String) {
    Text(text = "Hello $name!")
  }
  ```

  ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211115115340.png)

- **声明性范式转变**：在 Compose 的声明性方法中，微件相对无状态，并且不提供 setter 或 getter 函数。实际上，微件不会以对象形式提供。您可以通过调用带有不同参数的同一可组合函数来更新界面。这使得向架构模式（如 ViewModel）提供状态变得很容易，如应用架构指南中所述。然后，可组合项负责在每次可观察数据更新时将当前应用状态转换为界面。

- **动态** ：组合函数是用 Kotlin 而不是 XML 编写

- **重组**：在 Compose 中，您可以使用新数据再次调用可组合函数。这样做会导致函数进行重组 -- 系统会根据需要使用新数据重新绘制函数发出的微件。Compose 框架可以智能地仅重组已更改的组件。
  - 可组合函数可以按任何顺序执行
  - 可组合函数可以并行运行
  - 重组会跳过尽可能多的内容
  - 重组是乐观的操作
  - 可组合函数可能会非常频繁地运行


## 开发环境

- Arctic Fox 2020-3-1 版本以上，[下载最新AndroidStudio](https://developer.android.com/studio)
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211115145212.png)
- ComposeApp仅支持Kotlin 最低sdk 版本为21，Android 5.0
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211115145308.png)
- Gradle Compose相关依赖
	```groovy
	plugins {
    id 'com.android.application'
    id 'kotlin-android'
	}
	android {
    compileSdk 31
	
    defaultConfig {
        applicationId "com.zm.myjetpackcompose"
        minSdk 21
        targetSdk 31
        versionCode 1
        versionName "1.0"
    
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
        vectorDrawables {
            useSupportLibrary true
        }
    }
    
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
    kotlinOptions {
        jvmTarget = '1.8'
        useIR = true
    }
    buildFeatures {
        compose true
    }
    composeOptions {
        kotlinCompilerExtensionVersion compose_version
        kotlinCompilerVersion '1.5.21'
    }
    packagingOptions {
        resources {
            excludes += '/META-INF/{AL2.0,LGPL2.1}'
        }
    }
	}
	dependencies {
    implementation 'androidx.core:core-ktx:1.3.2'
    implementation 'androidx.appcompat:appcompat:1.2.0'
    implementation 'com.google.android.material:material:1.3.0'
    implementation "androidx.compose.ui:ui:$compose_version"
    implementation "androidx.compose.material:material:$compose_version"
    implementation "androidx.compose.ui:ui-tooling-preview:$compose_version"
    implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.3.1'
    implementation 'androidx.activity:activity-compose:1.3.0-alpha06'
    testImplementation 'junit:junit:4.+'
    androidTestImplementation 'androidx.test.ext:junit:1.1.2'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.3.0'
    androidTestImplementation "androidx.compose.ui:ui-test-junit4:$compose_version"
    debugImplementation "androidx.compose.ui:ui-tooling:$compose_version"
	}
	```

- 需要安装最新Java11， java 8 环境会报以下错误
	```apl
	Android Gradle plugin requires Java 11 to run. You are currently using Java 1.8.
     You can try some of the following options:
       - changing the IDE settings.
       - changing the JAVA_HOME environment variable.
       - changing `org.gradle.java.home` in `gradle.properties`.      
	```
	![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211115150103.png)

- @Preview生效，则环境正常
	![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211115150241.png)

## ComposeUI布局
### @Compose
所有关于构建View的方法都必须添加`@Compose`注解才可以。并且`@Compose`协程的`Suspend`的使用方法比较类似,被`@Compose`注解的方法只能在同样被`@Comopse`解的方法中才能被调用。
```kotlin
@Composable
fun Greeting(name: String) {
    Text(text = "Hello $name!")
}
```
### @Preview
`@Preview`注解的方法可以在不运行App的情况下就可以确认布局的情况。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211115153114.png)
常用的参数:
- `name`: String: 为该Preview命名，该名字会在布局预览中显示。
- `showBackground`: Boolean: 是否显示背景，true为显示。
- `backgroundColor`: Long: 设置背景的颜色。
- `showDecoration`: Boolean: 是否显示Statusbar和Toolbar，true为显示。
- `group`: String: 为该Preview设置group名字，可以在UI中以group为单位显示。
- `fontScale`: Float: 可以在预览中对字体放大，范围是从0.01。
- `widthDp`: Int: 在Compose中渲染的最大宽度，单位为dp。
- `heightDp`: Int: 在Compose中渲染的最大高度，单位为dp。
上面的参数都是可选参数，还有像背景设置等的参数并不是对实际的App进行设置，只是对Preview中的背景进行设置，为了更容易看清布局。
```kotlin
@Preview(showBackground = true,name = "Text UI",backgroundColor = 0xFF888888)
@Composable
private fun DefaultPreview() {
    MyJetpackComposeTheme {
        Greeting("Android")
    }
}
```
### setContent
setContent的作用是和Layout/View中的setContentView是一样的。
setContent的方法也是有@Compose注解的方法。所以，在setContent中写入关于UI的@Compopse方法，即可在Activity中显示。

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            MyJetpackComposeTheme {
                // A surface container using the 'background' color from the theme
                Surface(color = MaterialTheme.colors.background) {
                    Greeting("Android")
                }
            }
        }
    }
```
### Theme
在创建新的Compose项目时会自动创建一个Theme.kt文件。 我们可以通过更改颜色来完成对主题颜色的设置。
```kotlin
package com.zm.myjetpackcompose.ui.theme

import androidx.compose.foundation.isSystemInDarkTheme
import androidx.compose.material.MaterialTheme
import androidx.compose.material.darkColors
import androidx.compose.material.lightColors
import androidx.compose.runtime.Composable

private val DarkColorPalette = darkColors(
    primary = Purple200,
    primaryVariant = Purple700,
    secondary = Teal200
)

private val LightColorPalette = lightColors(
    primary = Purple500,
    primaryVariant = Purple700,
    secondary = Teal200

    /* Other default colors to override
    background = Color.White,
    surface = Color.White,
    onPrimary = Color.White,
    onSecondary = Color.Black,
    onBackground = Color.Black,
    onSurface = Color.Black,
    */
)
@Composable
fun MyJetpackComposeTheme(darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable() () -> Unit) {
    val colors = if (darkTheme) {
        DarkColorPalette
    } else {
        LightColorPalette
    }

    MaterialTheme(
        colors = colors,
        typography = Typography,
        shapes = Shapes,
        content = content
    )
}
```
### Modifier
[Modifier](https://developer.android.com/jetpack/compose/modifiers-list)是各个Compose的UI组件一定会用到的一个类。它是被用于设置UI的摆放位置，padding等信息的类。
#### padding 设置各个UI的padding
```kotlin
Modifier.padding(10.dp) // 给上下左右设置成同一个值
Modifier.padding(10.dp, 11.dp, 12.dp, 13.dp) // 分别为上下左右设值
Modifier.padding(10.dp, 11.dp) // 分别为上下和左右设值
Modifier.padding(InnerPadding(10.dp, 11.dp, 12.dp, 13.dp))// 分别为上下左右设值
```
#### plus 可以把其他的Modifier加入到当前的Modifier中。
```kotlin
Modifier.plus(otherModifier) // 把otherModifier的信息加入到现有的modifier中
```
#### fillMaxHeight,fillMaxWidth,fillMaxSize 类似于match_parent,填充整个父layout。
```kotlin
Modifier.fillMaxHeight() // 填充整个高度
```
#### width,heigh,size 设置Content的宽度和高度。
```kotlin
Modifier.width(2.dp) // 设置宽度
Modifier.height(3.dp)  // 设置高度
Modifier.size(4.dp, 5.dp) // 设置高度和宽度 复制代码
```
#### widthIn, heightIn, sizeIn 设置Content的宽度和高度的最大值和最小值。
```kotlin
Modifier.widthIn(2.dp) // 设置最大宽度
Modifier.heightIn(3.dp) // 设置最大高度
Modifier.sizeIn(4.dp, 5.dp, 6.dp, 7.dp) // 设置最大最小的宽度和高度 
```
#### gravity 在Column中元素的位置。
```kotlin
Modifier.gravity(Alignment.CenterHorizontally) // 横向居中
Modifier.gravity(Alignment.Start) // 横向居左
Modifier.gravity(Alignment.End) // 横向居右
```
#### rtl, ltr 开始布局UI的方向。
```kotlin
Modifier.rtl  // 从右到左
Modifier.ltr  // 从左到右 复制代码`</pre>

Modifier的方法都返回Modifier的实例的链式调用，所以只要连续调用想要使用的方法即可。

@Composable
fun Greeting(name: String) {
 Text(text = "Hello $name!", modifier = Modifier.padding(20.dp).fillMaxSize())
} 

```
### Column，Row
Column 线性布局 ≈ Android LinearLayout-VERTICAL
Row 水平布局 ≈ Android LinearLayout-HORIZONTAL
Column和Row可以理解为在View/Layout体系中的纵向和横向的ViewGroup。
- Modifier 用上述的方法传入已经按需求设置好的Modifier即可。
- Arrangement.Horizontal, Arrangement.Vertical 需要给Row传入Arrangement.Horizontal，为Column传入Arrangement.Vertical。 这些值决定如何布置内部UI组件。
可传入的值为Center, Start, End, SpaceEvenly, SpaceBetween, SpaceAround。
- Alignment.Vertical, Alignment.Horizontal 需要给Row传入Alignment.Vertical，为Column传入Alignment.Horizontal。 使用方法和Modifier的gravity中传入参数的用法是一样的.
- @Composable ColumnScope.() -> Unit 需要传入标有@Compose的UI方法。但是这里我们会有lamda函数的写法来实现。
```kotlin
Column {
        Row(modifier = Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceAround, verticalAlignment = Alignment.CenterVertically) {
            Text(text = "Hello $name!")
        }
    }
```

### Box
帧布局≈Android FrameLayout，可将一个元素放在另一个元素上，如需在 Row 中设置子项的位置，请设置 horizontalArrangement 和 verticalAlignment 参数。对于 Column，请设置 verticalArrangement 和 horizontalAlignment 参数

### ConstraintLayout
需要引入`implementation "androidx.constraintlayout:constraintlayout-compose:1.0.0-beta02"`

#### 列表
- 可以滚动的布局
	```kotlin
	//我们可以使用 verticalScroll() 修饰符使 Column 可滚动，但以上布局并无法实现重用，可能导致性能问题
    Column(
        modifier = Modifier.verticalScroll(rememberScrollState())
    ) {
        messages.forEach { message ->
            MessageRow(message)
        }
    }
	```

- LazyColumn/LazyRow == RecylerView/listView 列表布局，解决了滚动时的性能问题，LazyColumn和LazyRow之间的区别就在于它们的列表项布局和滚动方向不同。
	- 内边距 
		```kotlin
		LazyColumn(
        contentPadding = PaddingValues(horizontal = 16.dp, vertical = 8.dp),
    ) {
        // ...
    }
    ```
  - item间距
  	```kotlin
  	LazyColumn(
        verticalArrangement = Arrangement.spacedBy(4.dp),
    ) {
        // ...
    }   
    ```
  - 浮动列表的浮动标题，使用 LazyColumn 实现粘性标题，可以使用stickyHeader()函数
  	```kotlin
  	@Composable
    fun ListWithHeader(items: List<Item>) {
        LazyColumn {
            stickyHeader {
                Header()
            }
	  
            items(items) { item ->
                ItemRow(item)
            }
        }
    }
    ```
  - 网格布局LazyVerticalGrid
  	```kotlin
  	 @Composable
    fun PhotoGrid(photos: List<Photo>) {
        LazyVerticalGrid(
            cells = GridCells.Adaptive(minSize = 128.dp)
        ) {
            items(photos) { photo ->
                PhotoItem(photo)
            }
        }
    }
    ```

### 自定义布局
#### 通过重组基础布局实现
#### Canvas绘制
```kotlin 
Canvas(modifier = Modifier.fillMaxSize()) {
        val canvasWidth = size.width
        val canvasHeight = size.height
        //drawCircle 画圆
        //drawRectangle 画矩形
        //drawLine //画线
        drawCircle(
            color = Color.DarkGray,
            center = Offset(x = canvasWidth / 2, y = canvasHeight / 2),
            radius = size.minDimension / 4
        )
    }
```
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20211115161558.png)

