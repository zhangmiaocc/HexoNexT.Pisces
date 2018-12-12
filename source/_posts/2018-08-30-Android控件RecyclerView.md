---
title: Android 控件 RecyclerView
date: 2018-08-30 15:34:12
tags:
- Android 
- RecyclerView
categories:
- Android 
- View
---

转自：https://www.jianshu.com/p/4f9591291365

## 概述

### RecyclerView是什么

从Android 5.0开始，谷歌公司推出了一个用于大量数据展示的新控件RecylerView，可以用来代替传统的ListView，更加强大和灵活。RecyclerView的官方定义如下：

> A flexible view for providing a limited window into a large data set.

从定义可以看出，flexible（可扩展性）是RecyclerView的特点。

RecyclerView是**support-v7包**中的**新组件**，是一个强大的滑动组件，与经典的ListView相比，同样拥有item回收复用的功能，这一点从它的名字Recyclerview即回收view也可以看出。

### <!--more-->

### RecyclerView的优点

RecyclerView并不会完全替代ListView（这点从ListView没有被标记为@Deprecated可以看出），两者的使用场景不一样。但是RecyclerView的出现会让很多开源项目被废弃，例如横向滚动的ListView, 横向滚动的GridView, 瀑布流控件，因为RecyclerView能够实现所有这些功能。

比如：有一个需求是**屏幕竖着**的时候的显示形式是**ListView**，屏幕**横着**的时候的显示形式是2列的**GridView**，此时如果用**RecyclerView**，则通过设置LayoutManager**一行代码实现替换**。

RecylerView相对于ListView的优点罗列如下：

- RecyclerView**封装了viewholder**的回收复用，也就是说RecyclerView**标准化了ViewHolder**，**编写Adapter面向**的是**ViewHolder**而不再是View了，复用的逻辑被封装了，写起来更加简单。
   直接省去了listview中convertView.setTag(holder)和convertView.getTag()这些繁琐的步骤。
- 提供了一种**插拔式的体验**，**高度的解耦**，异常的灵活，针对一个Item的显示RecyclerView专门抽取出了**相应的类**，来**控制Item的显示**，使其的扩展性非常强。
- 设置**布局管理器**以控制**Item**的**布局方式**，**横向**、**竖向**以及**瀑布流**方式
   例如：你想控制横向或者纵向滑动列表效果可以通过**LinearLayoutManager**这个类来进行控制(与GridView效果对应的是**GridLayoutManager**,与**瀑布流**对应的还**StaggeredGridLayoutManager**等)。也就是说RecyclerView不再拘泥于ListView的线性展示方式，它也可以实现GridView的效果等多种效果。
- 可设置**Item的间隔样式**（可绘制）
   通过**继承RecyclerView的ItemDecoration**这个类，然后针对自己的业务需求去书写代码。
- 可以控制**Item增删的动画**，可以通过**ItemAnimator**这个类进行控制，当然针对增删的动画，RecyclerView有其自己默认的实现。

但是关于Item的点击和长按事件，需要用户自己去实现。

### 基本使用

```
recyclerView = (RecyclerView) findViewById(R.id.recyclerView);  
LinearLayoutManager layoutManager = new LinearLayoutManager(this );  
//设置布局管理器  
recyclerView.setLayoutManager(layoutManager);  
//设置为垂直布局，这也是默认的  
layoutManager.setOrientation(OrientationHelper. VERTICAL);  
//设置Adapter  
recyclerView.setAdapter(recycleAdapter);  
 //设置分隔线  
recyclerView.addItemDecoration( new DividerGridItemDecoration(this ));  
//设置增加或删除条目的动画  
recyclerView.setItemAnimator( new DefaultItemAnimator());  
```

在使用RecyclerView时候，必须指定一个适配器Adapter和一个布局管理器LayoutManager。适配器继承**RecyclerView.Adapter**类，具体实现类似ListView的适配器，取决于数据信息以及展示的UI。布局管理器用于确定RecyclerView中Item的展示方式以及决定何时复用已经不可见的Item，避免重复创建以及执行高成本的`findViewById()`方法。

可以看见RecyclerView相比ListView会多出许多操作，这也是RecyclerView灵活的地方，它将许多动能暴露出来，用户可以选择性的自定义属性以满足需求。

## 基本使用

### 引用

在build.gradle文件中**引入该类**。

```
    compile 'com.android.support:recyclerview-v7:23.4.0'
```

### 布局

Activity布局文件activity_rv.xml
 ...

Item的布局文件item_1.xml
 ...

### 创建适配器

标准实现步骤如下：
 ① **创建Adapter**：创建一个**继承RecyclerView.Adapter<VH>**的Adapter类（VH是ViewHolder的类名）
 ② **创建ViewHolder**：在Adapter中创建一个**继承RecyclerView.ViewHolder**的静态内部类，记为VH。ViewHolder的实现和ListView的ViewHolder实现几乎一样。
 ③ 在**Adapter中实现3个方法**：

-  **onCreateViewHolder()**
   这个方法主要**生成**为**每个Item inflater出一个View**，但是该方法**返回**的是一个**ViewHolder**。该方法把View直接封装在ViewHolder中，然后我们面向的是ViewHolder这个实例，当然这个ViewHolder需要我们自己去编写。

需要**注意**的是在`onCreateViewHolder()`中，映射**Layout必须为**

```
View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_1, parent, false);
```

而不能是：

```
View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_1, null);
```

-  **onBindViewHolder()**
   这个方法主要用于**适配渲染数据到View**中。方法**提供**给你了一**viewHolder**而不是原来的convertView。
-  **getItemCount()**
   这个方法就**类似**于BaseAdapter的**getCount**方法了，即总共有多少个条目。

可以看出，RecyclerView将ListView中`getView()`的功能拆分成了`onCreateViewHolder()`和`onBindViewHolder()`。

基本的Adapter实现如下：

```
// ① 创建Adapter
public class NormalAdapter extends RecyclerView.Adapter<NormalAdapter.VH>{
    //② 创建ViewHolder
    public static class VH extends RecyclerView.ViewHolder{
        public final TextView title;
        public VH(View v) {
            super(v);
            title = (TextView) v.findViewById(R.id.title);
        }
    }
    
    private List<String> mDatas;
    public NormalAdapter(List<String> data) {
        this.mDatas = data;
    }

    //③ 在Adapter中实现3个方法
    @Override
    public void onBindViewHolder(VH holder, int position) {
        holder.title.setText(mDatas.get(position));
        holder.itemView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //item 点击事件
            }
        });
    }

    @Override
    public int getItemCount() {
        return mDatas.size();
    }

    @Override
    public VH onCreateViewHolder(ViewGroup parent, int viewType) {
        //LayoutInflater.from指定写法
        View v = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_1, parent, false);
        return new VH(v);
    }
}
```

### 设置RecyclerView

创建完Adapter，接着对RecyclerView进行设置，一般来说，需要为RecyclerView进行**四大设置**，也就是后文说的四大组成：

- Layout Manager(必选)
- Adapter(必选)
- Item Decoration(可选，默认为空)
- Item Animator(可选，默认为DefaultItemAnimator)

如果要实现ListView的效果，只需要设置Adapter和Layout Manager，如下：

```
List<String> data = initData();
RecyclerView rv = (RecyclerView) findViewById(R.id.rv);
rv.setLayoutManager(new LinearLayoutManager(this));
rv.setAdapter(new NormalAdapter(data));
```

## 四大组成

RecyclerView的四大组成是：

- Layout Manager：Item的布局。
- Adapter：为Item提供数据。
- Item Decoration：Item之间的Divider。
- Item Animator：添加、删除Item动画。

### Layout Manager布局管理器

在最开始就提到，RecyclerView 能够支持各种各样的布局效果，这是 ListView 所不具有的功能，那么这个功能如何实现的呢？其核心关键在于 [RecyclerView.LayoutManager](https://link.jianshu.com?t=https%3A%2F%2Fdeveloper.android.com%2Freference%2Fandroid%2Fsupport%2Fv7%2Fwidget%2FRecyclerView.LayoutManager.html) 类中。从前面的基础使用可以看到，RecyclerView 在使用过程中要比 ListView 多一个 setLayoutManager 步骤，这个 LayoutManager 就是用于控制我们 RecyclerView 最终的展示效果的。

LayoutManager负责RecyclerView的布局，其中包含了Item View的获取与回收。

RecyclerView提供了**三种布局管理器**：

-  **LinerLayoutManager** 以**垂直**或者**水平列表**方式展示Item
-  **GridLayoutManager** 以**网格**方式展示Item
-  **StaggeredGridLayoutManager** 以**瀑布流**方式展示Item

如果你想用 RecyclerView 来实现自己**自定义效果**，则应该去**继承实现自己的 LayoutManager**，并重写相应的方法，而不应该想着去改写 RecyclerView。

#### LayoutManager 常见 API

关于 LayoutManager 的使用有下面一些常见的 API（有些在 LayoutManager 实现的子类中）

```
    canScrollHorizontally();//能否横向滚动
    canScrollVertically();//能否纵向滚动
    scrollToPosition(int position);//滚动到指定位置

    setOrientation(int orientation);//设置滚动的方向
    getOrientation();//获取滚动方向

    findViewByPosition(int position);//获取指定位置的Item View
    findFirstCompletelyVisibleItemPosition();//获取第一个完全可见的Item位置
    findFirstVisibleItemPosition();//获取第一个可见Item的位置
    findLastCompletelyVisibleItemPosition();//获取最后一个完全可见的Item位置
    findLastVisibleItemPosition();//获取最后一个可见Item的位置
```

上面仅仅是列出一些常用的 API 而已，更多的 API 可以查看官方文档，通常你想用 RecyclerView 实现某种效果，例如指定滚动到某个 Item 位置，但是你在 RecyclerView 中又找不到可以调用的 API 时，就可以跑到 LayoutManager 的文档去看看，基本都在那里。
 另外还有一点关于瀑布流布局效果 StaggeredGridLayoutManager 想说的，看到网上有些文章写的示例代码，在设置了 StaggeredGridLayoutManager 后仍要去 Adapter 中动态设置 View 的高度，才能实现瀑布流，这种做法是完全错误的，之所以 StaggeredGridLayoutManager 的瀑布流效果出不来，基本是 item 布局的 xml 问题以及数据问题导致。如果要在 Adapter 中设置 View 的高度，则完全违背了 LayoutManager 的设计理念了。

#### LinearLayoutManager源码分析

 这里我们简单分析LinearLayoutManager的实现。

对于LinearLayoutManager来说，比较重要的几个方法有：

-  `onLayoutChildren()`: 对RecyclerView进行布局的入口方法。
-  `fill()`: 负责填充RecyclerView。
-  `scrollVerticallyBy()`:根据手指的移动滑动一定距离，并调用`fill()`填充。
-  `canScrollVertically()`或`canScrollHorizontally()`: 判断是否支持纵向滑动或横向滑动。

`onLayoutChildren()`的核心实现如下：

```
public void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state) {
    detachAndScrapAttachedViews(recycler); //将原来所有的Item View全部放到Recycler的Scrap Heap或Recycle Pool
    fill(recycler, mLayoutState, state, false); //填充现在所有的Item View
}
```

RecyclerView的回收机制有个重要的概念，即将回收站分为Scrap Heap和Recycle Pool，其中Scrap Heap的元素可以被直接复用，而不需要调用`onBindViewHolder()`。`detachAndScrapAttachedViews()`会根据情况，将原来的Item View放入Scrap Heap或Recycle Pool，从而在复用时提升效率。

`fill()`是对剩余空间不断地调用`layoutChunk()`，直到填充完为止。`layoutChunk()`的核心实现如下：

```
public void layoutChunk() {
    View view = layoutState.next(recycler); //调用了getViewForPosition()
    addView(view);  //加入View
    measureChildWithMargins(view, 0, 0); //计算View的大小
    layoutDecoratedWithMargins(view, left, top, right, bottom); //布局View
}
```

其中`next()`调用了`getViewForPosition(currentPosition)`，该方法是从RecyclerView的回收机制实现类Recycler中获取合适的View，在后文的回收机制中会介绍该方法的具体实现。

如果要自定义LayoutManager，可以参考：

- [创建一个 RecyclerView LayoutManager – Part 1](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fhehonghui%2Fandroid-tech-frontier%2Fblob%2Fmaster%2Fissue-9%2F%25E5%2588%259B%25E5%25BB%25BA-RecyclerView-LayoutManager-Part-1.md)
- [创建一个 RecyclerView LayoutManager – Part 2](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fhehonghui%2Fandroid-tech-frontier%2Fblob%2Fmaster%2Fissue-13%2F%25E5%2588%259B%25E5%25BB%25BA-RecyclerView-LayoutManager-Part-2.md)
- [创建一个 RecyclerView LayoutManager – Part 3](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fhehonghui%2Fandroid-tech-frontier%2Fblob%2Fmaster%2Fissue-13%2F%25E5%2588%259B%25E5%25BB%25BA-RecyclerView-LayoutManager-Part-3.md)

### Adapter适配器

Adapter的使用方式前面已经介绍了，功能就是为RecyclerView提供数据，这里主要介绍万能适配器的实现。其实万能适配器的概念在ListView就已经存在了，即[base-adapter-helper](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2FJoanZapata%2Fbase-adapter-helper)。

这里我们只针对RecyclerView，聊聊万能适配器出现的原因。为了创建一个RecyclerView的Adapter，每次我们都需要去做重复劳动，包括重写`onCreateViewHolder()`,`getItemCount()`、创建ViewHolder，并且实现过程大同小异，因此万能适配器出现了。

#### 万能适配器

这里讲解下万能适配器的实现思路。

我们通过`public abstract class QuickAdapter<T> extends RecyclerView.Adapter<QuickAdapter.VH>`定义万能适配器QuickAdapter类，T是列表数据中每个元素的类型，QuickAdapter.VH是QuickAdapter的ViewHolder实现类，称为万能ViewHolder。

首先介绍QuickAdapter.VH的实现：

```
static class VH extends RecyclerView.ViewHolder{
    private SparseArray<View> mViews;
    private View mConvertView;

    private VH(View v){
        super(v);
        mConvertView = v;
        mViews = new SparseArray<>();
    }

    public static VH get(ViewGroup parent, int layoutId){
        View convertView = LayoutInflater.from(parent.getContext()).inflate(layoutId, parent, false);
        return new VH(convertView);
    }

    public <T extends View> T getView(int id){
        View v = mViews.get(id);
        if(v == null){
            v = mConvertView.findViewById(id);
            mViews.put(id, v);
        }
        return (T)v;
    }

    public void setText(int id, String value){
        TextView view = getView(id);
        view.setText(value);
    }
}
```

其中的关键点在于通过`SparseArray<View>`存储item view的控件，`getView(int id)`的功能就是通过id获得对应的View（首先在mViews中查询是否存在，如果没有，那么`findViewById()`并放入mViews中，避免下次再执行`findViewById()`）。

**QuickAdapter的实现**如下：

```
public abstract class QuickAdapter<T> extends RecyclerView.Adapter<QuickAdapter.VH>{
    private List<T> mDatas;
    public QuickAdapter(List<T> datas){
        this.mDatas = datas;
    }

    public abstract int getLayoutId(int viewType);

    @Override
    public VH onCreateViewHolder(ViewGroup parent, int viewType) {
        return VH.get(parent,getLayoutId(viewType));
    }

    @Override
    public void onBindViewHolder(VH holder, int position) {
        convert(holder, mDatas.get(position), position);
    }

    @Override
    public int getItemCount() {
        return mDatas.size();
    }

    public abstract void convert(VH holder, T data, int position);
    
    static class VH extends RecyclerView.ViewHolder{
        private SparseArray<View> mViews;
        private View mConvertView;
    
        private VH(View v){
            super(v);
            mConvertView = v;
            mViews = new SparseArray<>();
        }
    
        public static VH get(ViewGroup parent, int layoutId){
            View convertView = LayoutInflater.from(parent.getContext()).inflate(layoutId, parent, false);
            return new VH(convertView);
        }
    
        public <T extends View> T getView(int id){
            View v = mViews.get(id);
            if(v == null){
                v = mConvertView.findViewById(id);
                mViews.put(id, v);
            }
            return (T)v;
        }
    
        public void setText(int id, String value){
            TextView view = getView(id);
            view.setText(value);
        }
    }
}
```

其中：

-  `getLayoutId(int viewType)`是根据viewType返回布局ID。
-  `convert()`做具体的bind操作。

就这样，万能适配器实现完成了。

通过万能适配器能通过以下方式快捷地创建一个Adapter：

```
mAdapter = new QuickAdapter<String>(data) {
    @Override
    public int getLayoutId(int viewType) {
        return R.layout.item;
    }

    @Override
    public void convert(VH holder, String data, int position) {
        holder.setText(R.id.text, data);
        //holder.itemView.setOnClickListener(); 此处还可以添加点击事件
    }
};
```

是不是很方便。当然复杂情况也可以轻松解决。

```
mAdapter = new QuickAdapter<Model>(data) {
    @Override
    public int getLayoutId(int viewType) {
        switch(viewType){
            case TYPE_1:
                return R.layout.item_1;
            case TYPE_2:
                return R.layout.item_2;
        }
    }

    @Override
    public int getItemViewType(int position) {
        if(position % 2 == 0){
            return TYPE_1;
        } else{
            return TYPE_2;
        }
    }

    @Override
    public void convert(VH holder, Model data, int position) {
        int type = getItemViewType(position);
        switch(type){
            case TYPE_1:
                holder.setText(R.id.text, data.text);
                break;
            case TYPE_2:
                holder.setImage(R.id.image, data.image);
                break;
        }
    }
};
```

## 结论

1. 在一些场景下，如界面初始化，滑动等，ListView和RecyclerView都能很好地工作，两者并没有很大的差异：
2. 数据源频繁更新的场景，如弹幕：[http://www.jianshu.com/p/2232a63442d6](https://www.jianshu.com/p/2232a63442d6)等RecyclerView的优势会非常明显；

进一步来讲，结论是：
 **列表页展示界面，需要支持动画，或者频繁更新，局部刷新，建议使用RecyclerView，更加强大完善，易扩展；其它情况(如微信卡包列表页)两者都OK，但ListView在使用上会更加方便，快捷。**

## 扩展阅读

- [Google I/O 2016: RecyclerView Ins and Outs](https://link.jianshu.com?t=http%3A%2F%2Fv.youku.com%2Fv_show%2Fid_XMTU4MTQ1ODg2NA%3D%3D.html%3Ff%3D27314446)
- [RecyclerView优秀文章集](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2FCymChad%2FCymChad.github.io)

**引用：**
 ★★★★[RecyclerView 必知必会](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Ftencent_bugly%2Farticle%2Fdetails%2F54287626%23t7)
 ★★★★[Android ListView 与 RecyclerView 对比浅析–缓存机制](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Ftencent_bugly%2Farticle%2Fdetails%2F52981210)
 ★★★[RecyclerView使用完全指南，是时候体验新控件了（一）](https://www.jianshu.com/p/4fc6164e4709)
 ★★★[RecyclerView使用完全指南，是时候体验新控件了（二）](https://www.jianshu.com/p/7c3c549a0ec4)
 [一篇博客理解Recyclerview的使用](https://link.jianshu.com?t=http%3A%2F%2Fblog.csdn.net%2Fu012124438%2Farticle%2Fdetails%2F53495951)
 [RecyclerView使用全解析](https://link.jianshu.com?t=http%3A%2F%2Fwww.cnblogs.com%2Fanni-qianqian%2Fp%2F6587329.html)

**Demo地址：**

- [RecyclerView基本用法](https://link.jianshu.com?t=https://github.com/Kyogirante/MaterialDesignDemo)
- [RecyclerViewDemo](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fxiazdong%2FRecyclerViewDemo)

- Demo1: RecyclerView添加HeaderView和FooterView，ItemDecoration范例。
- Demo2: ListView实现局部刷新。
- Demo3: RecyclerView实现拖拽、侧滑删除。
- Demo4: RecyclerView闪屏问题。
- Demo5: RecyclerView实现`setEmptyView()`。
- Demo6: RecyclerView实现万能适配器，瀑布流布局，嵌套滑动机制。

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

  

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 

 