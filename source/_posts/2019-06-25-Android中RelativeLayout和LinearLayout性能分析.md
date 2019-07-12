---
title: Android中RelativeLayout和LinearLayout性能分析
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: e61a64df
date: 2018-12-13 22:02:53
---

# Optimizing Layout Hierarchies

It is a common misconception that using the basic layout structures leads to the most efficient layouts. However, each widget and layout you add to your application requires initialization, layout, and drawing. For example, using nested instances of `LinearLayout` can lead to an excessively deep view hierarchy. Furthermore, nesting several instances of`LinearLayout` that use the `layout_weight` parameter can be especially expensive as each child needs to be measured twice. This is particularly important when the layout is inflated repeatedly, such as when used in a `ListView`or `GridView`.

这句话是对减少布局层次的描述。

有一个误解就是，使用基类布局可用产生更有效的布局方式。然而每一个你添加到application里面的控件和布局，都需要初始化，布局，绘制。例如，布局复杂的时候使用LinearLayout，会导致深层次的布局嵌套问题，而进一步来说，使用LinearLayout的weight属性给每个子view去分配位置的时候，会导致每一个子view被绘制两次，而LinearLayout嵌套LinearLayout使用weight会更加严重。如果布局被重复的inflated的话，当我们使用ListView或者GridView的时候就会特别明显的影响绘制效率。

<!--more-->

先看一些现象吧：用eclipse或者Android studio，新建一个Activity自动生成的布局文件都是RelativeLayout，或许你会认为这是IDE的默认设置问题，其实不然，这是由 android-sdk\tools\templates\activities\BlankActivity\root\res\layout\activity_simple.xml.ftl 这个文件事先就定好了的，也就是说这是Google的选择，而非IDE的选择。那SDK为什么会默认给开发者新建一个默认的RelativeLayout布局呢？当然是因为RelativeLayout的性能更优，性能至上嘛。但是我们再看看默认新建的这个RelativeLayout的父容器，也就是当前窗口的顶级View——DecorView，它却是个垂直方向的LinearLayout，上面是标题栏，下面是内容栏。那么问题来了，Google为什么给开发者默认新建了个RelativeLayout，而自己却偷偷用了个LinearLayout，到底谁的性能更高，开发者该怎么选择呢？

### View的一些基本工作原理

先通过几个问题，简单的了解写android中View的工作原理吧。

##### View是什么？

简单来说，View是Android系统在屏幕上的视觉呈现，也就是说你在手机屏幕上看到的东西都是View。

##### View是怎么绘制出来的？

View的绘制流程是从ViewRoot的performTraversals（）方法开始，依次经过measure（），layout（）和draw（）三个过程才最终将一个View绘制出来。

##### View是怎么呈现在界面上的？

Android中的视图都是通过Window来呈现的，不管Activity、Dialog还是Toast它们都有一个Window，然后通过WindowManager来管理View。Window和顶级View——DecorView的通信是依赖ViewRoot完成的。

##### View和ViewGroup什么区别？

不管简单的Button和TextView还是复杂的RelativeLayout和ListView，他们的共同基类都是View。所以说，View是一种界面层控件的抽象，他代表了一个控件。那ViewGroup是什么东西，它可以被翻译成控件组，即一组View。ViewGroup也是继承View，这就意味着View本身可以是单个控件，也可以是多个控件组成的控件组。根据这个理论，Button显然是个View，而RelativeLayout不但是一个View还可以是一个ViewGroup，而ViewGroup内部是可以有子View的，这个子View同样也可能是ViewGroup，以此类推。

### RelativeLayout和LinearLayout性能PK

基于以上原理和大背景，我们要探讨的性能问题，说的简单明了一点就是：当RelativeLayout和LinearLayout分别作为ViewGroup，表达相同布局时绘制在屏幕上时谁更快一点。上面已经简单说了View的绘制，从ViewRoot的performTraversals（）方法开始依次调用perfromMeasure、performLayout和performDraw这三个方法。这三个方法分别完成顶级View的measure、layout和draw三大流程，其中perfromMeasure会调用measure，measure又会调用onMeasure，在onMeasure方法中则会对所有子元素进行measure，这个时候measure流程就从父容器传递到子元素中了，这样就完成了一次measure过程，接着子元素会重复父容器的measure，如此反复就完成了整个View树的遍历。同理，performLayout和performDraw也分别完成perfromMeasure类似的流程。通过这三大流程，分别遍历整棵View树，就实现了Measure，Layout，Draw这一过程，View就绘制出来了。那么我们就分别来追踪下RelativeLayout和LinearLayout这三大流程的执行耗时。
如下图，我们分别用两用种方式简单的实现布局测试下

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190625220831.png)

##### LinearLayout

Measure：0.738ms
 Layout：0.176ms
 draw：7.655ms

##### RelativeLayout

Measure：2.280ms
 Layout：0.153ms
 draw：7.696ms
 从这个数据来看无论使用RelativeLayout还是LinearLayout，layout和draw的过程两者相差无几，考虑到误差的问题，几乎可以认为两者不分伯仲，关键是Measure的过程RelativeLayout却比LinearLayout慢了一大截。

### Measure都干什么了

##### RelativeLayout的onMeasure()方法

```java
View[] views = mSortedHorizontalChildren;
    int count = views.length;

    for (int i = 0; i < count; i++) {
      View child = views[i];
      if (child.getVisibility() != GONE) {
        LayoutParams params = (LayoutParams) child.getLayoutParams();
        int[] rules = params.getRules(layoutDirection);

        applyHorizontalSizeRules(params, myWidth, rules);
        measureChildHorizontal(child, params, myWidth, myHeight);

        if (positionChildHorizontal(child, params, myWidth, isWrapContentWidth)) {
          offsetHorizontalAxis = true;
        }
      }
    }

    views = mSortedVerticalChildren;
    count = views.length;
    final int targetSdkVersion = getContext().getApplicationInfo().targetSdkVersion;

    for (int i = 0; i < count; i++) {
      View child = views[i];
      if (child.getVisibility() != GONE) {
        LayoutParams params = (LayoutParams) child.getLayoutParams();
       
        applyVerticalSizeRules(params, myHeight);
        measureChild(child, params, myWidth, myHeight);
        if (positionChildVertical(child, params, myHeight, isWrapContentHeight)) {
          offsetVerticalAxis = true;
        }

        if (isWrapContentWidth) {
          if (isLayoutRtl()) {
            if (targetSdkVersion < Build.VERSION_CODES.KITKAT) {
              width = Math.max(width, myWidth - params.mLeft);
            } else {
              width = Math.max(width, myWidth - params.mLeft - params.leftMargin);
            }
          } else {
            if (targetSdkVersion < Build.VERSION_CODES.KITKAT) {
              width = Math.max(width, params.mRight);
            } else {
              width = Math.max(width, params.mRight + params.rightMargin);
            }
          }
        }

        if (isWrapContentHeight) {
          if (targetSdkVersion < Build.VERSION_CODES.KITKAT) {
            height = Math.max(height, params.mBottom);
          } else {
            height = Math.max(height, params.mBottom + params.bottomMargin);
          }
        }

        if (child != ignore || verticalGravity) {
          left = Math.min(left, params.mLeft - params.leftMargin);
          top = Math.min(top, params.mTop - params.topMargin);
        }

        if (child != ignore || horizontalGravity) {
          right = Math.max(right, params.mRight + params.rightMargin);
          bottom = Math.max(bottom, params.mBottom + params.bottomMargin);
        }
      }
    }
```

根据源码我们发现RelativeLayout会对子View做两次measure。这是为什么呢？首先RelativeLayout中子View的排列方式是基于彼此的依赖关系，而这个依赖关系可能和布局中View的顺序并不相同，在确定每个子View的位置的时候，就需要先给所有的子View排序一下。又因为RelativeLayout允许A，B 2个子View，横向上B依赖A，纵向上A依赖B。所以需要横向纵向分别进行一次排序测量。

##### LinearLayout的onMeasure()方法

```java
  @Override
  protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
    if (mOrientation == VERTICAL) {
      measureVertical(widthMeasureSpec, heightMeasureSpec);
    } else {
      measureHorizontal(widthMeasureSpec, heightMeasureSpec);
    }
  }
```

与RelativeLayout相比LinearLayout的measure就简单明了的多了，先判断线性规则，然后执行对应方向上的测量。随便看一个吧。

```java
for (int i = 0; i < count; ++i) {
      final View child = getVirtualChildAt(i);

      if (child == null) {
        mTotalLength += measureNullChild(i);
        continue;
      }

      if (child.getVisibility() == View.GONE) {
       i += getChildrenSkipCount(child, i);
       continue;
      }

      if (hasDividerBeforeChildAt(i)) {
        mTotalLength += mDividerHeight;
      }

      LinearLayout.LayoutParams lp = (LinearLayout.LayoutParams) child.getLayoutParams();

      totalWeight += lp.weight;
     
      if (heightMode == MeasureSpec.EXACTLY && lp.height == 0 && lp.weight > 0) {
        // Optimization: don't bother measuring children who are going to use
        // leftover space. These views will get measured again down below if
        // there is any leftover space.
        final int totalLength = mTotalLength;
        mTotalLength = Math.max(totalLength, totalLength + lp.topMargin + lp.bottomMargin);
      } else {
        int oldHeight = Integer.MIN_VALUE;

        if (lp.height == 0 && lp.weight > 0) {
          // heightMode is either UNSPECIFIED or AT_MOST, and this
          // child wanted to stretch to fill available space.
          // Translate that to WRAP_CONTENT so that it does not end up
          // with a height of 0
          oldHeight = 0;
          lp.height = LayoutParams.WRAP_CONTENT;
        }

        // Determine how big this child would like to be. If this or
        // previous children have given a weight, then we allow it to
        // use all available space (and we will shrink things later
        // if needed).
        measureChildBeforeLayout(
           child, i, widthMeasureSpec, 0, heightMeasureSpec,
           totalWeight == 0 ? mTotalLength : 0);

        if (oldHeight != Integer.MIN_VALUE) {
         lp.height = oldHeight;
        }

        final int childHeight = child.getMeasuredHeight();
        final int totalLength = mTotalLength;
        mTotalLength = Math.max(totalLength, totalLength + childHeight + lp.topMargin +
           lp.bottomMargin + getNextLocationOffset(child));

        if (useLargestChild) {
          largestChildHeight = Math.max(childHeight, largestChildHeight);
        }
      }
```

父视图在对子视图进行measure操作的过程中，使用变量mTotalLength保存已经measure过的child所占用的高度，该变量刚开始时是0。在for循环中调用measureChildBeforeLayout（）对每一个child进行测量，该函数实际上仅仅是调用了measureChildWithMargins(),在调用该方法时，使用了两个参数。其中一个是heightMeasureSpec，该参数为LinearLayout本身的measureSpec；另一个参数就是mTotalLength，代表该LinearLayout已经被其子视图所占用的高度。 每次for循环对child测量完毕后，调用child.getMeasuredHeight()获取该子视图最终的高度，并将这个高度添加到mTotalLength中。**在本步骤中，暂时避开了lp.weight>0的子视图，即暂时先不测量这些子视图，因为后面将把父视图剩余的高度按照weight值的大小平均分配给相应的子视图。**源码中使用了一个局部变量totalWeight累计所有子视图的weight值。处理lp.weight>0的情况需要注意，如果变量heightMode是EXACTLY，那么，当其他子视图占满父视图的高度后，weight>0的子视图可能分配不到布局空间，从而不被显示，只有当heightMode是AT_MOST或者UNSPECIFIED时，weight>0的视图才能优先获得布局高度。最后我们的结论是：如果不使用weight属性，LinearLayout会在当前方向上进行一次measure的过程，如果使用weight属性，LinearLayout会避开设置过weight属性的view做第一次measure，完了再对设置过weight属性的view做第二次measure。由此可见，weight属性对性能是有影响的，而且本身有大坑，请注意避让。

##### 小结

从源码中我们似乎能看出，我们先前的测试结果中RelativeLayout不如LinearLayout快的根本原因是RelativeLayout需要对其子View进行两次measure过程。而LinearLayout则只需一次measure过程，所以显然会快于RelativeLayout，但是如果LinearLayout中有weight属性，则也需要进行两次measure，但即便如此，应该仍然会比RelativeLayout的情况好一点。

### RelativeLayout另一个性能问题

对比到这里就结束了嘛？显然没有！我们再看看View的Measure（）方法都干了些什么？

```java
public final void measure(int widthMeasureSpec, int heightMeasureSpec) {
    
    if ((mPrivateFlags & PFLAG_FORCE_LAYOUT) == PFLAG_FORCE_LAYOUT ||
        widthMeasureSpec != mOldWidthMeasureSpec ||
        heightMeasureSpec != mOldHeightMeasureSpec) {
                     ......
      }
       mOldWidthMeasureSpec = widthMeasureSpec;
    mOldHeightMeasureSpec = heightMeasureSpec;

    mMeasureCache.put(key, ((long) mMeasuredWidth) << 32 |
        (long) mMeasuredHeight & 0xffffffffL); // suppress sign extension
  }
```

View的measure方法里对绘制过程做了一个优化，如果我们或者我们的子View没有要求强制刷新，而父View给子View的传入值也没有变化（也就是说子View的位置没变化），就不会做无谓的measure。但是上面已经说了RelativeLayout要做两次measure，而在做横向的测量时，纵向的测量结果尚未完成，只好暂时使用myHeight传入子View系统，假如子View的Height不等于（设置了margin）myHeight的高度，那么measure中上面代码所做得优化将不起作用，这一过程将进一步影响RelativeLayout的绘制性能。而LinearLayout则无这方面的担忧。解决这个问题也很好办，如果可以，尽量使用padding代替margin。

### 结论

1.RelativeLayout会让子View调用2次onMeasure，LinearLayout 在有weight时，也会调用子View2次onMeasure
 2.RelativeLayout的子View如果高度和RelativeLayout不同，则会引发效率问题，当子View很复杂时，这个问题会更加严重。如果可以，尽量使用padding代替margin。
 3.在不影响层级深度的情况下,使用LinearLayout和FrameLayout而不是RelativeLayout。
 最后再思考一下文章开头那个矛盾的问题，为什么Google给开发者默认新建了个RelativeLayout，而自己却在DecorView中用了个LinearLayout。因为DecorView的层级深度是已知而且固定的，上面一个标题栏，下面一个内容栏。采用RelativeLayout并不会降低层级深度，所以此时在根节点上用LinearLayout是效率最高的。而之所以给开发者默认新建了个RelativeLayout是希望开发者能采用尽量少的View层级来表达布局以实现性能最优，因为复杂的View嵌套对性能的影响会更大一些。

