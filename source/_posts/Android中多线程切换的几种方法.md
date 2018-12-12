---
title: Android中多线程切换的几种方法
date: 2018-07-22 22:26:54
tags:
- Android 
categories:
- Android
- 代码片段 
---

我们知道，多线程是Android开发中必现的场景，很多原生API和开源项目都有多线程的内容，这里简单总结和探讨一下常见的多线程切换方式。
我们先回顾一下Java多线程的几个基础内容，然后再分析总结一些经典代码中对于线程切换的实现方式。

#### 几点基础

多线程切换，大概可以切分为这样几个内容：如何开启多个线程，如何定义每个线程的任务，如何在线程之间互相通信。

**Thread**
Thread可以解决开启多个线程的问题。
Thread是Java中实现多线程的线程类，每个Thread对象都可以启动一个新的线程，注意是可以启动，也可以不启动新线程：

```java
thread.run();//不启动新线程，在当前线程执行
thread.start();//启动新线程。
```

<!--more-->

另外就是Thread存在线程优先级问题，如果为Thread设置较高的线程优先级，就有机会获得更多的CPU资源，注意这里也是有机会，优先级高的Thread不是必然会先于其他Thread执行，只是系统会倾向于给它分配更多的CPU。
默认情况下，新建的Thread和当前Thread的线程优先级一致。
设置线程优先级有两种方式：

```java
thread.setPriority(Thread.MAX_PRIORITY);//1~10，通过线程设置
Process.setThreadPriority(Process.THREAD_PRIORITY_BACKGROUND);//-20~19，通过进程设置
```

这两种设置方式是相对独立的，在Android中，一般建议通过Process进程设置优先级。

**ThreadPool**
Thread本身是需要占用内存的，开启/销毁过量的工作线程会造成过量的资源损耗，这种场景我们一般会通过对资源的复用来进行优化，针对IO资源我们会做IO复用（例如Http的KeepAlive），针对内存我们会做内存池复用（例如Fresco的内存池），针对CPU资源，我们一般会做线程复用，也就是线程池。
所以，在Android开发中，一般不会直接开启大量的Thread，而是会使用ThreadPool来复用线程。

**Runnable**
Runnable主要解决如何定义每个线程的工作任务的问题。
Runnable是Java中实现多线程的接口，相对Thread而言，Runnable接口更容易扩展（不需要单继承），而且，Thread本身也是一种Runnable：

```java
public class Thread implements Runnable {
```

相比Thread而言，Runnable不关注如何调度线程，只关心如何定义要执行的工作任务，所以在实际开发中，多使用Runnable接口完成多线程开发。

**Callable**
Callable和Runnable基本类似，但是Callable可以返回执行结果。

**线程间通信**
Thread和Runnable能实现切换到另一个线程工作（Runnable需要额外指派工作线程），但它们完成任务后就会退出，并不注重如何在线程间实现通信，所以切换线程时，还需要在线程间通信，这就需要一些线程间通信机制。

**Future**
一般来说，如果要做简单的通信，我们最常用的是通过接口回调来实现。
Future就是这样一种接口，它可以部分地解决线程通信的问题，Future接口定义了done、canceled等回调函数，当工作线程的任务完成时，它会（在工作线程中）通过回调告知我们，我们再采用其他手段通知其他线程。

```java
    mFuture = new FutureTask<MyBizClass>(runnable) {
        @Override
        protected void done() {
            ...//还是在工作线程里
        }
    };
```

**Condition**
Condition其实是和Lock一起使用的，但如果把它视为一种线程间通信的工具，也说的通。
因为，Condition本身定位就是一种多线程间协调通信的工具，Condition可以在某些条件下，唤醒等待线程。

```java
 Lock lock = new ReentrantLock();
 Condition notFull  = lock.newCondition(); //定义Lock的Condition
...
   while (count == items.length)
                notFull.await();//等待condition的状态
...
   notFull.signal();//达到condition的状态
```

**Handler**
其实，最完整的线程间通信机制，也是我们最熟悉的线程间通信机制，莫过于Handler通信机制，Handler利用线程封闭的ThreadLocal维持一个消息队列，Handler的核心是通过这个消息队列来传递Message，从而实现线程间通信。

#### AsyncTask的多线程切换

回顾完多线程的几个基础概念，先来看看简单的多线程切换，Android自带的AsyncTask。
AsyncTask主要在doInBackground函数中定义工作线程的工作内容，在其他函数中定义主线程的工作内容，例如onPostExecute，这里面必然涉及两个问题：
1.如何实现把doInBackground抛给工作线程
2.如何实现把onPostExecute抛给主线程
其实非常简单，我们先看第一个

**1.如何实现把doInBackground抛给工作线程**
在使用AsyncTask时，我们一般会创建一个基于AsyncTask的扩展类或匿名类，在其中实现几个抽象函数，例如：

```java
    private class MyTask extends AsyncTask<String, Object, Long> {
        @Override
        protected void onPreExecute() {... }
        @Override
        protected Long doInBackground(String... params) {... }
        @Override
        protected void onProgressUpdate(Object... values) {... }
        @Override
        protected void onPostExecute(Long aLong) {... }
        @Override
        protected void onCancelled() {... }
```

然后，我们会实例化这个AsyncTask：

```java
MyTask mTask = new MyTask();
```

在AsyncTask源码中，我们看到，构造函数里会创建一个WorkerRunnable：

```java
    public AsyncTask() {
        mWorker = new WorkerRunnable<Params, Result>() {//这是一个Callable
            public Result call() throws Exception {
                ...
                    result = doInBackground(mParams);//在工作线程中执行
                    ...
```

WorkerRunnable实际上是一个Callable对象，所以，doInBackground是被包在一个Callable对象中了，这个Callable还会被继续包装，最终被交给一个线程池去执行：

```java
Runnable mActive;
...
if ((mActive = mTasks.poll()) != null) {
    THREAD_POOL_EXECUTOR.execute(mActive);//交给线程池执行
}
```

梳理一下，大致过程为：
定义doInBackground-->被一个Callable调用-->层层包为一个Runnable-->交给线程池执行。
这样就解决了第一个问题，如何实现把doInBackground抛给工作线程。
我们再来看第二个问题。

**2.如何实现把onPostExecute抛给主线程**
首先，我们要知道工作任务何时执行完毕，就需要在工作完成时触发一个接口回调，也就是前面说过的Future，还是看AsyncTask源码：

```java
public AsyncTask() {
        mWorker = new WorkerRunnable<Params, Result>() {
            public Result call() throws Exception {
                ...
        };

        mFuture = new FutureTask<Result>(mWorker) {
            @Override
            protected void done() {//Future的回调
                try {
                    postResultIfNotInvoked(get());//get()是FutureTask接口函数
                ...
            }
        };
    }
```

这样，我们就知道可以处理onPostExecute函数了，但是，我们还需要把它抛给主线程，主要源码如下：

```java
    //mWorker、mFuture和都会指向postResult函数
    private Result postResult(Result result) {
        @SuppressWarnings("unchecked")
        Message message = getHandler().obtainMessage(MESSAGE_POST_RESULT,
                new AsyncTaskResult<Result>(this, result));
        message.sendToTarget();
        return result;
    }
    //getHandler()会指向InternalHandler
    private static class InternalHandler extends Handler {
        public InternalHandler() {
            super(Looper.getMainLooper());//指向MainLooper
        }
        @SuppressWarnings({"unchecked", "RawUseOfParameterizedType"})
        @Override
        public void handleMessage(Message msg) {
            AsyncTaskResult<?> result = (AsyncTaskResult<?>) msg.obj;
            switch (msg.what) {
                case MESSAGE_POST_RESULT:
                    // There is only one result
                    result.mTask.finish(result.mData[0]);//通过handler机制，回到主线程，调用finish函数
       ...
    }
    //在Handler中，最终会在主线程中调用finish
    private void finish(Result result) {
        if (isCancelled()) {
            onCancelled(result);
        } else {
            onPostExecute(result);//调用onPostExecute接口函数
        }
        mStatus = Status.FINISHED;
    }
```

从源码可以看到，其实AsyncTask还是通过Handler机制，把任务抛给了主线程。

总体来说，AsyncTask的多线程任务是通过线程池实现的工作线程，在完成任务后利用Future的done回调来通知任务完成，并通过handler机制通知主线程去执行onPostExecute等回调函数。

#### EventBus的多线程切换

EventBus会为每个订阅事件注册一个目标线程，所以需要从发布事件的线程中，根据注册信息，实时切换到目标线程中，所以，这是个很典型的多线程切换场景。
根据EventBus源码，多线程切换的主要判断代码如下：

```java
        switch (subscription.subscriberMethod.threadMode) {
            case POSTING:
                invokeSubscriber(subscription, event);//直接在当前线程执行
                break;
            case MAIN:
                if (isMainThread) {
                    invokeSubscriber(subscription, event);//在当前主线程执行
                } else {
                    mainThreadPoster.enqueue(subscription, event);//当然不是主线程，交给主线程执行
                }
                break;
            case BACKGROUND:
                if (isMainThread) {
                    backgroundPoster.enqueue(subscription, event);//当前线程为主线程，交给工作线程
                } else {
                    invokeSubscriber(subscription, event);//直接在当前工作线程执行
                }
                break;
            case ASYNC:
                asyncPoster.enqueue(subscription, event);//异步执行
                break;
            default:
                throw new IllegalStateException("Unknown thread mode: " + subscription.subscriberMethod.threadMode);
        }
```

所以，在EventBus里，如果需要做线程间切换，主要是抛给不同的任务队列，实现线程间切换。
从任务队列判断，切换目标包括主线程Poster、backgroundPoster和asyncPoster这样三种。
我们先看任务队列的设计：

**任务队列**
因为EventBus不能判断有哪些任务会并行，所以它采用了队列的设计，多线程任务（EventBus的事件）会先进入队列，然后再处理队列中的工作任务，这是典型的生产--消费场景。
主线程Poster、backgroundPoster和asyncPoster都是任务队列的不同实现。

**主线程Poster**
负责处理主线程的mainThreadPoster是Handler的子类：

```java
final class HandlerPoster extends Handler {
...
    void enqueue(Subscription subscription, Object event) {
        ...
        synchronized (this) {//因为主线程只有一个，需要线程安全
            queue.enqueue(pendingPost);
            ...
                if (!sendMessage(obtainMessage())) {//作为handler发送消息
                 ...
    //在主线程中处理消息
    @Override
    public void handleMessage(Message msg) {
         ...
}
```

从源码可以看出，这个Poster其实是一个Handler，它采用了哪个线程的消息队列，就决定了它能和哪个线程通信，我们确认一下：

```java
   EventBus(EventBusBuilder builder) {
        ...
        mainThreadPoster = new HandlerPoster(this, Looper.getMainLooper(), 10);//获取主线程的MainLooper
```

所以，EventBus是扩展了一个Handler，作为主线程的Handler，通过Handler消息机制实现的多线程切换。
当然，这个Handler本事，又多了一层queue。

**backgroundPoster和asyncPoster**
backgroundPoster和asyncPoster其实都是使用了EventBus的线程池，默认是个缓存线程池：

```java
    private final static ExecutorService DEFAULT_EXECUTOR_SERVICE = Executors.newCachedThreadPool();
```

所以，backgroundPoster和asyncPoster都是把任务交给线程池处理，这样实现的多线程切换。
不过，backgroundPoster和asyncPoster也有一些不同，我们知道，在newCachedThreadPool中，最大线程数就是Integer的最大值，相当于不设上限，所以可以尽可能多的启动线程，asyncPoster就是这样做的，enqueu和run都没做同步，为每个事件单独开启新线程处理。
而在backgroundPoster中，可以尽量复用线程，主要方法是在run的时候，做个1秒的等待：

```
    @Override
    public void run() { 
        ...
        PendingPost pendingPost = queue.poll(1000);//允许等待1秒
```

因为做了这一秒的挂起等待，在enqueue和run时，都需要用synchronized (this) 来确保线程安全。

另外，其实这里面还有个很重要的用法，就是Executors.newCachedThreadPool()中的SynchronousQueue：

```java
    public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());//用于辅助线程切换的阻塞队列
    }
```

这个SynchronousQueue，在OkHttp中也使用了：

```java
  //okhttp3.Dispatcher源码
  public synchronized ExecutorService executorService() {
    if (executorService == null) {
      executorService = new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60, TimeUnit.SECONDS,
          new SynchronousQueue<Runnable>(), Util.threadFactory("OkHttp Dispatcher", false));//用于辅助线程切换的阻塞队列
    }
    return executorService;
  }
```

SynchronousQueue与普通队列不同，不是数据等线程，而是线程等数据，这样每次向SynchronousQueue里传入数据时，都会立即交给一个线程执行，这样可以提高数据得到处理的速度。

总的来看，EventBus还是采用线程池实现工作线程，采用handler机制通知到主线程。不同在于，它采用的queue的队列方式来管理所有的跨线程请求，而且它利用了SynchronousQueue阻塞队列来辅助实现线程切换。

#### RxJava的多线程切换

其实在多线程管理这方面，RxJava的线程管理能力是非常令人赞叹的。
RxJava的主要概念是工作流，它可以把一序列工作流定义在一个线程类型上：

```java
myWorkFlow.getActResponse(myParam)
                .subscribeOn(Schedulers.io())//指定线程
                .xxx//其他操作
```

这个构建工作流的过程其实挺复杂的，不过如果我们只看线程操作这部分，其实流程非常清晰，我们追踪一下subscribeOn的源码（RxJava2）：

```java
    //进入subscribeOn
    public final Flowable<T> subscribeOn(@NonNull Scheduler scheduler) {
        ObjectHelper.requireNonNull(scheduler, "scheduler is null");
        return subscribeOn(scheduler, !(this instanceof FlowableCreate));
    }
    //继续进入subscribeOn
    public final Flowable<T> subscribeOn(@NonNull Scheduler scheduler, boolean requestOn) {
        ObjectHelper.requireNonNull(scheduler, "scheduler is null");
        return RxJavaPlugins.onAssembly(new FlowableSubscribeOn<T>(this, scheduler, requestOn));
    }
```

然后，进入FlowableSubscribeOn类

```java
    //进入FlowableSubscribeOn类
        public FlowableSubscribeOn(Flowable<T> source, Scheduler scheduler, boolean nonScheduledRequests) {
       ...
        this.scheduler = scheduler;
        ...
    }

    @Override
    public void subscribeActual(final Subscriber<? super T> s) {
        Scheduler.Worker w = scheduler.createWorker();//根据参数值，如Schedulers.io()创建worker
        final SubscribeOnSubscriber<T> sos = new SubscribeOnSubscriber<T>(s, w, source, nonScheduledRequests);//根据worker创建SubscribeOnSubscriber
        s.onSubscribe(sos);
        w.schedule(sos);
    }
```

这个SubscribeOnSubscriber是个内部类：

```java
        SubscribeOnSubscriber(Subscriber<? super T> actual, Scheduler.Worker worker, Publisher<T> source, boolean requestOn) {
            ...
            this.worker = worker;
            ...
          }
...
        void requestUpstream(final long n, final Subscription s) {
            ...
                worker.schedule(new Request(s, n));//worker会安排如何执行runnable（Request是一个runnable）
            ...
        }
```

而这个worker，其实就是我们输入的线程参数，如Schedulers.io()，这个io是这样定义的：

```java
//io.reactivex.schedulers.Schedulers源码
    static {
        SINGLE = RxJavaPlugins.initSingleScheduler(new SingleTask());

        COMPUTATION = RxJavaPlugins.initComputationScheduler(new ComputationTask());

        IO = RxJavaPlugins.initIoScheduler(new IOTask());

        TRAMPOLINE = TrampolineScheduler.instance();

        NEW_THREAD = RxJavaPlugins.initNewThreadScheduler(new NewThreadTask());
    }
...
    static final class IOTask implements Callable<Scheduler> {
        @Override
        public Scheduler call() throws Exception {
            return IoHolder.DEFAULT;
        }
    }

    static final class NewThreadTask implements Callable<Scheduler> {
        @Override
        public Scheduler call() throws Exception {
            return NewThreadHolder.DEFAULT;
        }
    }

    static final class SingleTask implements Callable<Scheduler> {
        @Override
        public Scheduler call() throws Exception {
            return SingleHolder.DEFAULT;
        }
    }

    static final class ComputationTask implements Callable<Scheduler> {
        @Override
        public Scheduler call() throws Exception {
            return ComputationHolder.DEFAULT;
        }
    }
...
    static final class SingleHolder {
        static final Scheduler DEFAULT = new SingleScheduler();
    }

    static final class ComputationHolder {
        static final Scheduler DEFAULT = new ComputationScheduler();
    }

    static final class IoHolder {
        static final Scheduler DEFAULT = new IoScheduler();
    }

    static final class NewThreadHolder {
        static final Scheduler DEFAULT = new NewThreadScheduler();
    }
```

这里的IO，最终会指向一个Scheduler，如IoScheduler：

```java
//io.reactivex.internal.schedulers.IoScheduler源码
...
    static final class EventLoopWorker extends Scheduler.Worker {//Scheduler.Worker的实现类
        ...
        @NonNull
        @Override
        public Disposable schedule(@NonNull Runnable action, long delayTime, @NonNull TimeUnit unit) {
            if (tasks.isDisposed()) {
                // don't schedule, we are unsubscribed
                return EmptyDisposable.INSTANCE;
            }

            return threadWorker.scheduleActual(action, delayTime, unit, tasks);//交给线程池
        }
```

这样，Scheculer中的具体任务就交给了某个线程池来处理。

需要特别说明的是，RxJava中调用Android主线程(AndroidSchedulers.mainThread)，其实还是使用了Handler机制：

```java
public final class AndroidSchedulers {
    ...
        static final Scheduler DEFAULT = new HandlerScheduler(new Handler(Looper.getMainLooper()));
```

这个HandlerScheduler其实就是实现了Scheduler和Scheduler.Worker内部类。

```java
final class HandlerScheduler extends Scheduler {
private final Handler handler;

HandlerScheduler(Handler handler) {
    this.handler = handler;
}
private static final class HandlerWorker extends Worker {
    ...
    @Override
    public Disposable schedule(Runnable run, long delay, TimeUnit unit) {
        ...
        handler.sendMessageDelayed(message, Math.max(0L, unit.toMillis(delay)));
```

总的来看，RxJava的多线程切换其实是利用了Scheculer.Worker这个内部类，把任务交给Scheculer的Worker去做，而这个Scheculer的Worker是根据定义的线程来实现了不同的线程池，其实还是交给线程池去处理了。
至于主线程，RxJava也是使用了Handler机制。

#### 总结

小小总结一下，基本上来说，Android中的多线程切换，主要使用Runnable和Callable来定义工作内容，使用线程池来实现异步并行，使用Handler机制来通知主线程，有些场景下会视情况需要，使用Future的接口回调，使用SynchronousQueue阻塞队列等。

原文链接：[mp.weixin.qq.com](https://link.juejin.im/?target=https%3A%2F%2Fmp.weixin.qq.com%2Fs%3F__biz%3DMzAxMTg2MjA2OA%3D%3D%26mid%3D2649842586%26idx%3D1%26sn%3Da1b16a9ccf5a81b6c8ab26daf209f734%26chksm%3D83bf6ac1b4c8e3d78d8c0c3f9a8548e0d28fcd6a7668b10ef5bf88ef1a18e3538da0a7a82376%26mpshare%3D1%26scene%3D1%26srcid%3D07200em5TTsL1uFk7QXSdIHm%26key%3Daf4929c97b407)