---
title: Flutter自定义轮播图Banner
tags:
  - Android
  - Flutter Tips
categories:
  - Android
  - Flutter Tips
abbrlink: 2f3abe95
date: 2020-03-14 14:16:21
---

### widget_banner.dart

```dart
import 'dart:async';


import 'package:ajbaby/enum/enum_indicator_style.dart';
import 'package:ajbaby/route_manager.dart';
import 'package:flutter/material.dart';
import 'package:flutter_screenutil/flutter_screenutil.dart';


/*
 * 自定义banner
 */
// ignore: must_be_immutable
class CustomBanner extends StatefulWidget {
  List<String> images;
  double height;
  ValueChanged<int> onTap;


  IndicatorStyle indicatorStyleStr;


  CustomBanner({
    Key key,
    @required this.images,
    @required this.indicatorStyleStr,
    this.height = 375,
    this.onTap,
  }) : super(key: key);


  @override
  _CustomBannerState createState() => _CustomBannerState();
}


class _CustomBannerState extends State<CustomBanner> {
  PageController _pageController;
  int _curIndex;
  Timer _timer;


  @override
  void initState() {
    super.initState();
    _curIndex = widget.images.length * 5;
    _pageController = PageController(initialPage: _curIndex);
    _initTimer();
  }


  @override
  void dispose() {
    // TODO: implement dispose
    super.dispose();
//    _cancelTimer();
  }


  @override
  Widget build(BuildContext context) {
    return Stack(
      alignment: Alignment.bottomCenter,
      children: <Widget>[
        _buildPageView(),
        _buildIndicator(),
      ],
    );
  }


  Widget _buildIndicator() {
    var length = widget.images.length;
    return Positioned(
      bottom: 5,
      child: Row(
        children: widget.images.map((s) {
          return Padding(padding: const EdgeInsets.symmetric(horizontal: 3.0), child: _indicatorStyle(s, length));
        }).toList(),
      ),
    );
  }


  Widget _indicatorStyle(s, length) {
    switch (widget.indicatorStyleStr) {
      case IndicatorStyle.circle:
        return Container(
            child: ClipOval(
          child: Container(
            width: ScreenUtil().setWidth(8),
            height: ScreenUtil().setHeight(8),
            color: s == widget.images[_curIndex % length] ? Color(0xFFFF5F6D) : Color(0xFFE0E0E0),
          ),
        ));
        break;
      case IndicatorStyle.line:
        return Container(
            child: ClipRRect(
          borderRadius: BorderRadius.circular(10.0),
          child: Container(
            width: ScreenUtil().setWidth(20),
            height: ScreenUtil().setHeight(3),
            color: s == widget.images[_curIndex % length] ? Color(0xFFFF5F6D) : Color(0xFFE0E0E0),
          ),
        ));
        break;
    }
    return null;
  }


  Widget _buildPageView() {
    var length = widget.images.length;
    return Container(
      height: ScreenUtil().setHeight(widget.height),
      child: PageView.builder(
        controller: _pageController,
        onPageChanged: (index) {
          setState(() {
            _curIndex = index;
            if (index == 0) {
              _curIndex = length;
              _changePage();
            }
          });
        },
        itemBuilder: (context, index) {
          return GestureDetector(
            onPanDown: (details) {
              _cancelTimer();
            },
            onTap: () {
              //弹出路由，跳转到其他页面
              Navigator.of(context).pushNamed(RouteNames.productDetails);
//              Scaffold.of(context).showSnackBar(
//                SnackBar(
//                  content: Text('当前 page 为 ${index % length}'),
//                  duration: Duration(milliseconds: 500),
//                ),
//              );
            },
            child: Image.network(
              widget.images[index % length],
              fit: BoxFit.cover,
            ),
          );
        },
      ),
    );
  }


  /// 点击到图片的时候取消定时任务
  _cancelTimer() {
    if (_timer != null) {
      _timer.cancel();
      _timer = null;
      _initTimer();
    }
  }


  /// 初始化定时任务
  _initTimer() {
    if (_timer == null) {
      _timer = Timer.periodic(Duration(seconds: 3), (t) {
        _curIndex++;
        _pageController.animateToPage(
          _curIndex,
          duration: Duration(milliseconds: 300),
          curve: Curves.linear,
        );
      });
    }
  }


  /// 切换页面，并刷新小圆点
  _changePage() {
    Timer(Duration(milliseconds: 350), () {
      _pageController.jumpToPage(_curIndex);
    });
  }
}

```

### 引用

```dart
child: CustomBanner(
  images: _imgData,
  indicatorStyleStr: IndicatorStyle.line,
  height: double.infinity,
),
```

<!--more-->