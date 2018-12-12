---
title: Android Handler消息机制学习
date: 2018-11-13 11:01:02
tags:
- Android
- Handler
categories:
- Android
- 源码解析
---

### 1.概述

####   **Handler允许你发送和处理Message，以及和线程相关联的Runnable对象。每一个Handler实例都与一个线程及该线程的MessageQueue相关联。既当你创建一个Handler时，该Handler必须绑定一个线程以及该线程的消息队列，一旦它被创建，它能把messages和runnables传送到message queue，并在它们从message queue中出来的时候执行它们。**

  **Handler主要有两个主要用途：**

> - **在未来的某个时间点调度messages和runnables的执行**
> - **将要在不同线程上执行的操作加入队列**

> **当你的应用程序被创建出来的时候，主线程会专门运行一个message queue来管理最顶级的应用对象(如activities, broadcast receivers，等等)以及它们创建的任何其它窗口。你可以创建你自己的线程，通过Handler来与主线程建立联系**

<!--more-->

### 2.源码分析

#### 2.1 MessageQueue-消息队列

  **MessageQueue是一个通过Looper分发它持有的消息列表的低层级类。Messages没有直接添加到MessageQueue中，而是通过与Looper相关联的Handler对象。**

- Message-消息

> 该类实现了Parcelable接口，你可以把它看作一个数据类

```java
**
 *
 * Defines a message containing a description and arbitrary data object that can be
 * sent to a {@link Handler}.  This object contains two extra int fields and an
 * extra object field that allow you to not do allocations in many cases.
 * 定义包含描述和任意数据对象的消息
 * 发送到Handler。这个对象包含两个额外的int字段和一个
 * 额外的对象字段，允许您在很多情况下不进行分配。
 * <p class="note">While the constructor of Message is public, the best way to get
 * one of these is to call {@link #obtain Message.obtain()} or one of the
 * {@link Handler#obtainMessage Handler.obtainMessage()} methods, which will pull
 * them from a pool of recycled objects.</p>
 * 注意：不要直接使用New Message()创建Message对象，最好的方式是通过Handler.obtainMessage()
 * 方法，它会从对象回收池中拉取该消息对象
 */
public final class Message implements Parcelable {
    /** @hide */
    public static final Object sPoolSync = new Object();
    /**
     * Return a new Message instance from the global pool. Allows us to
     * avoid allocating new objects in many cases.
     * 从全局池返回一个新的消息实例。让我们在很多情况下避免分配新对象。
     */
    public static Message obtain() {
        synchronized (sPoolSync) {
            if (sPool != null) {
                Message m = sPool;
                sPool = m.next;
                m.next = null;
                m.flags = 0; // clear in-use flag
                sPoolSize--;
                return m;
            }
        }
        return new Message();
    }
    **
     * Same as {@link #obtain()}, but sets the value for the target member on the  Message returned.
     * 与obtain()相同，但在返回的消息上设置目标成员的值。
     * @return A Message object from the global pool.
     * 返回来自全局池的消息对象
     */
    public static Message obtain(Handler h) {
        Message m = obtain();
        m.target = h;//接收传递过来的Handler

        return m;
    }
}
```

#### 2.2 Looper-消息轮询器

  **Looper用于为线程轮询messages。线程默认是没有与之相关联的Lopper，我们必须通过Looper.prepare()方法去创建它。示例：**

```java
class LooperThread extends Thread {
    public Handler mHandler;

    public void run() {
        Looper.prepare();

        mHandler = new Handler() {
            public void handleMessage(Message msg) {
                // process incoming messages here
          }
        };

       Looper.loop();
    }
}
```

> - **ThreadLocal提供线程的局部变量，通过它访问线程独立初始化变量的副本。即Looper通过ThreadLocal类来与当前线程进行交互**

```java
//私用构造方法
//quitAllowed是否允许退出
private Looper(boolean quitAllowed) {
    mQueue = new MessageQueue(quitAllowed);//实例化消息队列
    mThread = Thread.currentThread();//获取当前线程
}

/** Initialize the current thread as a looper.
* This gives you a chance to create handlers that then reference
* this looper, before actually starting the loop. Be sure to call
* {@link #loop()} after calling this method, and end it by calling
* {@link #quit()}.
* 初始化当前线程的Looper
* 在它准备轮询之前，给你一个时机点去创建一个Handler并引用它
* 调用该方法后一定记得Looper.loop()方法，并结束的时候调用Looper.quit()方法退出
*/
public static void prepare() {
    prepare(true);
}

// sThreadLocal.get() will return null unless you have called prepare().
//必须先调用Looper.prepare()方法，否则返回空
static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();

private static void prepare(boolean quitAllowed) {
    //每一个线程只能有唯一的一个looper
    if (sThreadLocal.get() != null) {//不为空抛出异常
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));//设置Looper
}

/**
 * Return the Looper object associated with the current thread.  Returns
 * null if the calling thread is not associated with a Looper.
 * 返回与当前线程关联的looper
 */
public static @Nullable Looper myLooper() {
    return sThreadLocal.get();
}

/**
 * Run the message queue in this thread. Be sure to call
 * {@link #quit()} to end the loop.
 * 在当前线程运行消息队友
 */
public static void loop() {
    final Looper me = myLooper();//获取looper
    if (me == null) {//为空则抛出异常
        throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
    }
    final MessageQueue queue = me.mQueue;//获取looper的消息队列

    // Make sure the identity of this thread is that of the local process,
    // and keep track of what that identity token actually is.
    //确保这个线程的标识是本地进程的标识，
    //并跟踪身份令牌的实际情况。
    Binder.clearCallingIdentity();
    final long ident = Binder.clearCallingIdentity();

    // Allow overriding a threshold with a system prop. e.g.
    // adb shell 'setprop log.looper.1000.main.slow 1 && stop && start'
    final int thresholdOverride =
            SystemProperties.getInt("log.looper."
                    + Process.myUid() + "."
                    + Thread.currentThread().getName()
                    + ".slow", 0);

    boolean slowDeliveryDetected = false;

    for (;;) {//无限轮询
        Message msg = queue.next(); // might block
        if (msg == null) { //没有message则不往下执行
            // No message indicates that the message queue is quitting.
            return;
        }

        ***省略代码***
        
        final long dispatchStart = needStartTime ? SystemClock.uptimeMillis() : 0;
        final long dispatchEnd;
        try {
            msg.target.dispatchMessage(msg);//调用Handler的dispatchMessage(msg)方法
            dispatchEnd = needEndTime ? SystemClock.uptimeMillis() : 0;
        } finally {
            if (traceTag != 0) {
                Trace.traceEnd(traceTag);
            }
        }
        //回收可能正在使用的消息。
        //在处理消息队列时，MessageQueue和Looper在内部使用。
        msg.recycleUnchecked();
    }
}

//sThreadLocal.get()
public T get() {
    Thread t = Thread.currentThread();//通过Thread的静态方法获取当前的线程
    //ThreadLocalMap是一个定制的HashMap(),仅适用于维护线程局部值,每一个线程都持有一个ThreadLocalMap对象
    ThreadLocalMap map = getMap(t);//传入当前线程获取当前线程的ThreadLocalMap对象
-------------------------------------------------------------------------------—————————————
    ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }
-------------------------------------------------------------------------------—————————————
    if (map != null) {
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    return setInitialValue();
}
```

#### 2.3 Handler

#### 2.3.1 创建Handler对象

```java
/**
 * Callback interface you can use when instantiating a Handler to avoid
 * having to implement your own subclass of Handler.
 * 如果你不想通过继承Handler来实现，你必须实现Callback接口
 */
public interface Callback {
    /**
     * @param msg A {@link android.os.Message Message} object
     * @return True if no further handling is desired
     */
    public boolean handleMessage(Message msg);
}
//默认构成器
//如果该Handler所绑定的线程没有looper，它将收不到任何messages，并且会抛出异常
public Handler() {
    this(null, false);
}
//默认构造器的Callback为空，所以当我们通过new Handler()来创建实例时，
//我们必须重写Handler的handleMessage(Message msg)方法，即：
companion object {//注意写成静态内部类的形式，避免内存泄漏
    private class MyHandler :Handler(){
        override fun handleMessage(msg: Message?) {
            super.handleMessage(msg)
        }
    }
}
-------------------------------------------------------------------------------—————————————
//主动实现Callback接口，来创建Handler实例
public Handler(Callback callback) {
    this(callback, false);
}
 companion object {
    private val mHandler1:Handler = Handler(object :Handler.Callback{
        override fun handleMessage(msg: Message?): Boolean {
            return true
        }
    })
    或
    private val mHandler2:Handler = Handler(Handler.Callback {
        true
    })

}
-------------------------------------------------------------------------------—————————————
 /**
 * Use the provided {@link Looper} instead of the default one.
 *
 * @param looper The looper, must not be null.
 * //不使用默认的looper
 */
public Handler(Looper looper) {
    this(looper, null, false);
}
//之前我看到某些前辈是通过下面这种方式来创建Handler实例，通过传递主线程的looper
//但这是没必要的，Handler默认就是使用主线程的looper(下面会分析)
//并且源码注释也说了，使用你自己的提供的looper而不是默认的
private val mHandler:Handler = object :Handler(Looper.getMainLooper()){
    override fun handleMessage(msg: Message?) {
        super.handleMessage(msg)
    }
}
-------------------------------------------------------------------------------—————————————
//该构造同上，如果你不想重写handleMessage(msg: Message?)方法，那么你就重写Callback接口
public Handler(Looper looper, Callback callback) {
    this(looper, callback, false);
}
//是否需要异步处理messages，默认是异步的
public Handler(boolean async) {
    this(null, async);
}
//自己提供looper，实现Callback接口，决定是否异步
public Handler(Looper looper, Callback callback, boolean async) {
    mLooper = looper;
    mQueue = looper.mQueue;
    mCallback = callback;
    mAsynchronous = async;
}

-------------------------------------------------------------------------------—————————————
/*
 * Set this flag to true to detect anonymous, local or member classes
 * that extend this Handler class and that are not static. These kind
 * of classes can potentially create leaks.
 * 将此标志设置为true以检测匿名类、本地类或成员类
 * 继承了Handler不是静态的，可能存在内存泄漏
 */
private static final boolean FIND_POTENTIAL_LEAKS = false;

//通过Handler()、Handler(Callback callback)、Handler(boolean async)
//都会调用该构造器
public Handler(Callback callback, boolean async) {
    
    if (FIND_POTENTIAL_LEAKS) {//检查可能存在的内存泄漏，默认是false
        final Class<? extends Handler> klass = getClass();
        if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                (klass.getModifiers() & Modifier.STATIC) == 0) {
            Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                klass.getCanonicalName());
        }
    }

    mLooper = Looper.myLooper();//获取looper
    if (mLooper == null) {//如果为空抛出异常
        throw new RuntimeException(
            "Can't create handler inside thread " + Thread.currentThread()
                    + " that has not called Looper.prepare()");
    }
    mQueue = mLooper.mQueue;//获取Looper中的消息队列
    mCallback = callback;
    mAsynchronous = async;
}
private static void handleCallback(Message message) {
    message.callback.run();//执行Message持有的Runnable
}
/**
 * Handle system messages here.
 * 处理系统Messages
 */
public void dispatchMessage(Message msg) {
    if (msg.callback != null) {//如果Message的Runnable不为空
        handleCallback(msg);调用Handler的handleMessage(msg)方法处理Message
    } else {
        if (mCallback != null) {//如果Handler的Callback不为空
            if (mCallback.handleMessage(msg)) {//调用Callback的handleMessage(msg)方法
                return;
            }
        }
        //否则调用Handler的handleMessage(msg)方法
        handleMessage(msg);
    }
}
```

#### 2.3.2 创建Message对象

```java
public final Message obtainMessage()
{
    return Message.obtain(this);
}
public final Message obtainMessage(int what)
{
    return Message.obtain(this, what);
}
public final Message obtainMessage(int what, Object obj)
{
    return Message.obtain(this, what, obj);
}
public final Message obtainMessage(int what, int arg1, int arg2)
{
    return Message.obtain(this, what, arg1, arg2);
}
public final Message obtainMessage(int what, int arg1, int arg2, Object obj)
{
    return Message.obtain(this, what, arg1, arg2, obj);
}
```

#### 2.3.3 发送Message

```java
/**
 * Causes the Runnable r to be added to the message queue.
 * The runnable will be run on the thread to which this handler is 
 * attached. 
 * 将Runnable添加到消息队列中。
 * runnable将在此Handler所在的线程上运行。
 * 
 * @return Returns true if the Runnable was successfully placed in to the 
 *         message queue.  Returns false on failure, usually because the
 *         looper processing the message queue is exiting.
 * Runnable成功添加到消息队列中返回ture，失败返回false
 */
public final boolean post(Runnable r)
{
   return  sendMessageDelayed(getPostMessage(r), 0);
}
private static Message getPostMessage(Runnable r) {
    Message m = Message.obtain();创建Message对象
    m.callback = r;把Runnale传递给Message
    return m;
}
public final boolean sendMessageDelayed(Message msg, long delayMillis)
{
    if (delayMillis < 0) {//延迟时间小于0，则置为0
        delayMillis = 0;
    }
    return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
}
//延迟发送
public final boolean postDelayed(Runnable r, long delayMillis)
{
    return sendMessageDelayed(getPostMessage(r), delayMillis);
}

public final boolean postDelayed(Runnable r, Object token, long delayMillis)
{
    return sendMessageDelayed(getPostMessage(r, token), delayMillis);
}

public final boolean sendMessageDelayed(Message msg, long delayMillis)
{
    if (delayMillis < 0) {
        delayMillis = 0;
    }
    return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
}

 public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
    MessageQueue queue = mQueue;
    if (queue == null) {
        RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
        Log.w("Looper", e.getMessage(), e);
        return false;
    }
    return enqueueMessage(queue, msg, uptimeMillis);
}

private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
    msg.target = this;//把当前的Handler传递给Message的Handler
    if (mAsynchronous) {//是否异步
        msg.setAsynchronous(true);
    }
    //最终调用MessageQueue的enqueueMessage方法
    return queue.enqueueMessage(msg, uptimeMillis);
}
MessageQueue：
boolean enqueueMessage(Message msg, long when) {
    if (msg.target == null) {//Message的Handler为空，抛出异常
        throw new IllegalArgumentException("Message must have a target.");
    }
    if (msg.isInUse()) {//Message正在使用，抛出异常
        throw new IllegalStateException(msg + " This message is already in use.");
    }

    synchronized (this) {
        if (mQuitting) {//判断是否退出
            IllegalStateException e = new IllegalStateException(
                    msg.target + " sending message to a Handler on a dead thread");
            Log.w(TAG, e.getMessage(), e);
            msg.recycle();//回收Message
            return false;
        }

        msg.markInUse();设置Message是否正在使用的Flag
        msg.when = when;
        Message p = mMessages;
        boolean needWake;//是否需要唤醒
        if (p == null || when == 0 || when < p.when) {
            // New head, wake up the event queue if blocked.
            msg.next = p;
            mMessages = msg;
            needWake = mBlocked;
        } else {
            // Inserted within the middle of the queue.  Usually we don't have to wake
            // up the event queue unless there is a barrier at the head of the queue
            // and the message is the earliest asynchronous message in the queue.
            //插入到队列的中间。通常我们不需要唤醒
            //启动消息队列，除非队列头部有阻碍
            //消息是队列中最早的异步消息。
            needWake = mBlocked && p.target == null && msg.isAsynchronous();
            Message prev;
            for (;;) {//开启无限轮询
                prev = p;
                p = p.next;
                if (p == null || when < p.when) {
                    break;
                }
                if (needWake && p.isAsynchronous()) {
                    needWake = false;
                }
            }
            msg.next = p; // invariant: p == prev.next
            prev.next = msg;
        }

        // We can assume mPtr != 0 because mQuitting is false.
        if (needWake) {
            nativeWake(mPtr);
        }
    }
    return true;
}
```

### 3.总结

![](https://ws1.sinaimg.cn/large/006tNbRwly1fx697hdsfrj31kw0y7e3q.jpg)

