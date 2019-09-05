---
title: Android Webview里设置Cookie
tags:
  - Android
  - Webview
categories:
  - Android
  - Webview
abbrlink: 3defa4a1
date: 2018-08-03 15:39:42
---

> Android中WebView加载网页，有时候需要通过cookie想网页传递信息，这时候这样操作。

#### Step 1 设置接收cookie
```java
CookieManager.setAcceptFileSchemeCookies(true);
CookieManager.getInstance().setAcceptCookie(true);
CookieManager.setAcceptFileSchemeCookies(true);
```

#### Step 2 设置cookie的值，通过setcookie方法
```java
List<String> cookies = new ArrayList<>();
cookies.add("app_key=" + App.getAppKey());
cookies.add("os=" + "Android" + Build.VERSION.SDK_INT);
```
<!--more-->

#### Step 3 通过sync方法，将cookie同步

```java
if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
    CookieSyncManager.createInstance(context);
}
CookieManager cookieManager = CookieManager.getInstance();
cookieManager.setAcceptCookie(true);
if (cookies != null) {
    for (String cookie : cookies) {
        cookieManager.setCookie(url, cookie);
    }
}
String s = "Domain=.***.com";
String s1 = "Path=/";
cookieManager.setCookie(url, s);
cookieManager.setCookie(url, s1);
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP_MR1) {
    CookieManager.getInstance().flush();
} else {
    CookieSyncManager.getInstance().sync();
}
```
`";Domain=.xxxx.xxx.com"+//作用域（在哪个域名下cookie起作用）`
`";Path=/";//Domain这个作用域下的哪个文件夹，“/”代表所有文件夹 `

##### <span style="color:red">注意</span>
在调用设置Cookie之后不能再设置这类属性，否则设置Cookie无效。
```java
webView.getSettings().setBuiltInZoomControls(true);  
webView.getSettings().setJavaScriptEnabled(true);  
```


```java
public class WebviewUtil {
    public static void setWebCookie(Context context) {
        CookieManager.setAcceptFileSchemeCookies(true);
        CookieManager.getInstance().setAcceptCookie(true);
        CookieManager.setAcceptFileSchemeCookies(true);
        setCookie(context);
    }
 
    private static void setCookie(Context context) {
        List<String> cookies = new ArrayList<>();
        cookies.add("app_key=" + App.getAppKey());
        cookies.add("plat=" + "2");
        cookies.add("os=" + "Android" + Build.VERSION.SDK_INT);
        cookies.add("channel=" + AndroidUtil.getChannel());
        cookies.add("cver=" + String.valueOf(App.getVersionCode()));
        cookies.add("ctype=" + 2);
        cookies.add("cspec=" + "");
        if (AccountManager.hasLogin()) {
            cookies.add("user_id=" + getUserId());
            cookies.add("gz_id=" + getUserId());
        }
        syncCookie(context, ".7gz.com/", cookies);
    }
 
    private static void syncCookie(Context context, String url, List<String> cookies) {
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
            CookieSyncManager.createInstance(context);
        }
        CookieManager cookieManager = CookieManager.getInstance();
        cookieManager.setAcceptCookie(true);
        if (cookies != null) {
            for (String cookie : cookies) {
                cookieManager.setCookie(url, cookie);
            }
        }
        String s = "Domain=.7gz.com";
        String s1 = "Path=/";
        cookieManager.setCookie(url, s);
        cookieManager.setCookie(url, s1);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP_MR1) {
            CookieManager.getInstance().flush();
        } else {
            CookieSyncManager.getInstance().sync();
        }
    }
 
    private static String getUserId() {
        return AccountManager.getInstance().getAccount().id;
    }
 
    //清空所有Cookie
    public static void removeAllCookie(Context context) {
        CookieSyncManager.createInstance(context);  //Create a singleton CookieSyncManager within a context
        CookieManager cookieManager = CookieManager.getInstance(); // the singleton CookieManager instance
        cookieManager.removeAllCookie();// Removes all cookies.
        CookieSyncManager.getInstance().sync(); // forces sync manager to sync now
 
    }

```