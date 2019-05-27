---
title: Flutter入门指南(十一)之状态管理，BLoC
tags:
  - Android
  - Flutter
categories:
  - Android
  - Flutter
abbrlink: d121323a
date: 2019-04-27 15:07:53
---

#### Stream

在 `dart` 部分记得分享过 `Stream` 的文章链接，但是我知道你们肯定没几个愿意看的，所以这里再提下。还是得从源码开始...因为源码的注释比较长，就不贴注释了，可以自己看，我这边就提取一些关键信息。

`Stream` 是 `Dart` 提供的一种数据流订阅管理的"工具"，感觉有点像 `Android` 中的 `EventBus` 或者 `RxBus`，`Stream` 可以接收任何对象，包括是另外一个 `Stream`，接收的对象通过 `StreamController` 的 `sink` 进行添加，然后通过 `StreamController` 发送给 `Stream`，通过 `listen` 进行监听，`listen` 会返回一个 `StreamSubscription` 对象，`StreamSubscription` 可以操作对数据流的监听，例如 `pause`，`resume`，`cancel` 等。

`Stream` 分两种类型：

1.  `Single-subscription Stream`：单订阅 stream，整个生命周期只允许有一个监听，如果该监听 cancel 了，也不能再添加另一个监听，而且只有当有监听了，才会发送数据，主要用于文件 `IO` 流的读取等。
2.  `Broadcast Stream`：广播订阅 stream，允许有多个监听，当添加了监听后，如果流中有数据存在就可以监听到数据，这种类型，不管是否有监听，只要有数据就会发送，用于需要多个监听的情况。

<!--more-->

还是看下例子会比较直观

```dart
class _StreamHomeState extends State<StreamHome> {
  StreamController _controller = StreamController();  // 创建单订阅类型 `StreamController`
  Sink _sink;
  StreamSubscription _subscription;

  @override
  void initState() {
    super.initState();

    _sink = _controller.sink; // _sink 用于添加数据
    // _controller.stream 会返回一个单订阅 stream，
    // 通过 listen 返回 StreamSubscription，用于操作流的监听操作
    _subscription = _controller.stream.listen((data) => print('Listener: $data'));

    // 添加数据，stream 会通过 `listen` 方法打印
    _sink.add('A');
    _sink.add(11);
    _sink.add(11.16);
    _sink.add([1, 2, 3]);
    _sink.add({'a': 1, 'b': 2});
  }

  @override
  void dispose() {
    super.dispose();
    // 最后要释放资源...
    _sink.close();
    _controller.close();
    _subscription.cancel();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Container(),
    );
  }
}
```

看下控制台的输出：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190527150910.png)

果然把所有的数据都打印出来了，前面有说过，单订阅的 stream 只有当 `listen` 后才会发送数据，不试试我还是不相信的，我们把 `_sink.add` 放到 `listen` 前面去执行，再看控制台的打印结果。居然真的是一样的，Google 粑粑果然诚不欺我。接着试下 `pause`，`resume` 方法，看下数据如何监听，修改代码

```dart
_sink = _controller.sink;
_subscription = _controller.stream.listen((data) => print('Listener: $data'));
_sink.add('A');
_subscription.pause(); // 暂停监听
_sink.add(11);
_sink.add(11.16);
_subscription.resume(); // 恢复监听
_sink.add([1, 2, 3]);
_sink.add({'a': 1, 'b': 2});
```

再看控制台的打印，你们可以先猜下是什么结果，我猜大部分人都会觉得应该是不会有 11 和 11.16 打印出来了。然鹅事实并非这样，打印的结果并未发生变化，也就是说，调用 `pause` 方法后，stream 被堵住了，数据不继续发送了。

接下来看下广播订阅 stream，对代码做下修改

```dart
StreamController _controller = StreamController.broadcast();
  Sink _sink;
  StreamSubscription _subscription;

  @override
  void initState() {
    super.initState();

    _sink = _controller.sink;

    _sink.add('A');
    _subscription = _controller.stream.listen((data) => print('Listener: $data'));

    _sink.add(11);
    _subscription.pause();
    _sink.add(11.16);
    _subscription.resume();

    _sink.add([1, 2, 3]);
    _sink.add({'a': 1, 'b': 2});
  }
// ...
}
```

我们再看下控制台的打印：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190527150935.png)

你猜对答案了吗，这边做下小总结：

**单订阅 Stream 只有当存在监听的时候，才发送数据，广播订阅 Stream 则不考虑这点，有数据就发送；当监听调用 pause 以后，不管哪种类型的 stream 都会停止发送数据，当 resume 之后，把前面存着的数据都发送出去。**

sink 可以接受任何类型的数据，也可以通过泛型对传入的数据进行限制，比如我们对 `StreamController` 进行类型指定 `StreamController<int> _controller = StreamController.broadcast();` 因为没有对 `Sink` 的类型进行限制，还是可以添加除了 `int` 外的类型参数，但是运行的时候就会报错，`_controller` 对你传入的参数做了类型判定，拒绝进入。

`Stream` 中还提供了很多 `StremTransformer`，用于对监听到的数据进行处理，比如我们发送 0~19 的 20 个数据，只接受大于 10 的前 5 个数据，那么可以对 stream 如下操作

```dart
_subscription = _controller.stream
    .where((value) => value > 10)
    .take(5)
    .listen((data) => print('Listen: $data'));

List.generate(20, (index) => _sink.add(index));
```

那么打印出来的数据如下图

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190527150951.png)

除了 `where`，`take` 还有很多 `Transformer`， 例如 `map`，`skip` 等等，小伙伴们可以自行研究。了解了 `Stream` 的基本属性后，就可以继续往下了~

#### StreamBuilder

前面提到了 stream 通过 `listen` 进行监听数据的变化，`Flutter` 就为我们提供了这么个部件 `StreamBuilder` 专门用于监听 stream 的变化，然后自动刷新重建。接着来看下源码

```dart
const StreamBuilder({
    Key key,
    this.initialData, // 初始数据，不传入则为 null
    Stream<T> stream,
    @required this.builder
  }) : assert(builder != null),
       super(key: key, stream: stream);

@override
AsyncSnapshot<T> initial() => AsyncSnapshot<T>.withData(ConnectionState.none, initialData);
```

`StreamBuilder` 必须传入一个 `AsyncWidgetBuilder` 参数，初始值 `initialData` 可为空， `stream` 用于监听数据变化，`initial` 方法的调用在其父类 `StremBuilderBase` 中，接着看下 `StreamBuilderBaseState` 的源码，这里我删除一些不必要的源码，方便查看，完整的源码可自行查看

```dart
class _StreamBuilderBaseState<T, S> extends State<StreamBuilderBase<T, S>> {
  // ...
  @override
  void initState() {
    super.initState();
    _summary = widget.initial(); // 通过传入的初始值生成默认值，如果没有传入则会是 null
    _subscribe(); // 注册传入的 stream，用于监听变化
  }
  
  // _summary 为监听到的数据
  @override
  Widget build(BuildContext context) => widget.build(context, _summary);

  // ...
  void _subscribe() {
    if (widget.stream != null) { 
      // stream 通过外部传入，对数据的变化进行监听，
      // 在不同回调中，通过 setState 进行更新 _summary
      // 当 _summary 更新后，由于调用了 setState，重新调用 build 方法，将最新的 _summary 传递出去
      _subscription = widget.stream.listen((T data) {
        setState(() { 
          _summary = widget.afterData(_summary, data); 
        });
      }, onError: (Object error) {
        setState(() {
          _summary = widget.afterError(_summary, error);
        });
      }, onDone: () {
        setState(() {
          _summary = widget.afterDone(_summary);
        });
      });
      _summary = widget.afterConnected(_summary); // 
    }
  }
}
```

在之前更新数据都需要通过 `setState` 进行更新，这里了解完了 `stream`，我们就不使用 `setState` 更新，使用 `Stream` 来更新

```dart
class _StreamHomeState extends State<StreamHome> {
  // 定义一个全局的 `StreamController`
  StreamController<int> _controller = StreamController.broadcast();
  // `sink` 用于传入新的数据
  Sink<int> _sink;
  int _counter = 0;

  @override
  void initState() {
    super.initState();
    _sink = _controller.sink;
  }

  @override
  void dispose() {
    super.dispose();
    // 需要销毁资源
    _sink.close();
    _controller.close();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
          child: Container(
        alignment: Alignment.center,
        child: StreamBuilder(
          builder: (_, snapshot) => Text('${snapshot.data}', style: TextStyle(fontSize: 24.0)),
          stream: _controller.stream, // stream 在 StreamBuilder 销毁的时候会自动销毁
          initialData: _counter,
        ),
      )),
      // 通过 `sink` 传入新的数据，去通知 `stream` 更新到 builder 中
      floatingActionButton: FloatingActionButton(
        onPressed: () => _sink.add(_counter++),
        child: Icon(Icons.add),
      ),
    );
  }
}
```

那么当点击按钮的时候，就会刷新界面上的值，通过上面的源码分析，`StreamBuilder` 也是通过 `setState` 方法进行刷新，那么两种方法孰优孰劣呢，当然是通过 `Stream` 啦，这不是废话吗。**因为通过调用 setState 刷新的话，会把整个界面都进行重构，但是通过 StreamBuilder 的话，只刷新其 builder，这样效率就更高了**，最后看小效果吧，所谓有图有真相嘛

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/2888797-31cb9b8c3a99abaf.gif)

这一步，我们摒弃了 `setState` 方法，那么下一步，我们试试把 `StatefulWidget` 替换成 `StatelessWidget` 吧，而且**官方也推荐使用 StatelessWidget 替换 StatefulWidget**，这里就需要提下 `BLoC` 模式了。

#### BLoC

说实话，现在 Google 下 「flutter bloc」能搜到很多文章，基本上都是通过 `InheritedWidget` 来实现的，例如这篇[Flutter | 状态管理探索篇——BLoC(三)](https://links.jianshu.com/go?to=https%3A%2F%2Fjuejin.im%2Fpost%2F5bb6f344f265da0aa664d68a)，但是 `InheritedWidget` 没有提供 `dispose` 方法，那么就会存在 `StreamController` 不能及时销毁等问题，所以，参考了一篇国外的文章，[Reactive Programming - Streams - BLoC](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.didierboelens.com%2F2018%2F08%2Freactive-programming---streams---bloc%2F) 这里通过使用 `StatefulWidget` 来实现，当该部件销毁的时候，可以在其 `dispose` 方法中及时销毁 `StreamController`，这里我还是先当个搬运工，搬下大佬为我们实现好的基类

```dart
abstract class BaseBloc {
  void dispose(); // 该方法用于及时销毁资源
}

class BlocProvider<T extends BaseBloc> extends StatefulWidget {
  final Widget child; // 这个 `widget` 在 stream 接收到通知的时候刷新
  final T bloc; 
  
  BlocProvider({Key key, @required this.child, @required this.bloc}) : super(key: key);

  @override
  _BlocProviderState<T> createState() => _BlocProviderState<T>();

  // 该方法用于返回 Bloc 实例
  static T of<T extends BaseBloc>(BuildContext context) {
    final type = _typeOf<BlocProvider<T>>(); // 获取当前 Bloc 的类型
    // 通过类型获取相应的 Provider，再通过 Provider 获取 bloc 实例
    BlocProvider<T> provider = context.ancestorWidgetOfExactType(type); 
    return provider.bloc; 
  }

  static Type _typeOf<T>() => T;
}

class _BlocProviderState<T> extends State<BlocProvider<BaseBloc>> {
    
  @override
  void dispose() {
    widget.bloc.dispose(); // 及时销毁资源
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return widget.child;
  }
}
```

接着我们对前面的例子使用 `BLoC` 进行修改。

首先，我们需要创建一个 `Bloc` 类，用于修改 count 的值

```dart
class CounterBloc extends BaseBloc {
  int _count = 0;
  int get count => _count;

  // stream
  StreamController<int> _countController = StreamController.broadcast();

  Stream<int> get countStream => _countController.stream; // 用于 StreamBuilder 的 stream

  void dispatch(int value) {
    _count = value;
    _countController.sink.add(_count); // 用于通知修改值
  }

  @override
  void dispose() {
    _countController.close(); // 注销资源
  }
}
```

在使用 `Bloc` 前，需要在最上层的容器中进行注册，也就是 `MaterialApp` 中

```dart
void main() => runApp(StreamApp());

class StreamApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 这里对创建的 bloc 类进行注册，如果说有多个 bloc 类的话，可以通过 child 进行嵌套注册即可
    // 放在最顶层，可以全局调用，当 App 关闭后，销毁所有的 Bloc 资源，
    // 也可以在路由跳转的时候进行注册，至于在哪里注册，完全看需求
    // 例如实现主题色的切换，则需要在全局定义，当切换主题色的时候全局切换
    // 又比如只有某个或者某几个特殊界面调用，那么完全可以通过在路由跳转的时候注册
    return BlocProvider(  
        child: MaterialApp(
          debugShowCheckedModeBanner: false,
          home: StreamHome(),
        ),
        bloc: CounterBloc());
  }
}

class StreamHome extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 获取注册的 bloc，必须先注册，再去查找
    final CounterBloc _bloc = BlocProvider.of<CounterBloc>(context); 
    return Scaffold(
      body: SafeArea(
          child: Container(
        alignment: Alignment.center,
        child: StreamBuilder(
          initialData: _bloc.count,
          stream: _bloc.countStream,
          builder: (_, snapshot) => Text('${snapshot.data}', style: TextStyle(fontSize: 20.0)),
        ),
      )),
      floatingActionButton:
          // 通过 bloc 中的 dispatch 方法进行值的修改，通知 stream 刷新界面
          FloatingActionButton(onPressed: () => 
                               _bloc.dispatch(_bloc.count + 1), child: Icon(Icons.add)),
    );
  }
}
```

重新运行后，查看效果还是一样的。所以我们成功的对 `StatefulWidget` 进行了替换

再继续讲之前，先总结下 `Bloc`

​    **1. 成功的把页面和逻辑分离开了，页面只展示数据，逻辑通过 BLoC 进行处理**

​    **2. 减少了 setState 方法的使用，提高了性能**

​    **3. 实现了状态管理**

#### RxDart

因为上面的参考文章中提到了 `RxDart`，个人觉得有必要了解下，当然目前也有很多文章介绍 `RxDart`，所以我就讲下和 `BLoC` 有点关系的部分吧。`RxDart` 需要通过引入插件的方式引入(`rxdart: ^0.21.0`)

如果需要查看详细的内容，我这里提供几篇文章链接

[RxDart 文档](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2FReactiveX%2Frxdart)

[RxDart: Magical transformations of Streams](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.burkharts.net%2Fapps%2Fblog%2Frxdart-magical-transformations-of-streams%2F)

其实 RxDart 就是对 Stream 的进一步分装，RxDart 提供了三种 Subject，其功能类似 Stream 中的单订阅 stream 和 广播 stream。

1. `PublishSubject`

   ```dart
   /// PublishSubject is, by default, a broadcast (aka hot) controller, in order
   /// to fulfill the Rx Subject contract. This means the Subject's `stream` can
   /// be listened to multiple times.
   ```

   通过注释可以发现 `PuslishSubject` 不可被多次订阅，尽管实现是通过 `StreamController<T>.broadcast` 方式实现，其实三种都是通过 `broadcast` 方式实现的，所以实现的功能就是类似 `Single-subscription Stream` 的功能。

2. `BehaviorSubject`

   ```dart
   /// BehaviorSubject is, by default, a broadcast (aka hot) controller, in order
   /// to fulfill the Rx Subject contract. This means the Subject's `stream` can
   /// be listened to multiple times.
   ```

   `BehaviorSubject` 可以被多次订阅，那么这个就是实现了 `Broadcast Stream` 功能。

3. `ReplaySubject`

   ```dart
   /// ReplaySubject is, by default, a broadcast (aka hot) controller, in order
   /// to fulfill the Rx Subject contract. This means the Subject's `stream` can
   /// be listened to multiple times.
   ```

   `ReplaySubject` 其实也是实现 `Broadcast Stream` 功能，那么它和 `BehaviorSubject` 的区别在哪呢，别急，等我慢慢讲。

   ```dart
   /// As items are added to the subject, the ReplaySubject will store them.
   /// When the stream is listened to, those recorded items will be emitted to
   /// the listener.
   ```

   当有数据添加了，但是还没有监听的时候，它会将数据存储下来，等到有监听了，再发送出去，也就是说，`ReplaySubject` 实现了 `Brodacast Stream` 的多订阅功能，同时也实现了 `Single-subscription Stream` 的存储数据的功能，每次添加了新的监听，都能够获取到全部的数据。当然，这还不是它的全部功能，它还可以设置最大的监听数量，会只监听最新的几个数据，在注释中，提供了这么两个例子，可以看下

   ```dart
   /// ### Example 
   ///
   ///     final subject = new ReplaySubject<int>();
   ///
   ///     subject.add(1);
   ///     subject.add(2);
   ///     subject.add(3);
   ///
   ///     subject.stream.listen(print); // prints 1, 2, 3
   ///     subject.stream.listen(print); // prints 1, 2, 3
   ///     subject.stream.listen(print); // prints 1, 2, 3
   ///
   /// ### Example with maxSize
   ///
   ///     final subject = new ReplaySubject<int>(maxSize: 2); // 实现监听数量限制
   ///
   ///     subject.add(1);
   ///     subject.add(2);
   ///     subject.add(3);
   ///
   ///     subject.stream.listen(print); // prints 2, 3
   ///     subject.stream.listen(print); // prints 2, 3
   ///     subject.stream.listen(print); // prints 2, 3
   ```

那么我们可以使用 `RxDart` 对前面使用 `Stream` 实现的例子进行替换，最简单的其实只需要使用 `BehaviorSubject` 替换 `StreamController.broadcast()` 就可以了，别的都不需要变化。但是 `RxDart` 有自己的变量，还是按照 `RxDart` 的方式来

```dart
// 继承自 StreamController，所以 StreamController 拥有的属性都有
BehaviorSubject<int> _countController = BehaviorSubject();
//  StreamController<int> _countController = StreamController.broadcast();

// 继承自 Stream，所以这里直接用之前 stream 的写法也没问题，但是这样就有点不 RxDart 了
Observable<int> get countStream => Observable(_countController.stream);
//  Stream<int> get countStream => _countController.stream;

void dispatch(int value) {
  _count = value;
  // 直接提供了 add 方法，不需要通过 sink 来添加
  _countController.add(_count);
//    _countController.sink.add(_count);
}
```

再次运行还是能过实现相同的效果。如果说要在 `RxDart` 和 `Stream` 两种实现方式中选择一种，个人更偏向于 `RxDart`，因为它对 `Stream` 进行了进一步的封装，提供了更多更方便的数据转换方法，而且链式的写法真的很舒服，用过了就停不下来，具体的方法介绍可以参考上面提供的链接。

#### Provide

说实话自己封装 `BLoC` 来实现分离逻辑和界面，相对还是有点难度的，这边可以通过第三方来实现，这边推荐 Google 粑粑的库，[flutter_provide](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fgoogle%2Fflutter-provide)，看下官方对关键部件和静态方法的介绍

> -  `Provide<T>` - Widget used to obtain values from a `ProviderNode` higher up in the widget tree and rebuild on change. The `Provide<T>`widget should only be used with `Stream`s or `Listenable`s. Equivalent to `ScopedModelDescendant` in `ScopedModel` 
> -  `Provide.value<T>` - Static method used to get a value from a `ProviderNode` using the `BuildContext`. This will not rebuild on change. Similar to manually writing a static `.of()` method for an `InheritedWidget`.
> -  `Provide.stream<T>` - Static method used to get a `Stream` from a `ProviderNode`. Only works if either `T` is listenable, or if the `Provider`comes from a `Stream`.
> -  `Provider<T>` - A class that returns a typed value on demand. Stored in a `ProviderNode` to allow retrieval using `Provide`.
> -  `ProviderNode` - The equivalent of the `ScopedModel` widget. Contains `Providers` which can be found as an `InheritedWidget`.

`Provide` 这个部件主要用于从上层的 `ProvideNode` 中获取值，当变化的时候刷新重建，只能同 `Stream` 和 `Listenable`  一同使用，类似于 `ScopeMode` 中的 `ScopedModelDescendant`。*(这个部件放在需要状态管理的部件的上层，例如有个 Text 需要修改状态，那么就需要在外层提供一个 Provide 部件，通过内部 builder 参数返回 Text 部件)*

`Provide.value` 是个静态方法，用于从 `ProvideNode` 获取值，但是当接收的值改变的时候不会重建。类似于 `InheritedWidget` 的静态方法 `of`。*(这个方法用于获取指定类型的 provide，每个 provide 都需要提供一个数据类，该类 with ChangeNotifier，当数据变化的时候通过 notifyListeners 通知 provide 变化，进行刷新重建)*

`Provide.stream` 是个静态方法，用于从 `ProvideNode` 获取一个 `stream`，仅在 T 可被监听，或者 Provide 来自 stream 的情况下有效。*(这个通常结合 StreamBuilder 使用，StreamBuilder 在上面已经提到，就不多说了)*

`Provider` 按需要的类型返回相关值的类，存储在 `ProviderNode` 中方便 `Provide` 进行检索。*(这个类主要是将我们自己创建的数据类通过 function 等方法转换成 Provider，并在 Providers 中进行注册)*

`ProvideNode` 类似于 `ScopedModel` 的一个部件，包含所有能被查找的 `Providers`。*(这个需要放在顶层，方便下面的容器进行查找 provider，刷新相应的部件，一般放在 MaterialApp 上层)*

*这边再补充一个个人觉得关键的类 Providers，这个类主要用于存储定义的 Provider，主要是在建立 MaterialApp 的时候将需要用到的 Provider 通过 provide 方法添加进去存储起来，然后在 ProvideNode 中注册所有的 provider 方便下层容器获取值，并调用。*

说那么多，还不如直接看个例子直接，代码来了~，首先需要建立一个类似 `BLoC` 中监听数据变化的 `counter_bloc` 类的数据管理类，我们这边定义为 `count_provider` 需要混入 `ChangeNotifier` 类

```dart
class CountProvider with ChangeNotifier {
  int _value = 0; // 存储的数据，也是我们需要管理的状态值

  int get value => _value; // 获取状态值

  void changeValue(int value) {
    _value = value;
    notifyListeners(); // 当状态值发生变化的时候，通过该方法刷新重建部件
  }
}
```

然后需要将定义的类注册到全局的 `Providers` 中

```dart
void main() {
  final providers = Providers()
    // 将我们创建的数据管理类，通过 Provider.function 方法转换成 Provider，
    // 然后添加到 Providers 中
    ..provide(Provider.function((_) => CountProvider()));
  // 在 App 上层，通过包裹一层 ProvideNode，并将我们生成的 Providers 实例
  // 注册到 ProvideNode 中去，这样整个 App 都可以通过 Provide.value 查找相关的 Provider
  // 找到 Provider 后就可以找到我们的数据管理类
  runApp(ProviderNode(child: StreamApp(), providers: providers));
}
```

接着就是替换我们的界面实现了，前面通过 `BLoC` 实现，这里替换成 `Provide` 来实现

```dart
class StreamHome extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
          child: Container(
        alignment: Alignment.center,
        // 通过指定类型，获取特定的 Provide，这个 Provide 会返回我们的数据管理类 provider
        // 通过内部定义的方法，获取到需要展示的值
        child: Provide<CountProvider>(builder: (_, widget, provider) => Text('${provider.value}')),
      )),
      floatingActionButton: FloatingActionButton(
          onPressed: () =>
              // 通过 value 方法获取到我们的数据管理类 provider，
              // 通过调用改变值的方法，修改内部的值，并通知界面刷新重建
              Provide.value<CountProvider>(context).changeValue(
                  Provide.value<CountProvider>(context).value + 1),
          child: Icon(Icons.add))
    );
  }
}
```

*本文代码查看 bloc 包名下的所有文件，需要单独运行 stream_main.dart 文件*

最后运行后还是一样的效果，也摒弃了 `StatefulWidget` 部件和 `SetState` 方法，实现了逻辑和界面分离。但是 `Provide` 最终还是通过 `InheritedWidget` 来实现，当然在资源方面 Google 的大佬们做了一些相关的处理，至于如何处理，这边就不多说了。目前 `provide` 的这个库还存在一点争议的地方，具体查看 [issue#3](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fgoogle%2Fflutter-provide%2Fissues%2F3)，但是目前来看并没有太大的影响。当然你不放心的话，可以使用 `Scoped_model` 或者上面的 `Bloc` 模式，Google 在文档也有相关的注明

> If you must choose a package today, it's safer to go with `package:scoped_model` than with this package.

这篇概念性的比较多，但是等理解了以后，对于以后的开发还是非常有利的。

