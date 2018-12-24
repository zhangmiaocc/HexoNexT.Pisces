---
title: View获取width和height的四种方法
date: 2018-12-21 16:55:12
tags:
- Android 
- Android Tips
categories:
- Android
- Android Tips
---

> 很经常当我们动态创建某些View时，需要通过获取他们的width和height来确定别的view的布局，但是在onCreate()获取view的width和height会得到0.view.getWidth()和view.getHeight()为0的根本原因是控件还没有完成绘制，你必须等待系统将绘制完View时，才能获得。这种情况当你需要使用动态布局（使用wrap_content或match_parent）就会出现。一般来讲在Activity.onCreate(...)、onResume()方法中都没有办法获取到View的实际宽高。所以，我们必须用一种变通的方法，等到View绘制完成后去获取width和Height。下面有一些可行的解决方案。

## 1、监听Draw/Layout事件：ViewTreeObserver

ViewTreeObserver监听很多不同的界面绘制事件。一般来说[OnGlobalLayoutListener](http://developer.android.com/reference/android/view/ViewTreeObserver.OnGlobalLayoutListener.html)就是可以让我们获得到view的width和height的地方.下面onGlobalLayout内的代码会在View完成Layout过程后调用。

```java
view.getViewTreeObserver().addOnGlobalLayoutListener(new ViewTreeObserver.OnGlobalLayoutListener() {
        @Override
        public void onGlobalLayout() {
            mScrollView.post(new Runnable() {
                public void run() {
                    view.getHeight(); //height is ready
                }
            });
        }
});
```

但是要注意这个方法在每次有些view的Layout发生变化的时候被调用（比如某个View被设置为Invisible）,所以在得到你想要的宽高后，记得移除onGlobleLayoutListener：

<!--more-->

> 在 SDK Lvl < 16时使用
> public void removeGlobalOnLayoutListener (ViewTreeObserver.OnGlobalLayoutListener victim)
>
> 在 SDK Lvl >= 16时使用
> public void removeOnGlobalLayoutListener (ViewTreeObserver.OnGlobalLayoutListener victim)

<!--more-->

## 2、将一个runnable添加到Layout队列中：View.post()

这个解决方案是我最喜欢的，但是几乎没人知道有这个方法。简单地说，只要用View.post()一个runnable就可以了。runnable对象中的方法会在View的measure、layout等事件后触发，具体的参考[Romain Guy](http://stackoverflow.com/users/298575/romain-guy)：

UI事件队列会按顺序处理事件。在setContentView()被调用后，事件队列中会包含一个要求重新layout的message，所以任何你post到队列中的东西都会在Layout发生变化后执行。

```java
final View view=//smth;
...
view.post(new Runnable() {
            @Override
            public void run() {
                view.getHeight(); //height is ready
            }
        });
```

这个方法比ViewTreeObserver好：
1、你的代码只会执行一次，而且你不用在在每次执行后将Observer禁用，省心多了。
2、语法很简单参考：
<http://stackoverflow.com/a/3602144/774398>
<http://stackoverflow.com/a/3948036/774398>



## 3、重写View的onLayout方法

这个方法只在某些场景中实用，比如当你所要执行的东西应该作为他的内在逻辑被内聚、模块化在view中，否者这个解决方案就显得十分冗长和笨重。

```java
view = new View(this) {
    @Override
    protected void onLayout(boolean changed, int l, int t, int r, int b) {
        super.onLayout(changed, l, t, r, b);
        view.getHeight(); //height is ready
    }
};
```

需要注意的是onLayout方法会调用很多次，所以要考虑好在这个方法中要做什么，或者在第一次执行后禁用掉你的代码。



## 4、获取固定宽高

如果你要获取的view的width和height是固定的，那么你可以直接使用：

```java
View.getMeasureWidth()
View.getMeasureHeight()
```

但是要注意，这两个方法所获取的width和height可能跟实际draw后的不一样。[官方文档解释了不同的原因](http://developer.android.com/guide/topics/ui/declaring-layout.html#SizePaddingMargins)：

View的大小由width和height决定。一个View实际上同时有两种width和height值。

> 第一种是measure width和measure height。他们定义了view想要在父View中占用多少width和height（详情见Layout）。measured height和width可以通过getMeasuredWidth() 和 getMeasuredHeight()获得。
>
> 第二种是width和height，有时候也叫做drawing width和drawing height。这些值定义了view在屏幕上绘制和Layout完成后的实际大小。这些值有可能跟measure width和height不同。width和height可以通过getWidth()和getHeight获得。

 

**参考链接**

https://stackoverflow.com/questions/3591784/getwidth-and-getheight-of-view-returns-0/24035591#24035591





