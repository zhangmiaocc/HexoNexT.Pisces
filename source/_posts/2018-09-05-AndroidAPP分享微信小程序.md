---
title: Android APP分享微信小程序
date: 2018-09-05 14:40:51
tags:
- blog
- markdown
- Android 
categories:
- Android 
---

**需求：**APP端 将公司的微信小程序 分享至微信好友

最近，微信小程序比较火热，公司也在做这一块，目前公司的小程序都是由H5端开发的，我们Android端也接到一个任务，那就是Android端应支持微信小程序的分享，并且通过分享出去的小程序可以启动我们的APP；

今天我们先来完成：Android端应支持微信小程序的分享！！！

------

**分析：**
 微信开放平台SDK支持小程序类型分享，详见官方文档：
 [https://open.weixin.qq.com/cgi-bin/showdocument?action=dir_list&t=resource/res_list&verify=1&id=open1419317340&token=&lang=zh_CN](https://link.jianshu.com?t=https%3A%2F%2Fopen.weixin.qq.com%2Fcgi-bin%2Fshowdocument%3Faction%3Ddir_list%26t%3Dresource%2Fres_list%26verify%3D1%26id%3Dopen1419317340%26token%3D%26lang%3Dzh_CN)
 a) 要求发起分享的App与小程序属于同一微信开放平台帐号；
 b) 支持分享小程序类型消息至好友会话，不支持“分享至朋友圈” “收藏”；
 c) 微信客户端版本要求：6.5.6及以上微信客户端版本，若客户端版本低于6.5.6，小程序类型分享将自动转成网页类型分享。开发者必须填写网页链接字段，确保低版本客户端能正常打开网页链接；
 d) 支持分享大图卡片样式，自定义图片建议长宽比是 5:4。6.5.9及以上版本微信客户端小程序类型分享使用大图卡片样式。
 e)支持分享开发版/体验版小程序，为支持开发者调试，开发者工具包支持分享开发版/体验版小程序至微信，开发者可控制分享的小程序版本。

把文档看了一遍，发现限制是比较多的，但是功能实现还是很简单的，下面让我们开始吧！！！

**开发：**

#### 前期准备

![img](https://upload-images.jianshu.io/upload_images/10170988-3b81737f56ba521c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/574/format/webp)

#### 小程序与APP主体账号绑定
开发人员希望通过APP分享小程序，需要先将小程序与APP主体账号（即APP的微信开放平台账号）绑定，APP才具有分享对应小程序的能力。如果没有与主体账号绑定，分享时是报错的，如下图：
    
![img](https://upload-images.jianshu.io/upload_images/10170988-41434fc8f9cde5e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/468/format/webp)

##### 登录APP所在的微信开放平台：[https://open.weixin.qq.com/](https://link.jianshu.com?t=https%3A%2F%2Fopen.weixin.qq.com%2F)

##### 绑定小程序

![img](https://upload-images.jianshu.io/upload_images/10170988-f9301d33315a0600.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

由上图可以看到，绑定小程序的数量是有限制的。我们点击【绑定小程序】按钮，打开的新页面

![img](https://upload-images.jianshu.io/upload_images/10170988-9e4471ecd4f57858.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

 输入小程序开发的主账号和密码，输入验证码提交就可以了，然后再通过手机微信扫码验证一下。 绑定成功后，直接就能在绑定列表中看到已绑定的小程序！！！

#### 代码实现
##### 小程序端提供参数：

```
miniProgram.userName="xxx"; //小程序ID
miniProgram.path="pages/xxx/xxx"; //小程序路径
```

##### 配置gradle

```
dependencies {
    compile 'com.tencent.mm.opensdk:wechat-sdk-android-with-mta:+'
}
```

##### 分享小程序的核心代码

```java
public void shareMinP(ShareMiniPModel shareModel){
    if (shareModel == null) return;
    WXMiniProgramObject miniProgramObj = new WXMiniProgramObject();
    miniProgramObj.webpageUrl = shareModel.getWebPageUrl();                 // 兼容低版本的网页链接
    miniProgramObj.miniprogramType = shareModel.getMiniProgramType();       // 分享小程序版 正式版:0，测试版:1，体验版:2
    miniProgramObj.userName = shareModel.getMiniId();                       // 小程序原始id
    miniProgramObj.path = shareModel.getMiniPath();                         // 小程序页面路径
    localWXMediaMessage = new WXMediaMessage(miniProgramObj);
    localWXMediaMessage.title = shareModel.getTitle();                      // 小程序消息title
    localWXMediaMessage.description = shareModel.getDescription();          // 详细描述

    if (!TextUtils.isEmpty(shareModel.getImageUrl())){
        inflateImage(shareModel.getImageUrl(), new Callback() {                 // 小程序图片
            @Override
            public void onLoadingComplete(String s, View view, Bitmap bitmap) {
                thumbBmp = bitmap;
                localWXMediaMessage.thumbData = bmpToByteArray(thumbBmp, true);
                if (localWXMediaMessage.thumbData.length < 131072){
                    WXsendReq(SendMessageToWX.Req.WXSceneSession, localWXMediaMessage);
                } else {
                    LogUtils.e("分享小程序，缩略图不得超过128kb");
                }
            }
        });
    } else if (shareModel.getImageBitmap() != null){
        thumbBmp = shareModel.getImageBitmap();
        localWXMediaMessage.thumbData = bmpToByteArray(thumbBmp, true);
        if (localWXMediaMessage.thumbData.length < 131072){
            WXsendReq(SendMessageToWX.Req.WXSceneSession, localWXMediaMessage);
        } else {
            LogUtils.e("分享小程序，缩略图不得超过128kb");
        }
    }
}
```

#### Demo演示

    为了避免麻烦，我们直接下载使用官方Demo，在其源代码上直接修改，修改的内容主要如下：
    a. 包名（必须修改，使用你项目APP的实际包名）
    b. 配置gradle（微信sdk包、签名文件）
    c. 增加分享小程序的按钮和事件

##### 下载微信开放平台官方Demo
 [https://open.weixin.qq.com/zh_CN/htmledition/res/dev/download/sdk/WeChatSDK_sample_Android.zip](https://link.jianshu.com?t=https%3A%2F%2Fopen.weixin.qq.com%2Fzh_CN%2Fhtmledition%2Fres%2Fdev%2Fdownload%2Fsdk%2FWeChatSDK_sample_Android.zip)

##### 修改包名
 使用Androidstudio打开demo，目录结构如下图，修改其包名

![img](https://upload-images.jianshu.io/upload_images/10170988-ed00884ed3ececec.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/619/format/webp)

如上图，取消compact empty middle packages的默认选中
在对应包名的文件夹上，直接右键修改名称，改成包名对应的名称，并全部应用
可参考：<https://www.jianshu.com/p/557e1906db1a>
修改后的包名，必须是你项目APP的实际包名，且已通过微信开放平台审核的APP包名；

#####  配置gradle（修改依赖、修改签名）

 ![img](https://upload-images.jianshu.io/upload_images/10170988-994435cefdc1bb11.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/833/format/webp)

 

签名必须使用你项目APP对应的签名文件，即你申请微信开放平台时APP对应的签名文件；

##### 修改APP_ID

```java
public class Constants {
    // APP_ID 替换为你的应用从官方网站申请到的合法appId
    public static final String APP_ID = "wxf666676666636666";

    public static class ShowMsgActivity {
        public static final String STitle = "showmsg_title";
        public static final String SMessage = "showmsg_message";
        public static final String BAThumbData = "showmsg_thumb_data";
    }
}
```

##### 增加分享小程序的按钮和事件

![img](https://upload-images.jianshu.io/upload_images/10170988-6d9fa50e0424721e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

 ![img](https://upload-images.jianshu.io/upload_images/10170988-807127a9a61d72d5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/467/format/webp)



##### 测试
 选择分享的人员

![img](https://upload-images.jianshu.io/upload_images/10170988-4ecf4405b433089d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/470/format/webp)

收到分享的小程序卡片

![img](https://upload-images.jianshu.io/upload_images/10170988-d799ac004a10eb54.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/421/format/webp)



