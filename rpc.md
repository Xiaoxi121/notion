

## RPC介绍与应用

1. RPC（Remote Procedure Call Protocol） 远程过程调用协议。
2. RPC是一种通过网络从远程计算机程序上请求服务，不需要了解底层网络技术的协议。
3. RPC主要作用就是不同的服务间方法调用就像本地调用一样便捷。

4. 服务化：微服务化，跨平台的服务之间远程调用；
5. 分布式系统架构：分布式服务跨机器进行远程调用；
6. 服务可重用：开发一个公共能力服务，供多个服务远程调用。
7. 系统间交互调用：两台服务器A、B，服务器A上的应用a需要调用服务器B上的应用b提供的方法，而应用a和应用b不在一个内存空间，不能直接调用，此时，需要通过网络传输来表达需要调用的语义及传输调用的数据。

## **常用RPC技术或者框架**

- 应用级的服务框架：阿里的 Dubbo/Dubbox、Google gRPC、Spring Boot/Spring Cloud。
- 远程通信协议：RMI、Socket、SOAP(HTTP XML)、REST(HTTP JSON)。
- 通信框架：MINA 和 Netty

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

## 项目架构图和流程图

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241019200220112.png" alt="image-20241019200220112" style="zoom: 50%;" />

1. 客户端发起请求
2. 客户端根据服务地址通过Netty客户端API**创建一个客户端Channel，并连接到服务端的指定端口。**
3. 客户端**将RPC调用信息（如方法名、参数等）封装成请求消息**，并通过Netty的编码器（Encoder）将请求消息序列化成字节流。
4. 客户端将**序列化后的字节流通过网络发送给服务端。**
5. **服务端接收请求并处理**
6. 服**务端通过Netty服务端API监听指定端口，等待客户端的连接请求。**
7. 当接收到客户端的连接请求时，服务端通过Netty的解码器（Decoder）将接收到的字节流反序列化成请求消息。
8. 服务端根据请求消息中的方法名和参数等信息，通过反射调用本地服务实现，并将执行结果封装成响应消息。
9. 服务端通过Netty的编码器将响应消息序列化成字节流，并通过网络发送给客户端。
10. 客户端接收响应
11. 客户端接收到服务端的响应字节流后，通过Netty的解码器将字节流反序列化成响应消息。
12. 客户端根据响应消息中的结果信息，进行相应的业务处理。



## 如何实现一个基本的rpc调用？

### :star: 基本过程

假设A，B位于不同的服务器，A 想远程调用 B的xxx方法，该通过什么方式来完成这一操作呢

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241005112106108-1729339449578-42.png" alt="image-20241005112106108" style="zoom: 50%;" />

**服务端B：**有一个用户表

1. UserService 里有一个功能： getUserByUserId(Integer id)

2. UserServiceImpl 实现了UserService接口和方法

**客户端A：**

调用getUserByUserId方法， 内部传一个Id给服务端，服务端查询到User对象返回给客户端

- **客户端A怎么实现：**
  - 调用getUserByUserId方法时，内部将调用信息处理后发送给服务端B，告诉B我要获取User
  - 外部调用方法，内部进行其它的处理——这种场景我们可以使用动态代理的方式，改写原本方法的处理逻辑
    （比如，当我们正常调用getUserByUserId方法，代码逻辑是去数据库中找user，但是在当前远程调用的场景下肯定不能走这样的逻辑；
    我们**通过动态代理的方式，绕过【去数据库查询】这原始的逻辑，改成封装信息发送到B中请求调用服务）**
- **服务端B怎么实现：**
  - 监听到A的请求后，接收A的调用信息，并根据信息得到A想调用的服务与方法
  - 根据信息找到对应的服务，进行调用后将结果发送回给A
- **A与B之间怎么通信：**
  - 使用Java的socket网络编程进行通信
  - 为了方便A ，B之间对接收的消息进行处理，我们需要将请求信息和返回信息封装成统一的消息格式

### 1. 代理模式

#### 静态代理

静态代理中，我们对目标对象的每个方法的增强都是手动完成的，非常不灵活
（*比如接口一旦新增加方法，目标对象和代理对象都要进行修改*）且麻烦(*需要对每个目标类都单独写一个代理类*)。

静态代理实现步骤:

- 定义一个接口及其实现类；
- 创建一个代理类同样实现这个接口
- 将目标对象注入进代理类，然后在代理类的对应方法调用目标类中的对应方法。这样的话，我们就可以通过代理类屏蔽对目标对象的访问，并且可以在目标方法执行前后做一些自己想做的事情。

#### 动态代理

动态代理的实现方式有两种，比如 **JDK 动态代理**、**CGLIB** **动态代理**等等。

##### JDK 动态代理机制

JDK 动态代理类使用步骤：

- 定义一个接口及其实现类；
- 自定义 `InvocationHandler` 并重写`invoke`方法，在 `invoke` 方法中我们会调用原生方法（被代理类的方法）并自定义一些处理逻辑；
- 通过 `Proxy.newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)` 方法创建代理对象；

##### CGLIB 动态代理机制

CGLIB 动态代理类使用步骤：

- 定义一个类；
- 自定义 `MethodInterceptor` 并重写 `intercept` 方法，`intercept` 用于拦截增强被代理类的方法，和 JDK 动态代理中的 `invoke` 方法类似；
- 通过 `Enhancer` 类的 `create()`创建代理类；

#### 两种方式对比

- **JDK** **动态代理只能只能代理实现了接口的类，而** **CGLIB** **可以代理未实现任何接口的类。** 另外， CGLIB 动态代理是通过生成一个被代理类的子类来拦截被代理类的方法调用，因此不能代理声明为 final 类型的类和方法。
- 大部分情况都是 JDK 动态代理效率更优秀。



### 定义消息格式

在common包中

定义**请求信息RpcRequest**和**返回信息RpcResponse**

- **为什么请求信息中的服务类名定为接口名？**
  - **因为我们使用动态代理，外部给定的信息是接口信息**

```java
//定义请求信息格式RpcRequest
@Data
@Builder
public class RpcRequest implements Serializable {
    //服务类名，客户端只知道接口
    private String interfaceName;
    //调用的方法名
    private String methodName;
    //参数列表
    private Object[] params;
    //参数类型
    private Class<?>[] paramsType;
}

//定义返回信息格式RpcResponse（类似http格式）
@Data
@Builder
public class RpcResponse implements Serializable {
    //状态码
    private int code;
    //状态信息
    private String message;
    //具体数据
    private Object data;
    //构造成功信息
    public static RpcResponse sussess(Object data){
        return RpcResponse.builder().code(200).data(data).build();
    }
    //构造失败信息
    public static RpcResponse fail(){
        return RpcResponse.builder().code(500).message("服务器发生错误").build();
    }
}
```

### 客户端实现

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241019204113752.png" alt="image-20241019204113752" style="zoom: 33%;" />

**客户端通过获取ClientProxy的代理对象，通过代理对象调用方法，获取结果**

如TestClient

```java
//创建ClientProxy对象
ClientProxy clientProxy=new ClientProxy("127.0.0.1",9999);
//通过ClientProxy对象获取代理对象
UserService proxy=clientProxy.getProxy(UserService.class);
//调用代理对象的方法
User user = proxy.getUserByUserId(1);
System.out.println("从服务端得到的user="+user.toString());
```

ClientProxy类-动态代理的实现

```java
@AllArgsConstructor
public class ClientProxy implements InvocationHandler {
    //传入参数service接口的class对象，反射封装成一个request
    private String host;
    private int port;

    //jdk动态代理，每一次代理对象调用方法，都会经过此方法增强（反射获取request对象，socket发送到服务端）
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //构建request
        RpcRequest request=RpcRequest.builder()
                .interfaceName(method.getDeclaringClass().getName())
                .methodName(method.getName())
                .params(args).paramsType(method.getParameterTypes()).build();
        //IOClient.sendRequest 和服务端进行数据传输
        RpcResponse response= IOClient.sendRequest(host,port,request);
        return response.getData();
    }
     public <T>T getProxy(Class<T> clazz){
        Object o = Proxy.newProxyInstance(clazz.getClassLoader(), new Class[]{clazz}, this);
        return (T)o;
    }
}
```

IOClient类 sendRequest方法负责和服务端进行数据传输

```java
public class IOClient {
    public static RpcResponse sendRequest(String host, int port, RpcRequest request) {
        try {
            // 创建Socket连接
            Socket socket = new Socket(host, port);

            // 获取Socket的输出流
            ObjectOutputStream oos = new ObjectOutputStream(socket.getOutputStream());

            // 获取Socket的输入流
            ObjectInputStream ois = new ObjectInputStream(socket.getInputStream());

            // 将请求对象序列化并通过输出流发送到服务端
            oos.writeObject(request);
            oos.flush();

            // 从输入流中反序列化服务端返回的响应对象
            RpcResponse response = (RpcResponse) ois.readObject();

            // 关闭资源
            oos.close();
            ois.close();
            socket.close();

            return response;
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

### 服务端实现

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241019204134085.png" alt="image-20241019204134085" style="zoom:33%;" />

定义服务端Server的接口，指定方法

```java
public interface RpcServer {
    //开启监听
    void start(int port);
    void stop();
}
```

SimpleRPCRPCServer实现RpcServer接口，实现开启监听的方法

```java
@AllArgsConstructor
public class SimpleRPCRPCServer implements RpcServer {
    private ServiceProvider serviceProvide;
    @Override
    public void start(int port) {
        try {
            ServerSocket serverSocket=new ServerSocket(port);
            System.out.println("服务器启动了");
            while (true) {
                //如果没有连接，会堵塞在这里
                Socket socket = serverSocket.accept();
                //有连接，创建一个新的线程执行处理
                new Thread(new WorkThread(socket,serviceProvide)).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    @Override
    public void stop() {
    }
}
```

**WorkThread类负责启动线程和客户端进行数据传输**

WorkThread类中的getResponse方法负责解析收到的request信息，寻找服务进行调用并返回结果

```java
public class WorkThread implements Runnable{
    private Socket socket;
    private ServiceProvider serviceProvide;
    @Override
    public void run() {
        try {
            ObjectOutputStream oos=new ObjectOutputStream(socket.getOutputStream());
            ObjectInputStream ois=new ObjectInputStream(socket.getInputStream());
            //读取客户端传过来的request
            RpcRequest rpcRequest = (RpcRequest) ois.readObject();
            //反射调用服务方法获取返回值
            RpcResponse rpcResponse=getResponse(rpcRequest);
            //向客户端写入response
            oos.writeObject(rpcResponse);
            oos.flush();
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
    private RpcResponse getResponse(RpcRequest rpcRequest){
        //得到服务名
        String interfaceName=rpcRequest.getInterfaceName();
        //得到服务端相应服务实现类
        Object service = serviceProvide.getService(interfaceName);
        //反射调用方法
        Method method=null;
        try {
            method= service.getClass().getMethod(rpcRequest.getMethodName(), rpcRequest.getParamsType());
            Object invoke=method.invoke(service,rpcRequest.getParams());
            return RpcResponse.sussess(invoke);
        } catch (NoSuchMethodException | IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
            System.out.println("方法执行错误");
            return RpcResponse.fail();
        }
    }
}
```

**因为一个服务器会有多个服务，所以需要设置一个本地服务存放器serviceProvider存放服务**

**在接收到服务端的request信息之后，我们在本地服务存放器找到需要的服务，通过反射调用方法，得到结果并返回**

```java
//本地服务存放器
public class ServiceProvider {
    //集合中存放服务的实例
    private Map<String,Object> interfaceProvider;

    public ServiceProvider(){
        this.interfaceProvider=new HashMap<>();
    }
    //本地注册服务

    public void provideServiceInterface(Object service){
        String serviceName=service.getClass().getName();
        Class<?>[] interfaceName=service.getClass().getInterfaces();

        for (Class<?> clazz:interfaceName){
            interfaceProvider.put(clazz.getName(),service);
        }

    }
    //获取服务实例
    public Object getService(String interfaceName){
        return interfaceProvider.get(interfaceName);
    }
}
```

### 客户端和服务端进行信息传输

服务端在指定端口上进行监听 ，客户端向指定端口发送信息

使用socket创建流进行网络传输

## Netty框架的引入

在客户端与服务端进行网络传输，采用Java原生的socket编程方式，效率低。引入`netty`高性能网络框架，进行优化

netty和传统socket编程相比有哪些优势

- **io传输由BIO ->NIO模式；底层 池化技术复用资源**
- **可以自主编写 编码/解码器，序列化器等等，可拓展性和灵活性高**
- **支持TCP,UDP多种传输协议；支持堵塞返回和异步返回**

**引入netty，就是在part1中的【信息传输部分】进行代码的重构**

在pom.xml中引入netty包

```xml
<dependency>
    <groupId>io.netty</groupId>
    <artifactId>netty-all</artifactId>
    <version>4.1.51.Final</version>
    <scope>compile</scope>
</dependency>
```

### :star: 基本过程

1. 客户端
   1. 配置netty对**消息的处理机制**，指定编码器、解码器、消息格式、消息长度等解决粘包问题，然后指定对接收消息的处理handler
   2. 配置NettyClientHandler，指定对接收消息的处理方式
2. 服务端
   1. NettyRPCServerHandler类，接收来自客户端的信息，并解析调用
3. **客户端调用RpcClient.sendRequest方法 --->NettyClientInitializer-->Encoder编码 --->发送**
4. **服务端RpcServer接收--->NettyServerInitializer-->Decoder解码--->NettyRPCServerHandler ---->getResponse调用---> 返回结果**
5. **客户端接收--->NettyServerInitializer-->Decoder解码--->NettyRPCServerHandler处理结果并返回给上层**

### 客户端重构

找到客户端 进行信息传输的入口

位于ClientProxy中invoke方法内的

```java
//IOClient.sendRequest 和服务端进行数据传输
RpcResponse response= IOClient.sendRequest(host,port,request);
```

对IOClient进行重写

首先对传输的类定义接口RpcClient

好处是：**在ClientProxy中用接口定义一个传输类属性，可以灵活选择不同方式的传输类，耦合性低**

```java
public interface RpcClient {
    
    //定义底层通信的方法
    RpcResponse sendRequest(RpcRequest request);
}
```

传输类NettyRpcClient类 实现接口

```java
public class NettyRpcClient implements RpcClient {
    private String host;
    private int port;
    private static final Bootstrap bootstrap;
    private static final EventLoopGroup eventLoopGroup;
    public NettyRpcClient(String host,int port){
        this.host=host;
        this.port=port;
    }
    //netty客户端初始化
    static {
        eventLoopGroup = new NioEventLoopGroup();
        bootstrap = new Bootstrap();
        bootstrap.group(eventLoopGroup).channel(NioSocketChannel.class)
            //NettyClientInitializer这里 配置netty对消息的处理机制
                .handler(new NettyClientInitializer());
    }
    @Override
    public RpcResponse sendRequest(RpcRequest request) {
        try {
            //创建一个channelFuture对象，代表这一个操作事件，sync方法表示堵塞直到connect完成
            ChannelFuture channelFuture  = bootstrap.connect(host, port).sync();
            //channel表示一个连接的单位，类似socket
            Channel channel = channelFuture.channel();
            // 发送数据
            channel.writeAndFlush(request);
            //sync()堵塞获取结果
            channel.closeFuture().sync();
            // 阻塞的获得结果，通过给channel设计别名，获取特定名字下的channel中的内容（这个在hanlder中设置）
            // AttributeKey是，线程隔离的，不会由线程安全问题。
            // 当前场景下选择堵塞获取结果
            // 其它场景也可以选择添加监听器的方式来异步获取结果 channelFuture.addListener...
            AttributeKey<RpcResponse> key = AttributeKey.valueOf("RPCResponse");
            RpcResponse response = channel.attr(key).get();

            System.out.println(response);
            return response;
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

NettyClientInitializer类，配置netty对**消息的处理机制**

- 指定**编码器（将消息转为字节数组），解码器**（将字节数组转为消息）

- 指**定消息格式，消息长度**，解决**沾包问题**
  - 什么是沾包问题？
  - netty默认底层通过TCP 进行传输，TCP**是面向流的协议**，接收方在接收到数据时无法直接得知一条消息的具体字节数，不知道数据的界限。由于TCP的流量控制机制，发生沾包或拆包，会导致接收的一个包可能会有多条消息或者不足一条消息，从而会出现接收方少读或者多读导致消息不能读完全的情况发生
  - 在发送消息时，先告诉接收方消息的长度，让接收方读取指定长度的字节，就能避免这个问题
- **指定对接收的消息的处理handler**

注：这里的addLast没有先后顺序，netty通过加入的类实现的**接口**来自动识别类实现的是什么功能

```java
public class NettyClientInitializer extends ChannelInitializer<SocketChannel> {
     @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();
        //消息格式 【长度】【消息体】，解决沾包问题
        pipeline.addLast(
                new LengthFieldBasedFrameDecoder(Integer.MAX_VALUE,0,4,0,4));
        
        
        //计算当前待发送消息的长度，写入到前4个字节中
        pipeline.addLast(new LengthFieldPrepender(4));
        
        
        //编码器
        //使用Java序列化方式，netty的自带的解码编码支持传输这种结构
        pipeline.addLast(new ObjectEncoder());
        
        
        //解码器
        //使用了Netty中的ObjectDecoder，它用于将字节流解码为 Java 对象。
        //在ObjectDecoder的构造函数中传入了一个ClassResolver 对象，用于解析类名并加载相应的类。
        pipeline.addLast(new ObjectDecoder(new ClassResolver() {
            @Override
            public Class<?> resolve(String className) throws ClassNotFoundException {
                return Class.forName(className);
            }
        }));
        

        pipeline.addLast(new NettyClientHandler());
    }
}
```

**LengthFieldBasedFrameDecoder**：

- 这个解码器用来从输入流中拆分出一个个独立的数据帧（frame）。它根据数据包头部的长度字段来确定每个数据包的边界。
- 参数说明：
  - `Integer.MAX_VALUE`：表示可以解码的最大消息长度，这里设置为整数的最大值，意味着不限制消息的最大长度。
  - `0`：表示长度字段的起始偏移量，即从消息的起始位置开始计算长度。
  - `4`：表示长度字段的长度，这里是4个字节。
  - `0`：表示消息体的起始偏移量，即消息体紧跟在长度字段后面。
  - `4`：表示长度字段的长度，再次确认长度字段的长度为4个字节。

**LengthFieldPrepender：**

- 这个编码器用来在发送消息之前，将消息的长度写入到前4个字节中。
- 参数说明：
  - 4：表示长度字段的长度，这里是4个字节。

配置NettyClientHandler，指定对接收消息的处理方式

```java
public class NettyClientHandler extends SimpleChannelInboundHandler<RpcResponse> {
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, RpcResponse response) throws Exception {
        // 接收到response, 给channel设计别名，让sendRequest里读取response
        AttributeKey<RpcResponse> key = AttributeKey.valueOf("RPCResponse");
        ctx.channel().attr(key).set(response);
        ctx.channel().close();
    }
    
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        //异常处理
        cause.printStackTrace();
        ctx.close();
    }
}
```

### 服务端重构

NettyRPCRPCServer类实现RpcServer接口

```java
@AllArgsConstructor
public class NettyRPCRPCServer implements RpcServer {
    private ServiceProvider serviceProvider;
    @Override
    public void start(int port) {
        // netty 服务线程组boss负责建立连接， work负责具体的请求
        NioEventLoopGroup bossGroup = new NioEventLoopGroup();
        NioEventLoopGroup workGroup = new NioEventLoopGroup();
        System.out.println("netty服务端启动了");
        try {
            //启动netty服务器
            ServerBootstrap serverBootstrap = new ServerBootstrap();
            //初始化
            serverBootstrap.group(bossGroup,workGroup)
                			.channel(NioServerSocketChannel.class)
                    //NettyClientInitializer这里 配置netty对消息的处理机制
                           .childHandler(new NettyServerInitializer(serviceProvider));
            //同步堵塞
            ChannelFuture channelFuture=serverBootstrap.bind(port).sync();
            //死循环监听
            channelFuture.channel().closeFuture().sync();
        }catch (InterruptedException e){
            e.printStackTrace();
        }finally {
            bossGroup.shutdownGracefully();
            workGroup.shutdownGracefully();
        }
    }

    @Override
    public void stop() {
    }
}
```

NettyServerInitializer类 和NettyClientInitializer类似

NettyRPCServerHandler类，接收来自客户端的信息，并解析调用

```java
@AllArgsConstructor
public class NettyRPCServerHandler extends SimpleChannelInboundHandler<RpcRequest> {
    private ServiceProvider serviceProvider;
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, RpcRequest request) throws Exception {
        //接收request，读取并调用服务
        RpcResponse response = getResponse(request);
        ctx.writeAndFlush(response);
        ctx.close();
    }
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace();
        ctx.close();
    }
    private RpcResponse getResponse(RpcRequest rpcRequest){
        //得到服务名
        String interfaceName=rpcRequest.getInterfaceName();
        //得到服务端相应服务实现类
        Object service = serviceProvider.getService(interfaceName);
        //反射调用方法
        Method method=null;
        try {
            method= service.getClass().getMethod(rpcRequest.getMethodName(), rpcRequest.getParamsType());
            Object invoke=method.invoke(service,rpcRequest.getParams());
            return RpcResponse.sussess(invoke);
        } catch (NoSuchMethodException | IllegalAccessException | InvocationTargetException e) {
            e.printStackTrace();
            System.out.println("方法执行错误");
            return RpcResponse.fail();
        }
    }
}
```

### netty执行流程

![image-20241005112154217](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241005112154217-1729339753861-46.png)

具体到类是

- **客户端调用RpcClient.sendRequest方法 --->NettyClientInitializer-->Encoder编码 --->发送**
- **服务端RpcServer接收--->NettyServerInitializer-->Decoder解码--->NettyRPCServerHandler ---->getResponse调用---> 返回结果**
- **客户端接收--->NettyServerInitializer-->Decoder解码--->NettyRPCServerHandler处理结果并返回给上层**



### **1.netty传输位于网络结构模型中的哪一层？**

**传输层**

Netty支持TCP和UDP等传输层协议，通过对这些协议的封装和抽象，Netty能够处理传输层的数据传输任务，如建立连接、数据传输和连接关闭等。

Netty的EventLoop和EventLoopGroup等组件基于Java NIO的多路复用器（Selector），实现了高效的IO事件处理机制，这在一定程度上与传输层的数据传输和事件处理机制相呼应。

**应用层**

Netty提供了丰富的协议支持，如HTTP、WebSocket、SSL、Protobuf等，这些协议主要工作在应用层。Netty通过编解码器等组件，能够方便地在应用层对数据进行编解码，从而实现与应用层协议的交互。

Netty的ChannelPipeline和ChannelHandler等组件构成了一个灵活的事件处理链，允许开发者在应用层自定义各种事件处理逻辑，如身份验证、消息加密、业务逻辑处理等。

### **2.讲一讲netty在你项目中的作用和执行流程？**

**作用**：引用高性能网络框架netty，实现了高效的信息传输；抽象了Java NIO底层的复杂性，提供了简单易用的API，简化了网络编程；提供各种组件方便网络数据的处理

**执行流程：**

1. 客户端发起请求
2. 客户端根据服务地址通过Netty客户端API**创建一个客户端Channel，并连接到服务端的指定端口。**
3. 客户端**将RPC调用信息（如方法名、参数等）封装成请求消息**，并通过Netty的编码器（Encoder）将请求消息序列化成字节流。
4. 客户端将**序列化后的字节流通过网络发送给服务端。**
5. **服务端接收请求并处理**
6. 服**务端通过Netty服务端API监听指定端口，等待客户端的连接请求。**
7. 当接收到客户端的连接请求时，服务端通过Netty的解码器（Decoder）将接收到的字节流反序列化成请求消息。
8. 服务端根据请求消息中的方法名和参数等信息，通过反射调用本地服务实现，并将执行结果封装成响应消息。
9. 服务端通过Netty的编码器将响应消息序列化成字节流，并通过网络发送给客户端。
10. 客户端接收响应
11. 客户端接收到服务端的响应字节流后，通过Netty的解码器将字节流反序列化成响应消息。
12. 客户端根据响应消息中的结果信息，进行相应的业务处理。

### 3. 在RPC框架中如何处理异步调用？

Netty通过Channel和ChannelFuture来支持异步调用。**客户端发送请求后，会得到一个ChannelFuture对象，可以注册监听器来处理响应结果。**

### 4. Netty是怎么实现长连接的

在 Netty 中实现长连接（Long-Lived Connection）主要是通过以下几个方面来实现的：

1. 异步非阻塞 IO

Netty 基于 NIO（Non-blocking I/O）模型，使用异步非阻塞的方式处理网络通信。这意味着当一个连接建立后，Netty 会将读写操作放入事件循环（EventLoop）中处理，而不是直接阻塞当前线程。这样，即使在网络流量较大时，也能有效地利用线程资源。

2. 心跳机制

为了维持长连接的有效性，Netty 支持心跳机制（Heartbeat）。心跳机制通过定期发送心跳包来检测连接是否仍然活跃。如果一段时间内没有数据交换，心跳机制可以帮助检测连接是否正常，从而避免无效连接占用资源。

3. 连接池

虽然 Netty 本身并不是一个连接池框架，但它可以很容易地与连接池一起工作。连接池可以复用已经建立的连接，避免频繁地建立和销毁连接带来的性能损耗。Netty 的连接通常会在首次建立后一直保持活跃状态，直到显式关闭或达到生命周期的终点。

4. 生命周期管理

Netty 提供了连接的生命周期管理机制，包括连接的建立、激活、去激活和关闭等阶段。通过这些阶段，可以方便地进行连接状态的监控和管理。

5. 优雅关闭

Netty 支持优雅地关闭连接，这通常通过 ChannelFutureListener 的 onCloseComplete 方法来实现。当一个连接不再需要时，可以通过 ChannelFuture 的 sync() 方法等待关闭操作完成，确保所有相关资源都被正确释放。

6. ChannelHandler

Netty 的 ChannelHandler 机制使得开发者可以方便地添加自定义处理逻辑来处理连接相关的事件，如连接建立、数据接收、异常处理等。这些处理逻辑可以帮助维护长连接的稳定性和有效性。

### 5. 为什么用Netty？Netty和Scoket的区别

**socket**

Socket编程主要涉及到客户端和服务端两个方面，
首先是在服务器端创建一个服务器套接字(ServerSocket)，并把它附加到一个端口上，服务器从这个端口监听连接。

客户端请求与服务器进行连接的时候，根据服务器的域名或者IP地址，加上端口号，打开一个套接字。当服务器接受连接后，服务器和客户端之间的通信就像输入输出流一样进行操作。

**缺点**

1. 需对传输的数据进行解析，转化成应用级的数据
2. 对开发人员的开发水平要求高
3. 相对于Http协议传输，增加了开发量

**Netty**

写入和 ChannelHandler 绑定的 ChannelHandlerContext 中，消息从 ChannelPipeline 中的下一个ChannelHandler 中移动



## Netty

### redis，nginx，netty 是依赖什么做的这么高性能？（多Reactor多进程、单Reactor单进程 Reactor）

主要是依赖**Reactor 模式**，也就是来了一个事件，Reactor 就有相对应的反应/响应

- Reactor 负责监听和分发事件，事件类型包含连接事件、读写事件
- 处理资源池负责处理事件，如 read -> 业务逻辑 -> send

**Redis**

![image-20241020175102710](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020175102710.png)

Redis 是由 C 语言实现的，在 Redis 6.0 版本之前采用的正是「单 Reactor 单进程」的方案

- Reactor 对象通过 select （IO 多路复用接口） 监听事件，收到事件后通过 dispatch 进行分发，具体分发给 Acceptor 对象还是 Handler 对象，还要看收到的事件类型
- 如果是连接建立的事件，则交由 Acceptor 对象进行处理，Acceptor 对象会通过 accept 方法 获取连接，并创建一个 Handler 对象来处理后续的响应事件
- 如果不是连接建立事件， 则交由当前连接对应的 Handler 对象来进行响应
- Handler 对象通过 read -> 业务处理 -> send 的流程来完成完整的业务流程

存在缺点：

- 第一个缺点，因为只有一个进程，无法充分利用 多核 CPU 的性能
- 第二个缺点，Handler 对象在业务处理时，整个进程是无法处理其他连接的事件的，如果业务处理耗时比较长，那么就造成响应的延迟

**nginx** 

nginx是多 Reactor 多进程方案，不过方案与标准的多 Reactor 多进程有些差异

具体差异表现在主进程中仅仅用来初始化 socket，并没有创建 mainReactor 来 accept 连接，而是由子进程的 Reactor 来 accept 连接

**Netty**

![image-20241020175710105](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020175710105.png)

- 主线程中的 MainReactor 对象通过 select 监控连接建立事件，收到事件后通过 Acceptor 对象中的 accept 获取连接，将新的连接分配给某个子线程
- 子线程中的 SubReactor 对象将 MainReactor 对象分配的连接加入 select 继续进行监听，并创建一个 Handler 用于处理连接的响应事件
- 如果有新的事件发生时，SubReactor 对象会调用当前连接对应的 Handler 对象来进行响应
- Handler 对象通过 read -> 业务处理 -> send 的流程来完成完整的业务流程。

优势：

- 主线程和子线程分工明确，主线程只负责接收新连接，子线程负责完成后续的业务处理
- 主线程和子线程的交互很简单，主线程只需要把新连接传给子线程，子线程无须返回数据，直接就可以在子线程将处理结果发送给客户端

### Netty是什么

1. Netty 是⼀个 **基于 NIO** 的 client-server(客户端服务器)框架，使⽤它可以快速简单地开发⽹络应⽤程序。
2. 它极⼤地简化并优化了 TCP 和 UDP 套接字服务器等⽹络编程,并且性能以及安全性等很多⽅⾯甚⾄都要更好。

### BIO,NIO 和 AIO？（阻塞IO/非阻塞IO）

- **BIO：** 同步阻塞 I/O 模式，数据的读取写⼊必须阻塞在⼀个线程内等待其完成
- **NIO**：同步⾮阻塞的 I/O 模型，线程不断地轮询调用 `read` 操作来判断是否有数据，提供了 Channel , Selector ， Buffer等抽象
- **AIO**：异步⾮阻塞的 IO 模型，当后台处理完成，操作系统会通知相应的线程进⾏后续的操作

### 直接用 NIO和用 Netty？（Netty的优势）

对编程功底要求⽐较⾼，⽽且，NIO 在⾯对断连重连、包丢失、粘包等问题时处理过程⾮常复杂。Netty相对来说有这些优势

- 统⼀的 API，⽀持多种传输类型，阻塞和⾮阻塞的
- 简单⽽强⼤的线程模型
- ⾃带编解码器解决 TCP 粘包/拆包问题
- 安全性不错，有完整的 SSL/TLS 以及 StartTLS ⽀持
- ⽐直接使⽤ Java 核⼼ API 有更⾼的吞吐量、更低的延迟、更低的资源消耗和更少的内存复制

### Netty 应用场景？

Netty 主要⽤来做**⽹络通信**：

1. **作为 RPC 框架的⽹络通信⼯具**：⽐如我调⽤另外⼀个节点的⽅法的话，⾄少是要让对知道我调⽤的是哪个类中的哪个⽅法以及相关参数吧
2. 可以聊天类似微信的即时通讯系统
3. **实现消息推送系统**：比如市面上的像Nacos，RocketMQ、Dubbo

### Netty 的核心组件？

 **Selector**

Netty 基于 **Selector 对象实现 I/O 多路复用**，通过 Selector **一个线程可以监听多个连接的 Channel 事件**。 当向一个 **Selector 中注册 Channel 后**，Selector 内部的机制就可以**自动不断地查询（Select）\**这些注册的 Channel 是否\**有已就绪的 I/O 事件**（例如可读，可写，网络连接完成等），这样程序就可以很简单地使用一个线程高效地管理多个 Channel。

**Bytebuf（字节容器）**

⽹络通信最终都是通过字节流进⾏传输的。 `ByteBuf` 就是 Netty 提供的⼀个字节容器，其内部是⼀个字节数组。 当我们通过 Netty 传输数据的时候，就是通过 `ByteBuf `进⾏的，可以将 `ByteBuf `看作是 Netty 对 `ByteBuffer` 字节容器的封装和抽象

**Bootstrap 和 ServerBootstrap（启动引导类）**

1. `Bootstrap` 通常使⽤ `connect()` ⽅法连接到远程的主机和端⼝，作为⼀个 Netty TCP 协议通信中的客户端
2. `ServerBootstrap` 通常使⽤ `bind()` ⽅法绑定本地的端⼝上，然后等待客户端的连接
3. `Bootstrap` 只需要配置⼀个线程组 `EventLoopGroup` ,⽽ `ServerBootstrap` 需要配置两个线程组`EventLoopGroup` ，⼀个⽤于接收连接，⼀个⽤于具体的 IO 处理。第一个EventLoopGroup通常只有一个EventLoop，通常叫做bossGroup，负责客户端的连接请求

**Channel（⽹络操作抽象类）**

`Channel` 接⼝是 Netty 对⽹络操作抽象类。通过 `Channel` 我们可以进⾏ I/O 操作。

**EventLoop（事件循环）**

`EventLoop`的主要作⽤实际就是责监听⽹络事件并调⽤事件处理器进⾏相关 I/O 操作（读写）的处理

![image-20241020175726559](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020175726559.png)

**ChannelHandler（消息处理器） 和 ChannelPipeline（ChannelHandler 对象链表）**

`ChannelHandler`是消息的具体处理器，主要负责处理客户端/服务端接收和发送的数据，当`Channel` 被创建时，它会被⾃动地分配到它专属的 `ChannelPipeline`

 当`ChannelHandler` 被添加到的 `ChannelPipeline` 它得到⼀个 `ChannelHandlerContext` ，它代表⼀个` ChannelHandler` 和 `ChannelPipeline` 之间的"绑定"

**ChannelFuture（操作执⾏结果）**

Netty 中所有的 I/O 操作都为异步的，我们不能⽴刻得到操作是否执⾏成功

可以通过 `ChannelFuture` 接⼝的 `addListener()` ⽅法注册⼀个` ChannelFutureListener `，当操作执⾏成功或者失败时，监听就会⾃动触发返回结果

### Netty-`NioEventLoopGroup` 默认的构造函数会起多少线程呢？

`NioEventLoopGroup` 默认的构造函数实际会起的线程数为 `CPU核⼼数*2`

### `Reactor`/`Proactor`(非阻塞同步网络模式/异步网络模式)

![image-20241020175746722](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020175746722.png)

- Reactor 是非阻塞同步网络模式，感知的是就绪可读写事件。
- Proactor 是异步网络模式， 感知的是已完成的读写事件

Reactor 可以理解为「来了事件操作系统通知应用进程，让应用进程来处理」，而 Proactor 可以理解为「来了事件操作系统来处理，处理完再通知应用进程」

### 主从多线程 Reactor

⼀组 NIO 线程负责接受请求，⼀组 NIO 线程处理 IO 操作

### Netty 线程模型？

Netty 主要靠 `NioEventLoopGroup` 线程池来实现具体的线程模型的

实现服务端的时候，⼀般会初始化两个线程组：

- `bossGroup`：接收连接
- `workerGroup`：负责具体的处理，交由对应的 `Handler` 处理

单线程模型：`eventGroup`既用于处理客户端连接，又负责具体的处理

单线程模型：⼀个 Acceptor 线程只负责监听客户端的连接，⼀个 NIO 线程池负责具体处理

主从多线程模型：从⼀个 主线程 NIO 线程池中选择⼀个线程作为 `Acceptor` 线程，绑定监听端⼝，接收客户端连接的连接，其他线程负责后续的接⼊认证等⼯作。连接建⽴完成后`SubNIO `线程池负责具体处理 I/O 读写

### Netty 服务端和客户端的启动过程？（Netty启动过程）

**服务端**

1. 创建了两个 `NioEventLoopGroup` 对象实例： `bossGroup` 和 `workerGroup`
2. 创建了⼀个服务端启动引导类： `ServerBootstrap`
3. 通过 `.group()` ⽅法给引导类 `ServerBootstrap` 配置两⼤线程组，确定了线程模型
4. 通过 `channel()` ⽅法给引导类 `ServerBootstrap` 指定了 IO 模型为 NIO
5. `bind()`绑定端口

**客户端**

1. 创建⼀个 `NioEventLoopGroup `对象实例
2. 创建客户端启动的引导类是 `Bootstrap`
3. 通过 `.group() `⽅法给引导类 `Bootstrap` 配置⼀个线程组
4. 通过` channel()` ⽅法给引导类 `Bootstrap` 指定了 IO 模型为 NIO
5. 通过 `.childHandler()` 给引导类创建⼀个 `ChannelInitializer` ，然后指定了客户端消息的业务处理逻辑 `HelloClientHandler` 对象
6. 调⽤ `Bootstrap` 类的 `connect()` ⽅法设定好ip和端口进⾏连接

### Netty 长连接、心跳机制了解么?

**TCP长连接和短链接**

TCP 在进行读写之前，`server` 与 `client`之间必须提前建立一个连接。建立连接的过程，需要三次握手，释放/关闭连接的话需要四次挥手。这个过程是比较消耗网络资源并且有时间延迟的。

所谓，短连接说的就是 `server` 端 与 `client `端建立连接之后，读写完成之后就关闭掉连接，如果下一次再要互相发送消息，就要重新连接。短连接的优点很明显，就是管理和实现都比较简单，缺点也很明显，每一次的读写都要建立连接必然会带来大量网络资源的消耗，并且连接的建立也需要耗费时间。

长连接说的就是 `client` 向 `server` 双方建立连接之后，即使 `client`与`server` 完成一次读写，它们之间的连接并不会主动关闭，后续的读写操作会继续使用这个连接。长连接的可以省去较多的TCP 建立和关闭的操作，降低对网络资源的依赖，节约时间。对于频繁请求资源的客户来说，非常适用长连接。

**为什么需要心跳机制，Netty中心跳机制了解吗**

在 TCP 保持长连接的过程中，可能会出现断网等网络异常出现，异常发生的时候， `client`与`server` 之间如果没有交互的话，它们是无法发现对方已经掉线的。为了解决这个问题，我们就需要引入心跳机制。

心跳机制的工作原理是：在 `client` 与 `server` 之间在一定时间内没有数据交互时，即处于 `idle` 状态时，客户端或服务器就会发送一个特殊的数据包给对方，当接收方收到这个数据报文后，也立即发送一个特殊的数据报文，回应发送方，此即一个 `PING-PONG` 交互。所以，当某一端收到心跳消息后，就知道了对方仍然在线，这就确保 TCP连接的有效性。

TCP 实际上自带的就有长连接选项，本身是也有心跳包机制，也就是TCP的选项：`SO_KEEPALIVE`。但是，TCP 协议层面的长连接灵活性不够。所以，一般情况下我们都是在应用层协议上实现自定义心跳机制的，也就是在 Netty 层面通过编码实现。通过 Netty 实现心跳机制的话，核心类是 `IdleStateHandler`

### Netty-零拷贝

Netty 中的零拷贝与操作系统层面上的不太一样，操作系统通过mmap或者sendfile来实现，Netty的零拷贝完全是在用户态层面的，他更偏向于优化数据操作这样的概念

- Netty 提供了 `CompositeByteBuf` 类，它可以将多个 ByteBuf 合并为一个逻辑上的 ByteBuf，避免了各个 ByteBuf 之间的拷贝
- 通过 wrap 操作，我们可以将 byte[] 数组、ByteBuf、ByteBuffer等包装成一个 Netty ByteBuf 对象，进而避免了拷贝操作
- ByteBuf 支持 slice 操作，因此可以将 ByteBuf 分解为多个共享同一个存储区域的 ByteBuf，避免了内存的拷贝
- 通过 `FileRegion` 包装的`FileChannel.tranferTo` 实现文件传输，可以直接将文件缓冲区的数据发送到目标 `Channel`，避免了传统通过循环 write 方式导致的内存拷贝问题

## Zookeeper

### 什么是Zookeeper

ZooKeeper 是一个开源的**分布式协调服务**，为我们提供了高可用、高性能、稳定的分布式数据一致性解决方案，通常被用于实现诸如**数据发布/订阅、负载均衡、**命名服务、分布式协调/通知、集群管理、**Master 选举、分布式锁和分布式队列**等功能。这些功能的实现主要依赖于 ZooKeeper 提供的 **数据存储+事件监听** 功能。

- 命名服务：可以通过 ZooKeeper 的顺序节点生成全局唯一 ID。
- 数据发布/订阅：通过 Watcher 机制 可以很方便地实现数据发布/订阅。当你将数据发布到 ZooKeeper 被监听的节点上，其他机器可通过监听 ZooKeeper 上节点的变化来实现配置的动态更新。
- 分布式锁：通过创建唯一节点获得分布式锁，当获得锁的一方执行完相关代码或者是挂掉之后就释放锁。分布式锁的实现也需要用到 Watcher 机制 ，我在 分布式锁详解 这篇文章中有详细介绍到如何基于 ZooKeeper 实现分布式锁。

ZooKeeper **将数据保存在内存中，性能是不错的。 在“读”多于“写”的应用程序中尤其地高性能**，因为“写”会导致所有的服务器间同步状态。（“读”多于“写”是协调服务的典型场景）

很多顶级的开源项目都用到了 ZooKeeper，比如：

- **Kafka** : ZooKeeper 主要为 Kafka 提供 Broker 和 Topic 的注册以及多个 Partition 的负载均衡等功能。不过，在 Kafka 2.8 之后，引入了基于 Raft 协议的 KRaft 模式，不再依赖 Zookeeper，大大简化了 Kafka 的架构。
- **Hbase** : ZooKeeper 为 Hbase 提供确保整个集群只有一个 Master 以及保存和提供 regionserver 状态信息（是否在线）等功能。
- **Hadoop** : ZooKeeper 为 Namenode 提供高可用支持。

### ZooKeeper特点

- **顺序一致性**： 从同一客户端发起的事务请求，最终将会严格地按照顺序被应用到 ZooKeeper 中去。
- **原子性：** 所有事务请求的处理结果**在整个集群中所有机器上的应用情况是一致的**，也就是说，要么整个集群中所有的机器都成功应用了某一个事务，要么都没有应用。
- 单一系统映像： 无论客户端连到哪一个 ZooKeeper 服务器上，其看到的服务端数据模型都是一致的。
- **可靠性： 一旦一次更改请求被应用，更改的结果就会被持久化**，直到被下一次更改覆盖。
- **实时性： 一旦数据发生变更，其他节点会实时感知到**。每个客户端的系统视图都是最新的。
- **集群部署：**3~5 台（最好奇数台）机器就可以组成一个集群，每台机器都在内存保存了 ZooKeeper 的全部数据，机器之间互相通信同步数据，客户端连接任何一台机器都可以。
- **高可用：**如果某台机器宕机，会保证数据不丢失**。集群中挂掉不超过一半的机器，都能保证集群可用**。比如 3 台机器可以挂 1 台，5 台机器可以挂 2 台

### -----Zookeeper重要概念------

### 1. Datamodel数据模型

**ZooKeeper 数据模型采用层次化的多叉树形结构，每个节点上都可以存储数据，**这些数据可以是数字、字符串或者是二进制序列。并且。每个节点还可以拥有 N 个子节点，最上层是根节点以“/”来代表。每个数据节点在 ZooKeeper 中被称为 **znode**，它是 ZooKeeper 中数据的最小单元。并且，每个 znode 都有一个唯一的路径标识。

强调一句：**ZooKeeper 主要是用来协调服务的，而不是用来存储业务数据的，所以不要放比较大的数据在 znode 上，ZooKeeper 给出的每个节点的数据大小上限是 1M 。**

### 2. znode数据节点

我们通常是将 znode 分为 4 大类：

- **持久（PERSISTENT）节点**：一旦创建就一直存在即使 ZooKeeper 集群宕机，直到将其删除。
- **临时（EPHEMERAL）节点**：临时节点的生命周期是与 **客户端会话（session）** 绑定的，**会话消失则节点消失**。并且，**临时节点只能做叶子节点** ，不能创建子节点。
- **持久顺序（PERSISTENT_SEQUENTIAL）节点**：除了具有持久（PERSISTENT）节点的特性之外， 子节点的名称还具有顺序性。比如 `/node1/app0000000001`、`/node1/app0000000002` 。
- **临时顺序（EPHEMERAL_SEQUENTIAL）节点**：除了具备临时（EPHEMERAL）节点的特性之外，子节点的名称还具有顺序性

每个 znode 由 2 部分组成:

- **stat**：状态信息
- **data**：节点存放的数据的具体内容

通过 get 命令来获取 根目录下的 dubbo 节点的内容

Stat 类中包含了一个数据节点的所有状态信息的字段**，包括事务 ID（cZxid）、节点创建时间（ctime） 和子节点个数（numChildren） 等等。**

| znode 状态信息 | 解释                                                         |
| -------------- | ------------------------------------------------------------ |
| cZxid          | create ZXID，即该数据节点被创建时的事务 id                   |
| ctime          | create time，即该节点的创建时间                              |
| mZxid          | modified ZXID，即该节点最终一次更新时的事务 id               |
| mtime          | modified time，即该节点最后一次的更新时间                    |
| pZxid          | 该节点的子节点列表最后一次修改时的事务 id，只有子节点列表变更才会更新 pZxid，子节点内容变更不会更新 |
| cversion       | 子节点版本号，当前节点的子节点每次变化时值增加 1             |
| dataVersion    | 数据节点内容版本号，节点创建时为 0，每更新一次节点内容(不管内容有无变化)该版本号的值增加 1 |
| aclVersion     | 节点的 ACL 版本号，表示该节点 ACL 信息变更次数               |
| ephemeralOwner | 创建该临时节点的会话的 sessionId；如果当前节点为持久节点，则 ephemeralOwner=0 |
| dataLength     | 数据节点内容长度                                             |
| numChildren    | 当前节点的子节点个数                                         |

### 3. 版本version

对应于每个 znode，ZooKeeper 都会为其维护一个叫作 Stat 的数据结构**，Stat 中记录了这个 znode 的三个相关的版本：**

- dataVersion：当前 znode 节点的版本号
- cversion：当前 znode 子节点的版本
- aclVersion：当前 znode 的 ACL 的版本

### 4. ACL权限控制

ZooKeeper 采用 ACL（AccessControlLists）策略来进行权限控制，类似于 UNIX 文件系统的权限控制。

对于 znode 操作的权限，ZooKeeper 提供了以下 5 种：

- **CREATE** : 能创建子节点
- **READ**：能获取节点数据和列出其子节点
- **WRITE** : 能设置/更新节点数据
- **DELETE** : 能删除子节点
- **ADMIN** : 能设置节点 ACL 的权限

其中尤其需要注意的是，**CREATE** 和 **DELETE** 这两种权限都是针对 **子节点** 的权限控制。

对于身份认证，提供了以下几种方式：

- **world**：默认方式，所有用户都可无条件访问。
- **auth** :不使用任何 id，代表任何已认证的用户。
- **digest** :用户名:密码认证方式：*username:password* 。
- **ip** : 对指定 ip 进行限制

### 5. Watcher事件监听器

Watcher（事件监听器），是 ZooKeeper 中的一个很重要的特性。ZooKeeper 允许用户在指定节点上注册一些 Watcher，**并且在一些特定事件触发的时候，ZooKeeper 服务端会将事件通知到感兴趣的客户端上去，**该机制是 ZooKeeper 实现分布式协调服务的重要特性。

![image-20241020175827979](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020175827979.png)

### 6. 会话Session

**Session 可以看作是 ZooKeeper 服务器与客户端的之间的一个 TCP 长连接**，通过这个连接，**客户端能够通过心跳检测与服务器保持有效的会话，也**能够向 ZooKeeper 服**务器发送请求并接受响应**，同时还能**够通过该连接接收来自服务器的 Watcher 事件通知。**

Session 有一个属性叫做：`sessionTimeout` ，`sessionTimeout` 代表会话的超时时间。当由于服务器压力太大、网络故障或是客户端主动断开连接等各种原因导致客户端连接断开时，只要在`sessionTimeout`规定的时间内能够重新连接上集群中任意一台服务器，那么之前创建的会话仍然有效。

另外，在为客户端创建会话之前，服务端首先会为每个客户端都分配一个 `sessionID`。由于 `sessionID`是 ZooKeeper 会话的一个重要标识，许多与会话相关的运行机制都是基于这个 `sessionID` 的，因此，无论是哪台服务器为客户端分配的 `sessionID`，都务必保证全局唯一。

### ------Zookeeper集群--------

最典型集群模式**：Master/Slave 模式（主备模式）**。
在这种模式中，**通常 Master 服务器作为主服务器提供写服务，其他的 Slave 服务器从服务器通过异步复制的方式获取 Master 服务器最新的数据提**供读服务

### Zookeeper集群角色

在 ZooKeeper 中没有选择传统的 Master/Slave 概念，而是引入了 L**eader、Follower 和 Observer** 三种角色

|   角色   |                             说明                             |
| :------: | :----------------------------------------------------------: |
|  Leader  | **为客户端提供读和写的服务**，负责**投票的发起和决议**，更新系统状态。 |
| Follower | 为客户端提供读服务，如果是写服务则转发给 Leader。参与选举过程中的投票。 |
| Observer | 为客户端提供读服务，如果是写服务则转发给 Leader。<br />**不参与选举**过程中的投票，也**不参与“过半写成功”策略**。<br />在不影响写性能的情况下提升集群的读性能。此角色于 ZooKeeper3.3 系列新增的角色 |

### Zookeeper集群Leader选举过程

当 Leader 服务器出现网络中断、崩溃退出与重启等异常情况时，就会进入 Leader 选举过程，这个过程会选举产生新的 Leader 服务器。

这个过程大致是这样的：

1. **Leader election（选举阶段）**：节点在一开始都处于选举阶段，**只要有一个节点得到超半数节点的票数，它就可以当选准 leader。**
2. **Discovery（发现阶段）**：在这个阶段**，followers 跟准 leader 进行通信，**同步 followers 最近接收的事务提议。
3. **Synchronization（同步阶段）**：**同步阶段主要是利用 leader 前一阶段获得的最新提议历史，同步集群中所有的副本**。同步完成之后准 leader 才会成为真正的 leader。
4. **Broadcast（广播阶段）**：到了这个阶段，ZooKeeper 集群才能正式对外提供事务服务，并且 leader 可以进行消息广播。同时如果有新的节点加入，还需要对新节点进行同步。

ZooKeeper 集群中的服务器状态有下面几种：

- **LOOKING**：寻找 Leader。
- **LEADING**：Leader 状态，对应的节点为 Leader。
- **FOLLOWING**：Follower 状态，对应的节点为 Follower。
- **OBSERVING**：Observer 状态，对应节点为 Observer，该节点不参与 Leader 选举。

### Zookeeper集群奇数原因

ZooKeeper 集群在宕掉几个 ZooKeeper 服务器之后，**如果剩下的 ZooKeeper 服务器个数大于宕掉的个数的话整个 ZooKeeper 才依然可用。**
假如我们的集群中有 n 台 ZooKeeper 服务器，那么也就是剩下的服务数必须大于 n/2。先说一下结论，2n 和 2n-1 的容忍度是一样的，都是 n-1，大家可以先自己仔细想一想，这应该是一个很简单的数学问题了。
比如假如我们有 3 台，那么最大允许宕掉 1 台 ZooKeeper 服务器，如果我们有 4 台的的时候也同样只允许宕掉 1 台。
假如我们有 5 台，那么最大允许宕掉 2 台 ZooKeeper 服务器，如果我们有 6 台的的时候也同样只允许宕掉 2 台。
**综上，何必增加那一个不必要的 ZooKeeper 呢？**

### Zookeeper选举的过半机制

**何为集群脑裂？**
对于一个集群，通常多台机器会部署在不同机房，来提高这个集群的可用性。保证可用性的同时，会发生一种机房间网络线路故障，导致机房间网络不通，而集群被割裂成几个小集群。这时候**子集群各自选主导致“脑裂”的情况。**
举例说明：比如现在有一个由 6 台服务器所组成的一个集群，部署在了 2 个机房，每个机房 3 台。正常情况下只有 1 个 leader，但是当两个机房中间网络断开的时候，每个机房的 3 台服务器都会认为另一个机房的 3 台服务器下线，而选出自己的 leader 并对外提供服务。若没有过半机制，当网络恢复的时候会发现有 2 个 leader。仿佛是 1 个大脑（leader）分散成了 2 个大脑，这就发生了脑裂现象。脑裂期间 2 个大脑都可能对外提供了服务，这将会带来数据一致性等问题。
**过半机制是如何防止脑裂现象产生的？**

ZooKeeper 的**过半机制导致不可能产生 2 个 leader，因为少于等于一半是不可能产生 leader 的，这就使得不论机房的机器如何分配都不可能发生脑裂。**

### ------ZAB协议和Paxos算法-----

Paxos 算法应该可以说是 ZooKeeper 的灵魂了。**ZooKeeper 并没有完全采用 Paxos 算法 ，而是使用 ZAB 协议作为其保证数据一致性的核心算法**。ZAB 协议并不像 Paxos 算法那样，是一种通用的分布式一致性算法，它是一种特别为 Zookeeper 设计的崩溃可恢复的原子消息广播算法

ZAB（ZooKeeper Atomic Broadcast，原子广播） 协议是为分布式协调服务 ZooKeeper 专门设计的一种支持崩溃恢复的原子广播协议。 在 ZooKeeper 中，主要依赖 ZAB 协议来实现分布式数据一致性，基于该协议，**ZooKeeper 实现了一种主备模式的系统架构来保持集群中各个副本之间的数据一致性**

### ZAB协议的两种基本模式：崩溃恢复和消息广播

ZAB 协议包括两种基本的模式，分别是
**崩溃恢复：**当整个服务框架在启动过程中，或是当 Leader 服务器出现网络中断、崩溃退出与重启等异常情况时，ZAB 协议就会进入恢复模式并选举产生新的 Leader 服务器。当选举产生了新的 Leader 服务器，同时集群中已经有过半的机器与该 Leader 服务器完成了状态同步之后，ZAB 协议就会退出恢复模式。其中，所谓的状态同步是指数据同步，用来保证集群中存在过半的机器能够和 Leader 服务器的数据状态保持一致。
**消息广播：**当集群中已经有过半的 Follower 服务器完成了和 Leader 服务器的状态同步，那么整个服务框架就可以进入消息广播模式了。 当一台同样遵守 ZAB 协议的服务器启动后加入到集群中时，如果此时集群中已经存在一个 Leader 服务器在负责进行消息广播，那么新加入的服务器就会自觉地进入数据恢复模式：找到 Leader 所在的服务器，并与其进行数据同步，然后一起参与到消息广播流程中去。

### ------Zookeeper和ETCD-------

[ETCD](https://etcd.io/) 是一种强一致性的分布式键值存储，它提供了一种可靠的方式来存储需要由分布式系统或机器集群访问的数据。ETCD 内部采用 [Raft 算法](https://javaguide.cn/distributed-system/protocol/raft-algorithm.html)作为一致性算法，基于 Go 语言实现。

|             |                          ZooKeeper                           |                      ETCD                       |
| :---------: | :----------------------------------------------------------: | :---------------------------------------------: |
|    语言     |                             Java                             |                       Go                        |
|    协议     |                             TCP                              |                      Grpc                       |
|  接口调用   |                必须要使用自己的client进行调用                |  可通过Http传输，即可以通过CURL等命令进行调用   |
| 一致性算法  |                           zab协议                            |                    Raft算法                     |
| Watcher机制 |                    较为局限，一次性触发器                    |           一次watch可以监听所有的事件           |
|  数据模型   |                      基于目录的层次模式                      |      参考了zk的数据模型，是个扁平的kv模型       |
|    存储     | kv存储，使用的是ConcurrentHashMap，内存存储，一般不建议存储较多数据 | kv存储，使用bbolt存储引擎，可以处理几个gb的数据 |
|    MVCC     |                            不支持                            |      支持，可以通过两个b+tree进行版本控制       |
| 全局Session |                           存在缺陷                           |         实现更为灵活，避免了安全性问题          |
|  权限校验   |                             ACL                              |                      RABC                       |
|  事务能力   |                     提供了简易的事务能力                     |            只提供了版本号的检查能力             |
|  部署维护   |                             复杂                             |                      简单                       |

## Zookeeper注册中心的引入

在**part1 和part2 中，我们在调用服务时，对目标的ip地址和端口port都是写死的，默认本机地址和9999端口号**

在实际场景下，**服务的地址和端口会被记录到【注册中心】中。**
**服务端上线时，在注册中心注册自己的服务与对应的地址，**
**而客户端调用服务时，去注册中心根据服务名找到对应的服务端地址。**

这里我们使用zookeeper作为注册中心。

`zookeepepr`是一个经典的**分布式**数据一致性解决方案，致力于为分布式应用提供一个高性能、高可用,且具有严格顺序访问控制能力的分布式协调存储服务。

1. 高性能

   `zookeeper`将全量数据存储在**内存**中，并直接服务于客户端的所有非事务请求，尤其用于以读为主的应用场景

2. 高可用

   `zookeeper`一般以集群的方式对外提供服务，一般`3~5`台机器就可以组成一个可用的 `Zookeeper`集群了，每台机器都会在内存中维护当前的服务器状态，井且每台机器之间都相互保持着通信。只要集群中超过一半的机器都能够正常工作，那么整个集群就能够正常对外服务

3. 严格顺序访问

   对于来自客户端的每个更新请求，`Zookeeper`都会分配一个全局唯一的递增编号，这个编号反应了所有事务操作的先后顺序

**数据模型**：

![image-20241005112202748](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241005112202748-1729339946893-48.png)

`zookeeper`的数据结点可以视为树状结构(或目录)，树中的各个结点被称为`znode`(即`zookeeper node`)，一个`znode`可以由多个子结点。`zookeeper`结点在结构上表现为树状；

使用路径`path`来定位某个`znode`，比如`/ns-1/itcast/mysqml/schemal1/table1`，此处`ns-1，itcast、mysql、schemal1、table1`分别是`根结点、2级结点、3级结点以及4级结点`；其中`ns-1`是`itcast`的父结点，`itcast`是`ns-1`的子结点，`itcast`是`mysql`的父结点....以此类推

`znode`，间距文件和目录两种特点，即像文件一样维护着数据、元信息、ACL、时间戳等数据结构，又像目录一样可以作为路径标识的一部分

那么如何描述一个`znode`呢？一个`znode`大体上分为`3`个部分：

- 结点的**数据**：即`znode data`**(结点`path`，结点`data`)的关系就像是`Java map`中的 `key value`关系**
- **结点的子结点**`children`
- 结点的**状态**`stat`：用来描述当前结点的创建、修改记录，包括`cZxid`、`ctime`等

**应用场景**：通常作为注册中心和配置中心

### :star: 基本过程

1. 客户端
   1. 与原先写死的ip和port不同，这次外界调用时直接创建ClientProxy，不用给参数。ClientProxy中也一样，去掉ip和port变量
   2. **改为在rpcClient.sendRequest方法中，先去注册中心中查找服务对应的ip和port，再去连接对应的服务器**
   3. 实现向注册中心中查询 服务地址的类 是实现ServiceCenter接口的ZKServiceCenter类
2. 服务端
   1. **在服务端上线时调用ServiceProvider的provideServiceInterface方法中添加逻辑，使得本地注册服务时也注册服务到注册中心上**

### 环境配置

[windows 环境下zookeeper的安装与配置] 

https://blog.csdn.net/tttzzzqqq2018/article/details/132093374?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522172149339116800211548359%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&amp;request_id=172149339116800211548359&amp;biz_id=0&amp;utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-2-132093374-null-null.142^v100^pc_search_result_base9&amp;utm_term=zookeeper%E5%AE%89%E8%A3%85%E4%B8%8E%E9%85%8D%E7%BD%AE&amp;spm=1018.2226.3001.4187 

pom.xml中 引入包Curator客户端

```xml
<!--这个jar包应该依赖log4j,不引入log4j会有控制台会有warn，但不影响正常使用-->
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>5.1.0</version>
</dependency>
```

Curator：对zookeeper进行连接操作的工具

**启动步骤**

bin目录下 1.双击zkServer.cmd 2.双击zkCli.cmd

zkCli界面显示![image-20241005112236364](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241005112236364-1729339946893-49.png)

### 客户端重构

与原先写死的ip和port不同，这次外界调用时直接创建ClientProxy，不用给参数

TestClient中如下

```java
        ClientProxy clientProxy=new ClientProxy();
        //ClientProxy clientProxy=new part2.Client.proxy.ClientProxy("127.0.0.1",9999,0);
        UserService proxy=clientProxy.getProxy(UserService.class);

        User user = proxy.getUserByUserId(1);
        System.out.println("从服务端得到的user="+user.toString());

```

ClientProxy中也一样，去掉ip和port变量

```java
public class ClientProxy implements InvocationHandler {
    //传入参数service接口的class对象，反射封装成一个request

    private RpcClient rpcClient;
    public ClientProxy(){
        rpcClient=new NettyRpcClient();
    }

    //jdk动态代理，每一次代理对象调用方法，都会经过此方法增强（反射获取request对象，socket发送到服务端）
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //构建request
        RpcRequest request=RpcRequest.builder()
                .interfaceName(method.getDeclaringClass().getName())
                .methodName(method.getName())
                .params(args).paramsType(method.getParameterTypes()).build();
        //数据传输
        RpcResponse response= rpcClient.sendRequest(request);
        return response.getData();
    }
     public <T>T getProxy(Class<T> clazz){
        Object o = Proxy.newProxyInstance(clazz.getClassLoader(), new Class[]{clazz}, this);
        return (T)o;
    }
}
```

**改为在rpcClient.sendRequest方法中，先去注册中心中查找服务对应的ip和port，再去连接对应的服务器**

```java
public class NettyRpcClient implements RpcClient {

    private static final Bootstrap bootstrap;
    private static final EventLoopGroup eventLoopGroup;

    private ServiceCenter serviceCenter;
    public NettyRpcClient(){
        this.serviceCenter=new ZKServiceCenter();
    }

    //netty客户端初始化
    static {
        eventLoopGroup = new NioEventLoopGroup();
        bootstrap = new Bootstrap();
        bootstrap.group(eventLoopGroup).channel(NioSocketChannel.class)
                .handler(new NettyClientInitializer());
    }
    @Override
    public RpcResponse sendRequest(RpcRequest request) {
        //这里 
        //从注册中心获取host,post
        InetSocketAddress address = serviceCenter.serviceDiscovery(request.getInterfaceName());
        String host = address.getHostName();
        int port = address.getPort();
        try {
            ChannelFuture channelFuture  = bootstrap.connect(host, port).sync();
            Channel channel = channelFuture.channel();
            // 发送数据
            channel.writeAndFlush(request);
            //sync()堵塞获取结果
            channel.closeFuture().sync();
            // 阻塞的获得结果，通过给channel设计别名，获取特定名字下的channel中的内容（这个在hanlder中设置）
            // AttributeKey是，线程隔离的，不会由线程安全问题。
            // 当前场景下选择堵塞获取结果
            // 其它场景也可以选择添加监听器的方式来异步获取结果 channelFuture.addListener...
            AttributeKey<RpcResponse> key = AttributeKey.valueOf("RPCResponse");
            RpcResponse response = channel.attr(key).get();

            System.out.println(response);
            return response;
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

实现向注册中心中查询 服务地址的类 是实现ServiceCenter接口的ZKServiceCenter类

```java
public class ZKServiceCenter implements ServiceCenter{
    // curator 提供的zookeeper客户端
    private CuratorFramework client;
    //zookeeper根路径节点
    private static final String ROOT_PATH = "MyRPC";

    //负责zookeeper客户端的初始化，并与zookeeper服务端进行连接
    public ZKServiceCenter(){
        // 指数时间重试
        RetryPolicy policy = new ExponentialBackoffRetry(1000, 3);
        // zookeeper的地址固定，不管是服务提供者还是，消费者都要与之建立连接
        // sessionTimeoutMs 与 zoo.cfg中的tickTime 有关系，
        // zk还会根据minSessionTimeout与maxSessionTimeout两个参数重新调整最后的超时值。默认分别为tickTime 的2倍和20倍
        // 使用心跳监听状态
        this.client = CuratorFrameworkFactory.builder().connectString("127.0.0.1:2181")
                .sessionTimeoutMs(40000).retryPolicy(policy).namespace(ROOT_PATH).build();
        this.client.start();
        System.out.println("zookeeper 连接成功");
    }
    //根据服务名（接口名）返回地址
    @Override
    public InetSocketAddress serviceDiscovery(String serviceName) {
        try {
            List<String> strings = client.getChildren().forPath("/" + serviceName);
            // 这里默认用的第一个，后面加负载均衡
            String string = strings.get(0);
            return parseAddress(string);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
    // 地址 -> XXX.XXX.XXX.XXX:port 字符串
    private String getServiceAddress(InetSocketAddress serverAddress) {
        return serverAddress.getHostName() +
                ":" +
                serverAddress.getPort();
    }
    // 字符串解析为地址
    private InetSocketAddress parseAddress(String address) {
        String[] result = address.split(":");
        return new InetSocketAddress(result[0], Integer.parseInt(result[1]));
    }
}
```

### 服务端重构

服务端要改动的地方在于：注册服务。还记得我们在part1中的ServiceProvider类吗，他的功能是注册服务到本地集合中

**我们添加一个ServiceRegister变量用于注册服务到注册中心**

**在服务端上线时调用ServiceProvider的provideServiceInterface方法中添加逻辑，使得本地注册服务时也注册服务到注册中心上**

```java
public class ServiceProvider {
    private Map<String,Object> interfaceProvider;

    private int port;
    private String host;
    //注册服务类
    private ServiceRegister serviceRegister;

    public ServiceProvider(String host,int port){
        //需要传入服务端自身的网络地址
        this.host=host;
        this.port=port;
        this.interfaceProvider=new HashMap<>();
        this.serviceRegister=new ZKServiceRegister();
    }

    public void provideServiceInterface(Object service){
        String serviceName=service.getClass().getName();
        Class<?>[] interfaceName=service.getClass().getInterfaces();

        for (Class<?> clazz:interfaceName){
            //本机的映射表
            interfaceProvider.put(clazz.getName(),service);
            //在注册中心注册服务
            serviceRegister.register(clazz.getName(),new InetSocketAddress(host,port));
        }
    }

    public Object getService(String interfaceName){
        return interfaceProvider.get(interfaceName);
    }

}
```

实现ServiceRegister接口的ZKServiceRegister类，提供了注册服务到注册中心的register方法

```java
public class ZKServiceRegister implements ServiceRegister {
    // curator 提供的zookeeper客户端
    private CuratorFramework client;
    //zookeeper根路径节点
    private static final String ROOT_PATH = "MyRPC";

    //负责zookeeper客户端的初始化，并与zookeeper服务端进行连接
    public ZKServiceRegister(){
        // 指数时间重试
        RetryPolicy policy = new ExponentialBackoffRetry(1000, 3);
        // zookeeper的地址固定，不管是服务提供者还是，消费者都要与之建立连接
        // sessionTimeoutMs 与 zoo.cfg中的tickTime 有关系，
        // zk还会根据minSessionTimeout与maxSessionTimeout两个参数重新调整最后的超时值。默认分别为tickTime 的2倍和20倍
        // 使用心跳监听状态
        this.client = CuratorFrameworkFactory.builder().connectString("127.0.0.1:2181")
                .sessionTimeoutMs(40000).retryPolicy(policy).namespace(ROOT_PATH).build();
        this.client.start();
        System.out.println("zookeeper 连接成功");
    }
    //注册服务到注册中心
    @Override
    public void register(String serviceName, InetSocketAddress serviceAddress) {
        try {
            // serviceName创建成永久节点，服务提供者下线时，不删服务名，只删地址
            if(client.checkExists().forPath("/" + serviceName) == null){
                client.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT).forPath("/" + serviceName);
            }
            // 路径地址，一个/代表一个节点
            String path = "/" + serviceName +"/"+ getServiceAddress(serviceAddress);
            // 临时节点，服务器下线就删除节点
            client.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath(path);
        } catch (Exception e) {
            System.out.println("此服务已存在");
        }
    }
    // 地址 -> XXX.XXX.XXX.XXX:port 字符串
    private String getServiceAddress(InetSocketAddress serverAddress) {
        return serverAddress.getHostName() +
                ":" +
                serverAddress.getPort();
    }
    // 字符串解析为地址
    private InetSocketAddress parseAddress(String address) {
        String[] result = address.split(":");
        return new InetSocketAddress(result[0], Integer.parseInt(result[1]));
    }
}
```

### 加入zookeeper后的架构图

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241019201246856.png" alt="image-20241019201246856" style="zoom: 33%;" />

### **1.zookeeper在项目中的角色？你为什么使用zookeeper**

1. zookeeper作为项目的注册中心，实现着服务注册，服务发现和维护服务状态的功能；
2. zookeeper具有高可用性，一致性，有着丰富的api，应用广阔，很多大数据框架都有他的身影。

### **2.  你为什么选择Zookeeper作为注册中心呢，为什么不用Nacos**

zookeeper 的特性就是强一致性，这个不好答，
可以从另一个优点：成熟稳定来答。
因为 zookeeper 是一个成熟且运用广泛的分布式应用，
在学习中发现 目前很多大数据应用如 **kafka，hadoop，hbase **
**都是使用 zookeeper 来协调管理服务，所以我考虑采用了 zookeeper 作为注册中心**

### **3. 注册中心的意义？**

①服务注册与发现：注册中心实现了微服务架构中各个微服务的服务注册与发现，这是其最基础也是最重要的功能。通过注册中心，各个微服务可以将自己的地址信息（如IP地址、端口号等）注册到中心，同时也能够从中发现其他微服务的地址信息。

②动态性：在微服务架构中，服务的数量和位置可能会频繁变化。注册中心能够动态地处理这些变化，确保服务消费者能够实时获取到最新的服务提供者信息。

③增强微服务之间的去中心化在单体项目中，模块之间的依赖关系是通过内部的直接引用来实现的。而在微服务架构中，注册中心的存在使得微服务之间的依赖关系不再是直接的函数引用，而是通过注册中心来间接调用。这种方式增强了微服务之间的去中心化，提高了系统的灵活性和可扩展性。

④提升系统的可用性和容错性注册中心通常具有高可用性的设计，能够确保在部分节点故障时仍然能够正常工作。这使得整个微服务架构在面临故障时能够更加稳定地运行。

### 4. 服务注册到ZooKeeper的结构是怎样的？

1. 服务注册目录

首先，需要定义一个根目录来存放所有服务的信息。这个根目录可以是任意命名，例如 `/services` 或 `/registry`。

2. 服务名称节点

在根目录下，为每个服务创建一个节点，节点名通常是服务的名字。例如，对于一个名为 `UserService` 的服务，可以创建一个名为 `/services/UserService` 的节点。

3. 服务实例节点

在每个服务节点下，为每个服务实例创建一个临时节点（ephemeral node）。临时节点的特点是，当创建它的客户端断开连接时，该节点会被自动删除。这样可以保证失效的服务实例能够被及时清除。

每个服务实例节点的命名可以包含实例的唯一标识信息，例如 IP 地址和端口号。例如，一个名为 `192.168.1.100:8080` 的服务实例节点可以表示该服务运行在 IP 地址为 `192.168.1.100` 的机器上，端口号为 `8080`。

### 5. 服务提供者的掉线处理逻辑

1. 服务提供者启动：

   启动时，服务提供者向ZooKeeper注册一个临时节点，表示自身已上线。

2. 服务消费者订阅：

   服务消费者订阅服务名称节点，监听服务提供者的上线和下线事件。

3. 服务提供者掉线：

   当服务提供者掉线时，其临时节点会被自动删除。

4. 服务消费者感知掉线：

   服务消费者通过监听机制感知到服务提供者临时节点的删除事件。

5. 重新选择服务实例：

   服务消费者根据新的服务实例列表重新选择一个可用的服务实例。

6. 更新缓存：

   更新服务消费者的本地缓存，确保缓存中的服务实例列表是最新的。



## 在客户端建立本地服务缓存并实时更新

### :star: 基本过程

1. 客户端

   1. 建立一个本地缓存，缓存服务地址信息，作为优化的方案
   2. 服务发现时应该先去寻找本地缓存

2. 服务端

   1. 如何更新缓存？

      首先去本地缓存中读，读不到，再去注册中心中读，返回数据时刷新缓存....等等老生常谈的八股

      如果一个服务在注册中心中新增了一个地址，但是调用方始终能在本地缓存中读到这个服务，**那么 新增的变化就永远无法感知

      **如何解决？**

      通过在注册中心注册Watcher，监听注册中心的变化，实现本地缓存的动态更新

      ​	客户端**首先将 `Watcher`注册到服务端**，同时将 `Watcher`对象**保存到客户端的`watch`管理器中**。
      ​	当`Zookeeper`服务端监听的数据状态发生变化时，服务端会**主动通知客户端**，
      ​	接着客户端的 `Watch`管理器会**触发相关 `Watcher`**来回调相应处理逻辑，从而完成整体的数据 `发布/订阅`流程

   2. **在ZKServiceCenter 加入缓存和监听器**

   

### **1.本地缓存怎么做的？能保证缓存和服务的一致性吗？**

在客户端设计一个缓存层，每次调用服务时从缓存层中获取地址，避免直接调用注册中心，优化速度和资源

可以。这里使用了zookeeper的监听机制，在服务节点上注册Watcher，当注册中心的服务地址发生改动时，Watcher会异步通知客户端的缓存层修改对应的地址，从而实现两者的一致性



以前的版本中，**调用方每次调用服务，都要去注册中心zookeeper中查找地址**，性能是不是很差呢？

**我们可以在客户端建立一个本地缓存，缓存服务地址信息，作为优化的方案**

### 创建缓存

在Client层建立cache包

**本地缓存serviceCache**

```java
public class serviceCache {
    //key: serviceName 服务名
    //value： addressList 服务提供者列表
    private static Map<String, List<String>> cache=new HashMap<>();

    //添加服务
    public void addServcieToCache(String serviceName,String address){
        if(cache.containsKey(serviceName)){
            List<String> addressList = cache.get(serviceName);
            addressList.add(address);
            System.out.println("将name为"+serviceName+"和地址为"+address+"的服务添加到本地缓存中");
        }else {
            List<String> addressList=new ArrayList<>();
            addressList.add(address);
            cache.put(serviceName,addressList);
        }
    }
    //从缓存中取服务地址
    public  List<String> getServcieFromCache(String serviceName){
        if(!cache.containsKey(serviceName)) {
            return null;
        }
        List<String> a=cache.get(serviceName);
        return a;
    }
    //从缓存中删除服务地址
    public void delete(String serviceName,String address){
        List<String> addressList = cache.get(serviceName);
        addressList.remove(address);
        System.out.println("将name为"+serviceName+"和地址为"+address+"的服务从本地缓存中删除");
    }
}
```

既然设立了服务缓存，那么在ZKServiceCenter中，服务发现时应该先去寻找本地缓存

**修改ZKServiceCenter的serviceDiscovery方法**

```java
@Override
public InetSocketAddress serviceDiscovery(String serviceName) {
    try {
        //先从本地缓存中找
        List<String> serviceList=cache.getServcieFromCache(serviceName);
        //如果找不到，再去zookeeper中找
        //这种i情况基本不会发生，或者说只会出现在初始化阶段
        if(serviceList==null) {
            serviceList=client.getChildren().forPath("/" + serviceName);
        }
        // 这里默认用的第一个，后面加负载均衡
        String string = serviceList.get(0);
        return parseAddress(string);
    } catch (Exception e) {
        e.printStackTrace();
    }
    return null;
}
```

### 动态更新缓存的实现

那么如何更新缓存呢？

**首先在你脑海的肯定是最经典的cache aside旁路缓存策略**

- 首先去本地缓存中读，读不到，再去注册中心中读，返回数据时刷新缓存....等等老生常谈的八股

但是这种策略，不适用于当前场景，或者说，在当前场景下，存在很多问题（这是笔者在刚拿rpc项目时面试被问到的问题，属实被问懵逼了）

比如如下场景

![image-20241005112413320](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241005112413320-1729340963499-2.png)

如果一个服务在注册中心中新增了一个地址，但是调用方始终能在本地缓存中读到这个服务，**那么 新增的变化就永远无法感知到....**

**那么问题来了，Server端新上一个服务地址，Client端的本地缓存该怎么才能感知到呢**

- 笔者当时的回答是：Server新上一个地址时，去更新Client端的本地缓存....简直就是倒反天罡了。当时还停留在单体架构的项目思维中，所以回答的莫名其妙。面罢研究了一番，才幡然醒悟

- 那么正解是：**通过在注册中心注册Watcher，监听注册中心的变化，实现本地缓存的动态更新**


简单介绍一下zookeeper的事务监听机制

### 事件监听机制

### **watcher概念**

- `zookeeper`**提供了数据的`发布/订阅`功能，多个订阅者可同时监听某一特定主题对象，当该主题对象的自身状态发生变化时例如节点内容改变、节点下的子节点列表改变等，会实时、主动通知所有订阅者**
- `zookeeper`**采用了 `Watcher`机制实现数据的发布订阅功能。该机制在被订阅对象发生变化时会异步通知客户端**，因此客户端不必在 `Watcher`注册后轮询阻塞，从而减轻了客户端压力
- `watcher`机制事件上与观察者模式类似，也可**看作是一种观察者模式在分布式场景下的实现方式**

### watcher架构

`watcher`实现由三个部分组成

- `zookeeper`服务端
- `zookeeper`客户端
- 客户端的`ZKWatchManager对象`

客户端**首先将 `Watcher`注册到服务端**，同时将 `Watcher`对象**保存到客户端的`watch`管理器中**。当`Zookeeper`服务端监听的数据状态发生变化时，服务端会**主动通知客户端**，接着客户端的 `Watch`管理器会**触发相关 `Watcher`**来回调相应处理逻辑，从而完成整体的数据 `发布/订阅`流程

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241019205815720.png" alt="image-20241019205815720" style="zoom:50%;" />

使用curator，可以方便的进行watcher的使用。建议大家先写一个小demo熟悉一下使用

**watchZK 监听zookeeper的实现**

```java
public class watchZK {
    // curator 提供的zookeeper客户端
    private CuratorFramework client;
    //本地缓存
    serviceCache cache;

    public watchZK(CuratorFramework client,serviceCache  cache){
        this.client=client;
        this.cache=cache;
    }

    /**
     * 监听当前节点和子节点的 更新，创建，删除
     * @param path
     */
    public void watchToUpdate(String path) throws InterruptedException {
        CuratorCache curatorCache = CuratorCache.build(client, "/");
        curatorCache.listenable().addListener(new CuratorCacheListener() {
            @Override
            public void event(Type type, ChildData childData, ChildData childData1) {
                // 第一个参数：事件类型（枚举）
                // 第二个参数：节点更新前的状态、数据
                // 第三个参数：节点更新后的状态、数据
                // 创建节点时：节点刚被创建，不存在 更新前节点 ，所以第二个参数为 null
                // 删除节点时：节点被删除，不存在 更新后节点 ，所以第三个参数为 null
                // 节点创建时没有赋予值 create /curator/app1 只创建节点，在这种情况下，更新前节点的 data 为 null，获取不到更新前节点的数据
                switch (type.name()) {
                    case "NODE_CREATED": // 监听器第一次执行时节点存在也会触发次事件
                        //获取更新的节点的路径
                        String path=new String(childData1.getPath());
                        //按照格式 ，读取
                        String[] pathList= path.split("/");
                        if(pathList.length<=2) break;
                        else {
                            String serviceName=pathList[1];
                            String address=pathList[2];
                            //将新注册的服务加入到本地缓存中
                            cache.addServcieToCache(serviceName,address);
                        }
                        break;
                    case "NODE_CHANGED": // 节点更新
                        if (childData.getData() != null) {
                            System.out.println("修改前的数据: " + new String(childData.getData()));
                        } else {
                            System.out.println("节点第一次赋值!");
                        }
                        System.out.println("修改后的数据: " + new String(childData1.getData()));
                        break;
                    case "NODE_DELETED": // 节点删除
                        String path_d=new String(childData.getPath());
                        //按照格式 ，读取
                        String[] pathList_d= path_d.split("/");
                        if(pathList_d.length<=2) break;
                        else {
                            String serviceName=pathList_d[1];
                            String address=pathList_d[2];
                            //将新注册的服务加入到本地缓存中
                            cache.delete(serviceName,address);
                        }
                        break;
                    default:
                        break;
                }
            }
        });
        //开启监听
        curatorCache.start();
    }
}
```

**在ZKServiceCenter 加入缓存和监听器**

```java
//负责zookeeper客户端的初始化，并与zookeeper服务端进行连接
public ZKServiceCenter() throws InterruptedException {
    // 指数时间重试
    RetryPolicy policy = new ExponentialBackoffRetry(1000, 3);
    // zookeeper的地址固定，不管是服务提供者还是，消费者都要与之建立连接
    // sessionTimeoutMs 与 zoo.cfg中的tickTime 有关系，
    // zk还会根据minSessionTimeout与maxSessionTimeout两个参数重新调整最后的超时值。默认分别为tickTime 的2倍和20倍
    // 使用心跳监听状态
    this.client = CuratorFrameworkFactory.builder().connectString("127.0.0.1:2181")
            .sessionTimeoutMs(40000).retryPolicy(policy).namespace(ROOT_PATH).build();
    this.client.start();
    System.out.println("zookeeper 连接成功");
    //初始化本地缓存
    cache=new serviceCache();
    //加入zookeeper事件监听器
    watchZK watcher=new watchZK(client,cache);
    //监听启动
    watcher.watchToUpdate(ROOT_PATH);
}
```



## 自定义编码器、解码器和序列化器

netty中的重要组件：

**ChannelHandler 与 ChannelPipeline** 

- **ChannelHandler 是对 Channel 中数据的处理器，这些处理器可以是系统本身定义好的编解码器，也可以是用户自定义的。**
- **这些处理器会被统一添加到一个 ChannelPipeline 的对象中，然后按照添加的类别对 Channel 中的数据进行依次处理。**

在上一节中，我们使用netty自带的编码器和解码器 来实现数据的传输

而在这里，我们可以通过**继承netty提供的基类**，实现自定义的编码器和解码器

在common包下建立serializer包，实现自定义编码器，解码器和序列化器

### **自定义编码器myEnCoder**

```java
/**
 * @author wxx
 * @version 1.0
 * @create 2024/6/2 22:24
 *   依次按照自定义的消息格式写入，传入的数据为request或者response
 *   需要持有一个serialize器，负责将传入的对象序列化成字节数组
 */
@AllArgsConstructor
public class MyEncoder extends MessageToByteEncoder {
    private Serializer serializer;
    @Override
    protected void encode(ChannelHandlerContext ctx, Object msg, ByteBuf out) throws Exception {
        System.out.println(msg.getClass());
        //1.写入消息类型
        if(msg instanceof RpcRequest){
            out.writeShort(MessageType.REQUEST.getCode());
        }
        else if(msg instanceof RpcResponse){
            out.writeShort(MessageType.RESPONSE.getCode());
        }
        //2.写入序列化方式
        out.writeShort(serializer.getType());
        //得到序列化数组
        byte[] serializeBytes = serializer.serialize(msg);
        //3.写入长度
        out.writeInt(serializeBytes.length);
        //4.写入序列化数组
        out.writeBytes(serializeBytes);
    }
}
```

### **自定义解码器myDecoder**

```java
/**
 * @author wxx
 * @version 1.0
 * @create 2024/6/2 22:24
 * 按照自定义的消息格式解码数据
 */
public class MyDecoder extends ByteToMessageDecoder {
    @Override
    protected void decode(ChannelHandlerContext channelHandlerContext, ByteBuf in, List<Object> out) throws Exception {
        //1.读取消息类型
        short messageType = in.readShort();
        // 现在还只支持request与response请求
        if(messageType != MessageType.REQUEST.getCode() &&
                messageType != MessageType.RESPONSE.getCode()){
            System.out.println("暂不支持此种数据");
            return;
        }
        //2.读取序列化的方式&类型
        short serializerType = in.readShort();
        Serializer serializer = Serializer.getSerializerByCode(serializerType);
        if(serializer == null)
            throw new RuntimeException("不存在对应的序列化器");
        //3.读取序列化数组长度
        int length = in.readInt();
        //4.读取序列化数组
        byte[] bytes=new byte[length];
        in.readBytes(bytes);
        Object deserialize= serializer.deserialize(bytes, messageType);
        out.add(deserialize);
    }
}
```

encoder 加工出一条字节数组

decoder 读取字节数组，获得里面的对象

**就像一个礼物**，经过送礼人(encoder)层层包装成礼盒后，交给收礼人

收礼人(decoder)一层层打开礼盒后，获得里面的礼物

使用自定义编/解码器的好处？

- **将编码解码的过程进行封装，代码变得简洁易读，维护更加方便**

- **在内部实现消息头的加工，解决沾包问题**

- **消息头中加入messageType消息类型，对消息的读取机制有了进一步的拓展**

### **1. 为什么会出现沾包问题？如何解决的？**

​	netty默认底层通过TCP 进行传输，TCP**是面向流的协议**，接收方在**接收到数据时无法直接得知一条消息的具体字节数，不知道数据的界限**。由于TCP的流量控制机制，发生沾包或拆包，会导致接收的一个包可能会有多条消息或者不足一条消息，从而会出现接收方少读或者多读导致消息不能读完全的情况发生

​	在发送消息时，先告诉接收方消息的长度，让接收方读取指定长度的字节，就能避免这个问题；项目中通过自定义的消息传输协议来实现对沾包问题的解决。

### 2. 自定义消息格式

我的解决办法是自定义消息头：

![image-20240813150328363](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20240813150328363.png)

我的消息头包括：魔法数(4B)、版本(1B)、消息长度(4B)、消息类型(1B)、压缩类型(1B)、序列化类型(1B)、请求Id(4B)

魔法数主要是为了筛选来到服务端的数据包，有了这个魔数之后，服务端首先取出前面四个字节进行比对，能够在第一时间识别出这个数据包并非是遵循自定义协议的，也就是无效数据包，为了安全考虑可以直接关闭连接以节省资源





### **自定义序列化器**

序列化器是针对 消息中的对象进行序列化和反序列化

因为消息是一条字节数组，所以对象需要序列化成字节 放入消息中

借用上面的例子，

也就是说 礼物 需要适应礼盒的大小，需要变成礼盒的形状捏

**定义Serializer接口**

```java
public interface Serializer {
    // 把对象序列化成字节数组
    byte[] serialize(Object obj);
    // 从字节数组反序列化成消息, 使用java自带序列化方式不用messageType也能得到相应的对象（序列化字节数组里包含类信息）
    // 其它方式需指定消息格式，再根据message转化成相应的对象
    Object deserialize(byte[] bytes, int messageType);
    // 返回使用的序列器，是哪个
    // 0：java自带序列化方式, 1: json序列化方式
    int getType();
    // 根据序号取出序列化器，暂时有两种实现方式，需要其它方式，实现这个接口即可
    static Serializer getSerializerByCode(int code){
        switch (code){
            case 0:
                return new ObjectSerializer();
            case 1:
                return new JsonSerializer();
            default:
                return null;
        }
    }
}
```

#### 1.使用json进行序列化

首先 xml中引入 fastjson包

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.83</version>
</dependency>
```

**JsonSerializer定义Serializer接口**

```java
public class JsonSerializer implements Serializer {
    
    //对象序列化为字节数组
    @Override
    public byte[] serialize(Object obj) {
        byte[] bytes = JSONObject.toJSONBytes(obj);
        return bytes;
    }

    //字节数组反序列化为对象
    @Override
    public Object deserialize(byte[] bytes, int messageType) {
        Object obj = null;
        // 传输的消息分为request与response
        switch (messageType){
            case 0:
                RpcRequest request = JSON.parseObject(bytes, RpcRequest.class);
                Object[] objects = new Object[request.getParams().length];
                // 把json字串转化成对应的对象， fastjson可以读出基本数据类型，不用转化
                // 对转换后的request中的params属性逐个进行类型判断
                for(int i = 0; i < objects.length; i++){
                    Class<?> paramsType = request.getParamsType()[i];
                    //判断每个对象类型是否和paramsTypes中的一致
                    if (!paramsType.isAssignableFrom(request.getParams()[i].getClass())){
                        //如果不一致，就行进行类型转换
                        objects[i] = JSONObject.toJavaObject((JSONObject) request.getParams()[i],request.getParamsType()[i]);
                    }else{
                        //如果一致就直接赋给objects[i]
                        objects[i] = request.getParams()[i];
                    }
                }
                request.setParams(objects);
                obj = request;
                break;
            case 1:
                RpcResponse response = JSON.parseObject(bytes, RpcResponse.class);
                Class<?> dataType = response.getDataType();
                //判断转化后的response对象中的data的类型是否正确
                if(! dataType.isAssignableFrom(response.getData().getClass())){
                    response.setData(JSONObject.toJavaObject((JSONObject) response.getData(),dataType));
                }
                obj = response;
                break;
            default:
                System.out.println("暂时不支持此种消息");
                throw new RuntimeException();
        }
        return obj;
    }

    //1 代表json序列化方式
    @Override
    public int getType() {
        return 1;
    }
}
```

#### 2.protobuf序列化（待补充）

修改nettyInitializer

是不是层次分明了很多了呢？

**Client层**

```java
public class NettyClientInitializer extends ChannelInitializer<SocketChannel> {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();
        //使用自定义的编/解码器
        pipeline.addLast(new MyDecoder());
        pipeline.addLast(new MyEncoder(new JsonSerializer()));
        pipeline.addLast(new NettyClientHandler());
    }
}
```

**Server层**

```java
@AllArgsConstructor
public class NettyServerInitializer extends ChannelInitializer<SocketChannel> {
    private ServiceProvider serviceProvider;
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();
        //使用自定义的编/解码器
        pipeline.addLast(new MyEncoder(new JsonSerializer()));
        pipeline.addLast(new MyDecoder());
        pipeline.addLast(new NettyRPCServerHandler(serviceProvider));
    }
}
```

### 3 . 什么叫做序列化?Java原生序列化有什么问题？使用Kryo和JAVA序列化速度的差异?

序列化就是将**类对象编码成可在网络中传输的二进制数据**，

jdk原生序列化有下面三个问题：

1.Java序列化机制是Java内部的一种对象编解码技术，**无法跨语言使用。例**如对于异构系统之间的对接，Java序列化后的码流需要能够通过其他语言反序列化成原始对象(副本)，目前很难支持。

2.相比于其他开源的序列化框架，**Java序列化后的码流太大**，无论是网络传输还是持久化到磁盘，都会导致额外的资源占用。

**3.序列化性能差**，资源占用率高(主要是CPU资源占用高)。

**为什么选Kryo**

Kryo 是专门针对Java 语言序列化方式并且性能非常好，并且 Dubbo 官网的一篇文章中提到说推荐使用 Kryo 作为生产环境的序列化方式，Kryo 的序列化和反序列化速度非常快，序列化后的数据体积较小

使用Kryo需要注意

Kryo由于其变长存储特性并使用了字节码生成机制，拥有较高的运行速度和较小的字节码体积因为 Kryo 不是线程安全的。使用 ThreadLocal 来存储 Kryo 对象，一个线程一个 Kryo 实例。

**Kryo和其他序列化方式的比较**

**Hessian对java一些常见的对象类型不支持，如 Linked系列、Locale类等等**

Protobuf是由谷歌公司开发的数据语言，支持java、C++、python等多平台**，序列化后体积较小，转化性能较高。但需要预编译IDL**

Protostuff不需要依赖IDL文件，可以直接对Java 领域对象进行反/序列化操作，在效率上跟 Protobuf差不多，生成的二进制格式和Protobuf是完全相同的，可以说是一个Java版本的 Protobuf序列化框架。但不支持null，也不支持单纯的 Map、List 集合对象,需要包在对象里面。

**Kryo可以有效处理 Java 的复杂对象结构，包括嵌套对象、集合等，且对循环引用和深层次对象有很好的支持，而且Dubbo 官网的一篇文章中提到说推荐使用 Kryo 作为生产环境的序列化方式**



### 4. Rpc中的数据安全是如何实现的

1. **加密通信**：
   - 使用TLS/SSL（Transport Layer Security / Secure Sockets Layer）来加密客户端和服务器之间的通信。TLS/SSL 可以提供端到端的加密，防止中间人攻击，确保数据的机密性和完整性。

2. **身份验证**：
   - 实施双向认证（Mutual Authentication），即不仅客户端需要验证服务器的身份，服务器也需要验证客户端的身份。这通常通过使用数字证书来实现。

3. **授权控制**：
   - 在服务端对客户端进行授权检查，确保只有经过授权的客户端才能访问特定的服务。可以使用OAuth、JWT（JSON Web Tokens）或其他身份验证协议来管理访问权限。

4. **数据签名**：
   - 使用数字签名来确保消息的完整性和来源的真实性。数字签名可以通过非对称加密算法来实现，如RSA或ECDSA。

5. **消息完整性校验**：
   - 使用哈希函数（如SHA-256）计算消息摘要，并将其附加在消息后面，接收方可以验证摘要以确认消息没有被篡改。

6. **审计和监控**：
   - 记录和监控RPC请求和响应，以便检测任何异常行为或潜在的安全威胁。

7. **使用安全的协议和框架**：
   - 选择支持内置安全特性的RPC框架，如gRPC支持TLS加密，Thrift可以使用SSL/TLS进行加密等。

8. **最小权限原则**：
   - 为服务和客户端分配尽可能少的权限，仅授予执行其职责所需的最小权限。

9. **定期更新和打补丁**：
   - 定期更新RPC框架、库和其他依赖项，确保使用最新的安全补丁和修复。

10. **网络隔离**：
    - 将RPC服务部署在网络隔离区域（如DMZ），并限制只有必要的服务能够互相通信。

11. **安全配置**：
    - 确保RPC服务的安全配置正确无误，避免默认配置中可能存在的安全隐患。

实施这些措施时，需要综合考虑系统的性能、易用性和安全性，找到一个合适的平衡点。在实际应用中，通常会结合使用多种方法来增强系统的整体安全性。







## 负载均衡

在之前的版本中，当客户端请求服务时，取 **从注册中心返回的服务地址列表中的第一个 作为访问的地址**

这样做是明显存在问题的，**当请求量过大时，单节点会无法承载庞大的流量从而崩溃。**

所以在一些流量较大的服务上，我们会设置多个节点服务器处理请求，并使用负载均衡的思想 将请求分摊到每个节点上

![image-20241005112554819](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241005112554819-1729341172492-10.png)

### 常见的负载均衡算法

1. 轮询法（Round Robin）

   **原理**：轮询法将所有请求按顺序轮流分配给后端服务器，依次循环。

   **优点**

   - **简单易实现。**

   - 无状态，不保存任何信息，因此实现**成本低。**

   **缺点**

   - 当后端服务器性能差异大时，**无法根据服务器的负载情况进行动态调整，**可能导致某些服务器负载过大或过小。

   - **如果服务器配置不一样，不适合使用轮询法。**

2. 随机法（Random）

   **原理**：随机法将请求随机分配到各个服务器。

   **优点**

   - **分配较为均匀，**避免了轮询法可能出现的连续请求分配给同一台服务器的问题。

   - **使用简单，**不需要复杂的配置。

   **缺点**

   - **随机性可能导致某些服务器被频繁访问**，而另一些服务器则相对较少，这取决于随机数的生成情况。

   - 如果服务器配置不同，**随机法可能导致负载不均衡**，影响整体性能。

3. 一致性哈希法（Consistent Hashing）

   **原理**：一致性哈希法**将输入（如客户端IP地址）通过哈希函数映射到一个固定大小的环形空间（哈希环）上**，**每个服务器也映射到这个哈希环上**。客户端的请求会根据哈希值在哈希环上顺时针查找，遇到的第一个服务器就是该请求的目标服务器。

   **优点**

   - 当**服务器数量发生变化时，只有少数键需要被重新映射到新的服务器上**，这大大减少了缓存失效的数量，提高了系统的可用性。

   - **具有良好的可扩展性，**可以动态地添加或删除服务器。

   **缺点**

   - **在哈希环偏斜的情况下，大部分的缓存对象很有可能会缓存到一台服务器上**，导致缓存分布极度不均匀。

   - **实现较为复杂**，需要引入虚拟节点等技术来解决哈希偏斜问题。

**定义LoadBalance接口**

```java
public interface LoadBalance {
    //负责实现具体算法，返回分配的地址
    String balance(List<String> addressList);
    //添加节点
    void addNode(String node);
    //删除节点
    void delNode(String node);
}
```

**随机算法的实现**

```java
public class RandomLoadBalance implements LoadBalance {
    @Override
    public String balance(List<String> addressList) {
        Random random=new Random();
        int choose = random.nextInt(addressList.size());
        System.out.println("负载均衡选择了"+choose+"服务器");
        return null;
    }
    public void addNode(String node){} ;
    public void delNode(String node){};
}
```

**轮询算法的实现**

```java
public class RoundLoadBalance implements LoadBalance {
    private int choose=-1;
    @Override
    public String balance(List<String> addressList) {
        choose++;
        choose=choose%addressList.size();
        System.out.println("负载均衡选择了"+choose+"服务器");
        return addressList.get(choose);
    }
    public void addNode(String node) {};
    public void delNode(String node){};
}
```

**一致性哈希算法的实现**

一致性哈希算法的原理和优化，可以参考文章

https://blog.csdn.net/zhanglu0223/article/details/100579254?spm=1001.2014.3001.5506

```java
public class ConsistencyHashBalance implements LoadBalance {
    // 虚拟节点的个数
    private static final int VIRTUAL_NUM = 5;

    // 虚拟节点分配，key是hash值，value是虚拟节点服务器名称
    private static SortedMap<Integer, String> shards = new TreeMap<Integer, String>();

    // 真实节点列表
    private static List<String> realNodes = new LinkedList<String>();

    //模拟初始服务器
    private static String[] servers =null;

    private static void init(List<String> serviceList) {
        for (String server :serviceList) {
            realNodes.add(server);
            System.out.println("真实节点[" + server + "] 被添加");
            for (int i = 0; i < VIRTUAL_NUM; i++) {
                String virtualNode = server + "&&VN" + i;
                int hash = getHash(virtualNode);
                shards.put(hash, virtualNode);
                System.out.println("虚拟节点[" + virtualNode + "] hash:" + hash + "，被添加");
            }
        }
    }
    /**
     * 获取被分配的节点名
     *
     * @param node
     * @return
     */
    public static String getServer(String node,List<String> serviceList) {
        init(serviceList);
        int hash = getHash(node);
        Integer key = null;
        SortedMap<Integer, String> subMap = shards.tailMap(hash);
        if (subMap.isEmpty()) {
            key = shards.lastKey();
        } else {
            key = subMap.firstKey();
        }
        String virtualNode = shards.get(key);
        return virtualNode.substring(0, virtualNode.indexOf("&&"));
    }

    /**
     * 添加节点
     *
     * @param node
     */
    public  void addNode(String node) {
        if (!realNodes.contains(node)) {
            realNodes.add(node);
            System.out.println("真实节点[" + node + "] 上线添加");
            for (int i = 0; i < VIRTUAL_NUM; i++) {
                String virtualNode = node + "&&VN" + i;
                int hash = getHash(virtualNode);
                shards.put(hash, virtualNode);
                System.out.println("虚拟节点[" + virtualNode + "] hash:" + hash + "，被添加");
            }
        }
    }

    /**
     * 删除节点
     *
     * @param node
     */
    public  void delNode(String node) {
        if (realNodes.contains(node)) {
            realNodes.remove(node);
            System.out.println("真实节点[" + node + "] 下线移除");
            for (int i = 0; i < VIRTUAL_NUM; i++) {
                String virtualNode = node + "&&VN" + i;
                int hash = getHash(virtualNode);
                shards.remove(hash);
                System.out.println("虚拟节点[" + virtualNode + "] hash:" + hash + "，被移除");
            }
        }
    }

    /**
     * FNV1_32_HASH算法
     */
    private static int getHash(String str) {
        final int p = 16777619;
        int hash = (int) 2166136261L;
        for (int i = 0; i < str.length(); i++)
            hash = (hash ^ str.charAt(i)) * p;
        hash += hash << 13;
        hash ^= hash >> 7;
        hash += hash << 3;
        hash ^= hash >> 17;
        hash += hash << 5;
        // 如果算出来的值为负数则取其绝对值
        if (hash < 0)
            hash = Math.abs(hash);
        return hash;
    }

    @Override
    public String balance(List<String> addressList) {
        String random= UUID.randomUUID().toString();
        return getServer(random,addressList);
    }

}
```

在ServiceCenter中加入负载均衡层

```java
//根据服务名（接口名）返回地址
@Override
public InetSocketAddress serviceDiscovery(String serviceName) {
    try {
        //先从本地缓存中找
        List<String> addressList=cache.getServiceListFromCache(serviceName);
        //如果找不到，再去zookeeper中找
        //这种i情况基本不会发生，或者说只会出现在初始化阶段
        if(addressList==null) {
            addressList=client.getChildren().forPath("/" + serviceName);
        }
        // 负载均衡得到地址
        String address = new ConsistencyHashBalance().balance(addressList);
        return parseAddress(address);
    } catch (Exception e) {
        e.printStackTrace();
    }
    return null;
}
```

### 1.LRU适合作为负载均衡的一个实现吗？

不适合。

LRU的思想可以被借鉴用于负载均衡。例如可以设计一个基于请求历史的负载均衡算法，其中每个服务器都有一个“最近访问时间戳”，当新的请求到来时，可以选择“最久未被请求”的服务器进行处理。但这种做法通常不是最优的，因为它没有考虑到服务器的当前负载，并且还可能会出现 **部分某个时间被大量访问的节点之后无法再访问到**的问题。

但是我们可以在项目描述中加入LRU这个实现，因为LRU是**热门**的算法题之一，

写到其中，会**引导**面试官问你，LRU的实现 or 与其它算法的优缺点比较

算法题地址：https://leetcode.cn/problems/lru-cache/



### 2.当各个服务器的负载能力不一致时，该怎么设置负载均衡算法，来保证各服务器接收到的流量是合适的呢？

前面学习过一致性哈希算法 就会知道，在一致性哈希算法中，使用虚拟节点对真实节点进行映射，并且能通过设置虚拟节点的个数 来控制该节点接收到请求的概率。

所以在服务器负载能力不一致的情况下，我们可以在服务端将服务器的负载能力写入到注册中心中，客户端在进行负载均衡时会在注册中心中获取各服务器的能力，并设置对应的虚拟节点的数量，来控制流量的分发。



### 3. 自适应的负载均衡

RPC 的负载均衡完全由 RPC 框架自身实现，服务调用者发起请求时，会通过配置的负载均衡插件，自主地选择服务节点。那是不是只要调用者知道每个服务节点处理请求的能力，再根据服务处理节点处理请求的能力来判断要打给它多少流量就可以了？当一个服务节点负载过高或响应过慢时，就少给它发送请求，反之则多给它发送请求。

有点像日常工作中的分配任务，要多考虑实际情况。当一位下属身体欠佳，就少给他些工作；若刚好另一位下属状态很好，手头工作又不是很多，就多分给他一点。

那服务调用者节点又该如何判定一个服务节点的处理能力呢？

- 可以采用一种打分的策略，**服务调用者收集与之建立长连接的每个服务节点的指标数据，**

- 我们可以为每个指标都设置一个**指标权重占比，然后再根据这些指标数据，计算分数。**

服务调用者给每个服务节点都打完分之后，会发送请求，那这时候我们又该如何根据分数去控制给每个服务节点发送多少流量呢？

- 可以配合一致性哈希的的负载均衡策略去控制，**通过最终的指标分数修改服务节点最终的权重，即动态去修改对应的虚拟节点的数量**



### **4. 某个服务多个节点承压能力不一，怎么办？**

​	前面学习过一致性哈希算法 就会知道，在一致性哈希算法中，使用虚拟节点对真实节点进行映射，并且能通过设置虚拟节点的个数 来控制该节点接收到请求的概率。

​	所以在服务器负载能力不一致的情况下，我们可以在服务端将服务器的负载能力写入到注册中心中，客户端在进行负载均衡时会在注册中心中获取各服务器的能力，并设置对应的虚拟节点的数量，来控制流量的分发。

​	这里可以拓展一下自适应负载均衡的实现

### 5.  如何使用 SPI 实现负载均衡？

1. **定义负载均衡接口**：
   - 开发者首先定义一个负载均衡策略的接口，这个接口定义了负载均衡算法应该提供的方法，比如选择下一个服务器来处理请求。
2. **实现负载均衡策略**：
   - 不同的负载均衡策略（例如轮询、最少连接数、随机选择等）可以通过实现上述接口来具体化。每个实现类代表了一种具体的负载均衡算法。
3. **配置 SPI 文件**：
   - 对于每一个实现了负载均衡接口的类，都需要在 `META-INF/services` 目录下创建一个 SPI 配置文件。该文件名应为接口的全限定名，文件内容为实现类的全限定名。这使得 Java 运行时可以通过 ServiceLoader 自动加载这些实现。
4. **动态加载负载均衡策略**：
   - 在运行时，应用可以使用 `ServiceLoader` 加载所有可用的负载均衡实现，并根据需要选择其中的一种。选择过程可以基于配置文件或者环境变量来进行。
5. **集成到负载均衡器中**：
   - 最终，选定的负载均衡策略会被集成到负载均衡器中，用来决定如何分配请求到不同的后端服务器上。

这样的设计模式使得负载均衡策略可以灵活配置，而不必硬编码到应用中。此外，当需要添加新的负载均衡算法时，只需要添加新的实现类和 SPI 配置即可，无需更改已有的代码。

## 超时重试 &白名单

### **为什么需要超时重试？**

一次 RPC 调用，去调用远程的一个服务，比如用户的登录操作，会先对用户的用户名以及密码进行验证，验证成功之后会获取用户的基本信息。当通过远程的用户服务来获取用户基本信息的时候，**恰好网络出现了问题，比如网络突然抖了一下，导致请求失败了，而这个请求希望它能够尽可能地执行成功，那这时要怎么做呢？**

**当调用端发起的请求失败时，RPC 框架自身可以进行重试，再重新发送请求，通过这种方式保证系统的容错率**

### 如何实现超时重试？

java中有多种实现重试的方式，如**手动重试，Spring Retry ，Guava Retry ...**

这里我们选择灵活性和功能性更强的Guava Retry

**引入包**

```xml
<dependency>
    <groupId>com.github.rholder</groupId>
    <artifactId>guava-retrying</artifactId>
    <version>2.0.0</version>
</dependency>

```

**建立guavaRetry类**

```java
public class guavaRetry {
    private RpcClient rpcClient;
    public RpcResponse sendServiceWithRetry(RpcRequest request, RpcClient rpcClient) {
        this.rpcClient=rpcClient;
        Retryer<RpcResponse> retryer = RetryerBuilder.<RpcResponse>newBuilder()
            
                //无论出现什么异常，都进行重试
                .retryIfException()
            
                //返回结果为 error时进行重试
                .retryIfResult(response -> Objects.equals(response.getCode(), 500))
            
                //重试等待策略：等待 2s 后再进行重试
                .withWaitStrategy(WaitStrategies.fixedWait(2, TimeUnit.SECONDS))
            
                //重试停止策略：重试达到 3 次
                .withStopStrategy(StopStrategies.stopAfterAttempt(3))
                .withRetryListener(new RetryListener() {
                    @Override
                    public <V> void onRetry(Attempt<V> attempt) {
                        System.out.println("RetryListener: 第" + attempt.getAttemptNumber() + "次调用");
                    }
                })
            
                .build();
        try {
            return retryer.call(() -> rpcClient.sendRequest(request));
        } catch (Exception e) {
            e.printStackTrace();
        }
        return RpcResponse.fail();
    }
}
```

**通过很多方法来设置重试机制：**

1. retryIfException()：对所有异常进行重试 
2. retryIfRuntimeException()：设置对指定异常进行重试 
3. retryIfExceptionOfType()：对所有 RuntimeException 进行重试 
4. retryIfResult()：对不符合预期的返回结果进行重试

还有五个以 withXxx 开头的方法，用来**对重试策略/等待策略/阻塞策略/单次任务执行时间限制/自定义监听器进行设置，**以实现更加强大的异常处理：

1. withRetryListener()：设置重试监听器，用来执行额外的处理工作 
2. withWaitStrategy()：重试等待策略 
3. withStopStrategy()：停止重试策略 
4. withAttemptTimeLimiter：设置任务单次执行的时间限制，如果超时则抛出异常
5. withBlockStrategy()：设置任务阻塞策略，即可以设置当前重试完成，下次重试开始前的这段时间做什么事情

### 重试机制问题？白名单解决方式

如果这个服务业务逻辑不是幂等的，比如插入数据操作，那触发重试的话会不会引发问题呢？**会的。**

**在使用 RPC 框架的时候，要确保被调用的服务的业务逻辑是幂等的，这样才能考虑根据事件情况开启 RPC 框架的异常重试功能**

所以，我们可以**设置一个白名单**，服务端在注册节点时，**将幂等性的服务注册在白名单中，客户端在请求服务前，先去白名单中查看该服务是否为幂等服务，如果是的话使用重试框架进行调用**。白名单可以存放**在zookeeper中（充当配置中心的角色）**

**客户端**

**在serviceCenter接口中添加checkRetry方法**

```java
//服务中心接口
public interface ServiceCenter {
    //  查询：根据服务名查找地址
    InetSocketAddress serviceDiscovery(String serviceName);
    //判断是否可重试
    boolean checkRetry(String serviceName) ;
}
```

**在ZKServiceCenter中实现方法**

```java
public boolean checkRetry(String serviceName) {
    boolean canRetry =false;
    try {
        List<String> serviceList = client.getChildren().forPath("/" + RETRY);
        for(String s:serviceList){
            //如果列表中有该服务
            if(s.equals(serviceName)){
                System.out.println("服务"+serviceName+"在白名单上，可进行重试");
                canRetry=true;
            }
        }
    }catch (Exception e) {
        e.printStackTrace();
    }
    return canRetry;
}
```

**服务端**

**ZKServiceRegister中在register方法中添加逻辑：如果是可重试的服务，添加到zookeeper 的分支中**

```java
//注册服务到注册中心
@Override
public void register(String serviceName, InetSocketAddress serviceAddress,boolean canRetry) {
    try {
        // serviceName创建成永久节点，服务提供者下线时，不删服务名，只删地址
        if(client.checkExists().forPath("/" + serviceName) == null){
            client.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT).forPath("/" + serviceName);
        }
        // 路径地址，一个/代表一个节点
        String path = "/" + serviceName +"/"+ getServiceAddress(serviceAddress);
        // 临时节点，服务器下线就删除节点
        client.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath(path);
        //如果这个服务是幂等性，就增加到节点中
        if (canRetry){
            path ="/"+RETRY+"/"+serviceName;
            client.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath(path);
        }
    } catch (Exception e) {
        System.out.println("此服务已存在");
    }
}
```

**在客户端最上层的ClientProxy 调用服务时，添加重试机制和白名单的验证**

```java
public class ClientProxy implements InvocationHandler {
    //传入参数service接口的class对象，反射封装成一个request

    private RpcClient rpcClient;
    private ServiceCenter serviceCenter;
    public ClientProxy() throws InterruptedException {
        serviceCenter=new ZKServiceCenter();
        rpcClient=new NettyRpcClient(serviceCenter);
    }

    //jdk动态代理，每一次代理对象调用方法，都会经过此方法增强（反射获取request对象，socket发送到服务端）
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //构建request
        RpcRequest request=RpcRequest.builder()
                .interfaceName(method.getDeclaringClass().getName())
                .methodName(method.getName())
                .params(args).paramsType(method.getParameterTypes()).build();
        //数据传输
        RpcResponse response;
        //后续添加逻辑：为保持幂等性，只对白名单上的服务进行重试
        if (serviceCenter.checkRetry(request.getInterfaceName())){
            //调用retry框架进行重试操作
            response=new guavaRetry().sendServiceWithRetry(request,rpcClient);
        }else {
            //只调用一次
            response= rpcClient.sendRequest(request);
        }
        return response.getData();
    }
     public <T>T getProxy(Class<T> clazz){
        Object o = Proxy.newProxyInstance(clazz.getClassLoader(), new Class[]{clazz}, this);
        return (T)o;
    }
}
```



### **1. 网络抖动导致某个节点被下线了，过一会网络好了，考虑过这个问题吗？**

当调用端发起的请求失败时，RPC 框架自身可以进行重试，再重新发送请求，通过这种方式保证系统的容错率；

项目使用Google Guava这款性能强大且轻量的框架来实现失败重试的功能；

### **2. 每个服务都进行重试吗？**

如果这个服务业务逻辑不是幂等的，比如插入数据操作，那触发重试的话会不会引发问题呢？

会的。

在使用 RPC 框架的时候，要确保被调用的服务的业务逻辑是幂等的，这样才能考虑根据事件情况开启 RPC 框架的异常重试功能

所以，我们可以**设置一个白名单**，服务端在注册节点时，将幂等性的服务注册在白名单中，客户端在请求服务前，先去白名单中查看该服务是否为幂等服务，如果是的话使用重试框架进行调用

白名单存放在zookeeper中（充当配置中心的角色）





## 服务限流

**假如要发布一个 RPC 服务，作为服务端接收调用端发送过来的请求，这时服务端的某个节点负载压力过高了，该如何保护这个节点？**

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241005112703215-1729341407250-12.png" alt="image-20241005112703215" style="zoom:50%;" />

这个问题还是很好解决的**，既然负载压力高，那就不让它再接收太多的请求就好了**，等接收和处理的请求数量下来后，这个节点的负载压力自然就下来了。

限流是一个比较通用的功能，这里我们可以**当调用端发送请求过来时，服务端在执行业务逻辑之前先执行限流逻辑，如果发现访问量过大并且超出了限流的阈值，**就**让服务端直接抛回给调用端一个限流异常，否则就执行正常的业务逻辑**。

### 限流算法

常见的限流算法有4种：**计数器法，滑动窗口算法，漏桶算法和令牌桶算法**

对于上面4种算法的详细介绍和优缺点比较可以参考   https://javaguide.cn/high-availability/limit-request.html

这里我们使用最高频的令牌桶算法作为项目使用的限流算法

**令牌桶算法简介** 

令牌桶是指一个限流容器，容器有最大容量，每秒或每100ms产生一个令牌（具体取决于机器每秒处理的请求数），**当容量中令牌数量达到最大容量时，令牌数量也不会改变了，只有当有请求过来时，使得令牌数量减少（只有获取到令牌的请求才会执行业务逻辑），才会不断生成令牌，**所以令牌桶算法是一种弹性的限流算法

**令牌桶算法限流范围：** 

假设令牌桶最大容量为n，每秒产生r个令牌

平均速率：则随着时间推延，处理请求的平均速率越来越趋近于每秒处理r个请求，**说明令牌桶算法可以控制平均速率**

瞬时速率：如果在一瞬间有很多请求进来，此时来不及产生令牌，则在一瞬间最多只有n个请求能获取到令牌执行业务逻辑，**所以令牌桶算法也可以控制瞬时速率**

在这里提下漏桶**，漏桶由于出水量固定，所以无法应对突然的流量爆发访问，也就是没有保证瞬时速率的功能，但是可以保证平均速率**

### 1. 有哪些常见的限流算法

- **固定窗口计数器算法**：固定窗口其实就是时间窗口，其原理是将时间划分为固定大小的窗口，在每个窗口内限制请求的数量或速率，即固定窗口计数器算法规定了系统单位时间处理的请求数量。**限流不够平滑，无法保证限流速率。**
- **滑动窗口计数器算法**：滑动窗口计数器算法 算的上是固定窗口计数器算法的升级版，限流的**颗粒度更小**。滑动窗口计数器算法相比于固定窗口计数器算法的优化在于：它把时间以一定比例分片 。例如我们的接口限流每分钟处理 60 个请求，我们可以把 1 分钟分为 60 个窗口。每隔 1 秒移动一次，每个窗口一秒只能处理不大于 60(请求数)/60（窗口数） 的请求， 如果当前窗口的请求计数总和超过了限制的数量的话就不再处理其他请求。**可以应对突然激增的流量**，**依然存在限流不够平滑**
- **漏桶算法**：准备一个队列用来保存请求，然后我们定期从队列中拿请求来执行就好了（和消息队列削峰/限流的思想是一样的）。**无法应对突然激增的流量，因为只能以固定的速率处理请求，对系统资源利用不够友好。桶流入水（发请求）的速率如果一直大于桶流出水（处理请求）的速率的话，那么桶会一直是满的，一部分新的请求会被丢弃，导致服务质量下降**
- **令牌桶算法**：请求在被处理之前需要拿到一个令牌，请求处理完毕之后将这个令牌丢弃（删除）。我们根据限流大小，按照一定的速率往桶里添加令牌。如果桶装满了，就不能继续往里面继续添加令牌了。

### 代码实现

**建立RateLimit 限流器接口**

```java
public interface RateLimit {
    //获取访问许可
    boolean getToken();
}
```



**TokenBucketRateLimitImpl类实现RateLimit 接口**

capacity为当前令牌桶的中令牌数量，timeStamp为上一次请求获取令牌的时间，我们没必要真的实现计时器每秒产生多少令牌放入容器中，只要记住上一次请求到来的时间，和这次请求的差值就知道在这段时间内产生了多少令牌

```java
public class TokenBucketRateLimitImpl implements RateLimit {
    //令牌产生速率（单位为ms）
    private static  int RATE;
    //桶容量
    private static  int CAPACITY;
    //当前桶容量
    private volatile int curCapcity;
    //时间戳
    private volatile long timeStamp=System.currentTimeMillis();
    public TokenBucketRateLimitImpl(int rate,int capacity){
        RATE=rate;
        CAPACITY=capacity;
        curCapcity=capacity;
    }
    @Override
    public synchronized boolean getToken() {
        //如果当前桶还有剩余，就直接返回
        if(curCapcity>0){
            curCapcity--;
            return true;
        }
        //如果桶无剩余，
        long current=System.currentTimeMillis();
        //如果距离上一次的请求的时间大于RATE的时间，则进入令牌生成逻辑
        if(current-timeStamp>=RATE){
            //计算这段时间间隔中生成的令牌，如果>2,桶容量加上（计算的令牌-1）
            //如果==1，就不做操作（因为这一次操作要消耗一个令牌）
            if((current-timeStamp) / RATE>=2){
                curCapcity+=(int)(current-timeStamp)/RATE-1;
            }
            //保持桶内令牌容量<=10
            if(curCapcity>CAPACITY) curCapcity=CAPACITY;
            //刷新时间戳为本次请求
            timeStamp=current;
            return true;
        }
        //获得不到，返回false
        return false;
    }
}
```



**RateLimitProvider类维护每个服务对应限流器，并负责向外提供限流器**

```java
public class RateLimitProvider {
    private Map<String, RateLimit> rateLimitMap=new HashMap<>();

    public RateLimit getRateLimit(String interfaceName){
        if(!rateLimitMap.containsKey(interfaceName)){
            RateLimit rateLimit=new TokenBucketRateLimitImpl(100,10);
            rateLimitMap.put(interfaceName,rateLimit);
            return rateLimit;
        }
        return rateLimitMap.get(interfaceName);
    }
}
```



**将RateLimitProvider嵌入到ServiceProvider类中，以便于调用**



**在NettyRPCServerHandler类中加入逻辑：**

当获取到调用请求，**开始调用前，先加入限流器**的判断

```java
private RpcResponse getResponse(RpcRequest rpcRequest){
    //得到服务名
    String interfaceName=rpcRequest.getInterfaceName();
    //接口限流降级
    RateLimit rateLimit=serviceProvider.getRateLimitProvider().getRateLimit(interfaceName);
    if(!rateLimit.getToken()){
        //如果获取令牌失败，进行限流降级，快速返回结果
        return RpcResponse.fail();
    }
    ...
```

## 服务-熔断

服务端进行自我保护，最简单有效的方式就是限流。那么调用端呢？调用端是否需要自我保护呢？举个例子，假如要发布一个服务 B，而服务 B 又依赖服务 C，当一个服务 A 来调用服务 B 时，服务 B 的业务逻辑调用服务 C，而这时服务 C 响应超时了，由**于服务 B 依赖服务 C，C 超时直接导致 B 的业务逻辑一直等待，而这个时候服务 A 在频繁地调用服务 B，服务 B 就可能会因为堆积大量的请求而导致服务宕机**

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241019212134794.png" alt="image-20241019212134794" style="zoom:50%;" />

由此可见，服务 B 调用服务 C，服务 C 执行业务逻辑出现异常时，会影响到服务 B，甚至可能会引起服务 B 宕机。这还只是 A->B->C 的情况，试想一下 A->B->C->D->……呢？在整个调用链中，只要中间有一个服务出现问题，都可能会引起上游的所有服务出现一系列的问题，甚至会引起整个调用链的服务都宕机，这是非常恐怖的。

所以**说，在一个服务作为调用端调用另外一个服务时，为了防止被调用的服务出现问题而影响到作为调用端的这个服务，这个服务也需要进行自我保护。而最有效的自我保护方式就是熔断。**

### 熔断逻辑

熔断设计来源于日常生活中的电路系统，在电路系统中存在一种熔断器（Circuit Breaker），它的作用就是在电流过大时自动切断电路。熔断器一般要实现三个状态：**闭合、断开和半开，分别对应于正常、故障和故障后检测故障是否已被修复的场景。**

- **闭合**：正常情况，**后台会对调用失败次数进行积累，到达一定阈值或比例时则自动启动熔断机制。**

- **断开**：一旦对服务的**调用失败次数达到一定阈值时，熔断器就会打开**，这时候对服务的调用**将直接返回一个预定的错误，而不执行真正的网络调用**。同时，熔断器需要**设置一个固定的时间间隔，当处理请求达到这个时间间隔时会进入半熔断状态。**

- **半开**：在半开状态下，**熔断器会对通过它的部分请求进行处理，如果对这些请求的成功处理数量达到一定比例则认为服务已恢复正常**，就会关闭熔断器，反之就会打开熔断器。

熔断设计的一般思路是，在请求失败 N 次后在 X 时间内不再请求，进行熔断；然后再在 X 时间后恢复 M% 的请求，如果 M% 的请求都成功则恢复正常，关闭熔断，否则再熔断 Y 时间，依此循环。

在熔断的设计中，**根据 Netflix 的开源组件 hystrix 的设计，我们可以仿照以下二个模块：熔断请求判断算法、熔断恢复机制**

- 熔断请求判断机制算法**：根据事先设置的在固定时间内失败的比例来计算。**

- 熔断恢复：**对于被熔断的请求，每隔 X 时间允许部分请求通过，若请求都成功则恢复正常。**

### 代码实现

**建立CircuitBreaker类实现熔断器逻辑**

在这个实现中，熔断器类 `CircuitBreaker` 管理熔断器的状态，并根据请求的成功和失败情况进行状态转换。具体步骤如下：

1. **失败 N 次后熔断**：当失败次数达到阈值时，熔断器进入打开状态，拒绝请求。

2. **打开状态持续 X 时间**：在打开状态持续 X 时间后，熔断器进入半开状态，允许部分请求通过。

3. **恢复 M% 的请求**：在半开状态下，熔断器允许请求通过，并根据请求的成功率决定是否恢复到闭合状态或重新进入打开状态。
   1. **如果 M% 的请求都成功**：恢复到闭合状态，正常处理请求。

   2. **否则再熔断 Y 时间**：如果请求失败，则进入打开状态，等待 Y 时间，然后再次尝试进入半开状态。


```java
public class CircuitBreaker {
    //当前状态
    private CircuitBreakerState state = CircuitBreakerState.CLOSED;
    private AtomicInteger failureCount = new AtomicInteger(0);
    private AtomicInteger successCount = new AtomicInteger(0);
    private AtomicInteger requestCount = new AtomicInteger(0);
    //失败次数阈值
    private final int failureThreshold;
    //半开启-》关闭状态的成功次数比例
    private final double halfOpenSuccessRate;
    //恢复时间
    private final long retryTimePeriod;
    //上一次失败时间
    private long lastFailureTime = 0;

    public CircuitBreaker(int failureThreshold, double halfOpenSuccessRate,long retryTimePeriod) {
        this.failureThreshold = failureThreshold;
        this.halfOpenSuccessRate = halfOpenSuccessRate;
        this.retryTimePeriod = retryTimePeriod;
    }
    //查看当前熔断器是否允许请求通过
    public synchronized boolean allowRequest() {
        long currentTime = System.currentTimeMillis();
        switch (state) {
            case OPEN:
                if (currentTime - lastFailureTime > retryTimePeriod) {
                    state = CircuitBreakerState.HALF_OPEN;
                    resetCounts();
                    return true;
                }
                return false;
            case HALF_OPEN:
                requestCount.incrementAndGet();
                return true;
            case CLOSED:
            default:
                return true;
        }
    }
    //记录成功
    public synchronized void recordSuccess() {
        if (state == CircuitBreakerState.HALF_OPEN) {
            successCount.incrementAndGet();
            if (successCount.get() >= halfOpenSuccessRate * requestCount.get()) {
                state = CircuitBreakerState.CLOSED;
                resetCounts();
            }
        } else {
            resetCounts();
        }
    }
    //记录失败
    public synchronized void recordFailure() {
        failureCount.incrementAndGet();
        lastFailureTime = System.currentTimeMillis();
        if (state == CircuitBreakerState.HALF_OPEN) {
            state = CircuitBreakerState.OPEN;
            lastFailureTime = System.currentTimeMillis();
        } else if (failureCount.get() >= failureThreshold) {
            state = CircuitBreakerState.OPEN;
        }
    }
    //重置次数
    private void resetCounts() {
        failureCount.set(0);
        successCount.set(0);
        requestCount.set(0);
    }

    public CircuitBreakerState getState() {
        return state;
    }
}

enum CircuitBreakerState {
    //关闭，开启，半开启
    CLOSED, OPEN, HALF_OPEN
}
```



**建立CircuitBreakerProvider类 维护不同服务的熔断器**

```java
public class CircuitBreakerProvider {
    private Map<String,CircuitBreaker> circuitBreakerMap=new HashMap<>();

    public CircuitBreaker getCircuitBreaker(String serviceName){
        CircuitBreaker circuitBreaker;
        if(circuitBreakerMap.containsKey(serviceName)){
            circuitBreaker=circuitBreakerMap.get(serviceName);
        }else {
            circuitBreaker=new CircuitBreaker(3,0.5,10000);
        }
        return circuitBreaker;
    }
}
```

**在调用端最顶层的ClientProxy上添加熔断逻辑**

在动态代理调用前进行检测，如果异常立即返回

```java
//jdk动态代理，每一次代理对象调用方法，都会经过此方法增强（反射获取request对象，socket发送到服务端）
@Override
public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    //构建request
    RpcRequest request=RpcRequest.builder()
            .interfaceName(method.getDeclaringClass().getName())
            .methodName(method.getName())
            .params(args).paramsType(method.getParameterTypes()).build();
    //获取熔断器
    CircuitBreaker circuitBreaker=circuitBreakerProvider.getCircuitBreaker(method.getName());
    //判断熔断器是否允许请求经过
    if (!circuitBreaker.allowRequest()){
        //这里可以针对熔断做特殊处理，返回特殊值
        return null;
    }
    //数据传输
    RpcResponse response;
    //后续添加逻辑：为保持幂等性，只对白名单上的服务进行重试
    if (serviceCenter.checkRetry(request.getInterfaceName())){
        //调用retry框架进行重试操作
        response=new guavaRetry().sendServiceWithRetry(request,rpcClient);
    }else {
        //只调用一次
        response= rpcClient.sendRequest(request);
    }
    //记录response的状态，上报给熔断器
    if (response.getCode() ==200){
        circuitBreaker.recordSuccess();
    }
    if (response.getCode()==500){
        circuitBreaker.recordFailure();
    }
    return response.getData();
}
```

### 1. **如果下游有一个服务的所有服务器都宕机了，该怎么做避免失败请求的大量堆积**

​	项目在客户端调用的链路头部设置了熔断器，当检测到失败次数超过阈值时，熔断器会变为关闭状态，阻止后续的请求；在一定时间后，熔断器变为半开状态，并根据之后请求的成功情况来决定是否阻止或放行请求



## 1. HTTP和RPC的区别

- HTTP 和各类 RPC 协议是在 TCP 之上定义的应用层协议。纯裸 TCP 是能收发数据，但它是个无边界的数据流，**上层需要定义消息格式用于定义消息边界。**
- **RPC 本质上不算是协议，而是一种调用方式，**而像 gRPC 和 Thrift 这样的具体实现，才是协议，它们是实现了 RPC 调用的协议。目的是希望程序员能像调用本地方法那样去调用远端的服务方法。同时 **RPC 有很多种实现方式，不一定非得基于 TCP 协议。**
- 从发展历史来说，HTTP 主要用于 B/S 架构，而 RPC 更多用于 C/S 架构。但现在其实已经没分那么清了，B/S 和 C/S 在慢慢融合。很多软件同时支持多端，**所以对外一般用 HTTP 协议，而内部集群的微服务之间则采用 RPC 协议进行通讯。**
- **RPC 其实比 HTTP 出现的要早，且比目前主流的 HTTP/1.1 性能要更好，所以大部分公司内部都还在使用 RPC。**
- HTTP/2.0 在 HTTP/1.1 的基础上做了优化，性能可能比很多 RPC 协议都要好，但由于是这几年才出来的，所以也不太可能取代掉 RPC。

## 2. 项目是基于TCP还是HTTP来做的，为什么用TCP实现呢?

我是使用TCP的，因为TCP更灵活，我可以自定义消息格式、编码方式、传输方式，HTTP 有较为固定的请求/响应格式，限制了灵活性

而且TCP本身是双向的，允许客户端和服务器随时发送和接收数据，而 HTTP 是基于请求/响应的通信模式，服务器不能主动发送消息

在高并发场景下，使用 TCP 长连接可以更好地管理连接池、保持会话和重用连接，减少资源消耗和延迟。
HTTP 的设计更多是为 web 场景优化，而在纯粹的 RPC 调用中，TCP 的性能表现通常更优。

## 3. RPC中出现异常如何处理？

#### 客户端处理

- **捕获异常**：在客户端调用远程服务时，应该捕获可能发生的异常，并进行适当的处理。
- **重试机制**：对于某些临时性故障（如网络抖动），可以实现重试机制。
- **异常转换**：将服务端抛出的异常转换为客户端可以理解和处理的形式。

#### 服务端处理

- **统一异常处理**：在服务端，可以使用统一的异常处理机制来处理所有异常
- **业务逻辑异常**：对于业务逻辑中的异常，应该在服务端进行适当的处理，并返回合适的响应码和错误信息。

### 日志记录

- **记录异常信息**：无论是客户端还是服务端，都应该记录异常的相关信息，以便后续排查问题。

## 4. Rpc难点？举一个印象深刻的讲讲

我在做这个项目的时候，因为从缓存开始以及后面的限流熔断等等都是自己实现的，所以能说出来的很多。在面对这种提问的时候，需要以实现者的角度，从两个顺序来回答：1，考虑到了什么问题？2，用什么方式解决这个问题的。

因为思考到，**大量请求打到一个节点上的流量问题，所以我查阅了限流的原理和实现，了解到了限流的四种常见算法，最后使用令牌桶算法实现了项目的限流降级。**


## 5. 为什么要做这个项目，RPC框架的优势

**远程过程调用**，比如，微服务项目：服务提供者和服务消费者运行在两台不同物理机上的不同进程内，它们之间的调用相比于本地方法调用，可称之为远程方法调用，内部集群的微服务之间则采用 RPC 协议进行通讯

RPC框架一般使用长链接，不必每次通信都要3次握手，减少网络开销。

## 6. Rpc框架中提到的两个BeanPostProcessor是做什么的？

在RPC框架中提到的两个BeanPostProcessor通常用于增强Spring框架中的bean生命周期管理，特别是在服务注册与发现、服务代理生成以及拦截器注入等方面。以下是关于这两个BeanPostProcessor的主要功能和作用的文字描述：

1. 服务暴露（Service Exporting）
功能：自动注册服务提供者到服务注册中心。
作用：当一个服务提供者在Spring容器中被初始化时，BeanPostProcessor可以识别出这是一个服务提供者，并自动将其相关信息注册到服务注册中心（如ZooKeeper）。这样，服务消费者就可以通过服务注册中心发现并调用这些服务提供者。
2. 服务引用（Service Referencing）
功能：自动引用服务消费者所需的远程服务。
作用：当一个服务消费者在Spring容器中被初始化时，BeanPostProcessor可以识别出这是一个服务消费者，并自动从服务注册中心获取服务提供者的信息，并建立远程调用的连接。这样，服务消费者可以无缝地调用远程服务，就像调用本地服务一样。
3. 代理生成（Proxy Generation）
功能：生成服务消费者的代理对象。
作用：在服务消费者初始化完成后，BeanPostProcessor可以生成一个代理对象，这个代理对象实际上是一个远程调用代理，它看起来像是本地对象，但实际上它会在远程调用服务提供者。通过这种方式，可以隐藏远程调用的细节，使服务消费者可以像调用本地方法一样调用远程服务。
4. 拦截器注入（Interceptor Injection）
功能：注入拦截器以增强服务调用逻辑。
作用：BeanPostProcessor可以在bean初始化过程中注入拦截器，这些拦截器可以在远程调用前后执行一些逻辑，如日志记录、性能监控、异常处理等。通过这种方式，可以增强服务的调用逻辑，提高系统的健壮性和可维护性。
综合应用
在实际的RPC框架中，BeanPostProcessor的使用可以极大地简化服务注册与发现的过程，并且可以使服务消费者的实现更为简洁。通过在bean初始化的不同阶段注入特定的逻辑，可以实现服务的自动注册、自动引用、远程调用的代理生成以及调用逻辑的增强等功能，从而提高系统的灵活性和扩展性。

总的来说，BeanPostProcessor在RPC框架中的应用主要集中在自动注册服务、自动引用服务、生成远程调用代理以及增强服务调用逻辑等方面，这些功能可以帮助开发者更轻松地实现服务的分布式调用和管理





## TODO



### 介绍一下你的项目（你的RPC是怎么设计的）

![image-20240812151605117](D:\2024\Notes\Typora\项目rpc+im\image-20240812151605117.png)

#### 代理层

代理层的设计是为了简化和透明化客户端与服务端之间的远程调用过程。代理层通常包含客户端代理和服务端代理，它们负责处理调用的序列化、反序列化、网络传输，以及异常处理等操作

客户端侧的代理层，负责将本地方法调用转换为网络请求，并发送给服务端；服务端侧的代理层，负责接收网络请求，将其转换为本地方法调用，然后将结果返回给客户端

**客户端代理层的职责：**

- 方法拦截：代理层通过实现 `InvocationHandler` 接口，拦截客户端对接口方法的调用。

- 请求构建：在方法调用时，代理层会根据方法名、参数、接口名等信息构建 `RpcRequest` 对象。

- 请求传输：Netty 实现的 `NettyRpcClient`将请求发送到服务端，并接收 `RpcResponse`。

- 响应处理：代理层负责检查 `RpcResponse` 的合法性，包括校验请求和响应的匹配、检查响应状态码等。

- 结果返回：将远程调用的结果返回给客户端，就像调用本地方法的返回值一样。

执行流程：

- 如果某个字段标注了 `@RpcReference`，则表示这个字段需要被注入一个 RPC 客户端代理对象，以调用远程服务

- 代理对象生成：通过 `getProxy(Class<T> clazz)` 方法，使用 `Proxy.newProxyInstance` 方法动态生成代理对象
- 方法调用拦截：当客户端调用代理对象的方法时，调用会被拦截并传递到 `invoke` 方法中。
- 请求构建与传输：
  - 在 `invoke` 方法中，根据调用的 `Method` 对象和参数，构建 `RpcRequest` 对象。
  - 然后通过`NettyRpcClient`将请求发送到服务端，则发送请求后会返回一个 `CompletableFuture<RpcResponse<Object>>`，等待异步返回结果
- 响应校验与处理：在接收到响应后，代理层会调用 `check` 方法来验证响应的合法性，如果验证通过，则返回结果数据。

**服务端代理层**

- **服务获取**：`handle` 方法首先通过 `serviceProvider` 获取对应的服务实例。`serviceProvider` 提供了对服务的管理和获取功能。
- 使用 Java 反射机制根据 RPC 请求中的方法名和参数类型找到对应的方法，并调用该方法。

#### 注册中心层

注册中心层的核心功能是管理服务的注册和发现，我使用的是Nacos，让服务能够被正确地注册、管理，并在需要时供客户端查询和调用

这里主要的方法有：添加服务、获取服务、发布服务

当一个服务在应用程序中被标记为需要发布，`NacosServiceProviderImpl` 会自动执行以下步骤：

1. **检查并添加服务**：检查服务是否已经注册，未注册则添加到本地管理中。

2. **服务注册**：将服务的网络信息（如 IP 地址和端口）注册到 Nacos 服务注册中心。
3. **服务可用**：注册完成后，服务即可被客户端通过 Nacos 发现和调用。

#### 路由层

请求的路由和负载均衡

在服务发现功能里面有体现，将服务实例转换为服务 URL 列表。这一步将服务实例的 IP 和端口信息拼接成服务 URL，根据负载均衡策略选择一个服务地址。

#### 异步设计层

首先体现在通信方面，也就是使用netty连接，和发送RPC请求，当客户端通过`bootstrap.connect`方法连接服务器时，连接操作是异步执行的，发送RPC请求的时候使用`CompletableFuture`类来存储异步操作的结果，

### 说一下整个RPC的服务过程

1. 服务端启动的时候先扫描，检测 `@RpcService` 注解，读取 `@RpcService` 注解中的 `group` 和 `version` 信息，结合当前 Bean 实例生成 `RpcServiceConfig` 配置对象，将服务名称及其对应的地址(ip+port)注册到Nacos，这样客户端才能根据服务名称找到对应的服务地址
2. 客户端检测 `@RpcReference` 注解，读取 `@RpcReference` 注解中的 `group` 和 `version` 信息，生成 `RpcServiceConfig` 配置对象
3. 通过 `getProxy(Class<T> clazz)` 方法，使用方法动态生成代理对象
4. 当客户端调用代理对象的方法时，调用会被拦截并传递到 `invoke` 方法中
5. 然后通过`NettyRpcClient`将请求发送到服务端，`RpcRequest` 会被封装到一个 `RpcMessage` 对象中
6. `NettyRpcClient`将 `RpcMessage` 对象通过Kryo序列化为字节数组
7. 编码后的数据通过 Netty 的 `Channel` 发送到服务器端
8. 当客户端发起请求的时候，多台服务器都可以处理这个请求。通过负载均衡选一台服务器。
9. 发送请求后会返回一个 `CompletableFuture<RpcResponse<Object>>`，等待异步返回结果
10. 服务端收到了来自客户端的消息，判断收到的消息类型是 心跳检测 还是普通RPC请求
11. 心跳检测就回应心跳检测，普通的 RPC 请求就使用`rpcRequestHandler`来处理这个请求
12. `RpcRequestHandler` 首先通过 `ServiceProvider` 获取到对应的服务实现对象
13. 然后使用反射机制，调用服务实现对象中与客户端请求匹配的方法，并传递客户端提供的参数
14. 最后，将方法的执行结果返回
15. 服务端将响应消息发送给客户端通过 `Channel` 发送给客户端
16. 客户端接收到服务器的响应后，Netty 会将字节流传递给 `RpcMessageDecode`，解码后得到 `RpcResponse` 对象
17. 在接收到响应后，代理层会调用 `check` 方法来验证响应的合法性，如果验证通过，则返回结果数据

### 在RPC项目中使用短连接有什么问题?如何实现长连接?

由于采用TCP作为传输协议，所以若采用短连接，则每次传输都要建立连接和断开连接，有很多网络通信消耗，这和开发预期的高性能 RPC不符合。

将服务端注册入Nacos 服务器中，而客户端每次都从Nacos 服务器中获取服务端地址，连接后将返回的 ChannelFuture存进一个set里，每次发送请求都从set从取出ChannelFuture发送信息，并且发送消息完毕后并不关闭连接，这就保证了客户端和服务端的长连接。

### 同步阻塞调用性能瓶颈:有什么瓶颈?你是怎么解决的，怎么实现异步调用的?

同步阻塞的缺点是调用时线程会阻塞在请求中，cpu此时并不会切换到其他线程，若服务器不返回消息，则客户端会一直被阻塞，无法处理其他请求。

Netty是基于NIO开发的，采用了异步非阻塞通信方式，本项目采用了netty服务框架实现了客户端服务端开发。

### 为什么需要心跳检测,客户端发送请求服务端没回应会造成什么?心跳检测机制你是怎么实现的?

在系统空闲时，例如凌晨，往往没有业务消息。如果此时链路被防火墙Hang住，或者遭遇网络闪断、网络单通等，通信双方无法识别出这类链路异常。等到第二天业务高峰期到来时，瞬间的海量业务冲击会导致消息积压无法发送给对方，由于链路的重建需要时间，这期间业务会大量失败

为了解决这个问题，需要周期性的心跳对链路进行有效性检测，一旦发生问题，可以及时关闭链路，重建TCP连接。

当有业务消息时，无须心跳检测，可以由业务消息进行链路可用性检测。所以心跳消息往往是在链路空闲时发送的。使用ldleStateHandler实现心跳检测机制（netty自带）。



### 你是怎么基于动态代理进行的请求处理?用的哪一种?为什么使用这种?

客户端通过 `getProxy(Class<T> clazz)` 方法，使用方法动态生成代理对象

当客户端调用代理对象的方法时，调用会被拦截并传递到 `invoke` 方法中

我使用的是JDK动态代理，我的`RpcClientProxy`类中，JDK 动态代理被用来拦截对 RPC 服务接口方法的调用，并将其转换为一个 RPC 请求，发送到远程服务器

因为我只需要对接口进行代理，接口方法的调用就足够满足需求，JDK比CGLIB 更简单

### 你的项目哪里遇到进程通信问题了?

当大量客户端同时向服务器发送请求时，服务器响应可能会产生并发问题，导致出现进程通信问题。

### 怎么解决 RPC项目中的进程通信问题?你在项目中又遇到什么难题？

使用Netty和`CompletableFuture`

`CompletableFuture` 支持非阻塞的异步编程，将多个异步操作组合在一起，并处理结果，简化了复杂的异步操作流程。

Netty 是一个高性能的网络通信框架，它本身已经实现了高效的并发处理机制，包括事件循环、线程池等

### 还有哪些解决进程通信问题的方式？

Synchronized:重量级锁；volatile乐观锁CAS机制，通过引入wait和notify机制，使用ReentryLock和 Condition进行控制。

### 是怎么基于BeanPostProcessor机制和ApplicationListener机制实现客户端的自启动与基于注解的服务调用？（rpc-注解调用）

首先客户端类 `NettyRpcClient` 的静态代码块会初始化一个netty客户端，因此在`BeanPostProcessor`中完成请求的包装后调用 `rpcClientProxy`的 `getProxy()`方法，代理里面会调用`NettyRpcClient`的`sendRpcRequest()`方法，就实现了客户端的初始化自启动。

而服务端则将初始化代码放在`NettyRpcServer`的`start()`方法里，启动服务器手动创建Spring 应用上下文并启动了 `NettyRpcServer`。

基于注解的服务调用方面，我将service的实现类标注了`@RpcService`注解，这是一个自定义注解，同时在Spring启动过程中会有一个`BeanPostProcessor`，对`@RpcService`注解标注的类进行处理，主要是将服务注册到服务提供者；而客户端则对 service的接口标注了`@RpcReference`，在`BeanPostProcessor`的实现类中对被 `@RpcReference`标注的接口完成方法与接口的映射，这样服务器的map和客户端的 map里关于方法和类名都是一致的，因比服务端可以知道该执行什么处理

### 为什么RPC项目用Nacos作为注册中心?

服务端可以将服务实例（包括 IP 地址、端口号等）注册到 Nacos，客户端可以从 Nacos 查询可用的服务实例列表，从而进行负载均衡和请求路由

Nacos 支持健康检查功能，能够监控服务实例的健康状态，自带心跳检测

用户界面和管理工具，可以方便地查看和管理服务实例、配置项、健康状态等

### 那么当有新的服务上线或者旧服务下线的时候，你怎么保证得到最新结果?客户端怎么发现服务?怎么动态监听连接?给我讲一讲机制?

Nacos会主动通知客户端，这个机制基于 **订阅-发布** 模型，当服务实例的状态发生变化时，Nacos 会通知所有订阅了该服务的客户端

当服务实例的状态（如上线、下线、变更）发生变化时，Nacos 会向所有订阅该服务的客户端发送通知。通知内容包括服务实例的最新列表，这样客户端可以更新自己的服务实例缓存



### rpc-负载均衡的实现（一致性哈希）

使用了一致性哈希算法，通过**哈希环**的概念将服务地址映射到一个环状的空间中。每个服务地址会通过哈希函数映射到环上的一个或多个点（这些点称为虚拟节点），当有一个请求到来时，也会被映射到环上的某个点，然后按照顺时针方向查找到最近的虚拟节点，对应的服务地址就是该请求应当路由到的地址。

这种方法的优点是，当服务节点增加或减少时，只有与该服务节点相邻的部分请求会被重新映射到其他节点，其他的请求仍然路由到原来的节点，从而实现了高效的负载均衡。

**哈希环的构建**：
哈希环使用 `TreeMap<Integer, String>` 来表示，`Integer` 是通过哈希函数计算出的哈希值，`String` 则是节点的标识。`TreeMap` 保证了节点按哈希值顺序排列，方便查找顺时针方向上最近的节点。

**虚拟节点的实现**：
每个物理节点对应多个虚拟节点。`addNode` 方法会为每个物理节点生成多个虚拟节点（通过在节点名称后附加索引值，如 `Node1-0`, `Node1-1`），并将它们映射到哈希环上。虚拟节点的数量由 `numberOfReplicas` 参数指定。

**数据到节点的映射**：
`getNode` 方法用于根据数据（或键）找到最近的节点。通过计算键的哈希值，并在 `TreeMap` 中顺时针查找最近的节点。如果找不到顺时针方向上的节点，则会返回哈希环上的第一个节点（模拟环的闭合）。

**哈希函数**：
使用 MD5 哈希算法来计算节点和数据的哈希值。虽然这里使用了 MD5，但在实际应用中，可以使用更适合分布式系统的哈希算法，例如 MurmurHash，这样能够更好地分布哈希值，避免哈希碰撞。

**节点的添加和删除**：
添加节点时会生成多个虚拟节点并将它们加入哈希环；删除节点时会移除对应的虚拟节点。这样做的目的是减少节点增删时的数据迁移量，并保持负载均衡

### Nacos链路

1. 服务注册

   **服务提供者** 启动时，注册信息包括服务名、实例的 IP 和端口，**Nacos Server** 接收到注册请求后，将服务实例信息存储到其内部数据结构中，并更新服务列表

2. 服务发现

   **服务消费者** 启动时，发起服务发现请求，**Nacos Server** 返回服务实例的列表给消费者。如果服务状态发生变化（例如新增、删除实例），Nacos 通过长轮询的方式将变更推送给消费者。

3. 服务更新

   **服务提供者** 更新服务实例信息（例如状态变更），并将更新请求发送到 Nacos Server，**Nacos Server** 更新其内部服务实例信息，并通知所有订阅该服务的消费者（通过长轮询）

### Nacos原理，与zk的区别，与nginx的区别，为什么不用nginx呢?Nacos/Zookeeper/Nginx

在网上了解到，zk在高并发，或者长时间维持一定量的请求，那么还是会导致zk的磁盘耗尽、io读写异常、导致zk不可用，从而导致整个集群的服务注册发型能力不可用；而且当 master 节点因为网络故障与其他节点失去联系时，剩余节点会重新进行 leader 选举。问题在于，选举 leader 的时间太长，30 ~ 120s, 且选举期间整个 zk 集群都是不可用的，这就导致在选举期间注册服务瘫痪，nacos中的一致性不是像zk通过节点数据进行维护，并不会出现服务无限重复注册的情况

**Nginx** 是一个高性能的 HTTP 和反向代理服务器，可以用于负载均衡、反向代理、静态资源服务等，但是并没有服务发现、服务注册等功能

### 注册中心除了Nacos还有哪些？

**Zookeeper**

服务提供者在 ZooKeeper 上注册自己的服务信息，包括服务名称、地址、端口等。注册的信息存储在 ZooKeeper 的 ZNode 中，ZNode 类似于一个目录，可以存储服务的各种元数据，服务消费者可以从 ZooKeeper 中查找服务。通过查询 ZooKeeper 上的注册信息，消费者能够获得可用的服务实例列表，从而实现服务发现

**Eureka**

服务启动时，会将自身的信息（例如服务名称、IP 地址、端口号等）注册到 Eureka 服务器上，服务在需要调用其他服务时，可以从 Eureka 服务器获取已注册的服务实例列表，然后调用，比较轻量级

### rpc项目的整体架构

这个 RPC 项目的整体架构可以分为以下几个主要部分：客户端、服务端、注册中心、负载均衡、通信层、序列化与反序列化等

**客户端**

客户端代理，负责创建服务接口的代理对象，并拦截方法调用，构建 RPC 请求，将其发送到服务器，并获取响应

Netty客户端，维护连接池，通过 `ChannelProvider` 管理和复用 `Channel`，避免频繁创建连接，使用异步 `CompletableFuture` 机制来处理 RPC 请求的响应。

**服务端**

服务端代理，负责根据请求调用本地服务的实际方法，处理完成后返回结果。使用反射机制 (`Method.invoke`) 调用目标服务的具体方法

Netty服务端，负责启动服务器并监听客户端请求，处理来自客户端的请求，解码请求、调用实际的服务实现，并返回结果

**注册中心**

负责将服务注册到 Nacos 注册中心以及从 Nacos 中发现服务

**负载均衡**

客户端在从注册中心获取多个服务实例后，通过负载均衡算法选择一个实例进行调用

**通信层**

使用 Netty 作为底层通信框架，实现高效的网络通信

自定义编码解码器，防止粘包

**序列化和反序列化**

负责将 Java 对象转换为字节流，以及将字节流还原为 Java 对象，我使用的是Kryo

### 别人要怎么使用你的rpc框架(部署rpc)

将RPC框架打包成一个JAR文件

上传到公司内部的Maven仓库

使用者只需在他们的项目中添加对你RPC框架的Maven依赖

需要在他们的服务端项目中使用你的RPC框架来暴露服务，也就是用`@RpcService`，暴露服务

客户端使用使用`@RpcReference`注解来注入RPC接口，然后调用远程方法

连接到一个Nacos服务器，并在配置中指定相应的Nacos地址

### 如果遇到网络拥塞、超时等异常情况该如何处理?

为每个 RPC 请求设置超时机制。超时后，立即返回错误信息，避免客户端一直等待。在发送请求时使用 `CompletableFuture` 的 `get(long timeout, TimeUnit unit)` 方法来实现超时控制

在客户端的服务发现或调用阶段实现负载均衡机制。避免将请求集中在少数服务器上，平衡请求压力

`CompletableFuture`提供了异步调用和回调，客户端不需要一直等待服务端的响应，可以在接收到响应后执行回调函数

### 你使用了 TCP 长连接池，但是TCP貌似是2h才断开，你在应用层有做什么操作吗?

看netty的长连接和心跳那个问题，【Netty 长连接、心跳机制了解么?】

### 你的RPC框架有重试机制吗？策略是什么？比如说你调用失败了，是通过配置、接口、或者类去重试吗？怎么实现的？

我的RPC框架没有设置重试机制，但是可以通过配置或者接口，或者代理、或者中间件层加入重试

在 `invoke` 方法中使用 `while` 循环实现重试机制。比如说可以用`retryCount` 用于记录当前重试的次数。当 `retryCount` 小于 `MAX_RETRIES` 时，如果发生异常

### 如果获取到新的服务地址还是调用失败呢？比如可能不是地址的问题而是接口的问题？

在异常处理中记录详细的错误信息并采取适当的措施，如告警或通知，对于某些非关键业务逻辑，如果接口调用失败，可以提供一个降级方案。例如，返回一个默认值或者使用本地缓存的数据，避免系统中断

如果一个服务地址调用失败，框架可以尝试调用同一服务的其他实例。可以通过服务发现机制获取可用的服务实例列表，然后进行轮询或随机选择进行重试，在重试多次后，如果所有实例都无法成功调用，系统可以触发服务降级逻辑

如果接口问题导致多次重试失败，框架可以自动回退到上一个版本的服务地址

### dubbo和你的rpc框架？你怎么评价他们的差异？

- Dubbo 是一个成熟的分布式服务框架，支持多种注册中心，如 Zookeeper、Nacos、Consul 等，并且可以选择不同的序列化机制。Dubbo 拥有一个庞大的社区和生态系统，有大量的开源插件和扩展支持
- 基本的 RPC 功能，包括服务注册、发现、序列化和反序列化、以及客户端与服务端的通信。虽然功能上较为基础，但它为你提供了更大的灵活性，可以根据具体需求进行深度定制。与 Dubbo 相比，你的框架可能更轻量化，但需要在功能和稳定性上进行更多的开发与测试。

### 你的RPC框架跟springboot有什么关系？

我在RPC框架中使用了Spring的一些核心功能，比如`@Component`注解来管理Bean的生命周期，`BeanPostProcessor`来处理自定义注解（如`@RpcService`和`@RpcReference`）。这些都依赖于Spring的IoC容器来实现。

Spring Boot通常用于构建微服务，而我的RPC框架可以为这些微服务之间的通信提供支持。结合使用时，Spring Boot负责应用的整体架构和管理，而我的RPC框架负责具体的服务调用逻辑。

### 有些RPC框架用的是http，你怎么看待?

使用HTTP作为RPC传输协议适合对性能要求不太高，或者需要兼容多种语言、框架的系统，特别是在需要与已有的REST API或Web服务集成时非常合适。然而，在需要高性能、低延迟的系统中，使用更轻量、更高效的协议（如TCP或自定义的二进制协议）可能会更合适。

**缺点**

使用HTTP协议通常意味着使用文本格式（如JSON、XML）进行数据传输，这比起二进制格式要更加冗长，导致传输数据量大，序列化和反序列化开销也较大

HTTP协议的设计是为Web服务而非高性能RPC服务而优化的

HTTP是无状态协议，如果需要在多个RPC调用间保持会话状态，必须额外处理会话管理（如通过Token、Cookie等），增加了开发的复杂性

### 如果有一台单点故障了，你轮询的时候怎么屏蔽?你怎么把故障服务器从列表淘汰掉?

通过主动健康检查（Nacos主动检查），结合负载均衡策略，可以有效地屏蔽单点故障，确保客户端始终能够调用到可用的服务实例。

服务器的话Nacos检测到他不健康就会标记为不可用

配合重试、服务降级等策略进一步提高系统的容错能力和稳定性

### 抛开RPC不谈，你去怎么理解降级这个事情的?给异常情况做else分支，做这个事情其实在不同场景有不同的解决方案，你了解到的哪类场景可以用哪类方案去做降级这个事?

降级在系统设计中指的是在系统压力过大、部分功能故障或外部依赖不可用时，通过限制或减少系统的部分功能，确保核心功能的稳定性和可用性。

降级的核心理念是优先保证系统的整体稳定性。通过主动放弃一些非核心或次要功能，确保核心功能仍能正常运行。

- 当系统遇到突发的流量激增时，如促销活动或热点新闻，服务器压力骤增。如果不采取措施，整个系统可能崩溃。此时，可以通过降级策略限制某些非核心功能，如暂停推荐系统、减少动态内容加载等，确保关键业务（如订单处理）能够正常运行。
- 系统中的一些功能可能依赖外部服务或第三方API，例如支付网关、物流查询等。如果这些外部依赖不可用，可以通过降级措施提供备用方案或简单的占位信息，而不是让整个功能失效。例如，支付系统可以降级为仅支持现金支付，或者提供稍后的支付提醒。
- 如果系统的某个内部模块出现故障，如数据库连接池满载或缓存服务失效，可以通过降级措施限制对该模块的访问，或启用备用方案。例如，当缓存服务失效时，可以直接从数据库读取数据，尽管性能会有所下降，但功能仍然可用。
- 在双十一等大促活动时，面对大量访问，平台可能会选择关闭部分非核心功能（如历史订单查询、优惠券展示等），以保证订单支付和商品浏览的顺畅。
- 在网络带宽不足或服务器负载过高时，平台可以选择降低视频的分辨率，甚至暂时关闭高清播放，来确保流媒体播放的连续性。

### 怎么用netty创建一个channel去发送数据？在java的代码是怎么写的？

创建一个 `NioEventLoopGroup` 实例，用于管理处理客户端的事件，使用 `Bootstrap` 类配置客户端。指定使用 `NioSocketChannel` 作为 Channel 类，并添加一个 `ChannelInitializer`，用于初始化新的 `SocketChannel` 实例，在 `ChannelInitializer` 中，向 `ChannelPipeline` 添加编解码器，调用 `bootstrap.connect` 方法连接到指定的服务器地址和端口。获取连接的 `Channel` 实例，并通过 `channel.writeAndFlush()` 方法将数据（例如消息字符串）发送到服务器。

### 讲讲RPC的机制，比如说怎么实现SPI机制的?（ServiceLoader）

在 RPC 框架中，SPI 机制通常用于：

- **序列化/反序列化**：动态选择不同的序列化方式，如 JSON、Protobuf、Kryo 等。
- **网络传输协议**：选择不同的网络传输协议，如 Netty、HTTP、Dubbo 等。
- **负载均衡策略**：选择不同的负载均衡策略。

但是我的rpc框架序列化只使用了Kryo，传输用了Netty，负载均衡使用了一致性哈希算法

SPI 是一种服务发现机制，允许框架或应用程序动态加载实现类，定义 SPI 接口，实现 SPI 接口，在 `META-INF/services` 目录下配置服务提供者，加载服务提供者

SPI使用了`ServiceLoader`来实现，这里涉及类加载的过程：

- **类加载器查找**：`ServiceLoader` 首先使用类加载器查找 `META-INF/services` 目录中的服务提供者配置文件。这个目录位于每个 JAR 包的根目录下。
- `ServiceLoader` 读取 `META-INF/services` 目录下的文件，文件名是服务接口的完全限定名，文件内容是服务实现的完全限定名列表。
- 对于每个服务实现类，`ServiceLoader` 使用服务实现类的默认构造函数进行实例化
- `ServiceLoader` 将所有加载的服务提供者存储在集合中，并允许客户端通过迭代器访问这些服务提供者

### netty底层为什么用direct buffer？

Netty 底层使用 `DirectByteBuffer`（直接缓冲区）主要是为了提高性能和效率

直接缓冲区是直接分配在操作系统的物理内存中的，而不是 JVM 堆内存。这样，Netty 可以减少在数据读取和写入过程中不必要的内存复制。

Netty 使用直接缓冲区来管理网络数据的内存，这样可以更精确地控制内存的分配和释放，减少垃圾回收（GC）的压力，由于直接缓冲区不受 JVM 堆内存限制，它们可以在长时间的连接中重复使用，避免频繁的 GC 开销。

直接缓冲区可以处理比 JVM 堆内存更大的数据量。这对于处理大量数据的网络通信（例如大文件传输或高并发连接）非常重要。

### RPC项目的长连接池是怎么实现的？

连接池通常包括以下功能：

- **连接创建**：如果 `ChannelProvider` 找不到可用的连接或现有连接不可用，则尝试重新创建连接，并将其添加到连接池中
- **连接复用**：在连接池中查找可用连接，如果找到则复用。
- **连接释放**：将连接返回到池中以供后续使用。
- **连接保持**：定期检查和保持连接活跃。
- **连接销毁**：在连接不再使用时关闭连接，并释放资源

### 可以做到自动扩容缩容吗?现在让你实现你如何实现?

可以的，依赖Nacos注册中心就可以实现

**扩容**：

- 启动新的服务实例，将其注册到服务注册中心。注册中心会自动更新服务实例列表，负载均衡器会自动包括新的实例

**缩容**：

- 停止现有的服务实例，并从服务注册中心注销。注册中心会更新服务实例列表，负载均衡器会自动排除已注销的实例

### 远程调用的时候，我直接传输对象不可以吗?

远程调用时，通常需要将对象从客户端传输到服务器，或者从服务器传输到客户端。这需要序列化（将对象转换为字节流）和反序列化（将字节流转换回对象）。对象的版本变化（字段增加、删除或类型变化）可能导致序列化和反序列化失败。大对象或复杂对象的序列化和反序列化可能会影响性能和网络带宽

### CompletableFuture+线程池是如何减少响应时间的?

- **异步处理**：在后台线程中处理耗时操作，不阻塞主线程。
- **线程池管理**：复用线程，减少线程创建和销毁的开销。
- **非阻塞操作**：使用回调处理任务结果，避免阻塞等待。

### 如果不用CompletableFuture怎么实现多个异步任务同步

使用 `Future` 和 `ExecutorService`：

`ExecutorService` 提供了管理线程池的功能，并可以提交任务。

`Future` 对象表示异步计算的结果，可以用来获取任务的结果或检查任务的完成状态。

### RPC如何对某些数据进行压缩？int、int数组、String数组

基于 GZIP 的压缩和解压，

**压缩**：

1. `int` 类型
   - 将 `int` 转换为字节数组。可以使用 `ByteBuffer` 或自定义转换方法。
   - 使用 `GzipCompress` 类中的 `compress` 方法压缩字节数组。
2. `int` 数组
   - 将 `int` 数组转换为字节数组。遍历数组，将每个 `int` 转换为 4 字节的字节数组，并拼接成一个完整的字节数组。
   - 使用 `GzipCompress` 类中的 `compress` 方法压缩完整的字节数组。
3. `String` 数组
   - 将 `String` 数组转换为字节数组。可以先将每个 `String` 转换为 UTF-8 编码的字节数组，然后将所有字节数组合并。
   - 使用 `GzipCompress` 类中的 `compress` 方法压缩完整的字节数组。

### 服务下线怎么实现的

代码调用`deregisterInstance()`，Nacos把服务注销掉

rpc中使用事务该咋办
rpc和mq区别 应用场景
rpc这种模式和哪个设计模式比较相像



https://www.nowcoder.com/share/jump/32217479793689980



#### **2.   这个rpc的性能如何测试有没有测过自己rpc框架的并发量**

用jmeter,然后去搜索  jmeter测试java请求  的相关博客看看。















