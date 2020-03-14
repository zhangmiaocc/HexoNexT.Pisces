---
title: Flutter自定义TabBar
tags:
  - Android
  - Flutter Tips
categories:
  - Android
  - Flutter Tips
abbrlink: 251aa72f
date: 2020-03-14 11:07:37
---

### custom_tabbar.dart

```dart
import 'package:ajbaby/res/colors.dart';
import 'package:flutter/material.dart';


// ignore: must_be_immutable
class CustomTabBar extends StatefulWidget implements PreferredSizeWidget {
  Color containerBgColor;
  Color labelColor;
  Color unselectedLabelColor;
  Color indicatorColor;
  TabController tabController;
  List<Widget> tabsList = new List<Widget>();
  List<Widget> tabBarViewsList = new List<Widget>();

  // @required声明必传参数
  CustomTabBar({
    Key key,
    @required this.tabsList,
    @required this.tabBarViewsList,
    @required this.tabController,
    this.containerBgColor = YColors.color_FFFFFF,
    this.labelColor = YColors.color_FF5F6D,
    this.unselectedLabelColor = YColors.color_858585,
    this.indicatorColor = YColors.color_FF5F6D,
  }) : super(key: key);


  @override
  _CustomTabBarState createState() {
    return _CustomTabBarState();
  }


  @override
  // TODO: implement preferredSize
  Size get preferredSize => null;
}


class _CustomTabBarState extends State<CustomTabBar> with AutomaticKeepAliveClientMixin {
  @override
  void initState() {
    super.initState();
  }


  @override
  void dispose() {
    super.dispose();
//    widget.tabController.dispose();
  }


  @override
  Widget build(BuildContext context) {
    super.build(context);
    return Scaffold(
      appBar: PreferredSize(
          child: Container(
            color: widget.containerBgColor,
            child: TabBar(
              isScrollable: true,
              controller: widget.tabController,
              tabs: widget.tabsList,
              labelColor: widget.labelColor,
              unselectedLabelColor: widget.unselectedLabelColor,
              indicatorColor: widget.indicatorColor,
            ),
          ),
          preferredSize: Size.fromHeight(kToolbarHeight)),
      body: TabBarView(
        controller: widget.tabController,
        children: widget.tabBarViewsList,
      ),
    );
  }


  @override
  // TODO: implement wantKeepAlive
  bool get wantKeepAlive => true;
}
```

### 引用

```dart
body: CustomTabBar(
  tabsList: _tabsList,
  tabBarViewsList: _tabBarViewsList,
  tabController: _tabController,
),
```

<!--more-->