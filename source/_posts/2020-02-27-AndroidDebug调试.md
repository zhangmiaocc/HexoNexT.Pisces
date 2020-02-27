---
title: 一文读懂 Android Debug 调试
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: a9889f48
date: 2020-02-27 13:47:43
---

## 一、打上断点，启动debug模式

首先在我们需要打断点的代码行数上稍微偏右，点击鼠标左键，如图：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227134850.png)

 点击小爬虫按钮，启动debug模式。 

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227134905.png)

 运行成功后如下。可以看到红色框内，从下往上的顺序运行方法，一直阻塞在我们打断点的方法里；绿色款内，则是展示目前阻塞方法内变量和参数的数值。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227134915.png)

<!--more-->

## 二、接下来，我们一起分解debug的每个按钮操作

### 2.1、Step Over（F8）

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227134934.png)



这个按钮的意思：程序向下一步执行，但是要注意，这个按钮不会主动进入方法体内，而是会直接运行完整个方法后直接运行下一步。 
例如：我当前运行的debug，如果一直点击这个按钮的话，他会在onCreate()方法内，执行完add(),再执行完sub(),然后直接结束，并不会进add和sub方法内去打印。 


### 2.2、Step Into（F7）

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135110.png)



 这个按钮的意思：程序向下一步执行，和Step Over的区别是如果该行有方法调用且为自定义方法，则运行进入自定义方法（不会进入官方类库的方法）。 

例如：我当前运行的debug，如果你想进入到add()方法里，那么点击Step Into。假如我的add()方法里，还调用了其他的自定义方法，如果此时你都想进入各个方法查看则继续用Step Into，假如你只想停留在add()方法里，其他方法只需要得到个返回值的话，这个时候应该用Step Over; 



### 2.3、Force Step Into

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135124.png)

 从字面意思上你也能看得出来：可以进入包括官方类库在内的任何方法。一般我认为这个比较适合研究源码。 



### 2.4、Step out

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135138.png)

 假如此时调试在add()方法里，如果我们觉得add()方法没有问题，想跳出这个方法继续debug其他断点时，那么点击Step out，跳出该方法。 



### 2.5、Run to Cursor

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135156.png)

 从字面意思上看，他是移动到下个断点的意思。经过测试如下:



> 1、假如在我们当前运行的debug，如果还在onCreate()方法内，当前断点在add()方法时，点击Run to Cursor，断点确实会移动到下个断点停留在sub()方法。

> 2、如果此时我们已经进入到add()方法体内，点击Run to Cursor,我们会看到，他只是运行完一次for循环后，继续堵塞。如下图：



![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135207.png)

 所以我总结为，Run to Cursor是在当前方法体内，运行到下一个断点。（如果有误，请大佬及时纠正）； 



那么此时，如果我们已经在add()方法内，就是想直接运行到下个断点sub()上，怎么操作呢？点击Resume Program 

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135219.png)

 这个按钮不会管你在不在方法内，直接回到下一步断点上。 



## 三、Debug进阶用法。

### 3.1、Watches

如果我们在debug的时候，可能会出现很多变量，而我们就想观察那么几个变量。我们可以把他加到watches里。比如我add()方法里的变量i，

> 方法1：在我们观察的Variables里，找到那个变量右键，选择Add to Watches

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135231.png)



> 方法2：在我们的Watches界面，点击+号，在输入框内，输入i，进行搜索，也能添加到Watches，方便我们debug调试



![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135254.png)



### 3.2、Set Value

比如在我们的add()方法里，有一个for循环，正常调试是每次都会从i=0的时候进行调试，如果我们想直接从i=5的时候进行调试，那么我们可以在Variables界面，找到那个变量值，右键选择Set Value后，输入我们5，就能跳过前几次循环。 

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135308.png)



### 3.3、查看所有断点

开发中你打了很多断点忘记取消的情况下，你可以点击View Breakpoints查看所有断点 

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135319.png)

 打开如下界面：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135330.png)

### 3.4、停止debug调试



![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135344.png)



### 3.5、已经运行的程序，避免重新运行程序的情况下，怎么添加debug调试。

这里多说几句，因为是在已经运行的程序上，添加debug调试，那么比如进入一个页面，onCreate()方法里的代码，都已经全部执行完了，比如我们点击一个按钮，需要运行的方法，在这个方法里我们才能添加debug调试。比如首先是一个正常运行的程序，我们给点击事件里加断点。

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135357.png)



然后，点击Attach Debugger to Android Process

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135433.png)



弹出如下页面，点击OK就行了，点击按钮就能进行debug调试了

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20200227135420.png)

