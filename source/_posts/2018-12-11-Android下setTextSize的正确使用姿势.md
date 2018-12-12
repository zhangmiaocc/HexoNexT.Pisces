---
title: Android下setTextSize的正确使用姿势
date: 2018-12-11 15:05:53
tags:
- Android 
- Android Tips
- TypedValue
categories:
- Android
- Android Tips
---

### 问几个问题先

在app/src/main/res/values/dimens.xml中定义尺寸如下：

```js
<dimen name="font1">18sp</dimen>
```

在代码中引用此尺寸如下：

```java
mText.setTextSize(18);  // 方法1
mText.setTextSize(getResources().getDimension(R.dimen.font1));  // 方法2
mText.setTextSize(TypedValue.COMPLEX_UNIT_PX,getResources().getDimension(R.dimen.font1));  // 方法3
mText.setTextSize(TypedValue.COMPLEX_UNIT_SP,18);  // 方法4
```

问题1: 方法1和方法2设置的文字尺寸大小相同么？
问题2:方法3和方法4设置的文字尺寸大小相同么？
问题3:方法1和方法4设置的文字尺寸大小相同么？

如果你能很清楚的给出上面问题的答案，那就没必要再向下看了；
如果你对以上问题感到模棱两可的话，请继续往下看:

<!--more-->

要想解开以上疑惑，其实主要从以下两个方法的源码入手

### setTextSize(...)

进入TextView类，找到setTextSize(...)方法，发现它调用了另一个重载方法，注意这里调用重载方法时传入的第一个参数是一个默认值 TypedValue.COMPLEX_UNIT_SP，因此方法1和方法4设置的文字尺寸大小相同.

```java
public void setTextSize(float size) {
    setTextSize(TypedValue.COMPLEX_UNIT_SP, size);
}

public void setTextSize(int unit, float size) {   
    Context c = getContext();    
    Resources r;   
    if (c == null)        
        r = Resources.getSystem();    
    else        
        r = c.getResources();

    setRawTextSize(TypedValue.applyDimension(unit, size, r.getDisplayMetrics()));
}
```

#### 重载方法中有两个方法需要重点看

##### setRawTextSize(...)方法

通过它的几个方法会发现它的作用就是真正设置文字大小并刷新显示：

```java
private void setRawTextSize(float size) {    
    if (size != mTextPaint.getTextSize()) {            
        mTextPaint.setTextSize(size);        
        if (mLayout != null) {            
            nullLayouts();            
            requestLayout();            
            invalidate();        
        }    
    }
}
```

##### TypedValue类中的applyDimension(...)方法

根据传入的unit单位来处理文字大小，返回的尺寸为px (通过第一个case条件得知).

```java
public static float applyDimension(int unit, float value, DisplayMetrics metrics){
    switch (unit) {    
        case COMPLEX_UNIT_PX:        
            return value;    
        case COMPLEX_UNIT_DIP:        
            return value * metrics.density;    
        case COMPLEX_UNIT_SP:        
            return value * metrics.scaledDensity;    
        case COMPLEX_UNIT_PT:        
            return value * metrics.xdpi * (1.0f/72);    
        case COMPLEX_UNIT_IN:        
            return value * metrics.xdpi;    
        case COMPLEX_UNIT_MM:        
            return value * metrics.xdpi * (1.0f/25.4f);    
    }    
    return 0;
}
```

如果传入的unit为COMPLEX_UNIT_PX，则会将value直接返回
如果传入的unit为COMPLEX_UNIT_SP，则会将value处理成px返回

### getDimension(...)

进入Resources类，找到getDimension(...)方法

```java
public float getDimension(@DimenRes int id) throws NotFoundException {
    synchronized (mAccessLock) {        
        TypedValue value = mTmpValue;        
        if (value == null) {            
            mTmpValue = value = new TypedValue();        
        }        
        getValue(id, value, true);        
        if (value.type == TypedValue.TYPE_DIMENSION) {            
            return TypedValue.complexToDimension(value.data, mMetrics);        
        }        
        throw new NotFoundException("Resource ID #0x" + Integer.toHexString(id) + " type #0x" + Integer.toHexString(value.type) + " is not valid");    
    }
}
```

这里方法不多，点getValue(...)方法进去看会发现它内部又调用了native方法，这里我无法进一步追溯它的实现，不过没关系，因为我发现有个方法很眼熟那就是：TypedValue.complexToDimension(...) ，进入此方法会惊奇的发现它也调用了上面讲到的applyDimension(...)方法.

```java
public static float complexToDimension(int data, DisplayMetrics metrics){    
    return applyDimension(        
        (data>>COMPLEX_UNIT_SHIFT)&COMPLEX_UNIT_MASK, complexToFloat(data), metrics);
}
```

由此可以大胆的猜测 getDimension(...)方法最终也会将数据处理成px返回，因此方法3和方法4设置的文字尺寸大小相同，只是写法不同而已.

### 好了，回到开篇提到的四个问题，可以得出以下结论：

> 方法1：文字尺寸以sp为单位，大小为18
> 方法2：文字尺寸以sp为单位，大小为（18sp转换为px的值）
> 方法3：文字尺寸以px为单位，大小为（18sp转换为px的值）
> 方法4：文字尺寸以sp为单位，大小为18
> **方法1=方法3=方法4!＝方法2**

至此，文章结束，希望此文能帮助到你，如果对此文有不同见解，欢迎直接评论！

