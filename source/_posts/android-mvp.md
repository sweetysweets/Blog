---
title: android项目架构设计总结
date: 2018-12-13 17:00:01
tags:
- android
---

实习这几个月要一个人搞一个像模像样的安卓项目了，emmm感觉有点紧张呀，毕竟没一个人写过大的安卓项目:sob:但还是硬着头皮上了。。希望不要辜负这几个月折腾，老老实实学点有用的。

首先的准备工作就是要设计App的整体框架，所以花了两天时间学习了一下主流的安卓整体架构有哪些，以及各自的优缺点和改进方法。

 <!--more-->

### 开始之前

首先要清楚我们做的是什么：

一般我们与网络交互数据的方式有两种：主动请求(http)，长连接推送

结合网络交互数据的方式来说一下我们开发的App的类型和特点：

- **数据展示类型的App：**特点是页面多，需要频繁调用后端接口进行数据交互，以http请求为主；推送模块，IM类型App的IM核心功能以长连接为主，比较看重电量、流量消耗。
- **手机助手类App：**主要着眼于系统API的调用，达到辅助管理系统的目的，网络调用的方式以http为主。
- **游戏：**一般分为游戏引擎和业务逻辑，业务脚本化编写，网络以长连接为主，http为辅。

这次做的App是类型1，简要来说这类app的主要工作就是

1. 把服务端的数据拉下来给用户展示
2. 把用户在客户端修改的数据上传给服务端处理

所以这类App的网络调用相当频繁，而且需要考虑到网络差，没网络等情况下，App的运行，成熟的商业应用的网络调用一般是如下流程：

**UI发起请求 - 检查缓存 - 调用网络模块 - 解析返回JSON / 统一处理异常 - JSON对象映射为Java对象 - 缓存 - UI获取数据并展示**

这之中可以看到很明显职责划分，即：数据获取；数据管理；数据展示，基于这样的职责划分我们再考虑架构



### MVC

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/android-mvp/MVC.png)

MVC是Android最原生也是最基础的架构，C控制器由activity和fragment担任，大部分业务逻辑都写在activity里面，导致页面逻辑复杂，复杂的业务动辄上千行代码，要崩溃

**优点：**就是开发简单，以页面为导向；

**缺点：**

维护难，因为是以页面为导向的，有些需要共用的业务逻辑就会很烦，don't repeat your self， 你要不要repeat ？不想repeat就要写模块，慢慢的项目就会多出一堆乱七八糟的小模块。

另一方面，测试很困难，因为所有的数据处理都在Activity和Fragment，假如现在想先用假数据显示，就要直接改Activity和Fragment的数据控制逻辑。

**总结**：Activity和Fragment不应该管这么多数据处理逻辑



### MVP

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/android-mvp/MVP%E6%A8%A1%E5%BC%8F.png)

- M-Model : 业务逻辑和实体模型(biz/bean)
- V-View : 布局文件(XML)和Activity
- P-Presenter : 完成View和Model的交互

View和Model不在直接通信，所有交互的工作都通过Presenter来解决。既然两者都通过Presenter来通信，为了复用和可拓展性，MVP模式基于接口设计也就很好理解了。

**优点**：

解耦合，业务逻辑和视图分离；

复用性好，因为所有的交互和逻辑都发生Presenter内部，所以我们可以**将一个Presenter用于多个View层，不用修改Presenter中的逻辑**，就算有细微区别的的地方可以通过方法重载等方式处理，从而达到高效利用模型。并且做过开发的一半都知道一件事，就是**视图层的修改远比逻辑层的来的频繁**，所以逻辑层Presenter不用动是一件很幸福的事。

便于单元测试，可以直接对Presenter写Junit测试（可以脱离用户接口来测试这些逻辑）

提高了代码可扩展性、组件复用能力、团队协作的效率。

**缺点**：

类爆炸问题：由于我们使用了接口的方式去连接View层和Presenter层，如果有一个逻辑很复杂的页面，接口会有很多，维护接口的成本会很大。这个问题通过以下方案缓解：定义一些基类接口，把一些公共的逻辑，比如网络请求成功失败，toast等放在里面，之后你再定义新的接口时可以继承自这些基类。

复用的时候可能造成接口的冗余。解决方法是使用Contract

内存泄漏问题：由于Presenter 经常性的持有Activity 的强引用，如果在一些请求结束之前Activity 被销毁了，Activity对象将无法被回收，此时就会发生内存泄露。解决方案是p对v的弱引用

**总结**：



### MVVM

![](https://raw.githubusercontent.com/sweetysweets/BlogResource/master/android-mvp/MVVM.png)

MVVM模式相当于把MVP中的P换成了Viewmodel

- Model层，主要负责数据的提供。Model层提供业务逻辑的数据结构（比如，实体类），提供数据的获取（比如，从本地数据库或者远程网络获取数据），提供数据的存储。
- View层，主要负责界面的显示。View层不涉及任何的业务逻辑处理，它持有ViewModel层的引用，当需要进行业务逻辑处理时通知ViewModel层。
- ViewModel层，主要负责业务逻辑的处理。ViewModel层不涉及任何的视图操作。通过官方提供的**Data Binding**库，View层和ViewModel层中的数据可以实现绑定，ViewModel层中数据的变化可以自动通知View层进行更新，因此ViewModel层不需要持有View层的引用。ViewModel层可以看作是View层的数据模型和Presenter层的结合。

**MVVM与MVP的区别：**ViewModel层不持有View层的引用。这样进一步降低了耦合，View层代码的改变不会影响到ViewModel层。

**优点**：

降低了耦合。ViewModel层不持有View层的引用，当View层发生改变时，只要View层绑定的数据不变，那么ViewModel层就不需要改变。而在MVP模式下，当View层发生改变时，操作视图的接口就要进行相应的改变，那么Presenter层就需要修改了。
代码简洁。通过Data Binding库，UI和数据之间可以实现绑定，不用编写大量操作视图的代码，Activity/Fragment的代码可以做到相当简洁。

**缺点**：

对于view较多的项目，会趋向于创建大量的全局的本地数据，数据绑定需要花费更多的内存。并且ViewModel的构建和维护的成本都会比较高。

对于简单的视图反而会变成一种过度的设计。

数据绑定的声明是指令式地写在View的模版当中的，这些内容是没办法去打断点debug的。你看到界面异常了，有可能是你 View 的代码有 Bug，也可能是 Model 的代码有问题。数据绑定使得一个位置的 Bug 被快速传递到别的位置，要定位原始出问题的地方就变得不那么容易了。

### 小结

真正的最佳实践都是依托于具体项目的，很多项目不只是用了固定的某一种架构，也许结合一下MVP和MVVM，用presenter去做和model层的通信，并且使用data binding去轻松的bind data。也许是MVP和分层的改进，还是要具体问题具体分析。

所以基于学习和主流市场的角度，我决定先用MVP模式开发，后期如果有想法再进行重构。

今天听了一句话，软件的第一个版本往往都是不可用的，所以不要害怕推翻，也不要在开始之前考虑太多的场景或可能，先搞起来，再迭代，再重构，经过一段时间的开发之后你就会知道自己要什么。（选择恐惧症该改改了。。）

定一下下周flag，《解决MVP模式缺陷实战》

### 参考资料

[Android项目开发如何设计整体架构？](https://www.zhihu.com/question/45517397)

[google的mvp-demo](https://github.com/googlesamples/android-architecture/tree/todo-mvp/)

[Android开发中的MVP架构以及性能优化](https://www.jianshu.com/p/57e5f75e9408)

[Android MVP架构搭建](http://www.jcodecraeer.com/a/anzhuokaifa/2017/1020/8625.html?1508484926)

[Android架构模式：MVC & MVP & MVVM](https://www.jianshu.com/p/198b02a79f41)