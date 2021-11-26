---
title: Flutter_tabBarView切换页面防止页面重绘
tags:
  - Android
  - Flutter Tips
categories:
  - Android
  - Flutter Tips
abbrlink: dffd90b2
date: 2020-03-10 11:56:49
---

### 被重绘的tab页要 混入AutomaticKeepAliveClientMixin

```dart
// with 混入
class DevicePageLayout extends  WidgetState<DevicePage>  with AutomaticKeepAliveClientMixin
```

### 实现wantKeepAlive方法 ，返回值改成true

```dart
  @override
  // TODO: implement wantKeepAlive
  bool get wantKeepAlive => true;
```

### build中加入 super.build(context);

```java
@override
Widget build(BuildContext context) {
  super.build(context);
  // TODO: implement build
```

<!--more-->

