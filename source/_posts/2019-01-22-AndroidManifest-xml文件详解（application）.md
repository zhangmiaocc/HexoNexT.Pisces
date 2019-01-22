---
title: AndroidManifest.xml文件详解（application）
tags:
  - Android
  - Android Tips
categories:
  - Android
  - Android Tips
abbrlink: 6f32c165
date: 2019-01-22 12:58:42
---

**语法（SYNATX）：**
```xml
<applicationandroid:allowTaskReparenting=["true" | "false"]
             android:backupAgent="string"
             android:debuggable=["true" | "false"]
             android:description="string resource"
             android:enabled=["true" | "false"]
             android:hasCode=["true" | "false"]
             android:hardwareAccelerated=["true" | "false"]
             android:icon="drawable resource"
             android:killAfterRestore=["true" | "false"]
             android:label="string resource"
             android:logo="drawable resource"
             android:manageSpaceActivity="string"
             android:name="string"
             android:permission="string"
             android:persistent=["true" | "false"]
             android:process="string"
             android:restoreAnyVersion=["true" | "false"]
             android:taskAffinity="string"
             android:theme="resource or theme"
             android:uiOptions=["none" | "splitActionBarWhenNarrow"] >
    . . .
</application>
```
<!--more-->

被包含于（CONTAINED IN）：**

```
<manifest>
```
**能够包含的元素（CAN CONTAIN）：**
```
<activity>

<activity-alias>

<service>

<receiver>

<provider>

<meta-data>
```
**说明（DESCRIPTION）：**

这个元素用于应用程序的 声明。它包含了每个应用程序组件所声明的子元素，并且还有能够影响所有组件的属性。其中的很多属性（如icon、label、permission、 process、taskAffinity和allowTaskReparenting）会给组件元素中对应的属性设置默认值。其他的给是应用程序整体设 置的值（如debuggable、enabled、description、allowClearUserData），并且这些属性值不能被组件的属性所 覆盖。

**属性（ATTRIBUTES）：**

**Android:allowTaskReparenting**

当一个与当前任务有亲缘 关系的任务被带到前台时，用这个属性来指定应用程序中定义的Activity能否从他们当前的任务中转移到这个有亲缘关系的任务中。如果设置为true， 则能够转移，如果设置为false，则应用程序中的Activity必须保留在它们所在的任务中。默认值是false。
```
<activity>元素有它们自己的allowTaskReparenting属性，它能够覆盖<application>元素中的设置。
```


- `android:allowBackup`

  Whether to allow the application to participate in the backup and restore infrastructure. If this attribute is set to false, no backup or restore of the application will ever be performed, even by a full-system backup that would otherwise cause all application data to be saved via adb. The default value of this attribute is true.

  是否允许备份应用的数据，默认是true,当备份数据的时候，它的数据会被备份下来。如果设为false，那么绝对不会备份应用的数据，即使是备份整个系统。

 

**android:backupAgent**

这个属性用于定义应用程 序备份代理的实现类的名称，这个类是BackupAgent类的一个子类。它的属性值应该是完整的Java类的名称 （如，com.example.project.MyBackupAgent）。但是，也可以使用用”.”符号开头的简称 （如，.MyBackupAgent），系统会把<manifest>元素中指定的包名追加到”.”符号的前面。

**android:debuggable**

这个属性用于指定应用程序是否能够被调试，即使是以用户模式运行在设备上的时候。如果设置为true，则能够被调试，否则不能调试，默认值是false。

**android:description**

这个属性用于定义应用程序相关的用户可读文本，它要比应用程序标签更长、更详细。它的的值必须被设置成一个字符串资源的引用。跟label属性不一样，label属性可以使用原生的字符串。这个属性没有默认值。

**android:enabled**

这个属性用于指定 Android系统能否实例化应用程序组件。如果设置为true，这个可以实例化其组件，否则不能够实例化。如果这个属性被设置为true，那么就会使用 每个组件自己enabled属性的设置来判断其是否能够被实例化。如果这个属性被设置为false，它会覆盖其所有组件自己指定的值，应用程序中的所有组 件都会被禁用。

默认值是true。

**android:hasCode**

这个属性用于设置应用程序是否包含了代码，如果设置为true，则包含代码，否则不包含任何代码。在这个属性被设置为false的时候，系统在加载组件的时候不会试图加载任何应用程序的代码。默认值是true。

如果应用程序没有使用任何应用内置组件类以外的组件，那么这个应用程序就不会有任何自己的代码，像使用AliasActivity类的Activity，是很少发生的。

**android:hardwareAccelerated**

这个属性用于设置能够给应用程序中的所有Activity和View对象启用硬件加速渲染。如果设置为true，则应该启用，如果设置为false，则不会启用。默认值是false。

从Android3.0 开始，应用程序可以使用硬件加速的OpenGL渲染器，来改善很多共同的2D图形操作的性能。当硬件加速渲染被启动的时候，在Canvas、Paint、 Xfermode、ColorFilter、Shader和Camera中的大多数操作都会被加速。这样会使动画、滚动更加平滑，并且会改善整体的响应效 果，即使应用程序没有明确的使用框架的OpenGL类库。

要注意的是，不是所有的OpenGL 2D操作都会被加速。如果启用了硬件加速渲染器，就要对应用程序进行测试，以确保使用渲染器时不发生错误。

**android:icon**

这个属性用于设置应用程 序的整个图标，以及每个应用组件的默认图标。对于<activity>、<activity- alias>、<service>、<service>、<receiver> 和<provider>元素，请看它们各自的icon属性。

设置这个属性时，必须要引用一个包含图片的可绘制资源（例如，“@drawable/icon”）。没有默认的图标。

**android:killAfterRestore**

这个属性用于指定在全系统的恢复操作期间，应用的设置被恢复以后，对应的问题程序是否应该被终止。单包恢复操作不会导致应用程序被关掉。全系统的复原操作通常只会发生一次，就是在电话被首次建立的时候。第三方应用程序通常不需要使用这个属性。

默认值是true，这意味着在全系统复原期间，应用程序完成数据处理之后，会被终止。

 

- `android:largeHeap`

  Whether your application's processes should be created with a large Dalvik heap. This applies to all processes created for the application. It only applies to the first application loaded into a process; if you're using a shared user ID to allow multiple applications to use a process, they all must use this option consistently or they will have unpredictable results.Most apps should not need this and should instead focus on reducing their overall memory usage for improved performance. Enabling this also does not guarantee a fixed increase in available memory, because some devices are constrained by their total available memory.To query the available memory size at runtime, use the methods `getMemoryClass()` or `getLargeMemoryClass()`.无论您的应用程序的进程应该是一个多大的Dalvik堆。这适用于为应用程序创建的所有进程。它只适用于加载到进程中的第一个应用程序，如果你使用一个共享的用户身份证，允许多个应用程序使用一个进程，他们都必须使用此选项一致或他们将有不可预测的结果。大多数应用程序不应该需要这个，而应该把重点放在减少对性能的整体内存使用上。启用此也不保证可用内存的固定增加，因为一些设备被其总可用内存限制。查询可用的内存大小在运行时，使用的方法getmemoryclass()或getlargememoryclass()。  **android:label**

这个属性用于设置应用程 序整体的用户可读的标签，并也是每个应用程序组件的默认标签。对于<activity>、<activity- alias>、<service>、<receiver>和<provider>元素，请看它们各自的 label属性。

设置这个属性值时，应该引用一个字符串资源。以便它能够跟用户界面中的其他字符串一样能够被本地化。但是为了应用程序开发的便利，也能够用原生的字符串来设置。

**android:logo**

这个属性用于给整个应用程序设置一个Logo，而且它也是所有Activity的默认Logo。

设置这个属性时，必须要引用一个包含图片的可绘制资源（如：“@drawable/logo”）。没有默认的Logo。

**android:manageSpaceActivity**

这个属性定义了一个完整的Activity子类的名字，系统能够把这个名字加载到由用户管理被应用程序所占用的设备上的内存。这个Activity也应该用<activity>元素来声明。

**android:name**

这整个属性用完整的Java类名，给应用程序定义了一个Application子类的实现。当应用程序进程被启动时，这个类在其他任何应用程序组件被实例化之前实例化。

这个子类实现是可选的，大多数应用程序不需要一个子类的实现。如果没有实现自己的子类，Android系统会使用基本的Application类的一个实例。

**android:permission**

这个属性定义了一个权限，为了跟应用程序进行交互，客户端必须要有这个权限。这个属性是为给所有的应用程序组件设置权限提供了便利的方法。它能够被独立组件所设置的permission属性所覆盖。

**android:persistent**

这个属性用户设置应用程序是否应该时刻保持运行状态，如果设置为true，那么就保持，否则不保持。默认值是false。普通的应用程序不应该设置这个属性，持久运行模式仅用于某些系统级的应用程序。

**android:process**

这个属性用于定义一个进程名称，应用程序的所有组件都应该运行在这个进程中。每个组件都能够用它自己process属性的设置来覆盖这个<application>元素中的设置。

默认情况下，当应用程序的第一个组件需要运行时，Android系统就会给这个应用程序创建一个进程。然后，应用中的所有组件都运行在这个进程中。默认的进程名是跟<manifest>元素中设置的包名进行匹配的。

通过设置这个属性，能够跟另外一个应用程序共享一个进程名，能够把这两个应用程序中的组件都安排到同一个进程中运行---但是仅限于这两个应用程序共享一个用户ID，并且带有相同的数字证书。

如果这个进程名称用“：”开头，那么在需要的时候，就会给应用程序创建一个新的、私有的进程。如果进程名用小写字符开头，就会用这个名字创建一个全局的进程，这个全局的进程能够被其他应用程序共享，从而减少资源的使用。

**android:restoreAnyVersion**

设置这个属性表示应用程序准备尝试恢复任何备份的数据集，即使备份比设备上当前安装的应用程序的版本要新。这个属性设置为true，即使是在版本不匹配而产生数据兼容性提示的时候，也会允许备份管理来恢复备份的数据，所以要谨慎使用。

这个属性的默认值是false。

**android:taskAffinity**

这个属性给应用的所有的Activity设置了一个亲缘关系名，除了那些用它们自己的taskAffinity属性设置不同亲缘关系的组件。

默认情况下，应用程序中的所有Activity都会共享相同的亲缘关系，亲缘关系的名称跟由<manifest>元素设置的包名相同。

**android:theme**

这个属性给应用程序中所有的Activity设置默认的主题，属性值要引用一个样式资源。每个独立的Activity的主题会被它们自己的theme属性所覆盖。

**android:uiOptions**

这个属性设置了Activity的UI的额外选项。它必须是下表中的一个值：

| 值                       | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| none                     | 默认设置，没有额外的UI选项。                                 |
| splitActionBarWhenNarrow | 在水平空间受到限制的时 候，会在屏幕的底部添加一个用于显示ActionBar中操作项的栏，例如：在纵向的手持设备上。而不是在屏幕顶部的操作栏中显示少量的操作项。它会把操 作栏分成上下两部分，顶部用于导航选择，底部用于操作项目。这样就会确保可用的合理空间不仅只是针对操作项目，而且还会在顶部给导航和标题留有空间。菜单 项目不能被分开到两个栏中，它们要显示在一起。 |

