# Netty详解

[【硬核】肝了一月的Netty知识点-CSDN博客](https://blog.csdn.net/qq_35190492/article/details/113174359?ops_request_misc=%7B%22request%5Fid%22%3A%22172274117116800226523227%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172274117116800226523227&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-113174359-null-null.142^v100^control&utm_term=netty&spm=1018.2226.3001.4187)

## 1.Netty概述

### 1.1 Netty简介

- Netty是一个**异步**事件驱动的 **网络应用程序框架** 用于 快速开发 可维护的 高性能协议服务器和客户端。
- Netty是目前所有NIO框架中性能最好的框架。

### 1.2 Netty特点

Netty对JDK自带的NIO的API进行了封装，解决了原生NIO（NIO类库和API复杂、需要熟悉多线程和网络编程、开发工作量和难度大、JDK NIO bug）的问题。

1. **设计：**

   1. **适用于各种传输类型的统一 API - 阻塞和非阻塞套接字**

      Netty提供了**统一的API**，无论你是在使用阻塞还是非阻塞套接字，都可以**通过相同的编程接口**进行操作。这意味着开发者不需要关心底层的网络通信细节，只需要专注于业务逻辑即可。

   2. **高度可定制的线程模型 - 单线程、一个或多个线程池**

       Netty允许开发者自由地定制线程模型，可以选择单线程或多线程方案，甚至可以使用线程池来处理并发请求。

   3. 基于灵活且可扩展的事件模型，允许明确分离关注点

      即事件发生时**触发相应的处理器**进行处理。这种模式允许开发者明确地分离关注点，比如**将网络IO操作和业务逻辑分开**，从而提高代码的清晰度和可维护性。此外，由于事件模型是可扩展的，所以你可以轻松地添加**自定义的处理器**来处理特定的事件。

   4. **真正的无连接数据报套接字支持**

       Netty不仅支持传统的面向连接的TCP协议，还支持无连接的UDP协议。

2. 易用性

   有据可查的 Javadoc、用户指南和示例无需额外依赖，JDK 5 （Netty 3.x） 或 6 （Netty 4.x） 就足够了

3. 性能

   更高的吞吐量，更低的延迟。减少资源消耗最小化不必要的内存复制

4. 安全

   完整的SSL/TLS和StartTLS支持

### 1.3 Netty应用场景

在分布式系统中，各个节点之间需要远程服务调用，高性能的Rpc框架必不可少。Netty作为异步高性能的通信框架，往往作为基础通信组件被使用。

### 1.4 面试题：简述Netty

- Netty 是一个基于 NIO 的 client-server(客户端服务器)框架，使用它可以快速简单地开发网络应用程序。
- 它极大地简化并优化了 TCP 和 UDP 套接字服务器等网络编程，并且性能以及安全性等方面甚至都要更好。
- 支持多种协议如 FTP，SMTP，HTTP 以及各种二进制和基于文本的传统协议。
- 官方的总结就是：Netty 成功地找到了一种在不妥协可维护性和性能的情况下实现易于开发，性能，稳定性和灵活性的方法。

### 1.5 面试题：为什么不用NIO用Netty？

1. NIO的编程模型复杂而且存在一些bug，对编程功底要求较高
2. NIO在面对断联重连、包丢失、粘包等问题时处理过程非常复杂。

### 1.6 面试题：Netty的应用场景：

1. 作为 RPC 框架的网络通信工具：我们在分布式系统中，不同服务节点之间经常需要相互调用，这个时候就需要 RPC 框架了。不同服务节点的通信是如何做的呢？可以使用 Netty 来做。比如我调用另外 一个节点的方法的话，至少是要让对方知道我调用的是哪个方法以及相关参数吧！
2. 实现自己的 HTTP 服务器：通过 Netty 我们可以自己实现一个简单的 HTTP 服务器，这个大家应该不陌生。说到 HTTP 服务器的话，作为 Java 后端开发，我们一般使用 Tomcat 比较多。一个最基本的 HTTP 服务器要可以处理常见的 HTTP Method 的请求，比如 POST 请求，GET 请求等等。
3. 实现实时通讯系统：使用 Netty 我们可以实现一个可以聊天类似微信的实时通讯系统，这方面的开源项目还蛮多的，可以自行去 Github 找一找。
4. 实现消息推送系统：市面上有很多消息推送系统都是基于 Netty 来做的。
5. ……

### 1.7 面试题：哪些开源项目用到了Netty

1. Dubbo
2. RocketMQ
3. Elasticsearch
4. gRPC

## 2.JavaIO模型

**阻塞和非阻塞是进程在访问数据的时候，数据是否准备就绪的一种处理方式.**

- **阻塞**：往往需要等待缓冲区中的数据准备好过后才处理其他的事情，否则一直等待在那里。
- **非阻塞**:当我们的进程访问我们的数据缓冲区的时候，如果数据没有准备好则直接返回，不会等待。如果数据已经准备好，也直接返回

**同步和异步都是基于应用程序和操作系统处理 IO 事件所采用的方式。**

- **同步**方式在处理 IO 事件的时候，必须阻塞在某个方法上面等待我们的 IO 事件完成（**阻塞 IO 事件**或者通过**轮询 IO事**件的方式）。
- 对于**异步**来说，所有的 IO 读写都交给了操作系统。这个时候，我们可以去做其他的事情，并不需要去完成真正的 IO 操作，当操作完成 IO 后，会给我们的应用程序一个通知。

java支持三种网络编程模式IO模式：BIO、NIO、AIO

| 模型    |                |                                                  |                                                              |                                            |                                        |                     |
| ------- | -------------- | ------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------ | -------------------------------------- | ------------------- |
| **BIO** | **同步并阻塞** | 服务器实现模式为一个连接一个线程                 | 客户端有**连接请求**时服务器端就需要**启动一个线程**进行处理，如果这个连接不做任何事情会造成**不必要的线程开销。（可以通过线程池机制改善（实现多个客户连接服务器））** | 连接**数目比较小且固定**的架构             | 对服务器资源要求比较高，并发局限于应用 | 可靠性差， 吞吐量低 |
| **NIO** | **同步非阻塞** | 服务器实现模式为**一个线程**处理多个请求（连接） | 客户端发送的连接请求都会**注册到多路复用器**上，多路复用器**轮询到连接有 I/O 请求就进行处理**。 | 连接**数目多且连接比较短**（轻操作）的架构 | 聊天服务器，弹幕系统，服务器间通讯     | 可靠性好， 吞吐量高 |
| **AIO** | **异步非阻塞** | 有效的请求才启动线程                             | 先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用。 | 连接**数目多且连接比较长**（重操作）的架构 |                                        | 可靠性好， 吞吐量高 |

### 2.1 BIO

<img src="D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081456606.png" alt="image-20240930081456606" style="zoom:67%;" />

#### 2.1.1 工作机制

一个服务端对应一个客户端，对应一个线程

- **服务器端**启动一个**ServerSocket**（来监听客户端连接需求）。默认情况下服务器端需要对**每一个客户建立一个线程**与之通讯
- **客户端**启动**socke**t对服务器进行通讯。
- 客户端发出请求后，先咨询服务器是否**有线程响应**，如果没有则会等待，或者被拒绝。
- 如果有响应，客户端线程会等待**请求结束后**，在继续执行

#### 2.1.2 问题分析

- 每个**请求都需要创建独立的线程**，与对应的客户端进行数据 Read，业务处理，数据 Write
- 当**并发数较大**时，需要创建大量线程来处理连接，系统资源占用较大
- 连接建立后，如果当前线程暂时没有数据可读，则**线程就阻塞在 Read 操作上**，造成线程资源浪费

### 2.2 NIO

NIO 提供了与传统 BIO 模型中的 Socket 和 ServerSocket 相对应的 SocketChannel 和 ServerSocketChannel 两种不同的套接字通道实现,两种通道都支持阻塞和非阻塞两种模式

<img src="D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081540187.png" alt="image-20240930081540187" style="zoom:67%;" />

#### 2.2.1 简要说明

- NIO 有三大核心部分**: Channel（通道）、Buffer（缓冲区）、Selector（选择器）**
- NIO 是**面向缓冲区**，或者面向块编程的。数据读取到一个**它稍后处理的缓冲区，需要时可在缓冲区中前后移动**，这就增加了处理过程中的灵活性，使用它可以提供非阻塞式的高伸缩性网络
- 使一个线程从某通道发送请求或者读取数据，但是它仅能得到目前可用的数据，如果目前没有数据可用时，就什么都不会获取，而不是保持线程阻塞，**所以直至数据变的可以读取之前，该线程可以继续做其他的事情**。非阻塞写也是如此，一个线程请求写入一些数据到某通道，但**不需要等待它完全写入，这个线程同时可以去做别的事情。**
- 通俗理解：NIO 是可以做到用一个线程来处理多个操作的。假设有 10000 个请求过来,根据实际情况，可以分配 50 或者 100 个线程来处理。不像之前的阻塞 IO 那样，非得分配 10000 个。

#### 2.2.2 NIO和BIO的比较

- BIO 以流的方式处理数据，而 NIO 以块的方式处理数据，块 I/O 的效率比流 I/O 高很多。
- BIO 是阻塞的，NIO 则是非阻塞的。
- **BIO 基于字节流和字符流**进行操作，而 **NIO 基于 Channel（通道）和 Buffer（缓冲区）进行操作**，数据总是从通道读取到缓冲区中，或者从缓冲区写入到通道中。**Selector（选择器）用于监听多个通道的事件**（比如：连接请求，数据到达等），因此使用单个线程就可以监听多个客户端通道。

#### 2.2.3 NIO核心组件

<img src="D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081617502.png" alt="image-20240930081617502" style="zoom:67%;" />

- 每个Thread对应一个selector选择器，一个selector选择器对应多个通道channel，每个Channel 都会对应一个 Buffer
- 程序(Selector) 对应一个线程，一个线程对应多个连接（channel）,该图反应了有三个 Channel 注册到该 Selector
- 程序切换到哪个 Channel 是由事件决定的，Event 就是一个重要的概念
- Selector 会根据不同的事件，在各个通道上切换。
- Buffer 就是一个内存块，底层是有一个数组。
- 数据的读取写入是通过 Buffer，这个和 BIO，BIO 中要么是输入流，或者是输出流，不能双向，但是 NIO 的 Buffer 是可以读也可以写，需要 flip 方法切换 Channel 是双向的，可以返回底层操作系统的情况，比如 Linux，底层的操作系统通道就是双向的

#### 2.2.4 Selector（选择器）

1.Java 的 NIO，用非阻塞的 IO 方式。**可以用一个线程，处理多个的客户端连接，就会使用到 Selector**（选择器）。 2.Selector 能够**检测**多个注册的**通道上**是否有**事件**发生（注意：多个 Channel **以事件的方式可以注册**到同一个 Selector），如果有事件发生，便获取事件然后针对每个事件进行相应的处理。这样就可以只用一个单线程去管理多个通道，也就是管理多个连接和请求。 3.只有在连接/通道**真正有读写事件发生时，才会进行读写**，就大大地减少了系统开销，并且不必为每个连接都创建一个线程，不用去维护多个线程。 4.避免了多线程之间的上下文切换导致的开销。

#### 2.2.5 Channel（通道）

1.NIO 的通道类似于流，但有些区别如下：

- 通道可以**同时进行读写**，而流只能读或者只能写 通道可以实现**异步读写**数据 通道可以**从缓冲读数据，也可以写数据到缓冲:**

2.BIO 中的 Stream 是**单向**的，例如 FileInputStream 对象只能进行读取数据的操作，而 NIO 中的通道（Channel）是**双向**的，可以读操作，也可以写操作。 3.Channel 在 NIO 中是一个接口 public interface **Channel** extends Closeable{} 4.常用的 Channel 类有: FileChannel、DatagramChannel、ServerSocketChannel 和 SocketChannel 。**【ServerSocketChanne 类似 ServerSocket、SocketChannel 类似 Socket】**FileChannel 用于文件的数据读写，DatagramChannel 用于 UDP 的数据读写，ServerSocketChannel 和 SocketChannel 用于 TCP 的数据读写。

#### 2.2.6 Buffer（缓冲区）

缓冲区本质上是一个可以读写数据的**内存块**，可以理解成是一个容器对象（含数组），该对象提供了一组方法，可以更轻松地使用内存块，，缓冲区对象内置了一些机制，能够跟踪和记录缓冲区的状态变化情况。Channel 提供从文件、网络读取数据的渠道，但是读取或写入的数据都必须经由 Buffer。

![image-20240930081651153](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081651153.png)

#### 2.2.7 NIO非阻塞网络编程原理分析图

![image-20240930081737749](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081737749.png)

### 2.3 NIO与零拷贝

### 2.4 AIO

### 2.5 同步

应用程序要直接参与 IO 读写的操作

![image-20240930081823393](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081823393.png)

### 2.6 异步

所有的 IO 读写交给操作系统去处理，应用程序只需要等待通知

![image-20240930081847476](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081847476.png)

## 3. Netty高性能架构

- 不同的线程模式很能影响程序的性能，目前的线程模式有：

  - **传统阻塞I/O服务模型**

  - Reactor模式

    （根据

    Reactor的数量

    和

    处理资源池线程数量

    的不同，有三种典型实现）

    - 单Reactor单线程
    - 单Reactor多线程
    - 主从Reactor多线程
      - Netty线程模式 主要基于主从Reactor多线程模式做了一定改进

- BIO、NIO分别对应了不同的IO处理方式，是底层IO机制，传统阻塞IO服务模式和Reactor模式是基于这些IO机制的具体服务设计模式。

### 3.1 传统阻塞IO服务模型

![image-20240930081919307](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081919307.png)

![image-20240930081927271](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081927271.png)

### 3.2 Reactor模式

针对传统阻塞IO服务模式两个缺点的解决方案：

- IO复用

  多个**连接共用一个阻塞对象（事件选择器Selector）**，应用程序只需要在一个阻塞对象等待，**无需阻塞等待所有连接**，当某个**连接有新的数据**可以处理时，操作系统通知应用程序，**线程从阻塞状态返回，开始进行业务处理**。

  Reactor对应的叫法：

  - 反应器模式
  - 分发者模式 dispatcher
  - 通知者模式 notifier

- 线程复用

  基于**线程池**复用线程资源：不必再为每个连接创建线程，将连接完成后的业务处理任务分配给线程进行处理，**一个线程可以处理多个连接**的业务。

![image-20240930081946492](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930081946492.png)

> Reactor模式基本设计思想：IO复用结合线程池

#### 3.2.1 Reactor模式核心组成

- Reactor

  Reactor在一个单独的线程中运行，负责监听和分发事件，分发给适当的处理程序来对IO事件作出反应

- Handlers

  处理程序执行IO事件要完成的实际事件

#### 3.2.3 单Reactor单线程

![image-20240930082021648](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082021648.png)

![image-20240930082031771](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082031771.png)

- **Select** 是前面 I/O 复用模型介绍的标准网络编程 API，可以实现**应用程序**通过**一个阻塞对象监听多路连接请求**
- **Reactor** 对象通过 **Select 监控**客户端请求事件，收到事件后通过 **Dispatch** 进行**分发**
- 如果是**建立连接请求**事件，则由 **Acceptor 通过 Accept 处理连接**请求，然后**创建一个 Handler 对象**处理连接完成后的后续业务处理
- 如果不是建立连接事件，则 Reactor 会分发调**用连接对应的 Handler 来**响应
- Handler 会完成 Read → 业务处理 → Send 的完整业务流程

服务器端用一个线程通过多路复用搞定所有的 IO 操作（包括连接，读、写等），编码简单，清晰明了，但是**如果客户端连接数量较多，将无法支撑**，前面的 NIO 案例就属于这种模型。

#### 3.2.4 单Reactor多线程

![image-20240930082055960](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082055960.png)

![image-20240930082102477](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082102477.png)

- Reactor 对象通过 Select 监控客户端请求事件，收到事件后，通过 Dispatch 进行分发
- 如果建立连接请求，则右 Acceptor 通过 accept 处理连接请求，然后创建一个 Handler 对象处理完成连接后的各种事件
- 如果不是连接请求，则由 Reactor 分发调用连接对应的 handler 来处理
- handler **只负责响应事件，不做具体的业务处理**，通过 **read 读取数据后，会分发给后面的 worker 线程池的某个线程处**理业务
- **worker 线程池会分配独立线程**完成真正的业务**，并将结果返回给 handler**
- **handler** 收到响应后，通过 **send 将结果返回给 client**

#### 3.2.5 主从Reactor多线程

![image-20240930082126195](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082126195.png)

![image-20240930082136575](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082136575.png)

- Reactor **主线程 MainReactor** 对象通过 select 监听连接事件，收到事件后，通过 Acceptor 处理连接事件
- 当 Acceptor 处理连接事件后，**MainReactor 将连接分配给 SubReactor**
- **subreactor 将连接加入到连接队列进行监听**，并创建 handler 进行各种事件处理
- 当有新事件发生时，subreactor 就会调用对应的 handler 处理
- handler 通过 read 读取数据，分发给后面的 worker 线程处理
- worker 线程池分配独立的 worker 线程进行业务处理，并返回结果
- handler 收到响应的结果后，再通过 send 将结果返回给 client
- **Reactor 主线程**可以对应**多个 Reactor 子线程**，即 **MainRecator 可以关联多个 S**ubReactor

#### 3.2.6 Netty模式

![image-20240930082148821](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082148821.png)

![image-20240930082156385](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082156385.png)

- Netty 抽象出**两组线程池** **BossGroup** 专门负责**接收客户端的连接**，**WorkerGroup** 专门**负责网络的读写**

- BossGroup 和 WorkerGroup 类型都是 **NioEventLoopGroup**

- NioEventLoopGroup

   相当于一个

  事件循环组

  ，这个组中含

  有多个事件循环

  ，

  每一个

  事件循环

  是 NioEventLoop

  - NioEventLoop 表示一个**不断循环的执行处理任务**的**线程**，**每个 NioEventLoop 都有一个 Selecto**r**，用于监听绑定在其上的 socket 的网络通讯**
  - NioEventLoopGroup 可以**有多个线程**，即可以**含有多个 NioEventLoop**

- 每个 

  BossNioEventLoop

   循环执行的步骤

  有 3 步

  - **轮询** accept 事件
  - **处理** accept 事件，与 client **建立连接**，**生成 NioScocketChannel，\**并将其\**注册到**某个 **worker** NIOEventLoop 上的 **Selector**
  - **处理任务队列的任务，**即 runAllTasks

- 每个 Worker NIOEventLoop 循环执行的步骤

  - **轮询** read，write 事件
  - **处理** I/O 事件，即 read，write 事件，在对应 **NioScocketChannel 处理**
  - **处理任务队列的任务**，即 runAllTasks

- 每个 Worker NIOEventLoop 处理业务时，**会使用 pipeline（管道）**，pipeline **中包含了 channel**，即通过 pipeline 可以**获取到对应通道，管道中维护了很多的处理器**。

## 4. Netty核心组件说明

### 4.1 Bootstrap、ServerBootstrap

Bootstrap 意思是引导，一个 Netty 应用通常由一个 Bootstrap 开始，主要作用是配置整个 Netty 程序，串联各个组件，Netty 中 **Bootstrap 类是客户端程序的启动引导**类，**ServerBootstrap 是服务端启动引导类。**

Bootstarp 和 ServerBootstrap 被称为引导类，指对应用程序进行配置，并使他运行起来的过程。Netty处理引导的方式是使你的应用程序和网络层相隔离。

Bootstrap 是客户端的引导类，Bootstrap 在调用 bind()（连接UDP）和 connect()（连接TCP）方法时，会新创建一个 Channel，仅创建一个单独的、没有父 Channel 的 Channel 来实现所有的网络交换。

ServerBootstrap 是服务端的引导类，ServerBootstarp 在调用 bind() 方法时会创建一个 ServerChannel 来接受来自客户端的连接，并且该 ServerChannel 管理了多个子 Channel 用于同客户端之间的通信。

```java
//该方法用于服务器端，用来设置两个 EventLoop
public ServerBootstrap group(EventLoopGroup parentGroup, EventLoopGroup childGroup)
//该方法用于客户端，用来设置一个 EventLoop
public B group(EventLoopGroup group)
//该方法用来设置一个服务器端的通道实现
public B channel(Class<? extends C> channelClass)
//用来给接收到的通道添加配置
public <T> ServerBootstrap childOption(ChannelOption<T> childOption, T value)
//用来给 ServerChannel 添加配置
public <T> B option(ChannelOption<T> option, T value)
//该方法用来设置业务处理类（自定义的handler）
public ServerBootstrap childHandler(ChannelHandler childHandler)
//该方法用于服务器端，用来设置占用的端口号
public ChannelFuture bind(int inetPort)
//该方法用于客户端，用来连接服务器端
public ChannelFuture connect(String inetHost, int inetPort)
```

Bootstrap

```java
EventLoopGroup group = new NioEventLoopGroup();
try {
    // 创建客户端启动引导/辅助类：Bootstrap
    Bootstrap b = new Bootstrap();
    // 指定线程模型
    b.group(group).
        .....
    // 尝试建立连接
    ChannelFuture f = b.connect(host, port).sync();
    f.channel().closeFuture().sync();
} finally {
    // 优雅关闭相关线程组资源
    group.shutdownGracefully();
}
```

ServerBootstrap

```java
// 1.bossGroup 用于接收连接，workerGroup 用于具体的处理
EventLoopGroup bossGroup = new NioEventLoopGroup(1);
EventLoopGroup workerGroup = new NioEventLoopGroup();

try {
    // 2.创建服务端启动引导/辅助类：ServerBootstrap
    ServerBootstrap b = new ServerBootstrap();
    // 3.给引导类配置两大线程组，确定了线程模型
    b.group(bossGroup, workerGroup).

    // 6.绑定端口
    ChannelFuture f = b.bind(port).sync();
    // 等待连接关闭
    f.channel().closeFuture().sync();
} finally {
    // 7.优雅关闭相关线程组资源
    bossGroup.shutdownGracefully();
    workerGroup.shutdownGracefully();
}
```



### 4.2 Future、ChannelFuture

Netty 中所有的 IO 操作都是**异步**的，不能**立刻**得知消息**是否被正确处理**。但是可以过一会等它**执行完成**或者直接**注册一个监听**，具体的实现就是通过 **Future 和 ChannelFutures**，他们可以**注册一个监听**，当操作执**行成功或失败时监听会自动触发注册的监听事**件。

Netty 中所有的 I/O 操作都是异步的，即操作不会立即得到返回结果，所以 Netty 中定义了一个 ChannelFuture 对象作为这个异步操作的“代言人”，表示异步操作本身。如果想获取到该异步操作的返回值，可以通过该异步操作对象的addListener() 方法为该异步操作添加监 NIO 网络编程框架 Netty 听器，为其注册回调：当结果出来后马上调用执行。

Netty 的异步编程模型都是建立在 Future 与回调概念之上的。

```java
Channel channel()，返回当前正在进行 IO 操作的通道
ChannelFuture sync()，等待异步操作执行完毕
```

### 4.3 Channel

Netty 网络通信的组件，能够用于执行网络 I/O 操作。通过 Channel 可**获得当前网络连接的通道的状态**；通过 Channel 可获得**网络连接的配置参数**（例如接收缓冲区大小）。 Channel 提供**异步的网络 I/O 操作**(如**建立连接，读写，绑定端口**)，异步调用意味着任何 I/O 调用都将**立即返回**，并且不保证在调用结束时所请求的 I/O 操作已完成。调用立即**返回一个 ChannelFuture 实例**，通过**注册监听器到 ChannelFuture 上**，可以 I/O 操作成功、失败或取消时回调通知调用方。支持**关联** I/O **操作与对应的处理程序**。

Channel是 Java NIO 的一个基本构造。可以看作是传入或传出数据的载体。因此，它可以被打开或关闭，连接或者断开连接。

```java
不同协议、不同的阻塞类型的连接都有不同的 Channel 类型与之对应，常用的 Channel 类型：
    NioSocketChannel，异步的客户端 TCP Socket 连接。
    NioServerSocketChannel，异步的服务器端 TCP Socket 连接。
    
    NioDatagramChannel，异步的 UDP 连接。
    
    NioSctpChannel，异步的客户端 Sctp 连接。
    NioSctpServerChannel，异步的 Sctp 服务器端连接，这些通道涵盖了 UDP 和 TCP 网络 IO 以及文件 IO。
```

一旦客户端成功连接服务端，就会新建一个 Channel 同该客户端进行绑定，示例代码如下：

```java
// 通过 Bootstrap 的 connect 方法连接到服务端
public Channel doConnect(InetSocketAddress inetSocketAddress) {
    CompletableFuture<Channel> completableFuture = new CompletableFuture<>();
    bootstrap.connect(inetSocketAddress).addListener((ChannelFutureListener) future -> {
        if (future.isSuccess()) {
            completableFuture.complete(future.channel());
        } else {
            throw new IllegalStateException();
        }
    });
    return completableFuture.get();
}
```

比较常用的 Channel 接口实现类是：

- NioServerSocketChannel （服务端）
- NioSocketChannel （客户端）

这两个 Channel 可以和 BIO 编程模型中的 ServerSocket 以及 Socket 两个概念对应上。

### 4.4 Selector

Netty 基于 **Selector 对象实现 I/O 多路复用**，通过 Selector **一个线程可以监听多个连接的 Channel 事件**。 当向一个 **Selector 中注册 Channel 后**，Selector 内部的机制就可以**自动不断地查询（Select）\**这些注册的 Channel 是否\**有已就绪的 I/O 事件**（例如可读，可写，网络连接完成等），这样程序就可以很简单地使用一个线程高效地管理多个 Channel。

### 4.5 ChannelHandler及实现类 Pipeline和ChannelPipeLine

ChannelHandler **是一个接口**，**处理 I/O 事件或拦截 I/O 操作**，并将其转**发到其 ChannelPipeline（业务处理链）中的下一个处理程序。** ChannelHandler 本身并没有提供很多方法，因为这个接口有许多的方法需要实现，方便使用期间，可以继承它的子类 我们经常需**要自定义一个 Handler 类去继承 ChannelInboundHandlerAdapte**r，然后通过重写相应方法实现业务逻辑。

**ChannelPipeline 是一个 Handler 的集合**，它负**责处理和拦截 inbound 或者 outbound** 的事件和操作，相当于一个贯穿 Netty 的链。（也可以这样理解：**ChannelPipeline 是保存 ChannelHandler 的 List，用于处理或拦截 Channel 的入站事件和出站操作**）ChannelPipeline 实现了一种**高级形式的拦截过滤器模式**，使用户可以完全控制事件的处理方式，以及 Channel 中各个的 ChannelHandler 如何相互交互。在 Netty **中每个 Channel 都有且仅有一个 ChannelPipeline 与之对应**，它们的组成关系如图。

![image-20240930082244724](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082244724.png)

**ChannelHandlerContext**

保存 Channel 相关的所有**上下文信息**，同时**关联一个 ChannelHandler 对象。**即 ChannelHandlerContext 中包含一个具体的事件处理器 ChannelHandler，同时 **ChannelHandlerContext 中也绑定了对应的 pipeline 和 Channel 的信息**，方便对 ChannelHandler 进行调用。

```java
ChannelFuture close()，关闭通道
ChannelOutboundInvoker flush()，刷新
ChannelFuture writeAndFlush(Object msg)，将数据写到
ChannelPipeline 中当前 ChannelHandler 的下一个 ChannelHandler 开始处理（出站）
```

### 4.6 Event Loop Group和其实现类NioEventLoopGroup

ventLoop 定义了Netty的核心抽象，用来处理连接的生命周期中所发生的事件，在内部，将会为每个Channel分配一个EventLoop。

EventLoopGroup 是一个 EventLoop 池，包含很多的 EventLoop。

Netty 为每个 Channel 分配了一个 EventLoop，用于处理用户连接请求、对用户请求的处理等所有事件。EventLoop 本身只是一个线程驱动，在其生命周期内只会绑定一个线程，让该线程处理一个 Channel 的所有 IO 事件。**EvemtLoop的主要作用就是负责监听网络事件并调用事件处理器进行相关IO操作（读写）的处理**

一个 Channel 一旦与一个 EventLoop 相绑定，那么在 Channel 的整个生命周期内是不能改变的。一个 EventLoop 可以与多个 Channel 绑定。即 Channel 与 EventLoop 的关系是 n:1，而 EventLoop 与线程的关系是 1:1。

### 4.7 ByteBuf（字节容器）

网络通信最终都是通过字节流进行传输的。ByteBuf 就是 Netty 提供的一个字节容器，其内部是一个字节数组。当我们通过 Netty 传输数据的时候，就是通过 ByteBuf 进行的。

我们可以将 ByteBuf 看作是 Netty 对 Java NIO 提供的 ByteBuffer 字节容器的封装和抽象。

有很多小伙伴可能就要问了：为什么不用直接使用 Java NIO 提供的 ByteBuffer 呢？
因为 ByteBuffer 这个类使用起来过于复杂和繁琐。

## 5. Netty编解码器和Handler调用机制

Netty 的组件设计：Netty 的主要组件有 Channel、EventLoop、ChannelFuture、ChannelHandler、ChannelPipe 等。 **ChannelHandler 充当了处理入站和出站数据的应用程序逻辑的容器**。例如，实现 ChannelInboundHandler 接口（或ChannelInboundHandlerAdapter），你就可以接收入站事件和数据，这些数据会被业务逻辑处理。当要给客户端发送响应时，也可以从 ChannelInboundHandler 冲刷数据。业务逻辑通常写在一个或者多个 ChannelInboundHandler 中。ChannelOutboundHandler 原理一样，只不过它是用来处理出站数据的 **ChannelPipeline 提供了 ChannelHandler 链的容器**。以客户端应用程序为例，如果事件的运动方向是**从客户端到服务端的，那么我们称这些事件为出站的**，即客户端发送给服务端的数据会通过 pipeline 中的一系列 ChannelOutboundHandler，并被这些 Handler 处理，**反之则称为入站的。**

### 5.1 编码解码器

当 Netty **发送或者接受**一个消息的时候，就将会发生一次**数据转换**。**入站**消息会被**解码**：**从字节转换为另一种格式（比如 java 对象）**；如果是**出站**消息，它会**被编码成字节。** Netty 提供一系列实用的**编解码器**，他们都**实现了 ChannelInboundHadnler** 或者 **ChannelOutboundHandler 接口。在这些类中，channelRead 方法已经被重写了**。以入站为例，对于每个从入站 Channel 读取的消息，这个方法会被调用。随后，它将调用**由解码器所提供的 decode() 方法进行解码**，并将已**经解码的字节转发给 ChannelPipeline 中的下一个 ChannelInboundHandler。**

### 5.2 解码器 ByteToMessageDecoder

由于不可能知道远程节点是否会一次性发送一个完整的信息，tcp 有可能出现粘包拆包的问题，这个类会对入站数据进行缓冲，直到它准备好被处理. 一个关于 ByteToMessageDecoder 实例分析

![image-20240930082322244](D:\2024\Notes\Typora\八股\Netty.assets\image-20240930082322244.png)

### 5.3 解码器 ReplayingDecoder

## 6 Netty 源码分析







