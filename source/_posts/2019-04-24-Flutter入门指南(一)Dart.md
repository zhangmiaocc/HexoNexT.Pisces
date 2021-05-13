---
title: Flutter入门指南(一)Dart
tags:
  - Android
  - Flutter
categories:
  - Android
  - Flutter
abbrlink: 6114f592
date: 2019-04-24 16:22:52
---

### 前言

最近 Flutter 真心火爆到不要不要的，随大流，学一波，在这之前，对于语言的语法还是需要有些必要的了解的，**Dart** 这门语言，说实话对于会 Java 这类面向对象的语言的小伙伴们来说，真的灰常灰常简单，这边我总结了一些 Dart 和 Java 的一些语法差异，当然，只是部分，但是，按照我目前的学习进度来说，了解了这些对于写 Flutter 项目绝对够了。小伙伴也可以自己查看，我这边提供一个自己学习的网址 **Dart** 快速入门：https://www.dartcn.com/

温馨提示：本篇文章没有图，没有图，没有图，可能会造成部分不适，请注意，请注意，请注意，系好安全带，我们要「开车了」......

<!--more-->

### 1、Variables

**Dart** 变量类型可以通过具体的赋值进行推导，例如：var name = 'kuky' 则定义了一个 String 类型对象 name，也可以通过指定具体的类型 String name = 'kuky'，如果没有初始化变量，则默认值为 null，类型为数字的变量默认值同为 null（同 java 不同，java 中 int 默认为 0.）如果需要定义常量，可以通过 final 和 const 进行定义，final 变量只能赋值一次，const 是编译时常量。

### 2、Build-in-types

Dart 内置类型包括 ：

- **Numbers**    包括 int[-2^53 ~ 2^53]， double[64-bit 浮点数]
- **Strings **   Dart 字符串是 UTF-16 编码的字符序列， 可以使用单引号或者双引号来创建字符串。
- 通过 **==** 判断两个字符串是否相同
- 通过三对单引号'''aaa'''或者双引号"""aaa"""可以创建多行字符串对象
- 使用前缀 r 创建 raw string，字符串内不会进行转义，例如：var a = r'haha \n breakLine' 打印 a 对象则会按照输入的输出，不会进行换行
- **Booleans**    Dart 中，只有 true 对象才被认为是 true， 所有其他的值都是 false
- **Lists**    列表，例如：var list = [1, 2, 3, 4]
- 通过 **const** 关键词可以定义一个不可变列表 var list = const [1, 2, 3, 4]
- 参数化定义**var** name = <String>['Jone', 'Jack']
- **Maps**    键值对，例如：var map = {'one': 1, 'two': 2}
- 如果键值对需要添加新的键值对，直接指定即可，map['three'] = 3，若查找的键不存在，返回 null
- 参数化定义 var map = <String, int>{'one': 1, 'two': 2}
- **Runes**    代表字符串的 UTF-32 code points，通常使用 \uXXXX 的方式来表示 Unicode code point， XXXX 是4个 16 进制的数，例如 \u2665 返回心形符号 ()
- **Symbols**    代表 Dart 程序中声明的操作符或者标识符，几乎不使用

### 3、Function

函数方法的可选参数通过在参数列表中用 {} 指定，例如：

```dart
void say(String name, {String word = 'hello'}){    
	print('$name say $word');  
}
// 通过（可选参数名 + :）进行可选参数的赋值
main(){    
	say('zm', word: 'Hello World'); // kuky say Hello World
}
```

word 参数为可选参数，默认值为 hello

### 4、Operators

操作符几乎和别的语言类似，提个比较特殊的赋值操作符 ??= 和 ?.操作符

```dart
var a = 1;
var b ?? = a; // 如果 b 的值是 null 则将 a 赋值给 b，否则保持不变
var c = size?.x; // 如果 size 为 null 则返回 null，否则返回 size.a 的值
```

### 5、Conditional Expressions

Dart 可以通过两个特殊的操作符替换 if(){} else{} 表达式

```dart
/// condition? expr1: expr2 同 java 三目运算符
var a = if(a < 0) -a : a
/// expr1 ?? expr2 
String toString() => msg ?? super.toString() // 如果 expr1 不为 null 则返回 expr1 否则返回 expr
```

### 6、Cascade Notaion(..)

级联操作符 (..) 可以在同一个对象上 连续调用多个函数以及访问成员变量

```dart
class Size{
    double x;
    double y;

  @override
  String toString() {
    return 'Size{x: $x, y: $y}';
  }
}

var size = Size();
/// 通过级联操作符进行赋值，可以更加简洁，!!如果函数返回值为 void 则不能进行级联!!
print(size
      ..x = 10
      ..y = 100
      ..toString()); /// 输出 Size{x: 10.0, y: 100.0}
```

### 7、foreach

通过 foreach 循环遍历一个实现 Iterable 接口的对象

```dart
var items = [1, 2, 3, 4, 5];
var maps = {'a': 1, 'b': 2};

items.where((i) => i > 2).forEach((i) => print(i));  // 3, 4, 5
maps.forEach((key, value) => print('$key => $value')); // a => 1, b => 2
```

### 8、Switch and case

如果需要实现继续到下一个 case 语句中继续执行，则可以 使用 continue 语句跳转到对应的标签处继续执行

```dart
var command = 'Close';
switch (command.toLowerCase()) {
  case 'close':
    print('close');
    continue open;
        
  open: // 这是个标签
  case 'open':
    print('open');
    break;
}
```

### 9、Assert

如果条件表达式结果不满足需要，则可以使用 assert 语句俩打断代码的执行，例如：assert(a == 1);

### 10、Exceptions

所有的 Dart 异常是非检查异常。捕捉 exceptions 的时候可以通过 on 指定 exceptions 类型，再使用 catch 捕获

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
} on Exception catch (e) {
  print('Unknown exception: $e');
} catch (e, s) { // 函数 catch 可以带有一个或两个参数，第一个参数为抛出的异常对象，第二个为堆栈信息
  print('Something really unknown: $e');
  print('Stack trace:\n $s');
  rethrow; // 通过 rethrow 可以将异常重新抛出
}
```

### 11、Classes

Dart 中的类都是单继承，但是同时支持 mixin 的继承机制（除 Object 类，每个类都只有一个超类），所有的类都继承于 Object，通过调用 runtimeType 判断实例的类型。每个实例变量都会自动生成一个 getter 方法（隐含的）， Non-final 实例变量还会自动生成一个 setter 方法。

**Constructors**

Dart 的构造函数同 Java 类似

```dart
class Size {
  num x, y;

  Size(num nx, num y){
    x = nx;
    this.y = y; // this 关键字只有当名字冲突时候使用，否则 Dart 推荐省略 this
  }
    
  Size(this.x, this.y); // Dart 通过语法糖省略了构造函数的赋值过程，效果同上
}
```

如果没有定义构造函数，则会有个默认构造函数。默认构造函数没有参数，并且会调用超类的 没有参数的构造函数。子类不会继承超类的构造函数，子类如果没有定义构造函数，则只有一个默认构造函数。

Dart 通过命名构造函数为类创建多个构造函数，同时指明意图

```dart
class Size {
  num x, y;

  Size(this.x, this.y);

  Size.fromJson(Map json){
    this.x = json['x'];
    this.y = json['y'];
  } // 因为构造函数不能继承，如果希望子类也有超类一样的命名构造函数，必须在子类中实现该构造函数
  
  // 构造函数体执行之前除了可以调用超类构造函数之外，还可以初始化实例参数
  // 初始化列表非常适合用来设置 final 变量的值
  Size.fromJsonInit(Map json)
      : this.x = json['x'],
        this.y = json['y'];
}
```

常量构造函数（如果类需要提供一个状态不变的对象，通过 const 构造函数实现）

```dart
class ConstPoint {
  final num x;
  final num y;

  const ConstPoint(this.x, this.y);
}
```

工厂方法构造函数（如果一个类不需要每次都提供一个新的对象，通过 factory 构造函数实现）

```dart
class HttpCore {
  HttpCore._internal();

  factory HttpCore() {
    if (_instance == null) _instance = HttpCore._internal();
    return _instance;
  }

  static HttpCore _instance;

  static HttpCore get instance => HttpCore();
  
  void _request(){
      //...
  }
}
```

每个类都隐式的定义了一个包含所有实例成员的接口， 并且这个类实现了这个接口，通过抽象类实现类似 Java 接口的功能。

```dart
abstract class Callback {
  void print(String msg);
}

class A implements Callback{
  @override
  void print(String msg) {
    print(msg);
  }
}
```

Mixins  Dart | 什么是Mixin：https://www.jianshu.com/p/a578bd2c42aa

### 12、Asynchrony support

**Future**

```dart
loopIntegers() {
  // 通过 then 进行获取到 Future 对象后的操作
  getListDelay().then((ints) => ints.forEach((i) => print(i)));
}

// 生成一个 Future 对象
Future<List<int>> getListDelay() {
  return Future.delayed(Duration(seconds: 2), () => List.generate(10, (delta) => delta));
}
```

通过 async await 简化 Future 操作

```dart
runUsingFuture() {
  //...
  findEntrypoint().then((entrypoint) {
    return runExecutable(entrypoint, args);
  }).then(flushThenExit);
}

// 简化了 then
runUsingAsyncAwait() async {
  //...
  var entrypoint = await findEntrypoint();
  var exitCode = await runExecutable(entrypoint, args);
  await flushThenExit(exitCode);
}
```

有时候要求调用很多异步方法，并且等待 所有方法完成后再继续执行，通过使用 Future.wait() 进行管理

```dart
Future deleteDone = deleteLotsOfFiles();
Future copyDone = copyLotsOfFiles();
Future checksumDone = checksumLotsOfOtherFiles();

Future.wait([deleteDone, copyDone, checksumDone])
    .then((List values) {
      print('Done with all the long steps');
    });
```

Stream Dart|什么是 Stream：https://www.jianshu.com/p/a5d7758938ef

大概了解了 Dart 的语法，下节就开始写 Flutter 啦~，环境的安装具体查看官网，很详细 Flutter 环境安装 记得一定要**配置镜像，配置镜像，配置镜像**

https://flutterchina.club/get-started/install/