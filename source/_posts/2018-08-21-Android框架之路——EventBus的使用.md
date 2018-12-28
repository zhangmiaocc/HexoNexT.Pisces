---
title: Android框架之路——EventBus的使用
tags:
  - Android
  - EventBus
categories:
  - Android
  - 框架
abbrlink: 6037db63
date: 2018-08-21 14:08:08
---

### **一、简介**

EventBus是由greenrobot 组织贡献的一个Android事件发布/订阅轻量级框架。EventBus是一个Android端优化的publish/subscribe消息总线，简化了应用程序内各组件间、组件与后台线程间的通信。比如请求网络，等网络返回时通过Handler或Broadcast通知UI，两个Fragment之间需要通过Listener通信，这些需求都可以通过EventBus实现。

官网地址：[http://greenrobot.org/eventbus/](https://link.jianshu.com?t=http://greenrobot.org/eventbus/)
翻译：[http://blog.csdn.net/poorkick/article/details/55099311](https://link.jianshu.com?t=http://blog.csdn.net/poorkick/article/details/55099311)

 ![img](https://raw.githubusercontent.com/greenrobot/EventBus/master/EventBus-Publish-Subscribe.png)

 

### **<!--more-->**

### **二、添加依赖**

```
compile 'org.greenrobot:eventbus:3.0.0'
```

 

### **三、解锁技能**

1. **EventBus的三要素**

   -  **Event**：事件，可以是任意类型的对象。
   -  **Subscriber**：事件订阅者，在EventBus3.0之前消息处理的方法只能限定于onEvent、onEventMainThread、onEventBackgroundThread和onEventAsync，他们分别代表四种线程模型。而在EventBus3.0之后，事件处理的方法可以随便取名，但是需要添加一个注解@Subscribe，并且要指定线程模型（默认为POSTING）。
   -  **Publisher**：事件发布者，可以在任意线程任意位置发送事件，直接调用EventBus的post(Object)方法。可以自己实例化EventBus对象，但一般使用EventBus.getDefault()就好了，根据post函数参数的类型，会自动调用订阅相应类型事件的函数。

2. **EventBus的四种线程模型（ThreadMode）**

   -  **POSTING（默认）**：如果使用事件处理函数指定了线程模型为POSTING，那么该事件在哪个线程发布出来的，事件处理函数就会在这个线程中运行，也就是说发布事件和接收事件在同一个线程。在线程模型为POSTING的事件处理函数中尽量避免执行耗时操作，因为它会阻塞事件的传递，甚至有可能会引起应用程序无响应（ANR）。
   -  **MAIN**：事件的处理会在UI线程中执行。事件处理时间不能太长，长了会ANR的。
   -  **BACKGROUND**：如果事件是在UI线程中发布出来的，那么该事件处理函数就会在新的线程中运行，如果事件本来就是子线程中发布出来的，那么该事件处理函数直接在发布事件的线程中执行。在此事件处理函数中禁止进行UI更新操作。
   -  **ASYNC**：无论事件在哪个线程发布，该事件处理函数都会在新建的子线程中执行，同样，此事件处理函数中禁止进行UI更新操作。

3. **使用步骤**

   - **注册**：EventBus.getDefault().register(this);

   - **解注册**（为防止内存泄漏）：EventBus.getDefault().unregister(this);

   - **构造发送消息类**：

     ```
       public class MessageEvent {
           public String name;
           public String password;
       
           public MessageEvent(String name, String password) {
               this.name = name;
               this.password = password;
           }
       }
     ```

   - **发布消息**：EventBus.getDefault().post(new MessageEvent("name","password"));

   - **接收消息**：可以有四种线程模型选择

     ```
       @Subscribe(threadMode = ThreadMode.MAIN)
       public void messageEventBus(MessageEvent event){
           tv_result.setText("name:"+event.name+" passwrod:"+event.password);
       }
     ```

4. **粘性事件**
  ​     之前说的使用方法，都是需要先注册(register)，再post,才能接受到事件；如果你使用postSticky发送事件，那么可以不需要先注册，也能接受到事件，也就是一个延迟注册的过程。
  ​     普通的事件我们通过post发送给EventBus，发送过后之后当前已经订阅过的方法可以收到。但是如果有些事件需要所有订阅了该事件的方法都能执行呢？例如一个Activity，要求它管理的所有Fragment都能执行某一个事件，但是当前我只初始化了3个Fragment，如果这时候通过post发送了事件，那么当前的3个Fragment当然能收到。但是这个时候又初始化了2个Fragment，那么我必须重新发送事件，这两个Fragment才能执行到订阅方法。
  ​     粘性事件就是为了解决这个问题，通过 postSticky 发送粘性事件，这个事件不会只被消费一次就消失，而是一直存在系统中，知道被 removeStickyEvent 删除掉。那么只要订阅了该粘性事件的所有方法，只要被register 的时候，就会被检测到，并且执行。订阅的方法需要添加 sticky = true 属性。

   - **构造发送信息类**：

     ```
       public class StickyEvent {
           public String msg;
       
           public StickyEvent(String msg) {
               this.msg = msg;
           }
       }
     ```

   - **发布消息**：EventBus.getDefault().postSticky(new StickyEvent("我是粘性事件"));

   - **接收消息**：和之前的方法一样，只是多了一个 sticky = true 的属性。

     ```
       @Subscribe(threadMode = ThreadMode.MAIN, sticky = true)
       public void onEvent(StickyEvent event){
           tv_c_result.setText(event.msg);
       }
     ```

   - **注册**：

     ```
       EventBus.getDefault().register(CActivity.this);
     ```

   - **解注册**：

     ```
       EventBus.getDefault().removeAllStickyEvents();
       EventBus.getDefault().unregister(CActivity.class);
     ```

### 

 四、举个栗子

1. **主线程发送事件：**

   - 自定义事件（类似定义JavaBean），包含用户的姓名和密码；

     ```
       public class UserEvent {
           private String name;
           private String password;
       
           public UserEvent() {
           }
       
           public UserEvent(String name, String password) {
               this.name = name;
               this.password = password;
           }
       
           public String getName() {
               return name;
           }
       
           public void setName(String name) {
               this.name = name;
           }
       
           public String getPassword() {
               return password;
           }
       
           public void setPassword(String password) {
               this.password = password;
           }
       
           @Override
           public String toString() {
               return "UserEvent{" +
                       "name='" + name + '\'' +
                       ", password='" + password + '\'' +
                       '}';
           }
       }
     ```

   - 在onCreate方法中注册订阅者，在onDestroy中解注册。

     ```
       public class MainActivity extends AppCompatActivity {
       
           @BindView(R.id.jump)
           Button mJump;
           @BindView(R.id.send)
           Button mSend;
           @BindView(R.id.tv_result)
           TextView mTvResult;
       
           @Override
           protected void onCreate(Bundle savedInstanceState) {
               super.onCreate(savedInstanceState);
               setContentView(R.layout.activity_main);
               ButterKnife.bind(this);
               //注册订阅者
               EventBus.getDefault().register(this);
           }
       
           @OnClick({R.id.jump, R.id.send})
           public void onViewClicked(View view) {
               switch (view.getId()) {
                   case R.id.jump:
                       startActivity(new Intent(MainActivity.this, SecActivity.class));
                       break;
                   case R.id.send:
                       break;
               }
           }
       
           //定义处理接收的方法
           @Subscribe(threadMode = ThreadMode.MAIN)
           public void userEventBus(UserEvent userEvent){
               mTvResult.setText(userEvent.toString());
           }
       
           @Override
           protected void onDestroy() {
               super.onDestroy();
               //注销注册
               EventBus.getDefault().unregister(this);
           }
       }
     ```

   - 在另一个activity中发送事件，让订阅者能够接收；

     ```
       @OnClick({R.id.sendData, R.id.receive})
       public void onViewClicked(View view) {
           switch (view.getId()) {
               case R.id.sendData:
                   //发送事件
                   EventBus.getDefault().post(new UserEvent("Mr.sorrow", "123456"));
                   finish();
                   break;
               case R.id.receive:
                   break;
           }
       }
     ```

   - 实现结果：

![img](https://upload-images.jianshu.io/upload_images/5735230-6ff7db612aeb5c22?imageMogr2/auto-orient/strip%7CimageView2/2/w/286)

2. **发送粘性事件：**

- MainActivity中发送粘性事件；

  ```
    case R.id.send:
            EventBus.getDefault().postSticky(new MessageEvent("粘性事件", "urgent"));
            startActivity(new Intent(MainActivity.this, SecActivity.class));
            break;
  ```

- SecActivity中接受注册并处理；

  ```
    public class SecActivity extends AppCompatActivity {
        @BindView(R.id.sendData)
        Button mSendData;
        @BindView(R.id.receive)
        Button mReceive;
        @BindView(R.id.tv_receive)
        TextView mTvReceive;
    
        @Override
        protected void onCreate(@Nullable Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_sec);
            ButterKnife.bind(this);
        }
    
        @OnClick({R.id.sendData, R.id.receive})
        public void onViewClicked(View view) {
            switch (view.getId()) {
                case R.id.sendData:
                    //发送事件
                    EventBus.getDefault().post(new UserEvent("Mr.sorrow", "123456"));
                    finish();
                    break;
                case R.id.receive:
                    //要接收时开始注册
                    EventBus.getDefault().register(SecActivity.this);
                    break;
            }
        }
    
        //处理事件逻辑
        @Subscribe(threadMode = ThreadMode.MAIN, sticky = true)
        public void receiveEventBus(MessageEvent messageEvent) {
            mTvReceive.setText(messageEvent.toString());
        }
    
        @Override
        protected void onDestroy() {
            super.onDestroy();
            //解注册
            EventBus.getDefault().removeAllStickyEvents();
            EventBus.getDefault().unregister(SecActivity.this);
        }
    }
  ```

- 实现效果

![img](https://upload-images.jianshu.io/upload_images/5735230-7cfdaad8449a9837?imageMogr2/auto-orient/strip%7CimageView2/2/w/274) 

 

 

 






















