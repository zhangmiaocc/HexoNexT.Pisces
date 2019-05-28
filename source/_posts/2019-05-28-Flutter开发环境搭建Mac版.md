---
title: Flutter开发环境搭建Mac版
tags:
  - Android
  - Flutter
categories:
  - Android
  - Flutter
abbrlink: 3223dff5
date: 2019-04-24 11:07:40
---

### 系统环境要求

Flutter因为是新出的框架，所以对系统还是有一定的要求的。

- MacOS （64-bit）
- 磁盘空间：大于700M，如果算上Android Studio等编辑工具，尽量大于3G。
- 命令号工具：bash、mkdir、rm、git、curl、unzip、which、brew 这些命令在都可以使用。

注意：一般你会在brew这个命令下载坑，很多mac系统都没有安装这个，你可以进行安装，因为这个和本知识关系性不大，所以我就不写流程了，如果你出现问题，直接点击链接学习安装就可以了。

学习安装brew：https://segmentfault.com/a/1190000013317511

### 下载Flutter SDK包

这里推荐去官网下载就好，我挂了梯子，速度并不慢。

网址：https://flutter.dev/docs/development/tools/sdk/releases?tab=macos

<!--more-->

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190528111109.png)

### 配置环境变量

压缩包下载好以后，找个位置进行解压。这个位置很重要，因为下面配置环境变量的时候要用到。比如你配置到了根目录下的app文件夹。

1.打开终端工具（这个我就不用写了吧），使用vim进行配置环境变量，命令如下：

```bash
open ~/.bash_profile
```

在打开的文件里增加一行代码，意思是配置flutter命令在任何地方都可以使用。

```bash
export PATH=/app/flutter/bin:$PATH
```

提示：这行命令你要根据你把压缩包解压的位置来进行编写，写的是你的路径，很有可能不跟文章一样。

配置完成后，需要用`source`命令重新加载一下 ，具体命令如下：

```bash
source ~/.bash_profile
```

完成这部以后，就算我们flutter的安装工作完成了，但是这还不能进行开发。可以使用命令来检测一下，是否安装完成了。

```bash
flutter -h
```

### 检查开发环境

到上边为止，我们安装好了Flutter，但是还不具备开发环境。开发还需要很多软件和插件的支持，那到底需要哪些插件和软件那？我们可以使用Flutter为我们提供的命令来进行检查：

```text
flutter doctor
```

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190528111601.png)

如果你英文很好，你应该可以很容易读出上面的检测结果，有很多条目都没有通过。需要我们安装检测结果一条条进行安装，直到满足开发环境。（如果有[!]x标志，表示本行检测没有通过，就需要我们设置或者安装相应的软件了。）

如果你有安装，那么第一步要作的是允许协议（android-licenses）。允许方法就是在终端运行如下命令：

```bash
flutter doctor --android-licenses
```

然后让你输入Y/N的时候，一路Y就可以了（至于什么意思，我也没仔细看，大概就和安装软件的下一步下一步是一样的，你按N是不能成功的）。

这不完成后，我们再使用`flutter doctor`进行检测后，会看到还是有很多x。

其实大概意思就是我们需要这些软件，Flutter推荐你用brew命令进行安装。

我们可以直接在终端里输入下列命令（每输完一个都要等一会，等待软件包安装完成）

```bash
brew install --HEAD libimobiledevice
brew install ideviceinstaller
brew install ios-deploy
brew install cocoapods
pod setup
```

安装完这些，我们还需要为Android Studio安装一下Flutter插件（这个有可能你安装过，如果出现下面的提示，说明你还没有安装）

```bash
✗ Flutter plugin not installed; this adds Flutter specific functionality.
✗ Dart plugin not installed; this adds Dart specific functionality.
```

打开Android Stuido 软件，然后找到Plugin的配置，搜索Flutter插件。

点中间的`Search in repositories`,然后点击安装。

安装完成后，你需要重新启动一下Android Studio软件。

### Pub源的配置

如果你没有梯子，一个人人都知道的原因，你还需要在环境变量里配置一下Pub源，不然你是无法进行使用的。

运行：

```bash
open ~/.bash_profile
```

增加两行配置

```bash
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
```

重新加载环境变量

```bash
source ~/.bash_profile
```

### Android studio新建Flutter项目

打开Andorid Studio ，会出现下面的界面，我们选择第二项，新建Flutter项目。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190528113518.png)

打开第二个窗口后，选择第一个选项`Flutter Application`(flutter应用)。

这步完成后，系统就会自动为我们创建一个Flutter项目

### 安装AVD虚拟机

1. 现在需要一个虚拟机来运行我们的程序，可以点击Android Studio中的上方菜单`tool` -`AVD Manager`选项。

2. 出现新建菜单，选择`Create Virtual Device.....`,如果你一个虚拟机也没建过，这个选项在对话框的中间![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190528113803.png)

3. 选择虚拟机类型，这个你随意选就好，我选择的是`Nexus 5x`。（如果你屏幕小，就选择一个小屏幕的虚拟机）![image-20190528113941915](/Users/zhangmiao/Library/Application Support/typora-user-images/image-20190528113941915.png)

4. 选择系统，这里尽量选择最新的，我选择了`Android 9.0`系统，选择好后，又是一个漫长的等待过程。

5. 安装好后，点击开始按钮，运行虚拟机了（第一次运行，需要安装系统，会慢一些），运行起来后，如下图。

   ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190528115821.png)

   ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190528115944.png)

### 让Flutter跑起来

虚拟机运行以后，可以点击`debug`按钮，让Flutter程序跑起来。如果你幸运的话，你的Flutter程序经过编译后，就会跑起来了。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190528120632.png)

