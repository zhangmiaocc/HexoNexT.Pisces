---
title: Android对于有时间戳和token验证的网络请求的处理
tags:
  - Android
  - 时间戳超期
  - token效验失败
categories:
  - Android
  - Android Tips
abbrlink: 86cc3f20
date: 2019-09-16 17:40:49
---

有些项目为了提高安全性，设计接口时增加了时间戳和token效验，如下所示：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190916174259.png)

<!--more-->

## 时间戳

上表中的参数时间戳必须与服务器当前时间对应，前后不能超过10秒，超过则请求失败：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190916174321.png)

你肯定想问，如果手机系统时间不正确，比服务器快了或慢了10秒以上，那不每次都会请求失败吗？别着急，这样的接口设计，肯定会有一个获取服务器时间戳的接口：

![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20190916174309.png)

思路是这样的，每次APP启动的时候获取一次服务器时间戳，然后与手机本地时间相减，计算出差额，并保存；当其他的网络请求需要传入时间戳参数时，用保存的时间差额加上手机当前时间，就可以得到正确的时间戳了：

时间差 = 获取的服务器时间戳 - 手机当前时间

时间戳 = 手机当前时间 + 时间差



当然，现在的手机大部分都是联网获取时间，很少会出现比标准时间差10秒以上的情况，服务器的时间也是标准时间，因此，只传入手机的当前时间也能符合90%的情况。

那剩下的情况是什么呢？

​    1、网速慢的时候，请求时间和读取时间会比较长，很可能会超过10秒；

​    2、出国用户或国外用户，不在一个时区；

​    3、用户主动或被动调整了手机系统时间。

这些情况下，如果只传入手机的当前时间，是无法请求到正确的数据的，所以还是要计算时间差。



至于每次APP启动的时候获取一次服务器时间戳，这个时机对不对呢？

用户在使用App的过程中，很少会出现系统时间的调整，如果恰巧赶上，可以在接收到时间戳超期的错误码后，提示用户退出APP重新启动一次。当然也可以注册时间调整的广播接收者，接收到时间调整后，重新计算一次时间差。

至于网速慢的情况还是不能解决，加上提示用户重启APP和注册广播接收者这些不太优雅的操作，我们要寻找其他的思路。



怎么能彻底并且优雅的解决时间戳超期的问题呢？

其实我们不必每次APP启动都请求一次服务器时间戳，只有收到时间戳超期的错误之后，才有必要获取，此时计算出时间差，并保存，以后所有的请求都可以用这个时间差了，直到下一次再收到时间戳超期的错误，再获取一次即可。至于这个错误的请求，可以在计算出时间差之后，再重新请求一次。

跟着上述的思路，我们自然而然想到在请求回调基类里筛选这个时间戳超期错误，筛选出这个错误后，同步请求一次服务器时间戳，再进行一次之前的请求。想想就很困难，因为要保证在子线程里完成，还要记住之前的请求是什么，这条路走不通。



不卖关子了，okhttp的拦截器可以解决以上问题：



```java
            Interceptor interceptor = new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                    Request request = chain.request();
                    Response response= chain.proceed(request);
    
                    ResponseBody body = response.body();
    
                    BufferedSource source = body.source();
                    source.request(Long.MAX_VALUE);
                    Buffer buffer = source.buffer();
    
                    Charset charset = Charset.forName("UTF-8");
                    String json = buffer.clone().readString(charset);
    
                    BaseBean baseBean = gson.fromJson(json, BaseBean.class);
                    String code = baseBean.getCode();
    
                    if (code.equals("0210")) {// 时间戳超期
                        // 同步请求时间戳
                        Request timeRequest = new Request.Builder()
                                .url("http://uc.hivoice.cn:80/timestamp.jsp")
                                .build();
                        Response timeResponse = chain.proceed(timeRequest);
                        int timeCode = timeResponse.code();
                        if (timeCode == 200) {
                            // 计算时间差
                            String timestamp = timeResponse.body().string().trim();
                            long dTime = System.currentTimeMillis() / 1000 - Long.parseLong(timestamp);
                            SpUtil.setDTime(dTime);
            
                            // 构建新的请求
                            FormBody.Builder builder = new FormBody.Builder();
                            FormBody formBody = (FormBody) request.body();
            
                            List<String> param = new ArrayList<>();
                            for (int i = 0; i < formBody.size(); i++) {
                                String name = formBody.encodedName(i);
                                if (name.equals("timestamp")) {
                                    builder.add("timestamp", timestamp);
                                    param.add(timestamp);
                                } else if (!name.equals("signature")) {
                                    String value = formBody.encodedValue(i);
                                    param.add(value);
                                    builder.add(name, value);
                                }
                            }
            
                            String signature = SignUtil.getSignature(param);
                            builder.add("signature", signature);
            
                            Request newRequest = request.newBuilder().method("POST", builder.build()).build();
                            response = chain.proceed(newRequest);
                        }
                    }
                    return response;
                }
            };
    
            
            OkHttpClient httpClient = new OkHttpClient.Builder()
                    .addInterceptor(interceptor)
                    .build();
```

上述是针对post请求，考虑到这类请求多少post请求就没有区分，实际项目中如果有get请求也会发生时间戳超时问题，必须要做区分。



## Token

token也是这样，往往是APP启动的时候获取一次，保存下来，后面的请求传入这个参数即可。

但是有些情况下，会错过这个获取时机：

​    1、APP启动时手机没有联网，启动以后才联网；

​    2、用户启动APP以后一直没有退出，直到token过期。

对于第一种情况，有人说可以注册广播接收者，监测手机的网络状态，如果启动的时候没有网络则用户联网以后再获取token，这个思路是对的，但是有一种情况是监测不到网络变化的，即用户用安全管家之类的软件禁了APP的网络的情况。

当然，可以提示用户退出APP，重新进入。但是前面说过，这样做不太优雅，而且主流APP也没有发现过这种情况。

综上所述，token验证失败的处理也应该用okhttp的拦截器处理，思路和步骤与时间戳一致，不再重复。



## 关于性能

有人可能会担心性能问题，肯定会有影响，但是影响极其有限，因为官方出品的日志拦截器也是这么做的，看不出有什么影响。