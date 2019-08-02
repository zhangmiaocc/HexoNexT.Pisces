---
title: Weex 在Android端的实践
tags:
  - Android
  - Weex
categories:
  - Android
  - Weex
abbrlink: 31c875b0
date: 2018-07-21 16:14:01
---

|                           ShowPage                           |                           ShowPage                           |                           ShowPage                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/311559293360_.pic_hd.jpg) | ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/321559293361_.pic_hd.jpg) | ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/331559293362_.pic_hd.jpg) |
| ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/341559293370_.pic_hd.jpg) | ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/351559293371_.pic_hd.jpg) | ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/361559293372_.pic_hd.jpg) |
| ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/371559293373_.pic_hd.jpg) | ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/381559293374_.pic_hd.jpg) | ![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/391559293375_.pic_hd.jpg) |

<!--more-->

### 什么是weex

> Write Once, Run Everywhere

Weex能够完美兼顾性能与动态性，让移动开发者通过简捷的前端语法写出Native级别的性能体验，并支持iOS、安卓、YunOS及Web等多端部署。真正实现一次撰写，多平台运行。

Weex 提供了多端一致的技术方案。

- 首先 web 开发体验在各端当中是相同的。包括语法设计和工程链路。
- 其次，Weex 的组件、模块设计都是 iOS、Android、Web 的开发者共同讨论出来的，有一定的通用性和普遍性。
- Weex 开发同一份代码，可以在不同的端上分别执行，避免了多端的重复研发成本。

在同构这条路上，WEEX比ReactNative做得更彻底，他“几乎”做到了，“你来使用vue写一个webapp，我顺便给你编译成了ios和android的原生app”

### 为什么要用weex

东西是好东西，对于电商这类经常需要变动 APP 界面的尤其适用。看看天猫、淘宝首页你就知道了。但一定不会适用于所有人，需求千变万化，总有框架照顾不到的地方。我在下面也列几张我们用weex后的首页。

### 用原生如何实现？

当我没用weex的时候，我想：这还非得用weex，我用native也能实现。我接不同的type展现不同的UI不就行了。这当然可以了，我始终相信一点，需求通过不同的技术手段都可以实现，只是实现方式的简易程度和灵活度的差别。

### 用weex的优势？

首先，weex是组件化的。什么是组件化？很好理解，他的每一块儿都是独立，我们只需根据不同的类型，将不同的组件组合在一起就行了，耦合度很低，当一个组件出问题不会影响其他组件渲染。但如果我用原生写，肯定是一页，然后根据不同的数据，展现出来，可能一个数据出错，我这一整页都是空白的。其次，weex还有一个好处，可以热更新这个页面，假如：线上时突然又想新增一种组件样式（例如banner吧），这个时候原生界面肯定说：等下个版本迭代给你新加。但weex就可以直接新增一种组件，然后发一个热更新文件，让原生加载新的js文件即可。

### Android 嵌入weex

#### 集成weex
有两种模式，一种是源码集成，另一种sdk依赖。这里没有坑，就按照[接入文档](https://weex.apache.org/zh/guide/develop/integrate-to-android-app.html)接入就行，有一点注意一下，用源码依赖可以拿到最新版本，但通过源码依赖拿的版本就会落后一些，但比较稳定。

```properties
dependencies {
    implementation fileTree(dir: '../module_common/libs', include: ['*.jar'])
    implementation 'com.android.support:appcompat-v7:27.1.1'

    //router
    implementation 'com.alibaba:arouter-api:1.3.1'
    annotationProcessor 'com.alibaba:arouter-compiler:1.1.4'

    implementation 'com.alibaba:fastjson:1.2.31'
    implementation 'com.google.code.gson:gson:2.8.5'
    implementation 'com.github.bumptech.glide:glide:4.8.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.3.0'
    implementation 'com.taobao.android:weex_sdk:0.20.3.0-beta@aar'
    // 接入 weex inspector
    implementation 'com.taobao.android:weex_inspector:0.20.3.0-beta@aar'
    implementation 'com.squareup.okhttp3:okhttp:3.10.0'
    implementation 'com.squareup.okhttp:okhttp:2.3.0'
    implementation 'com.squareup.okhttp:okhttp-ws:2.3.0'
}
```

#### 实现一个ImageAdapter
接入weex后需要自己实现一个ImageAdapter，用于展示图片，我是基于glide框架的，当然也可以选择基于fresco，picasso等等。

```java
import android.text.TextUtils;
import android.widget.ImageView;

import com.bumptech.glide.Glide;
import com.taobao.weex.WXSDKManager;
import com.taobao.weex.adapter.IWXImgLoaderAdapter;
import com.taobao.weex.common.WXImageStrategy;
import com.taobao.weex.dom.WXImageQuality;

public class ImageAdapter implements IWXImgLoaderAdapter {

    @Override
    public void setImage(final String url, final ImageView view, WXImageQuality quality, WXImageStrategy strategy) {
        //实现自己的图片下载。
        WXSDKManager.getInstance().postOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (view == null || view.getLayoutParams() == null) {
                    return;
                }
                if (TextUtils.isEmpty(url)) {
                    view.setImageBitmap(null);
                    return;
                }
                String temp = url;
                if (url.startsWith("//")) {
                    temp = "http:" + url;
                }
                if (view.getLayoutParams().width <= 0 || view.getLayoutParams().height <= 0) {
                    return;
                }
                if (view != null && view.getContext() != null) {
                    try {
                        Glide.with(view.getContext()).load(temp).into(view);
                    }
                    catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        }, 0);
    }
}
```

#### weex加载方式
文档说只说了通过file的形式加载js文件，其实我们也可以通过URL的方式渲染，当我们调试的时候就需要连接服务器进行修改，也就是通过URL方式加载。

```java
//根据URL渲染
mInstance.renderByUrl(getPageName(), url, options, jsonInitData, WXRenderStrategy.APPEND_ONCE);
//根据文件渲染
mWXSDKInstance.render(name, WXFileUtils.loadAsset(name+".js", this), null, null, WXRenderStrategy.APPEND_ASYNC);
```

```java
/**
 * weex 基础容器类
 */
public abstract class BaseWeexContainerActivity extends AppCompatActivity implements IWXRenderListener, IWeexPageRefresh {

    private static final String TAG = BaseWeexContainerActivity.class.getSimpleName();

    protected String mBundleUrl;
    private FrameLayout mContainer;
    protected WXSDKInstance mWXSDKInstance;

    /** 网络异常页面 */
    private View mEmptyView;
    /** Loading页面 */
    private View mLoadingView;

    private Handler mHandler = new Handler();

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.weex_activity_container);

        mContainer = findViewById(R.id.container);
        mLoadingView = findViewById(R.id.loading_view);
        mEmptyView = findViewById(R.id.empty_view);

        initListeners();
    }

    private void initListeners() {
        mEmptyView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (!WeexBaseUtil.isInternetConnected(BaseWeexContainerActivity.this)) {
                    ToastUtil.showShort(BaseWeexContainerActivity.this, R.string.net_error);
                } else {
                    mLoadingView.setVisibility(View.VISIBLE);
                    mEmptyView.setVisibility(View.GONE);
                    mHandler.postDelayed(new Runnable() {
                        @Override
                        public void run() {
                            loadWeexPage(TAG, mBundleUrl);
                        }
                    }, 100);
                }
            }
        });
    }

    protected void loadWeexPage(String pageName, String bundleUrl) {
        if (!TextUtils.isEmpty(bundleUrl)) {
            mBundleUrl = bundleUrl;
            createWeexInstance();

            /**
             * pageName:自定义，一个标示符号。
             * url:远程bundle JS的下载地址
             * options:初始化时传入WEEX的参数，比如 bundle JS地址
             * flag:渲染策略。WXRenderStrategy.APPEND_ASYNC:异步策略先返回外层View，其他View渲染完成后调用onRenderSuccess。
             * WXRenderStrategy.APPEND_ONCE 所有控件渲染完后后一次性返回。
             */
            Map<String, Object> options = new HashMap<>();
            options.put(WXSDKInstance.BUNDLE_URL, mBundleUrl);
            String jsonData = WeexBaseUtil.convertHttpRequestParamData(mBundleUrl);
            // 获取缓存文件名
            final String filename = JsCacheTool.getCacheFilename(mBundleUrl);
            // 检测缓存文件是否存在
            if (JsCacheTool.checkCacheExist(mBundleUrl)) {
                // 存在，读取缓存
                String template = JsCacheTool.loadLocalCacheData(filename);
                mWXSDKInstance.render(pageName, template, options, jsonData, WXRenderStrategy.APPEND_ASYNC);
            } else {
                // 缓存不存在，异步从服务器拉取并缓存到本地
                mWXSDKInstance.renderByUrl(pageName, mBundleUrl, options, jsonData, WXRenderStrategy.APPEND_ASYNC);
                JsCacheTool.downloadCacheToFileAsync(mBundleUrl, filename, null);
            }
        }
    }

    protected void refreshPage() {
        if (!TextUtils.isEmpty(mBundleUrl)) {
            createWeexInstance();

            Map<String, Object> options = new HashMap<>();
            options.put(WXSDKInstance.BUNDLE_URL, mBundleUrl);
            String jsonData = WeexBaseUtil.convertHttpRequestParamData(mBundleUrl);
            mWXSDKInstance.renderByUrl(mWXSDKInstance.getWXPerformance().pageName, mBundleUrl, options, jsonData, WXRenderStrategy.APPEND_ASYNC);
        }

    }

    @Override
    public void onStart() {
        super.onStart();
        if (mWXSDKInstance != null) {
            mWXSDKInstance.onActivityStart();
        }
    }

    @Override
    public void onResume() {
        super.onResume();
        if (mWXSDKInstance != null) {
            mWXSDKInstance.onActivityResume();
        }
    }

    @Override
    public void onPause() {
        super.onPause();
        if (mWXSDKInstance != null) {
            mWXSDKInstance.onActivityPause();
        }
    }

    @Override
    public void onStop() {
        super.onStop();
        if (mWXSDKInstance != null) {
            mWXSDKInstance.onActivityStop();
        }
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        if (mWXSDKInstance != null) {
            mWXSDKInstance.onActivityDestroy();
        }
        destoryWeexInstance();
    }

    private void createWeexInstance() {
        destoryWeexInstance();
        if (mWXSDKInstance == null) {
            mWXSDKInstance = new WXSDKInstance(BaseWeexContainerActivity.this);
            mWXSDKInstance.registerRenderListener(this);
        }
    }

    private void destoryWeexInstance() {
        if (mWXSDKInstance != null) {
            mWXSDKInstance.registerRenderListener(null);
            mWXSDKInstance.destroy();
            mWXSDKInstance = null;
        }
    }

    @Override
    public void onViewCreated(WXSDKInstance instance, View view) {
        mLoadingView.setVisibility(View.GONE);
        mEmptyView.setVisibility(View.GONE);
        if (mContainer != null) {
            mContainer.removeAllViews();
            mContainer.addView(view);
        }
    }

    @Override
    public void onRenderSuccess(WXSDKInstance instance, int width, int height) {
        mLoadingView.setVisibility(View.GONE);
        mEmptyView.setVisibility(View.GONE);
    }

    @Override
    public void onRefreshSuccess(WXSDKInstance instance, int width, int height) {
        mLoadingView.setVisibility(View.GONE);
        mEmptyView.setVisibility(View.GONE);
    }

    @Override
    public void onException(WXSDKInstance instance, String errCode, String msg) {
        mLoadingView.setVisibility(View.GONE);
        mEmptyView.setVisibility(View.VISIBLE);
        if (WXErrorCode.WX_ERR_JS_FRAMEWORK.getErrorCode().equalsIgnoreCase(errCode)) {
            ToastUtil.showShort(BaseWeexContainerActivity.this, R.string.weex_create_instance_error);
        } else if (WXErrorCode.WX_DEGRAD_ERR_NETWORK_CHECK_CONTENT_LENGTH_FAILED.getErrorCode().equalsIgnoreCase(errCode)) {
            ToastUtil.showShort(BaseWeexContainerActivity.this, R.string.weex_network_error);
        } else if (WXErrorCode.WX_DEGRAD_ERR_BUNDLE_CONTENTTYPE_ERROR.getErrorCode().equalsIgnoreCase(errCode)) {
            ToastUtil.showShort(BaseWeexContainerActivity.this, R.string.weex_user_intercept_error);
        }
    }
}
```

### Android 嵌入weex devtools调试工具

1：先通过npm install 安装项目依赖。之后运行npm run dev和npm run serve
2：运行weex debug，就会开启一个chrome 的 inspect/debug 工具
3：完成上面两个步骤，服务端就相当于配置成功了，之后我们只需要在我们的代码中配好相应的库，完善代码就可以了

### Android 实现热调试功能

Android调试功能很简单就可以实现，但是热调试功能却花我一些时间。什么是热调试功能呢？当我修改服务器的代码，通过刷新浏览器，APP端数据也会跟着改变，这就是热调试。热调试功能是如何工作的呢？其实当我刷新时，会在APP的广播接收器接到相应的指令，此时我们重新reload相应的js文件即可。

```java
  public void registerBroadcastReceiver(BroadcastReceiver receiver, IntentFilter filter) {
        mBroadcastReceiver = receiver != null ? receiver : new DefaultBroadcastReceiver();
        if (filter == null) {
            filter = new IntentFilter();
        }
        filter.addAction(IWXDebugProxy.ACTION_DEBUG_INSTANCE_REFRESH);
        filter.addAction(WXSDKEngine.JS_FRAMEWORK_RELOAD);
        LocalBroadcastManager.getInstance(getApplicationContext())
                .registerReceiver(mBroadcastReceiver, filter);
        if (mReloadListener == null) {
            setReloadListener(new WxReloadListener() {
                @Override
                public void onReload() {
                    createWeexInstance();
                    renderPage();
                }
            });
        }
        if (mRefreshListener == null) {
            setRefreshListener(new WxRefreshListener() {
                @Override
                public void onRefresh() {
                    createWeexInstance();
                    renderPage();
                }

            });
        }
    }
```

weex上手还是比较容易的，希望每个人都有一直学习的热情与能力~

