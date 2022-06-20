---
title: FlutterAPP生命周期监听
tags:
  - Android
  - Flutter Tips
categories:
  - Android
  - Flutter Tips
abbrlink: b07d1684
date: 2022-06-20 13:56:43
---

State 的生命周期，定义了 Widget 的加载到构建的全过程，可以利用其回调机制根据 Widget 的状态选择合适的时机做合适的事情。而 APP 的生命周期，则定义了 APP 从启动到退出的全过程。

如果想在对应的 APP 的生命周期事件中做相应的处理，比如 APP 从后台进入前台、从前台退到后台，或是在 UI 绘制完后做一些处理，则可以应用 WidgetsBindingObserver 类来实现。

**WidgetsBindingObserver 中的回调方法**

```
// Accessibility 相关特性回调
void didChangeAccessibilityFeatures() { }

// App 生命周期改变回调
void didChangeAppLifecycleState(AppLifecycleState state) { }

// 本地化语言改变回调
void didChangeLocales(List<Locale> locale) { }

// 系统窗口相关改变回调
void didChangeMetrics() { }

// 系统亮度改变回调
void didChangePlatformBrightness() { }

// 文本缩放系数改变回调
void didChangeTextScaleFactor() { }

// 内存不足警告回调
void didHaveMemoryPressure() { }

// 页面 pop
Future<bool> didPopRoute() => Future<bool>.value(false);

// 页面 push
Future<bool> didPushRoute(String route) => Future<bool>.value(false);
```

要使用以上回调方法，只需通过给 WidgetsBindingObserver 单例对象设置监听器即可监听相关回调方法

<!--more-->

### 生命周期回调
didChangeAppLifecycleState 回调方法中，有一个参数类型为 AppLifecycleState 的枚举类，这个枚举类是 Flutter 对 App 生命周期状态的封装，常用的状态包括 inactive、paused、resumed

- inactive：处在不活动状态，无法处理用户响应
- paused：不可见且不能响应用户的输入，但在后台继续活动中
- resumed：可见的，且能响应用户的输入

```dart
class AppLifecycleReactor extends StatefulWidget {
  const AppLifecycleReactor({ Key key }) : super(key: key);

  @override
  _AppLifecycleReactorState createState() => _AppLifecycleReactorState();
}

class _AppLifecycleReactorState extends State<AppLifecycleReactor> with WidgetsBindingObserver {
  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);// 注册监听器
  }

  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);// 移除监听器
    super.dispose();
  }

  AppLifecycleState _notification;

  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    print("$state");
  }
}
```

可以试着切换下前后台，观察下控制台输出的 App 状态

- 从前台退到后台，控制台打印的 App 生命周期变化如下：

  AppLifecycleState.resumed->AppLifecycleState.inactive->AppLifecycleState.paused

- 从后台切换回前台，控制台打印的 App 生命周期变化如下：

  AppLifecycleState.paused->AppLifecycleState.inactive->AppLifecycleState.resumed

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20220620140229.png)

### 帧绘制回调

有时除了需要监听 App 的生命周期回调外，还需要在组件完成渲染后做一些与显示相关的其他处理，比如切换线程等，此时可以使用 WidgetsBinding 来实现

WidgetsBinding 提供了单次 Frame 绘制回调及实时 Frame 绘制回调两种机制

- 单次 Frame 绘制回调：通过 addPostFrameCallback 实现。在当前 Frame 绘制完后进行回调，且只会回调一次，如果需要多次回调则需设置多次
```dart
WidgetsBindingObserver.instance.addPostFrameCallback((_){
    print("addPostFrameCallback 绘制回调"); // 只回调一次
});
```

- 实时 Frame 绘制回调：通过 addPersistentFrameCallback 实现。在每次绘制 Frame 结束后进行回调
```dart
WidgetsBindingObserver.instance.addPersistentFrameCallback((_){
    print("addPersistentFrameCallback 绘制回调"); // 每帧都回调
});
```
