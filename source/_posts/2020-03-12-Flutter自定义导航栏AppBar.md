---
title: Flutter自定义导航栏AppBar
tags:
  - Android
  - Flutter Tips
categories:
  - Android
  - Flutter Tips
abbrlink: 16d03859
date: 2020-03-12 17:35:36
---

### 自定义AppBar
源码中可以看到,AppBar只是实现了 PreferredSizeWidget接口

```dart
class AppBar extends StatefulWidget implements PreferredSizeWidget 
```

那么我们也可以从这进行入手，自定义一个实现了 `PreferredSizeWidget`的Widget
具体代码并不多，比你想象的简单，直接上代码：

```dart
import 'package:flutter/material.dart';
import 'package:flutter_screenutil/flutter_screenutil.dart';

/// 这是一个可以指定SafeArea区域背景色的AppBar
/// PreferredSizeWidget提供指定高度的方法
/// 如果没有约束其高度，则会使用PreferredSizeWidget指定的高度

// ignore: must_be_immutable
class CustomAppbar extends StatefulWidget implements PreferredSizeWidget {
  final double contentHeight; //从外部指定高度
  Color navigationBarBackgroundColor; //设置导航栏背景的颜色
  Widget leadingWidget;
  Widget trailingWidget;
  String title;

  CustomAppbar({
    @required this.leadingWidget,
    @required this.title,
    this.contentHeight = 44,
    this.navigationBarBackgroundColor = Colors.white,
    this.trailingWidget,
  }) : super();

  @override
  State<StatefulWidget> createState() {
    return new _CustomAppbarState();
  }

  @override
  Size get preferredSize => new Size.fromHeight(contentHeight);
}

/// 这里没有直接用SafeArea，而是用Container包装了一层
/// 因为直接用SafeArea，会把顶部的statusBar区域留出空白
/// 外层Container会填充SafeArea，指定外层Container背景色也会覆盖原来SafeArea的颜色
///     var statusheight = MediaQuery.of(context).padding.top;  获取状态栏高度

class _CustomAppbarState extends State<CustomAppbar> {
  @override
  void initState() {
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return new Container(
      color: widget.navigationBarBackgroundColor,
      child: new SafeArea(
        top: true,
        child: new Container(
            decoration: new UnderlineTabIndicator(
              borderSide: BorderSide(width: 1.0, color: Color(0xFFeeeeee)),
            ),
            height: widget.contentHeight,
            child: new Stack(
              alignment: Alignment.center,
              children: <Widget>[
                Positioned(
                  left: 0,
                  child: new Container(
                    padding: const EdgeInsets.only(left: 5),
                    child: widget.leadingWidget,
                  ),
                ),
                new Container(
                  child: new Text(
                    widget.title,
                    style: new TextStyle(
                        fontSize: ScreenUtil().setSp(33, allowFontScalingSelf: true),
                        color: Color(0xFF333333),
                        fontWeight: FontWeight.w600),
                  ),
                ),
                Positioned(
                  right: 0,
                  child: new Container(
                    padding: const EdgeInsets.only(right: 5),
                    child: widget.trailingWidget,
                  ),
                ),
              ],
            )),
      ),
    );
  }
}

```

<!--more-->

### 引用的地方:

```dart
appBar: new CustomAppbar(
          title: '日历',
          leadingWidget: leftBarButtonItemWidget(),
          trailingWidget: rightBarButtonItemsWidget(),
        )
```

`leftBarButtonItemWidget() ` `rightBarButtonItemsWidget()`两个方法是我自定义的导航栏按钮，实现自己需要的即可。值得说的是，可以将leadingWidget设置为非@required的，在自定义的AppBar里面做好处理即可,另外在上面的代码中并没有限制导航栏上每个Widget所占用的最大空间，如果你的导航栏子组件可能很宽，提前进行妥善处理是个好主意。