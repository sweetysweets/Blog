---
title: netty原理大全解
date: 2019-04-06 14:25:07
tags:
- netty
---

Netty是由[JBOSS](https://baike.baidu.com/item/JBOSS)提供的一个[java开源](https://baike.baidu.com/item/java%E5%BC%80%E6%BA%90/10795649)框架。Netty提供==异步的、[事件驱动](https://baike.baidu.com/item/%E4%BA%8B%E4%BB%B6%E9%A9%B1%E5%8A%A8/9597519)的==网络应用程序框架和工具，用以快速开发高性能、高可靠性的[网络服务器](https://baike.baidu.com/item/%E7%BD%91%E7%BB%9C%E6%9C%8D%E5%8A%A1%E5%99%A8/99096)和客户端程序。

Netty 是一个可以快速开发网络应用程序的 NIO 框架，它大大简化了 TCP 或者 UDP 服务器的网络编程。Netty 的简易和快速开发并不意味着由它开发的程序将失去可维护性或者存在性能问题，它的设计参考了许多协议的实现，比如 FTP，SMTP，HTTP 和各种二进制和基于文本的传统协议，因此 Netty 成功的实现了兼顾快速开发，性能，稳定性，灵活性为一体，不需要为了考虑一方面原因而妥协其他方面。Netty 的应用还是比较广泛的，比如阿里巴巴开源的 Dubbo 和 Sofa-Bolt 框架底层网络通讯都是基于 Netty 来实现的。

<!--more-->

## JAVA 中BIO,NIO,AIO

<https://www.jianshu.com/p/ef418ccf2f7d>

​      一：事件分离器

​        在IO读写时，把 IO请求 与 读写操作 分离调配进行，需要用到事件分离器。根据处理机制的不同，事件分离器又分为：同步的Reactor和异步的Proactor。

​        Reactor模型：

```
    - 应用程序在事件分离器注册 读就绪事件 和 读就绪事件处理器
    - 事件分离器等待读就绪事件发生
    - 读就绪事件发生，激活事件分离器，分离器调用 读就绪事件处理器（即：可以进行读操作了，开始读）
    - 读事件处理器开始进行读操作，把读到的数据提供给程序使用
```

​        Proactor模型：

​      `- 应用程序在事件分离器注册 读完成事件 和 读完成事件处理器，并向操作系统发出异步读请求`

```
   - 事件分离器等待操作系统完成读取
   - 在分离器等待过程中，操作系统利用并行的内核线程执行实际的读操作，并将结果数据存入用户自定义缓冲区，最后通知事件分离器读操作完成
   - 事件分离器监听到 读完成事件 后，激活 读完成事件的处理器
   - 读完成事件处理器 处理用户自定义缓冲区中的数据给应用程序使用
```

​           同步和异步的区别就在于 读 操作由谁完成：同步的Reactor是指程序发出读请求后，由分离器监听到可以进行读操作时（需要获得读操作条件）通知事件处理器进行读操作，异步的Proactor是指程序发出读请求后，操作系统立刻异步地进行读操作了，读完之后在通知分离器，分离器激活处理器直接取用已读到的数据。

​              二：同步阻塞IO（BIO）

​        我们熟知的Socket编程就是BIO，一个socket连接一个处理线程（这个线程负责这个Socket连接的一系列数据传输操作）。阻塞的原因在于：操作系统允许的线程数量是有限的，多个socket申请与服务端建立连接时，服务端不能提供相应数量的处理线程，没有分配到处理线程的连接就会阻塞等待或被拒绝。

BIO是Blocking IO的意思。在类似于网络中进行`read`, `write`, `connect`一类的系统调用时会被卡住。

举个例子，当用`read`去读取网络的数据时，是无法预知对方是否已经发送数据的。因此在收到数据之前，能做的只有等待，直到对方把数据发过来，或者等到网络超时。

对于单线程的网络服务，这样做就会有卡死的问题。因为当等待时，整个线程会被挂起，无法执行，也无法做其他的工作。

> 顺便说一句，这种Block是不会影响同时运行的其他程序（进程）的，因为现代操作系统都是多任务的，任务之间的切换是抢占式的。这里Block只是指Block当前的进程。

于是，网络服务为了同时响应多个并发的网络请求，必须实现为多线程的。每个线程处理一个网络请求。线程数随着并发连接数线性增长。这的确能奏效。实际上2000年之前很多网络服务器就是这么实现的。但这带来两个问题：

- 线程越多，Context Switch就越多，而Context Switch是一个比较重的操作，会无谓浪费大量的CPU。
- 每个线程会占用一定的内存作为线程的栈。比如有1000个线程同时运行，每个占用1MB内存，就占用了1个G的内存。

> 也许现在看来1GB内存不算什么，现在服务器上百G内存的配置现在司空见惯了。但是倒退20年，1G内存是很金贵的。并且，尽管现在通过使用大内存，可以轻易实现并发1万甚至10万的连接。但是水涨船高，如果是要单机撑1千万的连接呢？

问题的关键在于，当调用`read`接受网络请求时，有数据到了就用，没数据到时，实际上是可以干别的。使用大量线程，仅仅是因为Block发生，没有其他办法。

当然你可能会说，是不是可以弄个线程池呢？这样既能并发的处理请求，又不会产生大量线程。但这样会限制最大并发的连接数。比如你弄4个线程，那么最大4个线程都Block了就没法响应更多请求了。

要是操作IO接口时，操作系统能够总是直接告诉有没有数据，而不是Block去等就好了。于是，NIO登场。

​          三：同步非阻塞IO（NIO）

​         New IO是对BIO的改进，基于Reactor模型。我们知道，一个socket连接只有在特点时候才会发生数据传输IO操作，大部分时间这个“数据通道”是空闲的，但还是占用着线程。NIO作出的改进就是“一个请求一个线程”，在连接到服务端的众多socket中，只有需要进行IO操作的才能获取服务端的处理线程进行IO。这样就不会因为线程不够用而限制了socket的接入。客户端的socket连接到服务端时，就会在事件分离器注册一个 IO请求事件 和 IO 事件处理器。在该连接发生IO请求时，IO事件处理器就会启动一个线程来处理这个IO请求，不断尝试获取系统的IO的使用权限，一旦成功（即：可以进行IO），则通知这个socket进行IO数据传输。

​         NIO还提供了两个新概念：Buffer和Channel

```
Buffer:
–        是一块连续的内存块。
–        是 NIO 数据读或写的中转地。
Channel:
–        数据的源头或者数据的目的地
–        用于向 buffer 提供数据或者读取 buffer 数据 ,buffer 对象的唯一接口。
–         异步 I/O 支持
      Buffer作为IO流中数据的缓冲区，而Channel则作为socket的IO流与Buffer的传输通道。客户端socket与服务端socket之间的IO传输不直接把数据交给CPU使用，
而是先经过Channel通道把数据保存到Buffer，然后CPU直接从Buffer区读写数据，一次可以读写更多的内容。
      使用Buffer提高IO效率的原因（这里与IO流里面的BufferedXXStream、BufferedReader、BufferedWriter提高性能的原理一样）：IO的耗时主要花在数据传输的路上，普通的IO是一个字节一个字节地传输，
而采用了Buffer的话，通过Buffer封装的方法（比如一次读一行，则以行为单位传输而不是一个字节一次进行传输）就可以实现“一大块字节”的传输。比如：IO就是送快递，普通IO是一个快递跑一趟，采用了Buffer的IO就是一车跑一趟。很明显，buffer效率更高，花在传输路上
的时间大大缩短。
```

 

NIO是指将IO模式设为“Non-Blocking”模式。在Linux下，一般是这样：

```
void setnonblocking(int fd) {
    int flags = fcntl(fd, F_GETFL, 0);
    fcntl(fd, F_SETFL, flags | O_NONBLOCK);
}
```

> 再强调一下，以上操作只对socket对应的文件描述符有意义；对磁盘文件的文件描述符做此设置总会成功，但是会直接被忽略。

这时，BIO和NIO的区别是什么呢？

在BIO模式下，调用read，如果发现没数据已经到达，就会Block住。

在NIO模式下，调用read，如果发现没数据已经到达，就会立刻返回-1, 并且errno被设为`EAGAIN`。

> 在有些文档中写的是会返回`EWOULDBLOCK`。实际上，在Linux下`EAGAIN`和`EWOULDBLOCK`是一样的，即`#define EWOULDBLOCK EAGAIN`

于是，一段NIO的代码，大概就可以写成这个样子。

```
struct timespec sleep_interval{.tv_sec = 0, .tv_nsec = 1000};
ssize_t nbytes;
while (1) {
    /* 尝试读取 */
    if ((nbytes = read(fd, buf, sizeof(buf))) < 0) {
        if (errno == EAGAIN) { // 没数据到
            perror("nothing can be read");
        } else {
            perror("fatal error");
            exit(EXIT_FAILURE);
        }
    } else { // 有数据
        process_data(buf, nbytes);
    }
    // 处理其他事情，做完了就等一会，再尝试
    nanosleep(sleep_interval, NULL);
}
```

这段代码很容易理解，就是轮询，不断的尝试有没有数据到达，有了就处理，没有(得到`EWOULDBLOCK`或者`EAGAIN`)就等一小会再试。这比之前BIO好多了，起码程序不会被卡死了。

但这样会带来两个新问题：

- 如果有大量文件描述符都要等，那么就得一个一个的read。这会带来大量的Context Switch（`read`是系统调用，每调用一次就得在用户态和核心态切换一次）
- 休息一会的时间不好把握。这里是要猜多久之后数据才能到。等待时间设的太长，程序响应延迟就过大；设的太短，就会造成过于频繁的重试，干耗CPU而已。

要是操作系统能一口气告诉程序，哪些数据到了就好了。

于是IO多路复用被搞出来解决这个问题。



​          四：异步阻塞IO（AIO）

​          NIO是同步的IO，是因为程序需要IO操作时，必须获得了IO权限后亲自进行IO操作才能进行下一步操作。AIO是对NIO的改进（所以AIO又叫NIO.2），它是基于Proactor模型的。每个socket连接在事件分离器注册 IO完成事件 和 IO完成事件处理器。程序需要进行IO时，向分离器发出IO请求并把所用的Buffer区域告知分离器，分离器通知操作系统进行IO操作，操作系统自己不断尝试获取IO权限并进行IO操作（数据保存在Buffer区），操作完成后通知分离器；分离器检测到 IO完成事件，则激活 IO完成事件处理器，处理器会通知程序说“IO已完成”，程序知道后就直接从Buffer区进行数据的读写。

​          也就是说：**AIO是发出IO请求后，由操作系统自己去获取IO权限并进行IO操作；NIO则是发出IO请求后，由线程不断尝试获取IO权限，获取到后通知应用程序自己进行IO操作。**

```
NIO方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中，编程比较复杂，JDK1.4开始支持。
AIO方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作，编程比较复杂，JDK7开始支持
```



简单总结下epoll和select的区别：

进程通过将一个或多个fd传递给select或poll系统调用，阻塞在select;这样select/poll可以帮我们侦测许多fd是否就绪；但是select/poll是顺序扫描fd是否就绪，而且支持的fd数量有限。linux还提供了一个epoll系统调用，epoll是基于事件驱动方式，而不是顺序扫描,当有fd就绪时，立即回调函数rollback  

## **Netty基础相关问题**

1. 讲讲Netty的特点？

2. BIO、NIO和AIO的区别？

3. NIO的组成是什么？

4. 如何使用 Java NIO 搭建简单的客户端与服务端实现网络通讯？

5. 如何使用 Netty 搭建简单的客户端与服务端实现网络通讯？

6. 讲讲Netty 底层操作与 Java NIO 操作对应关系？

7. Channel 与 Socket是什么关系，Channel 与 EventLoop是什么关系，Channel 与 ChannelPipeline是什么关系？

8. EventLoop与EventLoopGroup 是什么关系？

9. 说说Netty 中几个重要的对象是什么，它们之间的关系是什么？

   Channel：为通讯的载体 
   ChannelBuffer：用于装载消息的内容，包括读区，已读区和写区，通过类似指针去动态划分。 
   ChannelHandler：负责channel中的逻辑处理，相当于责任链中的一个链条。 
   ChannelPipeline：用于装ChannelHandler的容器，ChannelHandler会注册到此，多个ChannelHandler串联起来就相当于一个链条，有点类似struts2中的拦截器。 
   ChannelEvent：是数据或者状态的载体，如MessageEvent，是消息的载体。当对Channel进行操作是会产生一个ChannelEvent，然后注册到ChannelPipeline，ChannelPipeline会分配一个ChannelHandler对其进行处理，处理完后交给下一个ChannelHandler，如ChannelUpStreamHandler和ChannelDownStreamHandler。 
   ChannelHandlerContext：可以认为是ChannelHandler的代理类，记录了ChannelHandler的上下文，包括前一个ChannelHandler和后一个ChannelHandler，在DefaultChannelPipeline中不是对ChannelHandler的直接引用，而是引用的DefaultChannelHandlerContext,DefaultChannelHandlerContext在对ChannelHandler引用。

   

10. Netty 的线程模型是什么？

    <https://www.infoq.cn/article/netty-high-performance>

## **粘包与半包和分隔符相关问题**

1. 什么是粘包与半包问题?
2. 粘包与半包为何会出现?
3. 如何避免粘包与半包问题？
4. 如何使用包定长 FixedLengthFrameDecoder 解决粘包与半包问题？原理是什么？
5. 如何使用包分隔符 DelimiterBasedFrameDecoder 解决粘包与半包问题？原理是什么？
6. Dubbo 在使用 Netty 作为网络通讯时候是如何避免粘包与半包问题？
7. Netty框架本身存在粘包半包问题？
8. 什么时候需要考虑粘包与半包问题？

## **WebSocket 协议开发相关问题**

讲讲如何实现 WebSocket 长连接？

讲讲WebSocket 帧结构的理解？

浏览器、服务器对 WebSocket 的支持情况

如何使用 WebSocket 接收和发送广本信息？

如何使用 WebSocket 接收和发送二进制信息？

## **Netty源码分析相关问题**

服务端如何进行初始化？

何时接受客户端请求？

何时注册接受 Socket 并注册到对应的 EventLoop 管理的 Selector ？

客户端如何进行初始化？

何时创建的 DefaultChannelPipeline ？

讲讲Netty的零拷贝？

## **如何正确系统的学习Netty框架**

要理解框架的底层的原理，要掌握的就是最常用的原理。框架就是辅助我们开发的已经完成的一部分代码，帮助我们实现了一部分的功能，我们主要掌握的其实就是框架的内部原理，也就是框架给我们规定的一些内部的规定，有了这些规定就可以高效的开发我们的代码，按照规定办事效率会有很大的提高。其实很多的时候我们并没有注意这些东西，一个功能可以用很多的方法来实现，但是我们按照框架给我们规定的规则去实现的话应该是我们比较正确的一种选择。因此分享一份系统学习Netty框架的知识思维导图给有需要的朋友，希望能对你们有所帮助！

Netty 防止内存泄漏措施

<https://zhuanlan.zhihu.com/p/58444143>

## RPC

<https://www.zhihu.com/question/25536695>

## 参考

<https://www.jianshu.com/p/1b2f63a45476>

<https://blog.csdn.net/baiye_xing/article/details/76735113>

[Good Bio Nio](https://www.jianshu.com/p/ef418ccf2f7d)