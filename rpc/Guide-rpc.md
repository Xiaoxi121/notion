## 什么是RPC？

**RPC（Remote Procedure Call）** 即远程过程调用，通过名字我们就能看出 RPC 关注的是远程调用而非本地调用。一言蔽之：**RPC 的出现就是为了让你调用远程方法像调用本地方法一样简单。**

为什么要 RPC ？ 因为，**两个不同的服务器上的服务提供的方法不在一个内存空间**，所以，需要通过**网络编程**才能**传递**方法调用所需要的**参数**。并且，方法调用的**结果**也需要通过网络编程来**接收**。但是，如果我们自己手动网络编程来实现这个调用过程的话工作量是非常大的，因为，我们需要考虑底层传输方式（TCP 还是 UDP）、序列化方式等等方面。**RPC 能帮助我们做什么呢？**  简单来说，通过 RPC 可以帮助我们调用远程计算机上某个服务的方法，这个过程就像调用本地方法一样简单。并且！我们不需要了解底层网络编程的具体细节。

## **RPC 原理是什么？**

1. **客户端（服务消费端）** ：**调用**远程方法的一端。
2. **客户端 Stub（桩）** ： 这其实就是一**代理类（\**用代理类屏蔽很多细节，比如负载均衡、容错、序列化与反序列化等等操\**）**。代理类主要做的事情很简单，就是把你调用方法、类、方法参数等**信息传递**到服务端。
3. **网络传输** ： 网络传输就是你要把你调用的方法的信息比如说参数啊这些东西传输到服务端，然后服务端执行完之后再把返回结果通过网络传输给你传输回来。网络传输的实现方式有很多种比如最基本的 Socket 或者性能以及封装更加优秀的 Netty（推荐）。
4. **服务端 Stub（桩）** ：**这个桩就不是代理类**了。我觉得理解为桩实际不太好，大家注意一下就好。这里的服务端 Stub 实际指的就是接**收到客户端执行方法的请求后**，**去指定对应的方法然后返回结果给客户端的类**。（服务端 Stub（桩)做的事情就是根据客户端传过来的 RPC 请求去找到对应的目标类和目标方法执行,然后拿回来执行结果.然后再通过网络传输将结果返回给客户端.）
5. **服务端（服务提供端）** ：**提供**远程方法的一端。

<img src="D:\2024\Notes\Typora\rpc\Guide-rpc.assets\image-20241005222312151.png" alt="image-20241005222312151" style="zoom:50%;" />

1. 服务消费端（client）以本地调用的方式调用远程服务；
2. 客户端 Stub（client stub） 接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体**（序列化）**：`RpcRequest`；
3. 客户端 Stub（client stub） 找到远程服务的地址，并将消息发送到服务提供端；
4. 服务端 Stub（桩）收到消息将消息**反序列化为 Java 对象**: `RpcRequest`；
5. 服务端 Stub（桩）根据`RpcRequest`中的类、方法、方法参数等信息**调用本地的方法**；
6. 服务端 Stub（桩）得到方法执行结果并将组装成能够进行网络传输的消息体：**`RpcResponse`（序列化）**发送至消费方；
7. 客户端 Stub（client stub）接收到消息并将消息**反序列化为 Java 对象:`RpcResponse`**

序列化是指将一个对象的状态转换成一种可以被存储或者传输的形式的过程。这个过程通常涉及将对象转换成字节流、XML文档、JSON字符串等格式。

反序列化则是序列化的逆过程，即将序列化后的数据恢复成它原来的对象形式。

## 常见RPC框架介绍

<img src="D:\2024\Notes\Typora\rpc\Guide-rpc.assets\image-20241005222331000.png" alt="image-20241005222331000" style="zoom: 50%;" />

<img src="D:\2024\Notes\Typora\rpc\Guide-rpc.assets\image-20241005222346431.png" alt="image-20241005222346431" style="zoom:50%;" />

### Dubbo

Apache Dubbo 是一款微服务框架，为大规模微服务实践提供高性能 RPC 通信、流量治理、可观测性等解决方案，涵盖 Java、Golang 等多种语言 SDK 实现

Dubbo 提供了从服务定义、服务发现、服务通信到流量管控等几乎所有的服务治理能力，支持 Triple 协议（基于 HTTP/2 之上定义的下一代 RPC 通信协议）、应用级服务发现、Dubbo Mesh （Dubbo3 赋予了很多云原生友好的新特性）等特性。

Dubbo 是由阿里开源，后来加入了 Apache 。正是由于 Dubbo 的出现，才使得越来越多的公司开始使用以及接受分布式架构。

- Github ：https://github.com/apache/incubator-dubbo
- 官网：https://dubbo.apache.org/zh/

### Motan

Motan 更像是一个精简版的 Dubbo，可能是借鉴了 Dubbo 的思想，Motan 的设计更加精简，功能更加纯粹。

### gRPC

gRPC 是 Google 开源的一个高性能、通用的开源 RPC 框架。其由主要面向移动应用开发并基于 HTTP/2 协议标准而设计（支持双向流、消息头压缩等功能，更加节省带宽），基于 ProtoBuf 序列化协议开发，并且支持众多开发语言。

**何谓 ProtoBuf？** [ProtoBuf（ Protocol Buffer）](https://github.com/protocolbuffers/protobuf) 是一种更加灵活、高效的数据格式，可用于通讯协议、数据存储等领域，基本支持所有主流编程语言且与平台无关。不过，通过 ProtoBuf 定义接口和数据类型还挺繁琐的，这是一个小问题。

不得不说，gRPC 的通信层的设计还是非常优秀的，[Dubbo-go 3.0](https://dubbogo.github.io/)  的通信层改进主要借鉴了 gRPC。

不过，gRPC 的设计导致其几乎没有服务治理能力。如果你想要解决这个问题的话，就需要依赖其他组件比如腾讯的 PolarisMesh（北极星）了。

- Github：https://github.com/grpc/grpc
- 官网：https://grpc.io/

### Thrift

Apache Thrift 是 Facebook 开源的跨语言的 RPC 通信框架，目前已经捐献给 Apache 基金会管理，由于其跨语言特性和出色的性能，在很多互联网公司得到应用，有能力的公司甚至会基于 thrift 研发一套分布式服务框架，增加诸如服务注册、服务发现等功能。

`Thrift`支持多种不同的**编程语言**，包括`C++`、`Java`、`Python`、`PHP`、`Ruby`等（相比于 gRPC 支持的语言更多 ）。

- 官网：https://thrift.apache.org/
- Thrift 简单介绍：https://www.jianshu.com/p/8f25d057a5a9

### 总结

gRPC 和 Thrift 虽然支持跨语言的 RPC 调用，但是它们只提供了最基本的 RPC 框架功能，缺乏一系列配套的服务化组件和服务治理功能的支撑。

Dubbo 不论是从功能完善程度、生态系统还是社区活跃度来说都是最优秀的。而且，Dubbo在国内有很多成功的案例比如当当网、滴滴等等，是一款经得起生产考验的成熟稳定的 RPC 框架。最重要的是你还能找到非常多的 Dubbo 参考资料，学习成本相对也较低。

Dubbo 和 Motan 主要是给 Java 语言使用。虽然，Dubbo 和 Motan 目前也能兼容部分语言，但是不太推荐。如果需要跨多种语言调用的话，可以考虑使用 gRPC。

## **如何自己实现一个RPC框架？**

**一般情况下， RPC 框架不仅要提供服务发现功能，还要提供负载均衡、容错等功能，这样的 RPC 框架才算真正合格的(service、balance、tolerance)**

<img src="D:\2024\Notes\Typora\rpc\Guide-rpc.assets\image-20241005222407348.png" alt="image-20241005222407348" style="zoom:50%;" />

**服务提供端 Server 向注册中心注册服务，服务消费者 Client 通过注册中心拿到服务相关信息，然后再通过网络请求服务提供端 Server。**

<img src="D:\2024\Notes\Typora\rpc\Guide-rpc.assets\image-20241005222425408.png" alt="image-20241005222425408" style="zoom:30%;" />

### 注册中心

https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html

注册中心首先是要有的。比较推荐使用 Zookeeper 作为注册中心。当然了，你也可以使用 Nacos ，甚至是 Redis。

ZooKeeper 为我们提供了高可用、高性能、稳定的**分布式数据一致性解决方案**，通常被用于实现诸如数据发布/订阅、负载均衡、命名服务、分布式协调/通知、集群管理、Master 选举、分布式锁和分布式队列等功能。并且，ZooKeeper **将数据保存在内存中**，性能是非常棒的。 在“读”多于“写”的应用程序中尤其地高性能，因为“写”会导致所有的服务器间同步状态。（“读”多于“写”是协调服务的典型场景）。当然了，如果你想通过文件来存储服务地址的话也是没问题的，不过性能会比较差。

**注册中心负责服务地址的注册与查找，相当于目录服务。** **服务端启动**的时候将**服务名称**及其对应的**地址(ip+port**)**注册**到注册中心，**服务消费端**根据服务名称**找到**对应的服务地址。有了服务地址之后，服务消费端就可以**通过网络请求服务端**了。

<img src="D:\2024\Notes\Typora\rpc\Guide-rpc.assets\image-20241005222451512.png" alt="image-20241005222451512" style="zoom:50%;" />

Dubbo的架构图

- **Provider：** 暴露服务的服务提供方
- **Consumer：** 调用远程服务的服务消费方
- **Registry：** 服务注册与发现的注册中心
- **Monitor：** 统计服务的调用次数和调用时间的监控中心
- **Container：** 服务运行容器

1. **服务容器**负责**启动，加载，运行**服务**提供者**。
2. 服务**提供者**在启动时，向注册中心**注册**自己提供的服务。
3. 服务**消费者**在启动时，向注册中心**订阅**自己所需的服务。
4. **注册中心**返回服务**提供者地址列表**给消费者，如果有变更，注册中心将基**于长连接推送变更数据**给消费者。 5**. 服务消费者**，从提供者地址列表中，基于**软负载均衡算法**，选一台提供者进行**调用**，如果调用失败，再选另一台调用。
5. 服务消费者和提供者，在**内存**中累计调用**次数**和调用**时间**，定时每分钟**发送**一次**统计数据**到监控中心。

### 动态代理

代理模式就是： 我们给某一个对象提供一个代理对象，并由代理对象来代替真实对象做一些事情。你可以把代理对象理解为一个幕后的工具人。 举个例子：我们真实对象调用方法的时候，我们可以**通过代理对象去做一些事情比如安全校验、日志打印**等等。但是，这个过程是完全对真实对象屏蔽的。

RPC 的主要目的就是让我们调用远程方法像调用本地方法一样简单，我们不需要关心远程方法调用的细节比如网络传输。**怎样才能屏蔽远程方法调用的底层细节呢？**

答案就是**动态代理**。简单来说，当你调用远程方法的时候，实际会通过**代理对象来传输网络请求**，不然的话，怎么可能直接就调用到远程方法。

> [Java 代理模式详解 | JavaGuide](https://javaguide.cn/java/basis/proxy.html#_3-1-jdk-动态代理机制)

### 网络传输

既然我们要**调用远程的方法**，就要发送**网络请求**来**传递目标类**和方法的**信息**以及方法的**参数**等数据到**服务提供端。**

网络传输具体实现你可以使用 **Socket** （ Java 中最原始、最基础的网络通信方式。但是，Socket 是阻塞 IO、性能低并且功能单一）。

你也可以使用同步非阻塞的 I/O 模型 **NIO** ，但是用它来进行网络编程真的太麻烦了。不过没关系，你可以使用基于 NIO 的网络编程框架 Netty

1. **Netty 是一个基于 NIO 的 client-server(客户端服务器)框架，使用它可以快速简单地开发网络应用程序。**
2. 它极大地简化并简化了 TCP 和 UDP 套接字服务器等网络编程,并且性能以及安全性等很多方面甚至都要更好。
3. 支持多种协议如 FTP，SMTP，HTTP 以及各种二进制和基于文本的传统协议。

### 传输协议

我们还需要**设计一个私有的 RPC 协议**，这个协议是**客户端（服务消费方）和服务端（服务提供方）交流的基础**。

简单来说：通过设计协议，我们定义**需要传输哪些类型**的数据， 并且还会规定每一种类型的数据应该**占多少字节**。这样我们在接收到二进制数据之后，就可以**正确的解析**出我们需要的数据。有一点像密文传输。

通常一些标准的 RPC 协议包含下面这些内容：

- **魔数** ： 通常是 **4 个字节**。这个魔数主要是为了筛选来到服务端的数据包，有了这个魔数之后，服务端首先取出前面四个字节进行比对，能够在第一时间识别出这个数据包并非是遵循自定义协议的，也就是无效数据包，为了安全考虑可以直接关闭连接以节省资源。
- **序列化器编号** ：标识序列化的方式，比如是使用 Java 自带的序列化，还是 json，kryo 等序列化方式。
- **消息体长度** ： 运行时计算出来。
- ......

如果你想看 [guide-rpc-framework](https://github.com/Snailclimb/guide-rpc-framework) 的 RPC 协议设计的话，可以在 Netty 编解码器相关的类中找到。

### 序列化和反序列化

要在网络传输数据就要涉及到**序列化**。因为网络传输的数据必须是二进制的。为了能够让 Java 对象在网络中传输我们需要将其**序列化**为二进制的数据。我们最终需要的还是目标 Java 对象，因此我们还要将二进制的数据“解析”为目标 Java 对象，也就是对二进制数据再进行一次**反序列化**

另外，不仅网络传输的时候需要用到序列化和反序列化，将对象存储到文件、数据库等场景都需要用到序列化和反序列化。

JDK 自带的序列化，只需实现 `java.io.Serializable`接口即可，不过这种方式不推荐，因为不支持跨语言调用并且性能比较差。现在比较常用序列化的有 **hessian**、**kryo**、**protostuff**

### 负载均衡

我们的系统中的某个服务的访问量特别大，我们将这个服务部署在了多台服务器上，当客户端发起请求的时候，多台服务器都可以处理这个请求。那么，**如何正确选择处理该请求的服务器**就很关键。假如，你就要一台服务器来处理该服务的请求，那该服务部署在多台服务器的意义就不复存在了。负载均衡就是为了**避免单个服务器响应同一请求，容易造成服务器宕机、崩溃等问题**，我们从负载均衡的这四个字就能明显感受到它的意义。

### 更完善的一点的 RPC 框架可能还有监控模块

### 实现一个RPC框架需要的技术

按照这一款基于 Netty+kryo+Zookeeper 实现的 RPC 框架，需要下面这些技术支撑：

Java

1. 动态代理机制；
2. 序列化机制以及各种序列化框架的对比，比如 hession2、kryo、protostuff；
3. 线程池的使用；
4. `CompletableFuture` 的使用；
5. ......

Netty

1. 使用 Netty 进行网络传输；
2. `ByteBuf` 介绍；
3. Netty 粘包拆包；
4. Netty 长连接和心跳机制；
5. ......

Zookeeper

1. 基本概念；
2. 数据结构；
3. 如何使用 Netflix 公司开源的 Zookeeper 客户端框架 Curator 进行增删改查；
4. ......

## 序列化介绍及序列化协议选择

### 序列化与反序列化

- **序列化**： 将数据结构或对象转换成二进制字节流的过程
- **反序列化**：将在序列化过程中所生成的二进制字节流转换成数据结构或者对象的过程
- 对于 Java 这种面向对象编程语言来说，我们**序列化的都是对象（Object）也就是实例化后的类(Class)**，但是在 C++这种半面向对象的语言中，struct(结构体)定义的是数据结构类型，而 class 对应的是对象类型。

常见应用场景：

- 对象在进行网络传输（比如远程方法调用 RPC 的时候）之前需要先被序列化，接收到序列化的对象之后需要再进行反序列化；
- 将对象存储到文件之前需要进行序列化，将对象从文件中读取出来需要进行反序列化；
- 将对象存储到数据库（如 Redis）之前需要用到序列化，将对象从缓存数据库中读取出来需要反序列化；
- 将对象存储到内存之前需要进行序列化，从内存中读取出来之后需要进行反序列化。

**序列化的主要目的是通过网络传输对象或者说是将对象存储到文件系统、数据库、内存中。**

### 序列化协议对应TCP/IP 4层模型中的哪一层

TCP/IP 四层模型是下面这样的，序列化协议属于哪一层呢？

1. 应用层
2. 传输层
3. 网络层
4. 网络接口层

<img src="D:\2024\Notes\Typora\rpc\Guide-rpc.assets\image-20241005222518114.png" alt="image-20241005222518114" style="zoom:50%;" />

OSI 七层协议模型中，表示层做的事情主要就是对应用层的用户数据进行处理转换为二进制流。反过来的话，就是将二进制流转换成应用层的用户数据。这不就对应的是序列化和反序列化么？

因为，OSI 七层协议模型中的应用层、表示层和会话层对应的都是 TCP/IP 四层模型中的应用层，所以序列化协议属于 TCP/IP 协议应用层的一部分

### 常见的序列化协议

JDK 自带的序列化方式一般不会用 ，因为序列化效率低并且存在安全问题。比较常用的序列化协议有 Hessian、Kryo、Protobuf、ProtoStuff，这些都是基于二进制的序列化协议。

像 JSON 和 XML 这种属于文本类序列化方式。虽然可读性比较好，但是性能较差，一般不会选择。

**JDK自带的序列化方式**

JDK 自带的序列化，只需实现 `java.io.Serializable`接口即可。

```java
@AllArgsConstructor
@NoArgsConstructor
@Getter
@Builder
@ToString
public class RpcRequest implements Serializable {
    private static final long serialVersionUID = 1905122041950251207L;
    private String requestId;
    private String interfaceName;
    private String methodName;
    private Object[] parameters;
    private Class<?>[] paramTypes;
    private RpcMessageTypeEnum rpcMessageTypeEnum;
}
```

**serialVersionUID 有什么作用？**

序列化号 `serialVersionUID` 属于版本控制的作用。反序列化时，会检查 `serialVersionUID` 是否和当前类的 `serialVersionUID` 一致。如果 `serialVersionUID` 不一致则会抛出 `InvalidClassException` 异常。强烈推荐每个序列化类都手动指定其 `serialVersionUID`，如果不手动指定，那么编译器会动态生成默认的 `serialVersionUID`。

**serialVersionUID 不是被 static 变量修饰了吗？为什么还会被“序列化”？**

`static` 修饰的变量是**静态变量**，**位于方法区，本身是不会被序列化的**。但是，`serialVersionUID` 的序列化做了**特殊处理**，在序列化时，会将 `serialVersionUID` 序列化到二进制字节流中；在反序列化时，也会解析它并做一致性判断。

官方说明如下：

> A serializable class can declare its own serialVersionUID explicitly by declaring a field named `"serialVersionUID"` that must be `static`, `final`, and of type `long`;
>
> 如果想显式指定 `serialVersionUID` ，则需要在类中使用 `static` 和 `final` 关键字来修饰一个 `long` 类型的变量，变量名字必须为 `"serialVersionUID"` 。

也就是说，`serialVersionUID` 只是用来被 JVM 识别，实际并没有被序列化。

**如果有些字段不想进行序列化怎么办？**

对于不想进行序列化的变量，可以使用 `transient` 关键字修饰。

`transient` 关键字的作用是：阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被 `transient` 修饰的变量值不会被持久化和恢复。

关于 `transient` 还有几点注意：

- `transient` 只能修饰变量，不能修饰类和方法。
- `transient` 修饰的变量，在反序列化后变量值将会被置成类型的默认值。例如，如果是修饰 `int` 类型，那么反序列后结果就是 `0`。
- `static` 变量因为不属于任何对象(Object)，所以无论有没有 `transient` 关键字修饰，均不会被序列化

**为什么不推荐使用 JDK 自带的序列化？**

我们很少或者说几乎不会直接使用 JDK 自带的序列化方式，主要原因有下面这些原因：

- **不支持跨语言调用** : 如果调用的是其他语言开发的服务的时候就不支持了。
- **性能差** ：相比于其他序列化框架性能更低，主要原因是序列化之后的字节数组体积较大，导致传输成本加大。
- **存在安全问题** ：序列化和反序列化本身并不存在问题。但当输入的反序列化的数据可被用户控制，那么攻击者即可通过构造恶意输入，让反序列化产生非预期的对象，在此过程中执行构造的任意代码。相关阅读：[应用安全:JAVA反序列化漏洞之殇 - Cryin](https://cryin.github.io/blog/secure-development-java-deserialization-vulnerability/) 、[Java反序列化安全漏洞怎么回事? - Monica](https://www.zhihu.com/question/37562657/answer/1916596031)。

**Kryo**

Kryo 是一个高性能的序列化/反序列化工具，由于其**变长存储特性**并使用了**字节码生成机制**，拥有**较高的运行速度和较小的字节码体积。**

另外，Kryo 已经是一种非常成熟的序列化实现了，已经在 Twitter、Groupon、Yahoo 以及多个著名开源项目（如 Hive、Storm）中广泛的使用。[guide-rpc-framework](https://github.com/Snailclimb/guide-rpc-framework) 就是使用的 kryo 进行序列化，序列化和反序列化相关的代码如下：

```java
/**
 * Kryo serialization class, Kryo serialization efficiency is very high, but only compatible with Java language
 *
 * @author shuang.kou
 * @createTime 2020年05月13日 19:29:00
 */
@Slf4j
public class KryoSerializer implements Serializer {

    /**
     * Because Kryo is not thread safe. So, use ThreadLocal to store Kryo objects
     */
    private final ThreadLocal<Kryo> kryoThreadLocal = ThreadLocal.withInitial(() -> {
        Kryo kryo = new Kryo();
        kryo.register(RpcResponse.class);
        kryo.register(RpcRequest.class);
        return kryo;
    });

    @Override
    public byte[] serialize(Object obj) {
        try (ByteArrayOutputStream byteArrayOutputStream = new ByteArrayOutputStream();
             Output output = new Output(byteArrayOutputStream)) {
            Kryo kryo = kryoThreadLocal.get();
            // Object->byte:将对象序列化为byte数组
            kryo.writeObject(output, obj);
            kryoThreadLocal.remove();
            return output.toBytes();
        } catch (Exception e) {
            throw new SerializeException("Serialization failed");
        }
    }

    @Override
    public <T> T deserialize(byte[] bytes, Class<T> clazz) {
        try (ByteArrayInputStream byteArrayInputStream = new ByteArrayInputStream(bytes);
             Input input = new Input(byteArrayInputStream)) {
            Kryo kryo = kryoThreadLocal.get();
            // byte->Object:从byte数组中反序列化出对象
            Object o = kryo.readObject(input, clazz);
            kryoThreadLocal.remove();
            return clazz.cast(o);
        } catch (Exception e) {
            throw new SerializeException("Deserialization failed");
        }
    }

}
```

Github 地址：https://github.com/EsotericSoftware/kryo 。

**Protobuf**

Protobuf 出自于 Google，性能还比较优秀，也支持多种语言，同时还是跨平台的。就是在使用中过于繁琐，因为**你需要自己定义 IDL 文件和生成对应的序列化代码。**这样虽然不灵活，但是，另一方面导致 protobuf **没有序列化漏洞的风险。**

Protobuf 包含序列化格式的定义、各种语言的库以及一个 IDL 编译器。正常情况下你需要定义 proto 文件，然后使用 IDL 编译器编译成你需要的语言

一个简单的 proto 文件如下：

```protobuf
// protobuf的版本
syntax = "proto3";
// SearchRequest会被编译成不同的编程语言的相应对象，比如Java中的class、Go中的struct
message Person {
  //string类型字段
  string name = 1;
  // int 类型字段
  int32 age = 2;
}
```

Github 地址：https://github.com/protocolbuffers/protobuf。

**ProtoStuff**

由于 Protobuf 的易用性，它的哥哥 Protostuff 诞生了。

protostuff 基于 Google protobuf，但是提供了更多的功能和更简易的用法。虽然更加易用，但是不代表 ProtoStuff 性能更差。

Github 地址：https://github.com/protostuff/protostuff。

**Hessian**

Hessian 是一个轻量级的，自定义描述的二进制 RPC 协议。Hessian 是一个比较老的序列化实现了，并且同样也是跨语言的。Dubbo2.x 默认启用的序列化方式是 Hessian2 ,但是，Dubbo 对 Hessian2 进行了修改，不过大体结构还是差不多。

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/3c5136da-cde3-4287-8ec8-7f9a1ebea123/f89bd67d-d36a-4cbb-a4b3-76400cb48aa2/image.png)

### 总结

Kryo 是专门针对 Java 语言序列化方式并且性能非常好，如果你的应用是专门针对 Java 语言的话可以考虑使用，并且 Dubbo 官网的一篇文章中提到说推荐使用 Kryo 作为生产环境的序列化方式。(文章地址：https://dubbo.apache.org/zh/docs/v2.7/user/references/protocol/rest/)

像 Protobuf、 ProtoStuff、hessian 这类都是跨语言的序列化方式，如果有跨语言需求的话可以考虑使用。除了上面介绍到的序列化方式的话，还有像 Thrift，Avro 这些。

# Socket网络通信实战

## 什么是 Socket(套接字)

Socket 是一个抽象概念，**应用程序可以通过它发送或接收数据。**在使用 Socket 进行网络通信的时候，**通过 Socket 就可以让我们的数据在网络中传输**。操作套接字的时候，和我们读写文件很像。**套接字是 IP 地址与端口的组合，套接字 Socket=（IP 地址：端口号）。**

要通过互联网进行通信，至少需要**一对套接字：**

1. 运行于服务器端的 **Server Socket。**
2. 运行于客户机端的 **Client Socket**

在 Java 开发中使用 Socket 时会常用**到两个类**，都在 `java.net` 包中：

1. `Socket`: 一般用于客户端
2. `ServerSocket` :用于服务端

## Socket网络通信过程

<img src="D:\2024\Notes\Typora\rpc\Guide-rpc.assets\image-20241005222540807.png" alt="image-20241005222540807" style="zoom:50%;" />

**Socket 网络通信过程简单来说分为下面 4 步：**

1. 建立服务端并且监听客户端请求
2. 客户端请求，服务端和客户端建立连接
3. 两端之间可以传递数据
4. 关闭资源

对应到服务端和客户端的话，是下面这样的。

**服务器端：**

1. 创建 `ServerSocket` 对象并且绑定地址（ip）和端口号(port)：`server.bind(new InetSocketAddress(host, port))`
2. 通过 `accept()`方法监听客户端请求
3. 连接建立后，通过输入流读取客户端发送的请求信息
4. 通过输出流向客户端发送响应信息
5. 关闭相关资源

**客户端：**

1. 创建`Socket` 对象并且连接指定的服务器的地址（ip）和端口号(port)：`socket.connect(inetSocketAddress)`
2. 连接建立后，通过输出流向服务器端发送请求信息
3. 通过输入流获取服务器响应的信息
4. 关闭相关资源

[Socket网络通信实战代码](https://www.notion.so/Socket-b32f7cc039154b2787318e5c3dc58884?pvs=21)

# Netty从入门到网络通信实战

## 使用 Netty 能做什么？

理论上 NIO 可以做的事情 ，使用 Netty 都可以做并且更好。Netty 主要用来做**网络通信** :

- **作为 RPC 框架的网络通信工具** ： 我们在分布式系统中，不同服务节点之间经常需要相互调用，这个时候就需要 RPC 框架了。不同服务节点的通信是如何做的呢？可以使用 Netty 来做。比如我调用另外一个节点的方法的话，至少是要让对方知道我调用的是哪个类中的哪个方法以及相关参数吧！
- **实现一个自己的 HTTP 服务器** ：通过 Netty 我们可以自己实现一个简单的 HTTP 服务器，这个大家应该不陌生。说到 HTTP 服务器的话，作为 Java 后端开发，我们一般使用 Tomcat 比较多。一个最基本的 HTTP 服务器可要以处理常见的 HTTP Method 的请求，比如 POST 请求、GET 请求等等。
- **实现一个即时通讯系统** ： 使用 Netty 我们可以实现一个可以聊天类似微信的即时通讯系统，这方面的开源项目还蛮多的，可以自行去 Github 找一找。
- **消息推送系统** ：市面上有很多消息推送系统都是基于 Netty 来做的。
- ......

[Netty使用kryo序列化传输对象实战](https://www.notion.so/Netty-kryo-0e068107acdc49ddae23c8cb6cc64acc?pvs=21)

# 静态代理+JDK/CGLIB动态代理实战

> 1. **静态代理：**代理类（SmsProxy）和被代理类（SmsServiceImpl）都要实现共同的接口（SmsService），然后在代理类中聚合一个SmsService对象，这样这个代理类就可以调用被代理类的方法了。存在的问题就是因为实现了共同的接口，所以接口一增加方法，代理类和被代理类都要修改。
> 2. **动态代理：**利用了反射机制，获取到被代理类的信息，这样任何的类都可以被代理（执行要增强的方法）。
>    1. **JDK代理：**需要被代理类（SmsServiceImpl）实现接口（SmsService）
>    2. **CGLIB代理：**被代理类（SmsServiceImpl）不需要实现接口
> 3. 补充：
>    1. **动态代理是啥？**
>
> - 顾名思义，它真的是动态生成的，即在运行期间生成一个代理类文件 $Proxy0.class ！这个就是增强后的代理类字节码
>
> - 具体参考这位大佬的文章：[JDK动态代理为什么必须要基于接口？](https://blog.csdn.net/Trunks2009/article/details/123106582)
>
>   **b. 为什么JDK代理必须要求实现接口？**
>
> - 我们要扩展一个类无非两种方法，继承或者实现。而我们代理类是动态生成的，JDK代理选择让代理类继承父类Proxy，并把InvocationHandler存在父类的对象中。而java又是单继承，所以只能选择实现SmsService接口来增强被代理类（也就是说JDK代理是 通过让代理类Proxy实现 被代理类SmsServiceImpl的父类接口SmsService 对被代理类扩展）
>
> - 理解：JDK代理类说曾经我没得选（因为代理类必须继承Proxy），现在我只想做个好人（没法继承就只能实现了）🤣
>
>   **c. 为什么CGLIB就不需要实现接口？**
>
> - 上面一点其实已经说明了，JDK代理通过实现类扩展。即然CGLIB不需要实现接口，说明它就是通过**继承**来实现扩展的。
>
>   **d. JDK代理类和CGLIB的相似之处？**
>
> - InvocationHandler的invoke方法 与 MethodInterceptor的intercept方法很类似，它们都用了反射来执行被代理类的方法。

[**静态代理+JDK/CGLIB 动态代理实战**](https://www.notion.so/JDK-CGLIB-bc4ce73ffc404ec8a697c6f0d2451a2f?pvs=21)

[笔记-RPC 07 静态代理+JDK_CGLIB 动态代理实战.pdf](https://prod-files-secure.s3.us-west-2.amazonaws.com/3c5136da-cde3-4287-8ec8-7f9a1ebea123/485b9e54-206d-4d9d-8363-a8786d46e589/笔记-RPC_07_静态代理JDK_CGLIB_动态代理实战.pdf)

# ZooKeeper常用命令+Curator使用详解

[guide-rpc-framework](https://github.com/Snailclimb/guide-rpc-framework) 使用了 Zookeeper 来存储服务的相关信息 ，并且使用的是 ZooKeeper Java客户端  Curator 来对 ZooKeeper 进行增删改查等操作。

## ZooKeeper安装和使用

### 使用Docker安装Zookeeper

1. 使用Docker下载Zookeeper

```jsx
docker pull zookeeper:3.5.8
```

1. 运行Zookeeper

```jsx
docker run -d --name zookeeper -p 2181:2181 zookeeper:3.5.8
```

### 连接Zookeeper服务

1. 进入ZooKeeper容器中

先使用 `docker ps` 查看 ZooKeeper 的 ContainerID，然后使用 `docker exec -it ContainerID /bin/bash` 命令进入容器中。

1. **先进入 bin 目录**,然后通过  `./zkCli.sh -server 127.0.0.1:2181`命令连接ZooKeeper 服务

```bash
root@eaf70fc620cb:/apache-zookeeper-3.5.8-bin# cd bin
```

如果你看到控制台成功打印出如下信息的话，说明你已经成功连接 ZooKeeper 服务。

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/3c5136da-cde3-4287-8ec8-7f9a1ebea123/fb839a1a-8fb7-4b34-be51-3929a2a62383/image.png)

## 常用命令演示

### 查看常用命令(help 命令)

通过 `help` 命令查看 ZooKeeper 常用命令

### 创建节点(create 命令)

通过 `create` 命令在根目录创建了 node1 节点，与它关联的字符串是"node1"

```
[zk: 127.0.0.1:2181(CONNECTED) 34] create /node1 “node1”
```

通过 `create` 命令在根目录创建了 node1 节点，与它关联的内容是数字 123

```
[zk: 127.0.0.1:2181(CONNECTED) 1] create /node1/node1.1 123
Created /node1/node1.1
```

### 更新节点数据内容(set 命令)

```
[zk: 127.0.0.1:2181(CONNECTED) 11] set /node1 "set node1"
```

### 获取节点的数据(get 命令)

`get` 命令可以获取指定节点的数据内容和节点的状态,可以看出我们通过 `set` 命令已经将节点数据内容改为 "set node1"。

```
set node1
cZxid = 0x47
ctime = Sun Jan 20 10:22:59 CST 2019
mZxid = 0x4b
mtime = Sun Jan 20 10:41:10 CST 2019
pZxid = 0x4a
cversion = 1
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 9
numChildren = 1
```

### 查看某个目录下的子节点(ls 命令)

通过 `ls` 命令查看根目录下的节点

```
[zk: 127.0.0.1:2181(CONNECTED) 37] ls /
[dubbo, ZooKeeper, node1]
```

通过 `ls` 命令查看 node1 目录下的节点

```
[zk: 127.0.0.1:2181(CONNECTED) 5] ls /node1
[node1.1]
```

ZooKeeper 中的 ls 命令和 linux 命令中的 ls 类似， 这个命令将列出绝对路径 path 下的所有子节点信息（列出 1 级，并不递归）

### 查看节点状态(stat 命令)

通过 `stat` 命令查看节点状态

```
[zk: 127.0.0.1:2181(CONNECTED) 10] stat /node1
cZxid = 0x47
ctime = Sun Jan 20 10:22:59 CST 2019
mZxid = 0x47
mtime = Sun Jan 20 10:22:59 CST 2019
pZxid = 0x4a
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 11
numChildren = 1
```

上面显示的一些信息比如 cversion、aclVersion、numChildren 等等，我在上面 “[ZooKeeper 相关概念总结(入门)](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html)” 这篇文章中已经介绍到。

### 查看节点信息和状态(ls2 命令)

`ls2` 命令更像是  `ls` 命令和 `stat` 命令的结合。 `ls2` 命令返回的信息包括 2 部分：

1. 子节点列表
2. 当前节点的 stat 信息。

```
[zk: 127.0.0.1:2181(CONNECTED) 7] ls2 /node1
[node1.1]
cZxid = 0x47
ctime = Sun Jan 20 10:22:59 CST 2019
mZxid = 0x47
mtime = Sun Jan 20 10:22:59 CST 2019
pZxid = 0x4a
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 11
numChildren = 1
```

### 删除节点(delete 命令)

这个命令很简单，但是需要注意的一点是如果你要删除某一个节点，那么这个节点必须无子节点才行。

```
[zk: 127.0.0.1:2181(CONNECTED) 3] delete /node1/node1.1
```

在后面我会介绍到 Java 客户端 API 的使用以及开源 ZooKeeper 客户端 ZkClient 和 Curator 的使用。

## Zookeeper Java客户端 Curator简单实用

Curator 是Netflix公司开源的一套 ZooKeeper Java客户端框架，相比于 Zookeeper 自带的客户端 zookeeper 来说，Curator 的封装更加完善，各种 API 都可以比较方便地使用。

Curator4.0+版本对ZooKeeper 3.5.x支持比较好。开始之前，请先将下面的依赖添加进你的项目。

```xml
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>4.2.0</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.2.0</version>
</dependency>
```

### 连接 ZooKeeper 客户端

通过 `CuratorFrameworkFactory` 创建 `CuratorFramework` 对象，然后再调用  `CuratorFramework` 对象的 `start()` 方法即可！

```java
private static final int BASE_SLEEP_TIME = 1000;
private static final int MAX_RETRIES = 3;

// Retry strategy. Retry 3 times, and will increase the sleep time between retries.
RetryPolicy retryPolicy = new ExponentialBackoffRetry(BASE_SLEEP_TIME, MAX_RETRIES);
CuratorFramework zkClient = CuratorFrameworkFactory.builder()
    // the server to connect to (can be a server list)
    .connectString("127.0.0.1:2181")
    .retryPolicy(retryPolicy)
    .build();
zkClient.start();
```

对于一些基本参数的说明：

- `baseSleepTimeMs`：重试之间等待的初始时间
- `maxRetries` ：最大重试次数
- `connectString` ：要连接的服务器列表
- `retryPolicy` ：重试策略

### **数据节点的增删改查**

1. 创建节点

我们在 [ZooKeeper常见概念解读](https://javaguide.cn/distributed-system/distributed-process-coordination/zookeeper/zookeeper-intro.html) 中介绍到，我们通常是将 znode 分为 4 大类：

- **持久（PERSISTENT）节点** ：一旦创建就一直存在即使 ZooKeeper 集群宕机，直到将其删除。
- **临时（EPHEMERAL）节点** ：临时节点的生命周期是与 **客户端会话（session）** 绑定的，**会话消失则节点消失** 。并且，临时节点 **只能做叶子节点** ，不能创建子节点。
- **持久顺序（PERSISTENT_SEQUENTIAL）节点** ：除了具有持久（PERSISTENT）节点的特性之外， 子节点的名称还具有顺序性。比如 `/node1/app0000000001` 、`/node1/app0000000002` 。
- **临时顺序（EPHEMERAL_SEQUENTIAL）节点** ：除了具备临时（EPHEMERAL）节点的特性之外，子节点的名称还具有顺序性。

你在使用的 ZooKeeper 的时候，会发现  `CreateMode` 类中实际有 7种 znode 类型 ，但是用的最多的还是上面介绍的 4 种。

**a.创建持久化节点**

你可以通过下面两种方式创建持久化的节点。

```java
//注意:下面的代码会报错，下文说了具体原因
zkClient.create().forPath("/node1/00001");
zkClient.create().withMode(CreateMode.PERSISTENT).forPath("/node1/00002");
```

但是，你运行上面的代码会报错，这是因为的父节点`node1`还未创建。

你可以先创建父节点 `node1` ，然后再执行上面的代码就不会报错了。

```java
zkClient.create().forPath("/node1");
```

更推荐的方式是通过下面这行代码， **`creatingParentsIfNeeded()` 可以保证父节点不存在的时候自动创建父节点，这是非常有用的。**

```java
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT).forPath("/node1/00001");
```

**b.创建临时节点**

```java
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath("/node1/00001");
```

**c.创建节点并指定数据内容**

```java
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath("/node1/00001","java".getBytes());
zkClient.getData().forPath("/node1/00001");//获取节点的数据内容，获取到的是 byte数组
```

**d.检测节点是否创建成功**

```java
zkClient.checkExists().forPath("/node1/00001");//不为null的话，说明节点创建成功
```

1. 删除节点

**a.删除一个子节点**

```java
zkClient.delete().forPath("/node1/00001");
```

**b.删除一个节点以及其下的所有子节点**

```java
zkClient.delete().deletingChildrenIfNeeded().forPath("/node1");
```

1. 获取/更新节点数据内容

```java
zkClient.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath("/node1/00001","java".getBytes());
zkClient.getData().forPath("/node1/00001");//获取节点的数据内容
zkClient.setData().forPath("/node1/00001","c++".getBytes());//更新节点数据内容
```

1. 获取某个节点的所有子节点路径

```java
List<String> childrenPaths = zkClient.getChildren().forPath("/node1");
```

### 监听器

**下面简单演示一下如何给某个节点注册子节点监听器** 。注册了监听器之后，这个节点的

子节点发生变化比如增加、减少或者更新的时候，你可以自定义回调操作

```jsx
String path = "/node1";
PathChildrenCache pathChildrenCache = new PathChildrenCache(zkClient, path, true);
PathChildrenCacheListener pathChildrenCacheListener = (curatorFramework, pathChildrenCacheEvent) -> {
    // do something
};
pathChildrenCache.getListenable().addListener(pathChildrenCacheListener);
pathChildrenCache.start();
```

如果你要获取节点事件类型的话，可以通过：

```jsx
pathChildrenCacheEvent.getType()
```

一共有下面几种类型：

```java
 public static enum Type {
        CHILD_ADDED,//子节点增加
        CHILD_UPDATED,//子节点更新
        CHILD_REMOVED,//子节点被删除
        CONNECTION_SUSPENDED,
        CONNECTION_RECONNECTED,
        CONNECTION_LOST,
        INITIALIZED;

        private Type() {
        }
    }
```

