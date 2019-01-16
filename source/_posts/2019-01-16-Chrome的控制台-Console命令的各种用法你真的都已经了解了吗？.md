---
title: 'Chrome的控制台,Console命令的各种用法你真的都已经了解了吗？'
tags:
  - Console
  - 前端
categories:
  - 前端
  - tips
abbrlink: 8910c6e9
date: 2019-01-16 10:38:50
---

## 前言

作为前端工程师，我们每天都离不开用控制台调试代码，`console.log`也成了我们最常用的命令，那除了用`console.log`，还有许多console的方法可以使用，熟练掌握它们，可以让我们在控制台看到的信息更美观准确，也会大大提高我们的开发效率哦，下面就跟小肆一起来看看吧.

<!--more-->

### Chrome的控制台

大部分常用浏览器都有各自的控制台，不过小肆用着最习惯的还是Chrome的控制台，打开chrome，win系统按F12，mac系统按command+option+J就可以呼出控制台了，切换到Console标签就能看到如下信息：

![](https://ws3.sinaimg.cn/large/006tNc79ly1fz88a3ltgkj30u00h4djj.jpg)

我们可以看到，baidu还在控制台给我们留了个小彩蛋，我想这个彩蛋也是为我们程序员同学留的吧。让我们先学第一个命令清除控制台来开始吧。



## 清除控制台

在chorme下清除控制台的方法有很多：

- 输入`console.clear()`命令或`clear()`命令
- 使用快捷键 `Control + J` 或 `Command + K`
- 点击控制台左上角第二个图标 🚫

## 显示信息的命令

```javascript
console.log('技术放肆聊')     // 输出普通信息
console.info('技术放肆聊')    // 输出提示信息
console.warn('技术放肆聊')    // 输出警告信息
console.error('技术放肆聊')   // 输出错误信息
console.debug('技术放肆聊')   // 输出调试信息
```

`console.log`、`console.info`、`console.debug`这三个命令可以理解为一个，我们只需要用`console.log`就行，并且chrome还不支持`console.debug`命令。

`console.warn`命令输出警告信息，信息前带有黄色警告符号。
`console.error`输出错误信息，信息前带有红色错误符号，表示出错，同时会显示错误发生的堆栈。
上段代码在chrome控制台输出效果如下:

![](https://ws4.sinaimg.cn/large/006tNc79ly1fz88bksxt9j30u00e9ju0.jpg)

在safari输出效果如下:

![](https://ws1.sinaimg.cn/large/006tNc79ly1fz88byuqgsj30u00lzn00.jpg)

### 占位符

console上述的命令支持printf的占位符格式，支持的占位符有：字符（%s）、整数（%d或%i）、浮点数（%f）和对象（%o）:

| 占位符   | 作用                          |
| -------- | ----------------------------- |
| %s       | 字符串                        |
| %d or %i | 整数                          |
| %f       | 浮点数                        |
| %o       | 可展开的DOM                   |
| %O       | 列出DOM的属性                 |
| %c       | 根据提供的css样式格式化字符串 |

```javascript
//字符(%s)
console.log("%s","技术放肆聊");
//整数(%d或%i)
console.log("%d年%d月%d日",2019,1,6); 
//浮点数(%f)
console.log("PI=%f",3.1415926);
```

显示效果如下：

![图4](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)![](https://ws2.sinaimg.cn/large/006tNc79ly1fz88chd4skj30u00bqgnj.jpg)

%o、%O 都是用来输出 Object 对象的，对普通的 Object 对象，两者没区别，但是打印dom节点时就不一样了：

![](https://ws2.sinaimg.cn/large/006tNc79ly1fz88cq3p2ij30u00yuq8s.jpg)

%c 占位符是最常用的。使用 %c 占位符时，对应的后面的参数必须是 CSS 语句，用来对输出内容进行 CSS 渲染。常见的输出方式有两种：文字样式、图片输出。

![](https://ws1.sinaimg.cn/large/006tNc79ly1fz88d2bedjj30u00ebwh3.jpg)

## 信息分组

`console.group()`用于将显示的信息分组，可以把信息进行折叠和展开。
`console.groupEnd()`结束内联分组

![](https://ws2.sinaimg.cn/large/006tNc79ly1fz88dc5tlqj30u00hymzs.jpg)

## 将对象以树状结构展现

`console.dir()`可以显示一个对象所有的属性和方法.

![图8](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)![](https://ws4.sinaimg.cn/large/006tNc79ly1fz88dmdyo1j30u00mftbg.jpg)

## 显示某个节点的内容

`console.dirxml()`用来显示网页的某个节点(node)所包含的html/xml代码

![图9](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)![](https://ws2.sinaimg.cn/large/006tNc79ly1fz88dtj362j30u00adq4k.jpg)

## 判断变量是否是真

`console.assert()`用来判断一个表达式或变量是否为真，
此方法接受两个参数，第一个参数是表达式，第二个参数是字符串。只有当第一个参数为false，才会输出第二个参数，否则不会有任何结果。

![图10](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)![](https://ws4.sinaimg.cn/large/006tNc79ly1fz88dyzrcwj30u00ad0ue.jpg)

## 计时功能

`console.time()`和`console.timeEnd()`，用来显示代码的运行时间

```java
console.time("控制台计时器");
for(var i = 0; i < 10000; i++){
    for(var j = 0; j < 10000; j++){}       
}
console.timeEnd("控制台计时器");
```

![](https://ws3.sinaimg.cn/large/006tNc79ly1fz88erog6vj30u00bjdhl.jpg)

## 性能分析performance profile

`console.profile()`和`console.proileEnd()`用来分析程序各个部分的运行时间，找出瓶颈所在。

```js
 function All(){
     for(var i = 0; i < 10; i++){
         funcA(100);
     }
     funcB(1000);
 }
 function funcA(count){
     for(var i = 0; i < count; i++){};
 }
function funcB(count){
    for(var i = 0; i < count; i++){};
}
console.profile("性能分析器");
All();
console.profileEnd();
```

详细的信息在chrome控制台里的"profile"选项里查看

## console.count()统计代码被执行的次数

```javascript
function myFunction(){
    console.count("myFunction 被执行的次数");
}
myFunction();       //myFunction 被执行的次数: 1
myFunction();       //myFunction 被执行的次数: 2
myFunction();       //myFunction 被执行的次数: 3
```

## console.table表格显示方法

![](https://ws4.sinaimg.cn/large/006tNc79ly1fz88gtaf7hj30u00gedhh.jpg)

## 总结

合理的利用console的各种方法，会使我们的调试过程更加愉悦，今天的分享就到这里了，记得右下角点好看呦！

转自：https://mp.weixin.qq.com/s/8jcqYIPZGQVsLo3fou41Zw