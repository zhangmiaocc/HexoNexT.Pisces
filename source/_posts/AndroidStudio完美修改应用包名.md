---
title: Android Studio 完美修改应用包名
date: 2018-07-23 17:47:05
tags:
- blog
- markdown
- Android 
categories:
- Android 
- 知识巩固
---

我们平时新建项目有些朋友可能当时就是随意写的一个包名，然后在项目过程中， 又感觉这个包名不太好，所以就要对包名进行修改。

#### 修改最外层包名

![](https://ws1.sinaimg.cn/large/006tNc79ly1ftjy3k8gyzg30yv0rhwpi.gif)

#### 修改中间层包名
![](https://ws1.sinaimg.cn/large/006tNc79ly1ftjy490rqcg31040putn6.gif)

看到没有，我们只需要在setting里面，把 compact empty middle packages 这个选项去掉，这样，我们的包的层次结构就分开了，这个时候我们就可以根据自己的需要去做相应的修改了。 

------

##### 新增：

Studio 3.0 之后，setting 中的选项名字该成了 Hide empty middle packages 

------
另外说明一点，在 Studio 里面我们的 getPackageName 对应的是 applicationId , 而manifest 的那个package，在这里的作用其实是为了引用内部资源文件，以及保证 Activity 等源文件的路径正确而已，所以，在 Studio 中修改发布程序包名，则只需要在 build 文件中修改 applicationId 就可以了。

### **补充**

在 **Studio 3.0** 还有一种可直接通过 Androidmenifest 修改部分包名的方法（亲测过）。这里就不上图了。语言给大家描述一下，有什么问题可以博客下方留言。

#### 修改流程如下：
进入 Androidmanifest.xml 文件，找到 package 名称，选中需要修改的部分。 
比如原包名为 
`com.zm.android` 
如果需要修改中间的 **zm** ，那么我们就选中 `zm` , 
依次进行 右键 - > Refactor -> Rename , (Mac 快捷键为 fn + shift+F6) 
然后选择 **Rename package** , 输入要修改目标的名称 ，直接点击 **Refactor** , 左下方继续点击 **Do Refactor** , 等待修改成功~！
