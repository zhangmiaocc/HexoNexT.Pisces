---
title: Android Jetpack ViewModel
tags:
  - Kotlin
  - Jetpack
categories:
  - Android
  - Jetpack
abbrlink: 837f0209
date: 2020-10-19 15:43:53
---

[官方文档](https://developer.android.google.cn/topic/libraries/architecture/viewmodel)

# ViewModel概览

`ViewModel`类旨在以注重生命周期的方式存储和管理界面相关的数据。ViewModel类让数据可在发生屏幕旋转等配置更改后继续存在。

Android 框架可以管理界面控制器（如 Activity 和 Fragment）的生命周期。Android 框架可能会决定销毁或重新创建界面控制器，以响应完全不受您控制的某些用户操作或设备事件。

如果系统销毁或重新创建界面控制器，则存储在其中的任何临时性界面相关数据都会丢失。例如，应用的某个 Activity 中可能包含用户列表。因配置更改而重新创建 Activity 后，新 Activity 必须重新提取用户列表。对于简单的数据，Activity 可以使用 `onSaveInstanceState()` 方法从 `onCreate()` 中的捆绑包恢复其数据，但此方法仅适合可以序列化再反序列化的少量数据，而不适合数量可能较大的数据，如用户列表或位图。

另一个问题是，界面控制器经常需要进行异步调用，这些调用可能需要一些时间才能返回结果。界面控制器需要管理这些调用，并确保系统在其销毁后清理这些调用以避免潜在的内存泄露。此项管理需要大量的维护工作，并且在因配置更改而重新创建对象的情况下，会造成资源的浪费，因为对象可能需要重新发出已经发出过的调用。

诸如 Activity 和 Fragment 之类的界面控制器主要用于显示界面数据、对用户操作做出响应或处理操作系统通信（如权限请求）。如果要求界面控制器也负责从数据库或网络加载数据，那么会使类越发膨胀。为界面控制器分配过多的责任可能会导致单个类尝试自己处理应用的所有工作，而不是将工作委托给其他类。以这种方式为界面控制器分配过多的责任也会大大增加测试的难度。

从界面控制器逻辑中分离出视图数据所有权的做法更易行且更高效。

<!--more-->

# 实现ViewModel

架构组件为界面控制器提供了 `ViewModel` 辅助程序类，该类负责为界面准备数据。 在配置更改期间会自动保留 `ViewModel` 对象，以便它们存储的数据立即可供下一个 Activity 或 Fragment 实例使用。例如，如果您需要在应用中显示用户列表，请确保将获取和保留该用户列表的责任分配给 `ViewModel`，而不是 Activity 或 Fragment，如以下示例代码所示：

```java
    public class MyViewModel extends ViewModel {
        private MutableLiveData<List<User>> users;
        public LiveData<List<User>> getUsers() {
            if (users == null) {
                users = new MutableLiveData<List<User>>();
                loadUsers();
            }
            return users;
        }

        private void loadUsers() {
            // Do an asynchronous operation to fetch users.
        }
    }
    
123456789101112131415
```

然后，您可以从 Activity 访问该列表，如下所示：

```java
    public class MyActivity extends AppCompatActivity {
        public void onCreate(Bundle savedInstanceState) {
            // Create a ViewModel the first time the system calls an activity's onCreate() method.
            // Re-created activities receive the same MyViewModel instance created by the first activity.

            MyViewModel model = ViewModelProviders.of(this).get(MyViewModel.class);
            model.getUsers().observe(this, users -> {
                // update UI
            });
        }
    }
    
123456789101112
```

如果重新创建了该 Activity，它接收的 `MyViewModel` 实例与第一个 Activity 创建的实例相同。当所有者 Activity 完成时，框架会调用 `ViewModel` 对象的 `onCleared()` 方法，以便它可以清理资源。

> 注意：ViewModel 绝不能引用视图、Lifecycle 或可能存储对 Activity 上下文的引用的任何类。

`ViewModel` 对象存在的时间比视图或 `LifecycleOwners` 的特定实例存在的时间更长。这还意味着，您可以更轻松地编写涵盖 `ViewModel` 的测试，因为它不了解视图和 `Lifecycle` 对象。`ViewModel` 对象可以包含 `LifecycleObservers`，如 `LiveData` 对象。但是，`ViewModel` 对象绝不能观察对生命周期感知型可观察对象（如 `LiveData` 对象）的更改。 如果 `ViewModel` 需要 `Application` 上下文（例如，为了查找系统服务），它可以扩展 `AndroidViewModel` 类并设置用于接收 `Application` 的构造函数，因为 `Application` 类会扩展 `Context`。

# ViewModel的生命周期

`ViewModel` 对象存在的时间范围是获取 `ViewModel` 时传递给 `ViewModelProvider` 的 `Lifecycle`。`ViewModel` 将一直留在内存中，直到限定其存在时间范围的 `Lifecycle` 永久消失：对于 Activity，是在 Activity 完成时；而对于 Fragment，是在 Fragment 分离时。

图 1 说明了 Activity 经历屏幕旋转而后结束的过程中所处的各种生命周期状态。该图还在关联的 Activity 生命周期的旁边显示了 `ViewModel` 的生命周期。此图表说明了 Activity 的各种状态。这些基本状态同样适用于 Fragment 的生命周期。
![图1](https://img-blog.csdnimg.cn/20200323185652422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwODMzNzkw,size_16,color_FFFFFF,t_70#pic_center)
您通常在系统首次调用 Activity 对象的 `onCreate()` 方法时请求 `ViewModel`。系统可能会在 Activity 的整个生命周期内多次调用 `onCreate()`，如在旋转设备屏幕时。`ViewModel` 存在的时间范围是从您首次请求 `ViewModel` 直到 Activity 完成并销毁。

# 在 Fragment 之间共享数据

Activity 中的两个或更多 Fragment 需要相互通信是一种很常见的情况。想象一下主从 Fragment 的常见情况，假设您有一个 Fragment，在该 Fragment 中，用户从列表中选择一项，还有另一个 Fragment，用于显示选定项的内容。这种情况不太容易处理，因为这两个 Fragment 都需要定义某种接口描述，并且所有者 Activity 必须将两者绑定在一起。此外，这两个 Fragment 都必须处理另一个 Fragment 尚未创建或不可见的情况。

可以使用 `ViewModel` 对象解决这一常见的难点。这两个 Fragment 可以使用其 Activity 范围共享 ViewModel 来处理此类通信，如以下示例代码所示：

```java
    public class SharedViewModel extends ViewModel {
        private final MutableLiveData<Item> selected = new MutableLiveData<Item>();

        public void select(Item item) {
            selected.setValue(item);
        }

        public LiveData<Item> getSelected() {
            return selected;
        }
    }

    public class MasterFragment extends Fragment {
        private SharedViewModel model;
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            model = ViewModelProviders.of(getActivity()).get(SharedViewModel.class);
            itemSelector.setOnClickListener(item -> {
                model.select(item);
            });
        }
    }

    public class DetailFragment extends Fragment {
        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            SharedViewModel model = ViewModelProviders.of(getActivity()).get(SharedViewModel.class);
            model.getSelected().observe(this, { item ->
               // Update the UI.
            });
        }
    }
    
123456789101112131415161718192021222324252627282930313233
```

此方法具有以下优势：

- Activity 不需要执行任何操作，也不需要对此通信有任何了解。
- 除了 SharedViewModel 约定之外，Fragment 不需要相互了解。如果其中一个 Fragment 消失，另一个 Fragment 将继续照常工作。
- 每个 Fragment 都有自己的生命周期，而不受另一个 Fragment 的生命周期的影响。如果一个 Fragment 替换另一个 Fragment，界面将继续工作而没有任何问题。

# 将加载器替换为 ViewModel

诸如 `CursorLoader` 之类的加载器类经常用于使应用界面中的数据与数据库保持同步。您可以将 `ViewModel` 与一些其他类一起使用来替换加载器。使用 `ViewModel` 可将界面控制器与数据加载操作分离，这意味着类之间的强引用更少。

在使用加载器的一种常见方法中，应用可能会使用 `CursorLoader` 观察数据库的内容。当数据库中的值发生更改时，加载器会自动触发数据的重新加载并更新界面：
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20201019154523.png)
`ViewModel` 与 `Room` 和 `LiveData` 一起使用可替换加载器。`ViewModel` 确保数据在设备配置更改后仍然存在。`Room` 在数据库发生更改时通知 `LiveData`，`LiveData` 进而使用修订后的数据更新界面。
![](https://raw.githubusercontent.com/zhangmiaocc/blogImageResource/master/img/20201019154533.png)

# 示例

[官方示例](https://github.com/android/architecture-components-samples/tree/master/BasicSample)