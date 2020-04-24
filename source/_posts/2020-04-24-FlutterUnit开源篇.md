---
title: FlutterUnit开源篇
tags:
  - Android
  - Flutter Tips
categories:
  - Android
  - Flutter Tips
abbrlink: d75d6183
date: 2020-04-24 17:36:43
---

#### 零、前言

> `FlutterUnit`终于和大家见面了，这将是`【张风捷特烈】`长期维护的一个项目
>  [欢迎star](https://github.com/toly1994328/FlutterUnit) : [github.com/toly1994328…](https://github.com/toly1994328/FlutterUnit)
>  [可以在github 仓库里下载apk体验 : ](https://github.com/toly1994328/FlutterUnit/releases)

| FlutterUnit.apk 下载                                         | Github仓库地址                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/24/171a9911d22d34a8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/18/1718af8fec8c62e2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

<!--more-->

### 一、组件的展示页面

#### 1. `210+组件收录`

> Flutter源码中的可用的组件一共350个左右，纷繁复杂，也没有明确的分类标准
>  FlutterUnit 对`大大小小，常用不常用`的组件能收的尽量收录。并`根据个人感觉进行评星`
>  `目前收录组件211个`，每个都有至少一个演示展现和代码展示。

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175af35f63c8fb?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175b0c1c92a004?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175b0a95d5c549?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175af9b09f76f6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175b0766ed455b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175af6b9523083?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

#### 2. 组件详情页

> ```
> 207个组件`全部都有详情页。对于重要的组件会详细展现
>  一般都会有某个演示对应的组件和属性,尽量做到细致，如果有需要补充，欢迎联系我。
>  `最重要的是: 所有的演示展现都是Flutter的组件形成的，而非图片，这就意味着可操作性更高。
> ```

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175c3f21476fc5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175c44a1cfa94c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175c4a7cd90126?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175c5171d0373f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175c56ce136676?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175c61623c6462?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/171775f429e77628?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175d050c8ebdac?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175d09ac9ebd06?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

#### 3. 组件的可操作性

> 对一些操作交互的组件或有可操作性的某些组件，`提供操作演示`

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175df98f83e05c?imageslim) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175dcce9022ddc?imageslim) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175de9b348a26a?imageslim) |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175e07aeb7c5db?imageslim) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175e14f00bacd6?imageslim) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175e2353306a37?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

#### 4. 相关组件的关联切换

> `相关组件通过link to 可以进行切换, 满足你的探索欲。`
>  如果有的关联未加入，欢迎联系我，对我来说，加个数字就行了。



![img](https://user-gold-cdn.xitu.io/2020/4/14/17175ea0ea610669?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175e8c2a46e1f3?imageslim) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175e921dfc5c81?imageslim) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17175e968c4f68e4?imageslim) |

------

#### 5. 代码的查看和分享

> 激动人心的是，你可以通过右侧的图标`展开/隐藏 实现下面效果的代码`
>  并且`支持分享`，如果你想亲自体验，so，easy ! 而且`代码高亮样式可以自定义`。

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/171760369b9ae9d6?imageslim) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717603ad9119f2a?imageslim) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717604b10154271?imageslim) |

------

### 二、全局配置

#### 1. 颜色主题

> 只提供八种颜色，可在`右滑菜单页`的`我的主题`配置,`可以拓展`

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/171760c51633383d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171760cbc7d0ddba?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171760b8c24c188f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/171760e274f4bbd4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171760e5a8ef180d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171760fd8bb60a8f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

#### 2.字体配置

> 支持全局字体设置,`可以拓展`

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717615741f8d2e3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171761667bbf6051?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717617b8ab59421?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

#### 3.item样式设置

> 支持item样式设置，`可以拓展，支持征集`，详见`Flutter Unit 1.0 征集方案`

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717620037fd9a50?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717620161fa89ec?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171762026eb8656d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

#### 4.代码面板风格设置

> 支持代码风格设置，`可以拓展，支持征集`，详见`Flutter Unit 1.0 征集方案`

| .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717628b5fe1591c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717629001ade9b0?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/17176298797c49a7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171762a2e5534237?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/171762a5db361ce9?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171762aad1c14ce7?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

### 三、搜索与收藏功能

#### 1.搜索功能

> 由于Flutter中Widget比较杂乱，不太好分类，所以搜索是非常重要的
>  另外可以根据星级进行过滤，支持多选。目前正在考虑根据功能分类，之后会有所完善。

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/171775fc594e4605?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171775fd99268a78?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171775fefef50fb9?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

#### 2.搜藏功能

> 搜藏页做得比较简陋，后面打算做收藏夹，可以自己创建的那种。

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/17177668aa7fd135?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/17177665c53256b4?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717765ec688731c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

> `FlutterUnit 1.0`目前基本就是这么多功能，可以在Github中下载打包后的apk玩玩
>  希望能对你的Flutter学习有所帮助。

------

#### 3.关于我与项目

> 不多说，都在图里。

| .                                                            | .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/171777c67ed0c205?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171777c8ccfce16b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171777caed85b26a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |
|                                                              |                                                              |                                                              |

------

### 四、FlutterUnit 2.0 展望

> 后面将是一些集录，需要更多的Flutter爱好者参与，计划方案将陆续发布。

| .                                                            | .                                                            |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/171777f2cf762719?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717799a14c22a11?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |
| ![img](https://user-gold-cdn.xitu.io/2020/4/14/1717799eb0f01c6f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) | ![img](https://user-gold-cdn.xitu.io/2020/4/14/171779a42e429ce1?imageView2/0/w/1280/h/960/format/webp/ignore-error/1) |

------

#### 尾声

> 欢迎[Star和关注FlutterUnit](https://github.com/toly1994328/FlutterUnit) 的发展，让我们一起携手，成为Unit一员。
>  另外本人有一个Flutter微信交流群，欢迎小伙伴加入，共同探讨Flutter的问题，期待与你的交流与切磋。

> ```
> @张风捷特烈 2019.04.04 未允禁转`
>  `我的公众号:编程之王`
>  `联系我--邮箱:1981462002@qq.com --微信:zdl1994328`
>  `~ END ~
> ```

------

#### 最后， `Flutter Widget 图鉴` 奉上

> 目前只画了十张，大概100多个组件，过过眼也好。后面有时间会更新。
>  原图资源也放在[ FlutterUnit 中 ](https://github.com/toly1994328/FlutterUnit/tree/master/widgets_unity): 如发现错误欢迎联系我及时改正。



![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a0620c2e259?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a08357b00ec?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a0990f44709?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a0b2519089d?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a0c8ccc1b7a?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a0ea141bd92?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a0fe343d674?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a126bddafd8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a13b9553260?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)





![img](https://user-gold-cdn.xitu.io/2020/4/14/17177a23ecaf0fca?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



------



![img](https://user-gold-cdn.xitu.io/2020/4/19/17190d67e6f1bd39?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

作者：张风捷特烈

链接：https://juejin.im/post/5e94e4d3f265da480836b943

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。