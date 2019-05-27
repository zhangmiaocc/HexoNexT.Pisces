---
title: Flutter入门指南(九)之弹窗和提示(SnackBar、BottomSheet、Dialog)
tags:
  - Android
  - Flutter
categories:
  - Android
  - Flutter
abbrlink: 5da6faeb
date: 2019-04-27 11:30:42
---

前面的小节把常用的一些部件都介绍了，这节介绍下 `Flutter` 中的一些操作提示。`Flutter` 中的操作提示主要有这么几种 `SnackBar`、`BottomSheet`、`Dialog`，因为 `Dialog` 样式比较多，放最后讲好了

#### SnackBar

`SnackBar` 的源码相对简单

```dart
const SnackBar({
    Key key,
    @required this.content, // 提示信息
    this.backgroundColor, // 背景色
    this.action, // SnackBar 尾部的按钮，用于一些回退操作等
    this.duration = _kSnackBarDisplayDuration, // 停留的时长，默认 4000ms
    this.animation, // 进出动画
  })
```

<!--more-->

例如我们需要实现一个功能，修改某个值，修改后给用户一个提示，同时给用户一个撤销该操作的按钮，那么就可以通过 `SnackBar` 来简单实现。还有就是 `SnackBar` 可以和 `floatingActionButton` 完美的配合，弹出的时候不会遮挡住 `fab`

```dart
class _PromptDemoPageState extends State<PromptDemoPage> {
  var count = 0;

  @override
  void initState() {
    super.initState();
  }

  @override
  void dispose() {
    super.dispose();
  }

  // 自增操作
  increase() {
    setState(() => count++);
  }

  // 自减操作
  decrease() {
    setState(() => count--);
  }

  _changeValue(BuildContext context) {
    increase();
    Scaffold.of(context).showSnackBar(SnackBar(
        content: Text('当前值已修改'),
        action: SnackBarAction(label: '撤销', onPressed: decrease),
        duration: Duration(milliseconds: 2000)));
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Prompt Demo'),
      ),
      body: Column(children: <Widget>[
        Text('当前值：$count', style: TextStyle(fontSize: 20.0)),
        Expanded(
            // 为了方便拓展，我这边提取了 `snackBar` 的方法，并把按钮放在列表
            child: ListView(padding: const EdgeInsets.symmetric(horizontal: 12.0, vertical: 8.0), children: <Widget>[
          // SnackBar 需要提供一个包含 context，但是 context 不能是 Scaffold 节点下的 context，所以需要通过 Builder 包裹一层
          Builder(builder: (context) => RaisedButton(onPressed: () => _changeValue(context), child: Text('修改当前值'))),
        ]))
      ]),
      // 当 SnackBar 弹出时，fab 会上移一段距离
      floatingActionButton: Builder(
          builder: (context) => FloatingActionButton(onPressed: () => _changeValue(context), child: Icon(Icons.send))),
    );
  }
}
```

可以看下最后的效果图，请注意看 `fab` 和值的变化：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/2888797-da29f7ede96f2215.gif)

#### BottomSheet

`BottomSheet` 看命名就知道是从底部弹出的菜单，展示 `BottomSheet` 有两种方式，分别是 `showBottomSheet` 和 `showModalBottomSheet`，两种方式只有在展示类型上的差别，方法调用无差，而且 `showBottomSheet` 和 `fab` 有组合动画，`showModalBottomSheet` 则没有，看下实际的例子吧。在 `ListView` 中增加一个 `BottomSheet` 的按钮，因为 `BottomSheet` 需要的 `context` 也不能是 `Scaffold` 下的 `context`，所以需要通过 `Builder` 进行包裹一层，然后增加 `_showBottomSheet` 的方法

```dart
 _showBottomSheet(BuildContext context) {
    showBottomSheet(
      context: context,
      builder: (context) => ListView(
              // 生成一个列表选择器
              children: List.generate(
            20,
            (index) => InkWell(
                child: Container(alignment: Alignment.center, height: 60.0, child: Text('Item ${index + 1}')),
                onTap: () {
                  print('tapped item ${index + 1}');
                  Navigator.pop(context);
                }),
          )),
    );
  }
```

把 `showBottomSheet` 替换成 `showModalBottomSheet` 就是另外一种展示方式了，内部不需要做任何改变，我们看下两种的运行效果：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/2888797-70e344e56f98f683.gif)

可以看到 `showBottomSheet` 会充满整个屏幕，然后 `fab` 会跟随一起到 `AppBar` 的底部位置，而 `showModalBottomSheet` 展示的高度不会超过半个屏幕的高度，但是 `fab` 被其遮挡了。假如我们只需要展示 2-3 个 `item`，但是按照刚才的方式 `showModalBottomSheet` 的高度太高了，那我们可以在 `ListView` 外层包裹一层 `Container`，然后指定 `height` 即可

```dart
_showModalBottomSheet(BuildContext context) {
    showModalBottomSheet(
      context: context,
      builder: (context) => Container(
            child: ListView(
                children: List.generate(
              2,
              (index) => InkWell(
                  child: Container(alignment: Alignment.center, height: 60.0, child: Text('Item ${index + 1}')),
                  onTap: () {
                    print('tapped item ${index + 1}');
                    Navigator.pop(context);
                  }),
            )),
            height: 120,
          ),
    );
  }
```

修改高度后的效果：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/2888797-51e8092d6a0e879b.gif)

#### Dialog

相对于 `SnackBar` 和 `BottomSheet`，`Dialog` 的使用场景相对会更多，在 `MaterialDesign` 下，`Dialog` 主要有 3 种：`AlertDialog`，`SimpleDialog` 和 `AboutDialog`，当然在 `Cupertino` 风格下也有相应的 `Dialog`，因为这个系列以 `MaterialDesign` 风格为主，所以 `Cupertiono` 等下次有时间再写吧。

##### AlertDialog

在 `ListView` 中增加一个 `AlertDialog` 的按钮，用于点击显示 `AlertDialog` 用，然后加入显示 `AlertDilaog` 的方法，并将按钮的 `onPressed` 指向该方法，`Dialog` 的 `context` 可以是 `Scaffold` 下的 `context`，所以不需要用 `Builder` 来包裹一层。

```dart
_showAlertDialog() {
    showDialog(
        // 设置点击 dialog 外部不取消 dialog，默认能够取消
        barrierDismissible: false,
        context: context,
        builder: (context) => AlertDialog(
              title: Text('我是个标题...嗯，标题..'),
              titleTextStyle: TextStyle(color: Colors.purple), // 标题文字样式
              content: Text(r'我是内容\(^o^)/~, 我是内容\(^o^)/~, 我是内容\(^o^)/~'),
              contentTextStyle: TextStyle(color: Colors.green), // 内容文字样式
              backgroundColor: CupertinoColors.white,
              elevation: 8.0, // 投影的阴影高度
              semanticLabel: 'Label', // 这个用于无障碍下弹出 dialog 的提示
              shape: Border.all(),
              // dialog 的操作按钮，actions 的个数尽量控制不要过多，否则会溢出 `Overflow`
              actions: <Widget>[
                // 点击增加显示的值
                FlatButton(onPressed: increase, child: Text('点我增加')),
                // 点击减少显示的值
                FlatButton(onPressed: decrease, child: Text('点我减少')),
                // 点击关闭 dialog，需要通过 Navigator 进行操作
                FlatButton(onPressed: () => Navigator.pop(context), 
                           child: Text('你点我试试.')),
              ],
            ));
  }
```

最后看下效果：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/2888797-dd8dc81b43e02e44.gif)

##### SimpleDialog

`SimpleDialog` 相比于 `AlertDialog` 少了 `content` 和 `action` 参数，多了 `children` 属性，需要传入 `Widget` 列表，那就可以自定义全部内容了。那我们这里就实现一个性别选择的 `Dialog`，选择后通过 `Taost` 提示选择的内容，`Taost` 就是之前导入的第三方插件，先看下效果图吧

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/2888797-a9dd6de23478249e.gif)

只要实现 `children` 是个列表选择器就可以了，比较简单，直接上代码

```dart
_showSimpleDialog() {
    showDialog(
        barrierDismissible: false,
        context: context,
        builder: (context) => SimpleDialog(
              title: Text('我是个比较正经的标题...\n选择你的性别'),
              // 这里传入一个选择器列表即可
              children: _genders
                  .map((gender) => InkWell(
                        child: Container(height: 40.0, child: Text(gender), alignment: Alignment.center),
                        onTap: () {
                          Navigator.pop(context);
                          Fluttertoast.showToast(msg: '你选择的性别是 $gender');
                        },
                      ))
                  .toList(),
            ));
  }
```

##### AboutDialog

`AboutDialog` 主要是用于展示你的 `App` 或者别的相关东西的内容信息的，平时用的比较少，显示 `AboutDialog` 有两种方式可以展示，一种是前面一样的 `showDialog` 方法，传入一个 `AboutDialog` 实例，还有中方法是直接调用 `showAboutDialog` 方法。我们还是一样在列表加个按钮，并指向显示 `AboutDialog` 的事件。

```dart
_showAboutDialog() {
    showDialog(
        barrierDismissible: false,
        context: context,
        builder: (context) => AboutDialog(
              // App 的名字
              applicationName: 'Flutter 入门指北',
              // App 的版本号
              applicationVersion: '0.1.1',
              // App 基本信息下面会显示一行小字，主要用来显示版权信息
              applicationLegalese: 'Copyright: this is a copyright notice topically',
              // App 的图标
              applicationIcon: Icon(Icons.android, size: 28.0, color: CupertinoColors.activeBlue),
              // 任何你想展示的
              children: <Widget>[Text('我是个比较正经的对话框内容...你可以随便把我替换成任何部件，只要你喜欢(*^▽^*)')],
            ));
  }
```

也可以通过 `showAboutDialog` 实现同样的效果

```dart
  _showAboutDialog() {
    showAboutDialog(
      context: context,
      applicationName: 'Flutter 入门指北',
      applicationVersion: '0.1.1',
      applicationLegalese: 'Copyright: this is a copyright notice topically',
      applicationIcon: Image.asset('images/app_icon.png', width: 40.0, height: 40.0),
      children: <Widget>[Text('我是个比较正经的对话框内容...你可以随便把我替换成任何部件，只要你喜欢(*^▽^*)')],
    );
  }
```

最后的效果：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/2888797-692a6d8b1b1b2ad1.gif)

`AboutDialog` 会自带两个按钮 `VIEW LICENSES` 和 `CLOSE`，`VIEW LICENSES` 会跳转一个 `Flutter Licenses` 的网页，`CLOSE` 会关闭，至于为什么是英文的，是因为我们没有设置语言的原因，这个涉及到多语言，这边推荐几篇之前看过的文章，如果下次有时间的话会单独拿出来讲下

[英文原版多语言设置，介绍两种方式实现](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.didierboelens.com%2F2018%2F04%2Finternationalization---make-an-flutter-application-multi-lingual%2F)

[国人翻译版，未持续更新第二种方式](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fyumi0629%2Farticle%2Fdetails%2F81873141)

[使用插件 in18 版](https://links.jianshu.com/go?to=https%3A%2F%2Fjuejin.im%2Fpost%2F5c701379f265da2d9b5e196a)

这边为了支持中文，我们做下如下的修改，首先打开 `pubspec.ymal` 文件加入如下支持

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190522113832.png)

`get package` 后给 `MaterialApp` 加入如下属性，这样就会支持中文了，这里需要导入包 `package:flutter_localizations/flutter_localizations.dart`，再次运行，就会发现之前的英文变成中文了，当然你也可以设置成别的语言。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190522113849.png)

#### Dialog 状态保持

假如有个需求，需要在弹出的 `Dialog` 显示当前被改变的值，然后通过按钮可以修改这个值 ，该如何实现。相信很多小伙伴都会这么认为，通过 `setState` 来修改不就行了吗，没错，我一开始的确这么去实现的，我们先看下代码好了，增加一个 `DialogState` 按钮，然后指向对应的点击事件

```
_showStateDialog() {
    showDialog(
        context: context,
        barrierDismissible: false,
        builder: (context) => SimpleDialog(
              title: Text('我这边能实时修改状态值'),
              contentPadding: const EdgeInsets.symmetric(horizontal: 12.0, vertical: 8.0),
              children: <Widget>[
                Text('当前的值是： $_count', style: TextStyle(fontSize: 18.0)),
                Padding(
                  padding: const EdgeInsets.symmetric(vertical: 12.0),
                  child: Row(mainAxisAlignment: MainAxisAlignment.spaceAround, children: <Widget>[
                    RaisedButton(
                      onPressed: increase,
                      child: Text('点我自增'),
                    ),
                    RaisedButton(
                      onPressed: decrease,
                      child: Text('点我自减'),
                    ),
                    RaisedButton(
                      onPressed: () => Navigator.pop(context),
                      child: Text('点我关闭'),
                    )
                  ]),
                )
              ],
            ));
  }
```

然后我们运行看下

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/2888797-b90b21ff3cca2241.gif)

诶诶诶，怎么 `Dialog` 的值不改变呢，明明界面上的已经修改了啊。所以说图样图森破咯，看下官方对 `showDialog` 方法的解释吧

```dart
// This function takes a `builder` which typically builds a [Dialog] widget.
// Content below the dialog is dimmed with a [ModalBarrier]. The widget
// returned by the `builder` does not share a context with the location that
// `showDialog` is originally called from. Use a [StatefulBuilder] or a
// custom [StatefulWidget] if the dialog needs to update dynamically.
```

糟糕透的翻译又来了：该方法通过 `builder` 参数来传入一个 `Dialog` 部件，`dialog` 下的内容被一个「模态障碍」阻隔，`builder` 的 `context` 和调用 `showDialog` 时候的 `context` 不是共享的，如果需要动态修改 `dialog` 的状态值，需要通过 `StatefulBuilder` 或者自定义 `dialog` 继承于 `StatefulWidget` 来实现

所以解决的方法很明确，对上面的代码进行修改，在外层嵌套一个 `StatefulBuilder` 部件

```dart
 _showStateDialog() {
    showDialog(
        context: context,
        barrierDismissible: false,
        // 通过 StatefulBuilder 来保存 dialog 状态
        // builder 需要传入一个 BuildContext 和 StateSetter 类型参数
        // StateSetter 有一个 VoidCallback，修改状态的方法在这写
        builder: (context) => StatefulBuilder(
            builder: (context, dialogStateState) => SimpleDialog(
                  title: Text('我这边能实时修改状态值'),
                  contentPadding: const EdgeInsets.symmetric(horizontal: 12.0, vertical: 8.0),
                  children: <Widget>[
                    Text('当前的值是： $_count', style: TextStyle(fontSize: 18.0)),
                    Padding(
                      padding: const EdgeInsets.symmetric(vertical: 12.0),
                      child: Row(mainAxisAlignment: MainAxisAlignment.spaceAround, children: <Widget>[
                        RaisedButton(
                          // 通过 StatefulBuilder 的 StateSetter 来修改值
                          onPressed: () => dialogStateState(() => increase()),
                          child: Text('点我自增'),
                        ),
                        RaisedButton(
                          onPressed: () => dialogStateState(() => decrease()),
                          child: Text('点我自减'),
                        ),
                        RaisedButton(
                          onPressed: () => Navigator.pop(context),
                          child: Text('点我关闭'),
                        )
                      ]),
                    )
                  ],
                )));
  }
```

然后再运行下，可以看到 `dialog` 和界面的值保持一致了

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/2888797-fe488a85d5276b4c.gif)

*以上部分代码查看 prompt_main.dart 文件*

差不多常用弹窗和操作提示就在这了，好好消化吧~