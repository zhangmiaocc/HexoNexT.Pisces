---
title: Flutter_BottomNavigationBar切换页面防止页面重绘
tags:
  - Android
  - Flutter Tips
categories:
  - Android
  - Flutter Tips
abbrlink: 88ab6b09
date: 2020-03-10 15:07:10
---

开始尝试用flutter开发，flutter版本1.0，写类似微信底部tab切换界面时发现界面老被重置，网上找了一圈说保持状态需要子页面mixin AutomaticKeepAliveClientMixin，然后重写

```dart
 @override
  bool get wantKeepAlive => true; 
```

但发现需要配合其他组件，不是随便mixin就有用的，尝试几种写法总结BottomNavigationBar+List<Widget>+AutomaticKeepAliveClientMixin是没有用的

1. 首先尝试BottomNavigationBar+List<Widget>实现的页面切换保持状态，一般刚开始学都会这么写：

```dart
void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) => MaterialApp(
        title: "demo",
        home: MainPage(),
      );
}

class MainPage extends StatefulWidget {
  @override
  State<StatefulWidget> createState() => MainPageState();
}

class MainPageState extends State<MainPage> {
  int _currentIndex;
  List<Widget> _pages;

  @override
  void initState() {
    super.initState();
    _currentIndex = 0;
    _pages = List()..add(FirstPage("第一页"))..add(SecondPage("第二页"))..add(ThirdPage("第三页"));
  }

  @override
  Widget build(BuildContext context) => Scaffold(
        body: _pages[_currentIndex],
        bottomNavigationBar: BottomNavigationBar(
          items: getItems(),
          currentIndex: _currentIndex,
          onTap: onTap,
        ),
      );

  List<BottomNavigationBarItem> getItems() {
    return [
      BottomNavigationBarItem(icon: Icon(Icons.home), title: Text("Home")),
      BottomNavigationBarItem(icon: Icon(Icons.adb), title: Text("Adb")),
      BottomNavigationBarItem(icon: Icon(Icons.person), title: Text("Person"))
    ];
  }

  void onTap(int index) {
    setState(() {
      _currentIndex = index;
    });
  }
}
```

<!--more-->

子页面代码,三个界面一样：

```dart
class FirstPage extends StatefulWidget {
  String _title;

  FirstPage(this._title);

  @override
  State<StatefulWidget> createState() => FirstPageState();
}

class FirstPageState extends State<FirstPage> with AutomaticKeepAliveClientMixin{
  int _count = 0;

  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget._title),
      ),
      body: Center(
        child: Text(widget._title + ":点一下加1：$_count"),
      ),
      floatingActionButton: FloatingActionButton(
          heroTag: widget._title, child: Icon(Icons.add), onPressed: add),
    );
  }

  void add() {
    setState(() {
      _count++;
    });
  }
}
```

#### 结果无法实现保持页面

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/1794647-b3ba9af97689cfee.gif)

2.第二种BottomNavigationBar+PageView，与android的ViewPager类似,界面小改动一下，添加一个按钮，点击跳转到一个新的界面

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/1794647-1fe68bcfd3625422.png)

代码如下：

```dart
void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) => MaterialApp(
        title: "demo",
        home: MainPage(),
      );
}

class MainPage extends StatefulWidget {
  @override
  State<StatefulWidget> createState() => MainPageState();
}

class MainPageState extends State<MainPage> {
  int _currentIndex;
  List<Widget> _pages;
  PageController _controller;

  @override
  void initState() {
    super.initState();
    _currentIndex = 0;
    _pages = List() ..add(FirstPage("第一页"))  ..add(SecondPage("第二页")) ..add(ThirdPage("第三页"));
    _controller = PageController(initialPage: 0);
  }

  @override
  void dispose() {
    super.dispose();
    _controller.dispose();
  }

  @override
  Widget build(BuildContext context) => Scaffold(
        body: PageView.builder(
            physics: NeverScrollableScrollPhysics(),//viewPage禁止左右滑动
            onPageChanged: _pageChange,
            controller: _controller,
            itemCount: _pages.length,
            itemBuilder: (context, index) => _pages[index]),
        bottomNavigationBar: BottomNavigationBar(
          items: getItems(),
          currentIndex: _currentIndex,
          onTap: onTap,
        ),
      );

  List<BottomNavigationBarItem> getItems() {
    return [
      BottomNavigationBarItem(icon: Icon(Icons.home), title: Text("Home")),
      BottomNavigationBarItem(icon: Icon(Icons.adb), title: Text("Adb")),
      BottomNavigationBarItem(icon: Icon(Icons.person), title: Text("Person"))
    ];
  }

  void onTap(int index) {
    _controller.jumpToPage(index);
  }

  void _pageChange(int index) {
     if (index != _currentIndex) {
      setState(() {
        _currentIndex = index;
       });
     }
  }
}
```

子界面：

```dart
class FirstPage extends StatefulWidget {
  String _title;

  FirstPage(this._title);

  @override
  State<StatefulWidget> createState() => FirstPageState();
}

class FirstPageState extends State<FirstPage>
    with AutomaticKeepAliveClientMixin {
  int _count = 0;

  @override
  bool get wantKeepAlive => true;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget._title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(widget._title + ":点一下加1：$_count"),
            MaterialButton(
                child: Text("跳转"),
                color: Colors.pink,
                onPressed: () => Navigator.push(context,
                    MaterialPageRoute(builder: (context) => NewPage())))
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
          heroTag: widget._title, child: Icon(Icons.add), onPressed: add),
    );
  }

  void add() {
    setState(() {
      _count++;
    });
  }
}
```

需要跳转的一个界面：

```dart
class NewPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) => Scaffold(
        appBar: AppBar(
          title: Text("新的界面"),
        ),
        body: Center(
          child: Text("我是一个新的界面"),
        ),
      );
}
```

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/1794647-daaad3aaa7b2a1ed.gif)

猛一看效果出来了，左右切换界面没有问题，结果跳转新界面时又出现新问题，当第一页跳转新的界面再返回，再切第二、三页发现重置了，再切回第一页发现页被重置了。

发生这种情况需要在重写Widget build(BuildContext context)时调用下父类build(context)方法，局部代码：

```dart
@override
  Widget build(BuildContext context) {
    //在这边加上super.build(context);
    super.build(context);
    return Scaffold(
      appBar: AppBar(
        title: Text(widget._title),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(widget._title + ":点一下加1：$_count"),
            MaterialButton(
                child: Text("跳转"),
                color: Colors.pink,
                onPressed: () => Navigator.push(context,
                    MaterialPageRoute(builder: (context) => NewPage())))
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
          heroTag: widget._title, child: Icon(Icons.add), onPressed: add),
    );
  }
```

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/1794647-28e4802ac86eec75.gif)

这种布局样式网上还有一种用的比较多的是BottomNavigationBar+IndexedStack( )，这边就不贴出来了

- 经过长期测试BottomNavigationBar+TabBarView方案行不通，后期会遇到其他问题，目前最好用还是viewpage和IndexedStack。

最后像这种多页面使用FloatingActionButton，用它跳转新界面是一定要设置heroTag，要不然跳转会黑屏报错

