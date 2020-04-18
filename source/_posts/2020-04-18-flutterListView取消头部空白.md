---
title: flutter ListView取消头部空白
tags:
  - Android
  - Flutter Tips
categories:
  - Android
  - Flutter Tips
abbrlink: c3648888
date: 2020-04-18 18:08:25
---

**ListView头部有一段空白区域，是因为当ListView没有和AppBar一起使用时，头部会有一个padding，为了去掉padding，可以使用MediaQuery.removePadding:**

```dart
Widget _rubbishList(){
    return MediaQuery.removePadding(
        removeTop: true,
        context:  context,
        child: Container(
            margin: EdgeInsets.only(left: 20,right: 20),
            height: ScreenUtil().setHeight(700),
            child: ListView.builder(
                itemCount: rubbishList.length,
                itemBuilder: (context,index){
                  return _cardItem(index);
                }
            )
        )
    );
 
  }
```

<!--more-->