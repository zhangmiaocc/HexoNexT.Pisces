---
title: android采用ChromeIntent方式实现从浏览器启动应用
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: 8e25ebb8
date: 2019-11-07 16:12:42
---

在很多应用中需要我们从浏览器中直接启动应用，而网上大多数采用的是scheme的方式，即在启动activity的mainfest文件中配置如下字段：

```xml
<activity android:name="com.example.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="example" android:host="test" />
  </intent-filter>
</activity>
```
然后在网页的连接设置为example://test/… 来启动应用，但是如果手机中没有应用，该url会跳转到一个错误的界面。

google官方在chrome中推出了一种Android Intents的方式来实现应用启动，通过在iframe中设置src为的方式，具体示例如下。

```java
intent:HOST/URI-path // Optional host
#Intent;
package=[string];
action=[string];
category=[string];
component=[string]; 
scheme=[string];
end;
```

<!--more-->
我们定义一个a标签为

```xml
<pre name="code" class="html"><pre name="code" class="html"><a href="intent://zhangmiao/#Intent;scheme=myapp;package=com.what.ever.myapp;end">Do Whatever</a>
```

然后在mainfest文件中定义要启动的activity
```xml
<activity android:name=".TestUrlScheme" >
   <intent-filter>
     	<!-- 显示数据 -->
      <action android:name="android.intent.action.VIEW" />
      <category android:name="android.intent.category.DEFAULT" />
     	<!-- 定义成浏览器类型，有URL需要处理时会过滤 -->
      <category android:name="android.intent.category.BROWSABLE" />
     	<!-- 打开以whatever协议的URL,这个自己随便定义。 -->
      <data android:scheme="myapp" android:host="zhangmiao" android:path="/" />
   </intent-filter>
</activity>
```

然后在浏览器中点击a标签，就可以启动应用程序的对应activity了，如果手机中没有相应的应用，那么是否会跳转到错误页面呢，将a标签设置为

```xml
<a href="intent://whatever/#Intent;scheme=myapp;package=com.what.ever.myapp;S.browser_fallback_url=http%3A%2F%2Fzxing.org;end">Do Whatever</a>
```

这样如果没有对应应用，该链接就会跳转到S.browser_fallback_url指定的url上。

其中参数的类型如下

```java
String => 'S'
Boolean =>'B'
Byte => 'b'
Character => 'c'
Double => 'd'
Float => 'f'
Integer => 'i'
Long => 'l'
Short => 's'
```

```java
intent://RequestType/?name=zhangmiao&age=18#Intent;scheme=appname;package=com.example.appname;S.browser_fallback_url=http%3A%2F%2Fzxing.org;end
```

然后在启动activity的onCreate函数中利用bundle接收参数就行了
```java
Bundle parametros = getIntent().getExtras();
if (extras != null){
    String name = extras.getString("name");
    Integer age = extras.getInt("age");

    if (name!=null && age!=null)
    {
       //do whatever you have to
       //...
    }
}else{
     //no extras, get over it!!
}
```


