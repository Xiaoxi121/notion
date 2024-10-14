# Springcloud

## Springcloud01

### 认识微服务

![image-20240415183934924](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20240415183934924.png)

  ![image-20240415205830202](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20240415205830202.png)

微服务的架构特征：

- 单一职责：微服务拆分粒度更小，每一个服务都对应唯一的业务能力，做到单一职责
- 自治：团队独立、技术独立、数据独立，独立部署和交付
- 面向服务：服务提供统一标准的接口，与语言和技术无关
- 隔离性强：服务调用做好隔离、容错、降级，避免出现级联问题

微服务的上述特性其实是在给分布式架构制定一个标准，进一步降低服务之间的耦合度，提供服务的独立性和灵活性。做到高内聚，低耦合。因此，可以认为**微服务**是一种经过良好架构设计的**分布式架构方案** 。

### 服务拆分和远程调用

- 不同微服务，不要重复开发相同业务
- 微服务数据独立，不要访问其它微服务的数据库
- 微服务可以将自己的业务暴露为接口，供其它微服务调用

#### 2.3.1.案例需求：

修改order-service中的根据id查询订单业务，要求在查询订单的同时，根据订单中包含的userId查询出用户信息，一起返回。

![image-20210713213312278](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713213312278.png)

因此，我们需要在order-service中 向user-service发起一个http的请求，调用http://localhost:8081/user/{userId}这个接口。大概的步骤是这样的：

- 注册一个RestTemplate的实例到Spring容器
- 修改order-service服务中的OrderService类中的queryOrderById方法，根据Order对象中的userId查询User
- 将查询的User填充到Order对象，一起返回

#### 2.3.2.注册RestTemplate

首先，我们在order-service服务中的OrderApplication启动类中，注册RestTemplate实例：

```java
package cn.itcast.order;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@MapperScan("cn.itcast.order.mapper")
@SpringBootApplication
public class OrderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderApplication.class, args);
    }

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

#### 2.3.3.实现远程调用

修改order-service服务中的cn.itcast.order.service包下的OrderService类中的queryOrderById方法：

![image-20210713213959569](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713213959569.png)

#### 2.4.提供者与消费者

在服务调用关系中，会有两个不同的角色：

**服务提供者**：一次业务中，被其它微服务调用的服务。（提供接口给其它微服务）**服务消费者**：一次业务中，调用其它微服务的服务。（调用其它微服务提供的接口）

![image-20210713214404481](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713214404481.png)

但是，服务提供者与服务消费者的角色并不是绝对的，而是相对于业务而言。如果服务A调用了服务B，而服务B又调用了服务C，服务B的角色是什么？

- 对于A调用B的业务而言：A是服务消费者，B是服务提供者
- 对于B调用C的业务而言：B是服务消费者，C是服务提供者

因此，服务B既可以是服务提供者，也可以是服务消费者。

### Eukeka注册中心

#### 3.1.Eureka的结构和作用

这些问题都需要利用SpringCloud中的注册中心来解决，其中最广为人知的注册中心就是Eureka，其结构如下：

![image-20210713220104956](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713220104956.png)



回答之前的各个问题。

问题1：order-service如何得知user-service实例地址？

获取地址信息的流程如下：

- user-service服务实例启动后，将自己的信息注册到eureka-server（Eureka服务端）。这个叫服务注册
- eureka-server保存服务名称到服务实例地址列表的映射关系
- order-service根据服务名称，拉取实例地址列表。这个叫服务发现或服务拉取



问题2：order-service如何从多个user-service实例中选择具体的实例？

- order-service从实例列表中利用负载均衡算法选中一个实例地址
- 向该实例地址发起远程调用

 

问题3：order-service如何得知某个user-service实例是否依然健康，是不是已经宕机？

- user-service会每隔一段时间（默认30秒）向eureka-server发起请求，报告自己状态，称为心跳
- 当超过一定时间没有发送心跳时，eureka-server会认为微服务实例故障，将该实例从服务列表中剔除
- order-service拉取服务时，就能将故障实例排除了



> 注意：一个微服务，既可以是服务提供者，又可以是服务消费者，因此eureka将服务注册、服务发现等功能统一封装到了eureka-client端



因此，接下来我们动手实践的步骤包括：

![image-20210713220509769](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713220509769.png)

#### 3.2.搭建eureka-server

首先大家注册中心服务端：eureka-server，这必须是一个独立的微服务

##### 3.2.1.创建eureka-server服务

在cloud-demo父工程下，创建一个子模块：

![image-20210713220605881](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713220605881.png)

填写模块信息：

![image-20210713220857396](D:\2024\Code\Java\springcloud\学习资料\day01-SpringCloud01\讲义\assets\image-20210713220857396.png)



然后填写服务信息：

![image-20210713221339022](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713221339022.png)



##### 3.2.2.引入eureka依赖

引入SpringCloud为eureka提供的starter依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```



##### 3.2.3.编写启动类

给eureka-server服务编写一个启动类，一定要添加一个@EnableEurekaServer注解，开启eureka的注册中心功能：

```java
package cn.itcast.eureka;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```



##### 3.2.4.编写配置文件

编写一个application.yml文件，内容如下：

```yaml
server:
  port: 10086
spring:
  application:
    name: eureka-server
eureka:
  client:
    service-url: 
      defaultZone: http://127.0.0.1:10086/eureka
```



##### 3.2.5.启动服务

启动微服务，然后在浏览器访问：http://127.0.0.1:10086

看到下面结果应该是成功了：

![image-20210713222157190](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713222157190.png)







#### 3.3.服务注册

下面，我们将user-service注册到eureka-server中去。

##### 1）引入依赖

在user-service的pom文件中，引入下面的eureka-client依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

##### 2）配置文件

在user-service中，修改application.yml文件，添加服务名称、eureka地址：

```yaml
spring:
  application:
    name: userservice
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10086/eureka
```

##### 3）启动多个user-service实例

为了演示一个服务有多个实例的场景，我们添加一个SpringBoot的启动配置，再启动一个user-service。



首先，复制原来的user-service启动配置：

![image-20210713222656562](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713222656562.png)

然后，在弹出的窗口中，填写信息：

![image-20210713222757702](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713222757702.png)



现在，SpringBoot窗口会出现两个user-service启动配置：

![image-20210713222841951](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713222841951.png)

不过，第一个是8081端口，第二个是8082端口。

启动两个user-service实例：

![image-20210713223041491](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713223041491.png)

查看eureka-server管理页面：

![image-20210713223150650](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713223150650.png)

#### 3.4.服务发现

下面，我们将order-service的逻辑修改：向eureka-server拉取user-service的信息，实现服务发现。

##### 1）引入依赖

之前说过，服务发现、服务注册统一都封装在eureka-client依赖，因此这一步与服务注册时一致。

在order-service的pom文件中，引入下面的eureka-client依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

##### 2）配置文件

服务发现也需要知道eureka地址，因此第二步与服务注册一致，都是配置eureka信息：

在order-service中，修改application.yml文件，添加服务名称、eureka地址：

```yaml
spring:
  application:
    name: orderservice
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10086/eureka
```

##### 3）服务拉取和负载均衡

最后，我们要去eureka-server中拉取user-service服务的实例列表，并且实现负载均衡。

不过这些动作不用我们去做，只需要添加一些注解即可。

在order-service的OrderApplication中，给RestTemplate这个Bean添加一个@LoadBalanced注解：

![image-20210713224049419](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713224049419.png)



修改order-service服务中的cn.itcast.order.service包下的OrderService类中的queryOrderById方法。修改访问的url路径，用服务名代替ip、端口：

![image-20210713224245731](D:/2024/Code/Java/springcloud/学习资料/day01-SpringCloud01/讲义/assets/image-20210713224245731.png)

spring会自动帮助我们从eureka-server端，根据userservice这个服务名称，获取实例列表，而后完成负载均衡。



![image-20240418084403078](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20240418084403078.png)

### Ribbon负载均衡

![image-20240418092124129](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20240418092124129.png)

#### 负载均衡原理

SpringCloud底层其实是利用了一个名为Ribbon的组件，来实现负载均衡功能的。

![image-20210713224517686](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713224517686.png)

那么我们发出的请求明明是http://userservice/user/1，怎么变成了http://localhost:8081的呢？

SpringCloudRibbon的底层采用了一个拦截器，拦截了RestTemplate发出的请求，对地址做了修改。用一幅图来总结一下：

![image-20210713224724673](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713224724673.png)

基本流程如下：

- 拦截我们的RestTemplate请求http://userservice/user/1
- RibbonLoadBalancerClient会从请求url中获取服务名称，也就是user-service
- DynamicServerListLoadBalancer根据user-service到eureka拉取服务列表
- eureka返回列表，localhost:8081、localhost:8082
- IRule利用内置负载均衡规则，从列表中选择一个，例如localhost:8081
- RibbonLoadBalancerClient修改请求地址，用localhost:8081替代userservice，得到http://localhost:8081/user/1，发起真实请求

#### 负载均衡策略

负载均衡的规则都定义在IRule接口中，而IRule有很多不同的实现类：

![image-20210713225653000](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713225653000.png)

不同规则的含义如下：

| **内置负载均衡规则类**    | **规则描述**                                                 |
| ------------------------- | ------------------------------------------------------------ |
| RoundRobinRule            | 简单轮询服务列表来选择服务器。它是Ribbon默认的负载均衡规则。 |
| AvailabilityFilteringRule | 对以下两种服务器进行忽略：   （1）在默认情况下，这台服务器如果3次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续30秒，如果再次连接失败，短路的持续时间就会几何级地增加。  （2）并发数过高的服务器。如果一个服务器的并发连接数过高，配置了AvailabilityFilteringRule规则的客户端也会将其忽略。并发连接数的上限，可以由客户端的<clientName>.<clientConfigNameSpace>.ActiveConnectionsLimit属性进行配置。 |
| WeightedResponseTimeRule  | 为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择。 |
| **ZoneAvoidanceRule**     | 以区域可用的服务器为基础进行服务器的选择。使用Zone对服务器进行分类，这个Zone可以理解为一个机房、一个机架等。而后再对Zone内的多个服务做轮询。 |
| BestAvailableRule         | 忽略那些短路的服务器，并选择并发数较低的服务器。             |
| RandomRule                | 随机选择一个可用的服务器。                                   |
| RetryRule                 | 重试机制的选择逻辑                                           |

默认的实现就是ZoneAvoidanceRule，是一种轮询方案

#### 自定义负载均衡策略

通过定义IRule实现可以修改负载均衡规则，有两种方式：

1. 代码方式：在order-service中的OrderApplication类中，定义一个新的IRule：

```java
@Bean
public IRule randomRule(){
    return new RandomRule();
}
```

2. 配置文件方式：在order-service的application.yml文件中，添加新的配置也可以修改规则：

```yaml
userservice: # 给某个微服务配置负载均衡规则，这里是userservice服务
  ribbon:
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule # 负载均衡规则 
```

> **注意**，一般用默认的负载均衡规则，不做修改。

#### 饥饿加载

Ribbon默认是采用懒加载，即第一次访问时才会去创建LoadBalanceClient，请求时间会很长。

而饥饿加载则会在项目启动时创建，降低第一次访问的耗时，通过下面配置开启饥饿加载：

```yaml
ribbon:
  eager-load:
    enabled: true
    clients: userservice
```

### Nacos

#### 注册中心

![image-20240418094517647](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20240418094517647.png)

Ribbon默认是采用懒加载，即第一次访问时才会去创建LoadBalanceClient，请求时间会很长。而饥饿加载则会在项目启动时创建，降低第一次访问的耗时，通过下面配置开启饥饿加载：

```yaml
ribbon:
  eager-load:
    enabled: true
    clients: userservice
```

#### 服务注册到nacos

Nacos是SpringCloudAlibaba的组件，而SpringCloudAlibaba也遵循SpringCloud中定义的服务注册、服务发现规范。因此使用Nacos和使用Eureka对于微服务来说，并没有太大区别。

主要差异在于：

- 依赖不同
- 服务地址不同

##### 1）引入依赖

在cloud-demo父工程的pom文件中的`<dependencyManagement>`中引入SpringCloudAlibaba的依赖：

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-alibaba-dependencies</artifactId>
    <version>2.2.6.RELEASE</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

然后在user-service和order-service中的pom文件中引入nacos-discovery依赖：

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

> **注意**：不要忘了注释掉eureka的依赖。

##### 2）配置nacos地址

在user-service和order-service的application.yml中添加nacos地址：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
```

> **注意**：不要忘了注释掉eureka的地址

##### 3）重启

重启微服务后，登录nacos管理页面，可以看到微服务信息：

![image-20210713231439607](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713231439607.png)

#### 服务分级存储模型

一个**服务**可以有多个**实例**，例如我们的user-service，可以有:

- 127.0.0.1:8081
- 127.0.0.1:8082
- 127.0.0.1:8083

假如这些实例分布于全国各地的不同机房，例如：

- 127.0.0.1:8081，在上海机房
- 127.0.0.1:8082，在上海机房
- 127.0.0.1:8083，在杭州机房

Nacos就将同一机房内的实例 划分为一个**集群**。

也就是说，user-service是服务，一个服务可以包含多个集群，如杭州、上海，每个集群下可以有多个实例，形成分级模型，如图：

![image-20210713232522531](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713232522531.png)



微服务互相访问时，应该尽可能访问同集群实例，因为本地访问速度更快。当本集群内不可用时，才访问其它集群。例如：

![image-20210713232658928](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713232658928.png)

杭州机房内的order-service应该优先访问同机房的user-service。

##### 给user-service配置集群

修改user-service的application.yml文件，添加集群配置：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ # 集群名称
```

重启两个user-service实例后，我们可以在nacos控制台看到下面结果：

![image-20210713232916215](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713232916215.png)



我们再次复制一个user-service启动配置，添加属性：

```sh
-Dserver.port=8083 -Dspring.cloud.nacos.discovery.cluster-name=SH
```

配置如图所示：

![image-20210713233528982](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713233528982.png)

启动UserApplication3后再次查看nacos控制台：

![image-20210713233727923](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713233727923.png)

##### 同集群优先的负载均衡

默认的`ZoneAvoidanceRule`并**不能实现根据同集群优先来实现负载均衡。**

因此Nacos中提供了一个`NacosRule`的实现，可以优先从同集群中挑选实例。

1）给order-service配置集群信息

修改order-service的application.yml文件，添加集群配置：

```sh
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ # 集群名称
```

2）修改负载均衡规则

修改order-service的application.yml文件，修改负载均衡规则：

```yaml
userservice:
  ribbon:
    NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule # 负载均衡规则 
```

#### 权重配置

实际部署中会出现这样的场景：服务器设备性能有差异，部分实例所在机器性能较好，另一些较差，**我们希望性能好的机器承担更多的用户请求**。但默认情况下NacosRule是同集群内随机挑选，不会考虑机器的性能问题。因此，Nacos提供了权重配置来控制访问频率，**权重越大则访问频率越高**。在nacos控制台，找到user-service的实例列表，点击编辑，即可修改权重：

![image-20210713235133225](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713235133225.png)

在弹出的编辑窗口，修改权重：

<img src="D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210713235235219.png" alt="image-20210713235235219" style="zoom: 67%;" />

> **注意**：**如果权重修改为0，则该实例永远不会被访问**

#### 环境隔离

Nacos提供了**namespace来实现环境隔离功能。**

- nacos中可以有多个namespace
- namespace下可以有group、service等
- **不同namespace之间相互隔离，例如不同namespace的服务互相不可见**

<img src="D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714000101516.png" alt="image-20210714000101516" style="zoom:50%;" />

##### 创建namespace

默认情况下，所有service、data、group都在同一个namespace，名为public：

![image-20210714000414781](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714000414781.png)

我们可以点击页面新增按钮，添加一个namespace：

![image-20210714000440143](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714000440143.png)



然后，填写表单：

![image-20210714000505928](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714000505928.png)

就能在页面看到一个新的namespace：

![image-20210714000522913](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714000522913.png)

##### 给微服务配置namespace

给微服务配置namespace只能通过修改配置来实现。

例如，修改order-service的application.yml文件：

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848
      discovery:
        cluster-name: HZ
        namespace: 492a7d5d-237b-46a1-a99a-fa8e98e4b0f9 # 命名空间，填ID
```

重启order-service后，访问控制台，可以看到下面的结果：

![image-20210714000830703](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714000830703.png)

![image-20210714000837140](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714000837140.png)

此时访问order-service，因为namespace不同，会导致找不到userservice，控制台会报错：

![image-20210714000941256](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714000941256.png)

#### Nacos与Eureka的区别

Nacos的服务实例分为两种类型：

- **临时实例：如果实例宕机超过一定时间，会从服务列表剔除，默认的类型。**

- **非临时实例：如果实例宕机，不会从服务列表剔除，也可以叫永久实例。**

配置一个服务实例为永久实例：

```yaml
spring:
  cloud:
    nacos:
      discovery:
        ephemeral: false # 设置为非临时实例
```

Nacos和Eureka整体结构类似，服务注册、服务拉取、心跳等待，但是也存在一些差异：

![image-20210714001728017](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714001728017.png)

- Nacos与eureka的共同点
  - 都支持服务注册和服务拉取
  - 都支持服务提供者心跳方式做健康检测

- Nacos与Eureka的区别
  - Nacos支**持服务端主动检测提供者状态：临时实例采用心跳模式，非临时实例采用主动检测模式**
  - 临时实例心跳不正常会被剔除，非临时实例则不会被剔除
  - **Nacos支持服务列表变更的消息推送模式，服务列表更新更及时**(采用pull\push)
  - **Nacos集群默认采用AP方式，当集群中存在非临时实例时，采用CP模式；Eureka采用AP方式**

## Springcloud02

### 1.Nacos配置管理

Nacos除了可以做注册中心，同样可以做配置管理来使用。

#### 1.1.统一配置管理

当微服务部署的实例越来越多，达到数十、数百时，逐个修改微服务配置就会让人抓狂，而且很容易出错。我们需要一种**统一配置管理方案，可以集中管理所有实例的配置。**

![image-20210714164426792](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714164426792.png)

Nacos一方面可以将配置集中管理，另一方可以在配置变更时，及时通知微服务，实现配置的热更新。

##### 1.1.1.在nacos中添加配置文件

如何在nacos中管理配置呢？

![image-20210714164742924](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714164742924.png)

然后在弹出的表单中，填写配置信息：

![image-20210714164856664](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714164856664.png)

> 注意：项目的核心配置，需要热更新的配置才有放到nacos管理的必要。基本不会变更的一些配置还是保存在微服务本地比较好。

##### 1.1.2.从微服务拉取配置

微服务要拉取nacos中管理的配置，并且与本地的application.yml配置合并，才能完成项目启动。

但如果尚未读取application.yml，又如何得知nacos地址呢？

因此spring引入了一种新的配置文件：bootstrap.yaml文件，会在application.yml之前被读取，流程如下：

![img](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\L0iFYNF.png)

1）引入nacos-config依赖

首先，在user-service服务中，引入nacos-config的客户端依赖：

```xml
<!--nacos配置管理依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

2）添加bootstrap.yaml

然后，在user-service中添加一个bootstrap.yaml文件，内容如下：

```yaml
spring:
  application:
    name: userservice # 服务名称
  profiles:
    active: dev #开发环境，这里是dev 
  cloud:
    nacos:
      server-addr: localhost:8848 # Nacos地址
      config:
        file-extension: yaml # 文件后缀名
```

这里会根据spring.cloud.nacos.server-addr获取nacos地址，再根据

`${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}`作为文件id，来读取配置。

本例中，就是去读取`userservice-dev.yaml`：

![image-20210714170845901](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714170845901.png)

3）读取nacos配置

在user-service中的UserController中添加业务逻辑，读取pattern.dateformat配置：

![image-20210714170337448](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714170337448.png)

完整代码：

```java
package cn.itcast.user.web;

import cn.itcast.user.pojo.User;
import cn.itcast.user.service.UserService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

@Slf4j
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    @Value("${pattern.dateformat}")
    private String dateformat;
    
    @GetMapping("now")
    public String now(){
        return LocalDateTime.now().format(DateTimeFormatter.ofPattern(dateformat));
    }
    // ...略
}
```

在页面访问，可以看到效果：

![image-20210714170449612](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714170449612.png)

#### 1.2.配置热更新

我们最终的目的，是修改nacos中的配置后，微服务中无需重启即可让配置生效，也就是**配置热更新**。

要实现配置热更新，可以使用两种方式：

##### 1.2.1.方式一

**在@Value注入的变量所在类上添加注解@RefreshScope：**

![image-20210714171036335](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714171036335.png)

##### 1.2.2.方式二

**使用@ConfigurationProperties注解代替@Value注解。**

在user-service服务中，添加一个类，读取patterrn.dateformat属性：

```java
package cn.itcast.user.config;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@Data
@ConfigurationProperties(prefix = "pattern")
public class PatternProperties {
    private String dateformat;
}
```

在UserController中使用这个类代替@Value：

![image-20210714171316124](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714171316124.png)

完整代码：

```java
package cn.itcast.user.web;

import cn.itcast.user.config.PatternProperties;
import cn.itcast.user.pojo.User;
import cn.itcast.user.service.UserService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

@Slf4j
@RestController
@RequestMapping("/user")
public class UserController {

    @Autowired
    private UserService userService;

    @Autowired
    private PatternProperties patternProperties;

    @GetMapping("now")
    public String now(){
        return LocalDateTime.now().format(DateTimeFormatter.ofPattern(patternProperties.getDateformat()));
    }

    // 略
}
```

#### 1.3.配置共享

其实微服务启动时，会去nacos读取多个配置文件，例如：

- `[spring.application.name]-[spring.profiles.active].yaml`，例如：userservice-dev.yaml

- `[spring.application.name].yaml`，例如：userservice.yaml

**而`[spring.application.name].yaml`不包含环境，因此可以被多个环境共享。下面我们通过案例来测试配置共享**

##### 1）添加一个环境共享配置

我们在nacos中添加一个userservice.yaml文件：

![image-20210714173233650](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714173233650.png)

##### 2）在user-service中读取共享配置

在user-service服务中，修改PatternProperties类，读取新添加的属性：

![image-20210714173324231](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714173324231.png)

在user-service服务中，修改UserController，添加一个方法：

![image-20210714173721309](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714173721309.png)

##### 3）运行两个UserApplication，使用不同的profile

修改UserApplication2这个启动项，改变其profile值：

![image-20210714173538538](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714173538538.png)



![image-20210714173519963](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714173519963.png)

这样，UserApplication(8081)使用的profile是dev，UserApplication2(8082)使用的profile是test。启动UserApplication和UserApplication2。访问http://localhost:8081/user/prop，结果：

![image-20210714174313344](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714174313344.png)

访问http://localhost:8082/user/prop，结果：

![image-20210714174424818](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714174424818.png)

可以看出来，不管是dev，还是test环境，都读取到了envSharedValue这个属性的值。

##### 4）配置共享的优先级

**当nacos、服务本地同时出现相同属性时，优先级有高低之分：**

![image-20210714174623557](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714174623557.png)

#### 1.4.搭建Nacos集群

Nacos生产环境下一定要部署为集群状态，部署方式参考课前资料中的文档：

<img src="D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714174728042.png" alt="image-20210714174728042" style="zoom: 50%;" />



### 2.Feign远程调用



先来看我们以前利用RestTemplate发起远程调用的代码：

![image-20210714174814204](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714174814204.png)

存在下面的问题：

•代码可读性差，编程体验不统一

•参数复杂URL难以维护

Feign是一个声明式的http客户端，官方地址：https://github.com/OpenFeign/feign

其作用就是帮助我们优雅的实现http请求的发送，解决上面提到的问题。

![image-20210714174918088](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714174918088.png)

#### 2.1.Feign替代RestTemplate

Fegin的使用步骤如下：

##### 1）引入依赖

我们在order-service服务的pom文件中引入feign的依赖：

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

##### 2）添加注解

在order-service的启动类添加注解开启Feign的功能：

![image-20210714175102524](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714175102524.png)

##### 3）编写Feign的客户端

在order-service中新建一个接口，内容如下：

```java
package cn.itcast.order.client;

import cn.itcast.order.pojo.User;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient("userservice")
public interface UserClient {
    @GetMapping("/user/{id}")
    User findById(@PathVariable("id") Long id);
}
```

这个客户端主要是基于SringMVC的注解来声明远程调用的信息，比如：

- 服务名称：userservice
- 请求方式：GET
- 请求路径：/user/{id}
- 请求参数：Long id
- 返回值类型：User

这样，Feign就可以帮助我们发送http请求，无需自己使用RestTemplate来发送了。

##### 4）测试

修改order-service中的OrderService类中的queryOrderById方法，使用Feign客户端代替RestTemplate：

![image-20210714175415087](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714175415087.png)

是不是看起来优雅多了。

##### 5）总结

使用Feign的步骤：

① 引入依赖

② 添加@EnableFeignClients注解

③ 编写FeignClient接口

④ 使用FeignClient中定义的方法代替RestTemplate

#### 2.2.自定义配置

Feign可以支持很多的自定义配置，如下表所示：

| 类型                   | 作用             | 说明                                                   |
| ---------------------- | ---------------- | ------------------------------------------------------ |
| **feign.Logger.Level** | 修改日志级别     | 包含四种不同的级别**：NONE、BASIC、HEADERS、FULL**     |
| feign.codec.Decoder    | 响应结果的解析器 | http远程调用的结果做解析，例如解析json字符串为java对象 |
| feign.codec.Encoder    | 请求参数编码     | 将请求参数编码，便于通过http请求发送                   |
| feign. Contract        | 支持的注解格式   | 默认是SpringMVC的注解                                  |
| feign. Retryer         | 失败重试机制     | 请求失败的重试机制，默认是没有，不过会使用Ribbon的重试 |

一般情况下，默认值就能满足我们使用，如果要自定义时，只需要创建自定义的@Bean覆盖默认Bean即可

下面以日志为例来演示如何自定义配置。

##### 2.2.1.配置文件方式

基于配置文件修改feign的日志级别可以针对单个服务：

```yaml
feign:  
  client:
    config: 
      userservice: # 针对某个微服务的配置
        loggerLevel: FULL #  日志级别 
```

也可以针对所有服务：

```yaml
feign:  
  client:
    config: 
      default: # 这里用default就是全局配置，如果是写服务名称，则是针对某个微服务的配置
        loggerLevel: FULL #  日志级别 
```

而日志的级别分为四种：

- NONE：不记录任何日志信息，这是默认值。
- **BASIC：仅记录请求的方法，URL以及响应状态码和执行时间**
- HEADERS：在BASIC的基础上，额外记录了请求和响应的头信息
- FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据。

##### 2.2.2.Java代码方式

也可以基于Java代码来修改日志级别，先声明一个类，然后声明一个Logger.Level的对象：

```java
public class DefaultFeignConfiguration  {
    @Bean
    public Logger.Level feignLogLevel(){
        return Logger.Level.BASIC; // 日志级别为BASIC
    }
}
```

如果要**全局生效**，将其放到启动类的@EnableFeignClients这个注解中：

```java
@EnableFeignClients(defaultConfiguration = DefaultFeignConfiguration .class) 
```

如果是**局部生效**，则把它放到对应的@FeignClient这个注解中：

```java
@FeignClient(value = "userservice", configuration = DefaultFeignConfiguration .class) 
```

#### 2.3.Feign使用优化

Feign底层发起http请求，依赖于其它的框架。其底层客户端实现包括：

•URLConnection：默认实现，不支持连接池

•Apache HttpClient ：支持连接池

•OKHttp：支持连接池

**因此提高Feign的性能主要手段就是使用连接池代替默认的URLConnection。**这里我们**用Apache的HttpClient来演示。**

1）引入依赖

在order-service的pom文件中引入Apache的HttpClient依赖：

```xml
<!--httpClient的依赖 -->
<dependency>
    <groupId>io.github.openfeign</groupId>
    <artifactId>feign-httpclient</artifactId>
</dependency>
```

2）配置连接池

在order-service的application.yml中添加配置：

```yaml
feign:
  client:
    config:
      default: # default全局的配置
        loggerLevel: BASIC # 日志级别，BASIC就是基本的请求和响应信息
  httpclient:
    enabled: true # 开启feign对HttpClient的支持
    max-connections: 200 # 最大的连接数
    max-connections-per-route: 50 # 每个路径的最大连接数
```

接下来，在FeignClientFactoryBean中的loadBalance方法中打断点：

![image-20210714185925910](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714185925910.png)

Debug方式启动order-service服务，可以看到这里的client，底层就是Apache HttpClient：

![image-20210714190041542](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714190041542.png)

总结，Feign的优化：

**1.日志级别尽量用basic**

**2.使用HttpClient或OKHttp代替URLConnection**

①  引入feign-httpClient依赖

②  配置文件开启httpClient功能，设置连接池参数

#### 2.4.最佳实践

所谓最近实践，就是使用过程中总结的经验，最好的一种使用方式。自习观察可以发现，Feign的客户端与服务提供者的controller代码非常相似：

feign客户端：

![image-20210714190542730](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714190542730.png)

UserController：

![image-20210714190528450](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714190528450.png)

有没有一种办法简化这种重复的代码编写呢？

##### 2.4.1.继承方式

一样的代码可以通过继承来共享：

1）定义一个API接口，利用定义方法，并基于SpringMVC注解做声明。

2）Feign客户端和Controller都集成改接口

![image-20210714190640857](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714190640857.png)

优点：

- 简单
- 实现了代码共享

缺点：

- 服务提供方、服务消费方紧耦合

- 参数列表中的注解映射并不会继承，因此Controller中必须再次声明方法、参数列表、注解 

##### 2.4.2.抽取方式

将Feign的Client抽取为独立模块，并且把接口有关的POJO、默认的Feign配置都放到这个模块中，提供给所有消费者使用。例如，将UserClient、User、Feign的默认配置都抽取到一个feign-api包中，所有微服务引用该依赖包，即可直接使用。

![image-20210714214041796](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714214041796.png)

##### 2.4.3.实现基于抽取的最佳实践

###### 1）抽取

首先创建一个module，命名为feign-api：

![image-20210714204557771](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714204557771.png)

项目结构：

![image-20210714204656214](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714204656214.png)

在feign-api中然后引入feign的starter依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

然后，order-service中编写的UserClient、User、DefaultFeignConfiguration都复制到feign-api项目中

![image-20210714205221970](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714205221970.png)

###### 2）在order-service中使用feign-api

首先，删除order-service中的UserClient、User、DefaultFeignConfiguration等类或接口。

在order-service的om文件中中引入feign-api的依赖：

```xml
<dependency>
    <groupId>cn.itcast.demo</groupId>
    <artifactId>feign-api</artifactId>
    <version>1.0</version>
</dependency>
```

修改order-service中的所有与上述三个组件有关的导包部分，改成导入feign-api中的包

###### 3）重启

重启后，发现服务报错了：

![image-20210714205623048](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714205623048.png)

这是因为UserClient现在在cn.itcast.feign.clients包下，而order-service的@EnableFeignClients注解是在cn.itcast.order包下，不在同一个包，无法扫描到UserClient。

###### 4）解决扫描包问题

方式一：指定Feign应该扫描的包：

```java
@EnableFeignClients(basePackages = "cn.itcast.feign.clients")
```

方式二：指定需要加载的Client接口：

```java
@EnableFeignClients(clients = {UserClient.class})
```

### 3.Gateway服务网关

**Spring Cloud Gateway 是 Spring Cloud 的一个全新项目**，该项目是基于 Spring 5.0，Spring Boot 2.0 和 Project Reactor **等响应式编程和事件流技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。**

#### 3.1.为什么需要网关

Gateway网关是我们服务的守门神，所有微服务的统一入口。

网关的**核心功能特性**：

- 请求路由
- 权限控制
- 限流

架构图：

![image-20210714210131152](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714210131152.png)



**权限控制**：网关作为微服务入口，需要校验用户是是否有请求资格，如果没有则进行拦截。

**路由和负载均衡**：**一切请求都必须先经过gateway，但网关不处理业务，而是根据某种规则，把请求转发到某个微服务，这个过程叫做路由。**当然路由的目标服务有多个时，还需要做负载均衡。

**限流**：当请求流量过高时，在网关中按照下流的微服务能够接受的速度来放行请求，避免服务压力过大。

在SpringCloud中网关的实现包括两种：

- gateway
- zuul

Zuul是基于Servlet的实现，属于阻塞式编程。**而SpringCloudGateway则是基于Spring5中提供的WebFlux，属于响应式编程的实现，具备更好的性能。**

#### 3.2.gateway快速入门

下面，我们就演示下网关的基本路由功能。基本步骤如下：

1. 创建SpringBoot工程gateway，引入网关依赖
2. 编写启动类
3. 编写基础配置和路由规则
4. 启动网关服务进行测试

##### 1）创建gateway服务，引入依赖

创建服务：

![image-20210714210919458](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714210919458.png)

引入依赖：

```xml
<!--网关-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<!--nacos服务发现依赖-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

##### 2）编写启动类

```java
package cn.itcast.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class GatewayApplication {

	public static void main(String[] args) {
		SpringApplication.run(GatewayApplication.class, args);
	}
}
```

##### 3）编写基础配置和路由规则

创建application.yml文件，内容如下：

```yaml
server:
  port: 10010 # 网关端口
spring:
  application:
    name: gateway # 服务名称
  cloud:
    nacos:
      server-addr: localhost:8848 # nacos地址
    gateway:
      routes: # 网关路由配置
        - id: user-service # 路由id，自定义，只要唯一即可
          # uri: http://127.0.0.1:8081 # 路由的目标地址 http就是固定地址
          uri: lb://userservice # 路由的目标地址 lb就是负载均衡，后面跟服务名称
          predicates: # 路由断言，也就是判断请求是否符合路由规则的条件
            - Path=/user/** # 这个是按照路径匹配，只要以/user/开头就符合要求
```

我们将符合`Path` 规则的一切请求，都代理到 `uri`参数指定的地址。本例中，我们将 `/user/**`开头的请求，代理到`lb://userservice`，lb是负载均衡，根据服务名拉取服务列表，实现负载均衡。

##### 4）重启测试

重启网关，访问http://localhost:10010/user/1时，符合`/user/**`规则，请求转发到uri：http://userservice/user/1，得到了结果：

![image-20210714211908341](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714211908341.png)

##### 5）网关路由的流程图

整个访问的流程如下：

![image-20210714211742956](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714211742956.png)

总结：

网关搭建步骤：

1. 创建项目，引入nacos服务发现和gateway依赖

2. 配置application.yml，包括服务基本信息、nacos地址、路由

路由配置包括：

1. 路由id：路由的唯一标示

2. 路由目标（uri）：路由的目标地址，http代表固定地址，lb代表根据服务名负载均衡

3. 路由断言（predicates）：判断路由的规则，

4. 路由过滤器（filters）：对请求或响应做处理

接下来，就重点来学习路由断言和路由过滤器的详细知识

#### 3.3.断言工厂

我们在配置文件中写的断言规**则只是字符串，这些字符串会被Predicate Factory读取并处理，转变为路由判断的条件**

例如Path=/user/**是按照路径匹配，这个规则是由`org.springframework.cloud.gateway.handler.predicate.PathRoutePredicateFactory`类来

处理的，像这样的断言工厂在SpringCloudGateway还有十几个:

| **名称**   | **说明**                       | **示例**                                                     |
| ---------- | ------------------------------ | ------------------------------------------------------------ |
| After      | 是某个时间点后的请求           | -  After=2037-01-20T17:42:47.789-07:00[America/Denver]       |
| Before     | 是某个时间点之前的请求         | -  Before=2031-04-13T15:14:47.433+08:00[Asia/Shanghai]       |
| Between    | 是某两个时间点之前的请求       | -  Between=2037-01-20T17:42:47.789-07:00[America/Denver],  2037-01-21T17:42:47.789-07:00[America/Denver] |
| Cookie     | 请求必须包含某些cookie         | - Cookie=chocolate, ch.p                                     |
| Header     | 请求必须包含某些header         | - Header=X-Request-Id, \d+                                   |
| Host       | 请求必须是访问某个host（域名） | -  Host=**.somehost.org,**.anotherhost.org                   |
| Method     | 请求方式必须是指定方式         | - Method=GET,POST                                            |
| **Path**   | **请求路径必须符合指定规则**   | **- Path=/red/{segment},/blue/****                           |
| Query      | 请求参数必须包含指定参数       | - Query=name, Jack或者-  Query=name                          |
| RemoteAddr | 请求者的ip必须是指定范围       | - RemoteAddr=192.168.1.1/24                                  |
| Weight     | 权重处理                       |                                                              |

我们只需要掌握Path这种路由工程就可以了。

#### 3.4.过滤器工厂

**GatewayFilter是网关中提供的一种过滤器，可以对进入网关的请求和微服务返回的响应做处理：**

![image-20210714212312871](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714212312871.png)

##### 3.4.1.路由过滤器的种类

Spring提供了31种不同的路由过滤器工厂。例如：

| **名称**                 | **说明**                         |
| ------------------------ | -------------------------------- |
| **AddRequestHeader**     | **给当前请求添加一个请求头**     |
| **RemoveRequestHeader**  | **移除请求中的一个请求头**       |
| **AddResponseHeader**    | **给响应结果中添加一个响应头**   |
| **RemoveResponseHeader** | **从响应结果中移除有一个响应头** |
| **RequestRateLimiter**   | **限制请求的流量**               |

##### 3.4.2.请求头过滤器

下面我们以AddRequestHeader 为例来讲解。

> **需求**：给所有进入userservice的请求添加一个请求头：Truth=itcast is freaking awesome!

只需要修改gateway服务的application.yml文件，添加路由过滤即可：

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: user-service 
        uri: lb://userservice 
        predicates: 
        - Path=/user/** 
        filters: # 过滤器
        - AddRequestHeader=Truth, Itcast is freaking awesome! # 添加请求头
```

当前过滤器写在userservice路由下，因此仅仅对访问userservice的请求有效。

##### 3.4.3.默认过滤器

如果**要对所有的路由都生效，则可以将过滤器工厂写到default下。**格式如下：

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: user-service 
        uri: lb://userservice 
        predicates: 
        - Path=/user/**
      default-filters: # 默认过滤项
      - AddRequestHeader=Truth, Itcast is freaking awesome! 
```

##### 3.4.4.总结

过滤器的作用是什么？

① **对路由的请求或响应做加工处理，**比如添加请求头

② 配置**在路由下的过滤器只对当前路由的请求生效**

defaultFilters的作用是什么？

① 对**所有路由都生效**的过滤器

#### 3.5.全局过滤器

上一节学习的过滤器，网关提供了31种，但每一种过滤器的作用都是固定的。**如果我们希望拦截请求，做自己的业务逻辑则没办法实现。**

##### 3.5.1.全局过滤器作用

全局过滤器的作用也是处理一切进入网关的请求和微服务响应，与GatewayFilter的作用一样。区别在于GatewayFilter通过配置定义，处理逻辑是固定**的；而GlobalFilter的逻辑需要自己写代码实现。**

定义方式是实现GlobalFilter接口。

```java
public interface GlobalFilter {
    /**
     *  处理当前请求，有必要的话通过{@link GatewayFilterChain}将请求交给下一个过滤器处理
     *
     * @param exchange 请求上下文，里面可以获取Request、Response等信息
     * @param chain 用来把请求委托给下一个过滤器 
     * @return {@code Mono<Void>} 返回标示当前过滤器业务结束
     */
    Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain);
}
```

在filter中编写自定义逻辑，可以实现下列功能：

- 登录状态判断
- 权限校验
- 请求限流等

##### 3.5.2.自定义全局过滤器

需求：定义全局过滤器，拦截请求，判断请求的参数是否满足下面条件：

- 参数中是否有authorization，

- authorization参数值是否为admin

如果同时满足则放行，否则拦截

实现：

在gateway中定义一个过滤器：

```java
package cn.itcast.gateway.filters;

import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.annotation.Order;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Order(-1)
@Component
public class AuthorizeFilter implements GlobalFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        // 1.获取请求参数
        MultiValueMap<String, String> params = exchange.getRequest().getQueryParams();
        // 2.获取authorization参数
        String auth = params.getFirst("authorization");
        // 3.校验
        if ("admin".equals(auth)) {
            // 放行
            return chain.filter(exchange);
        }
        // 4.拦截
        // 4.1.禁止访问，设置状态码
        exchange.getResponse().setStatusCode(HttpStatus.FORBIDDEN);
        // 4.2.结束处理
        return exchange.getResponse().setComplete();
    }
}
```

##### 3.5.3.过滤器执行顺序

请求进入网关会碰到三类过滤器：**当前路由的过滤器、DefaultFilter、GlobalFilter**

**请求路由后**，会将**当前路由过滤器和DefaultFilter、GlobalFilter，合并到一个过滤器链（集合）中，排序后依次执行每个过滤器：**

![image-20210714214228409](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714214228409.png)

排序的规则是什么呢？

- **每一个过滤器都必须指定一个int类型的order值，order值越小，优先级越高，执行顺序越靠前。**
- GlobalFilter通过实现Ordered接口，或者添加@Order注解来指定order值，由我们自己指定
- **路由过滤器和defaultFilter的order由Spring指定，默认是按照声明顺序从1递增。**
- 当过滤器的order值**一样时，会按照 defaultFilter -> 路由过滤器 -> GlobalFilter的顺序执行。**

详细内容，可以查看源码：

`org.springframework.cloud.gateway.route.RouteDefinitionRouteLocator#getFilters()`方法是先加载defaultFilters，然后再加载某个route的filters，然后合并。

`org.springframework.cloud.gateway.handler.FilteringWebHandler#handle()`方法会加载全局过滤器，与前面的过滤器合并后根据order排序，组织过滤器链

#### 3.6.跨域问题

##### 3.6.1.什么是跨域问题

跨域：**域名不一致就是跨域，主要包括：**

- **域名不同**： www.taobao.com 和 www.taobao.org 和 www.jd.com 和 miaosha.jd.com

- **域名相同，端口不同**：localhost:8080和localhost8081

跨域问题：**浏览器****禁止请求的发起者与服务端发生跨域ajax请求，请求被浏览器拦截的问题**

解决方案：CORS，这个以前应该学习过，这里不再赘述了。不知道的小伙伴可以查看https://www.ruanyifeng.com/blog/2016/04/cors.html

##### 3.6.2.模拟跨域问题

找到课前资料的页面文件：

![image-20210714215713563](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714215713563.png)

放入tomcat或者nginx这样的web服务器中，启动并访问。

可以在浏览器控制台看到下面的错误：

![image-20210714215832675](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210714215832675.png)

从localhost:8090访问localhost:10010，端口不同，显然是跨域的请求。

##### 3.6.3.解决跨域问题

在gateway服务的application.yml文件中，添加下面的配置：

```yaml
spring:
  cloud:
    gateway:
      # 。。。
      globalcors: # 全局的跨域处理
        add-to-simple-url-handler-mapping: true # 解决options请求被拦截问题
        corsConfigurations:
          '[/**]':
            allowedOrigins: # 允许哪些网站的跨域请求 
              - "http://localhost:8090"
            allowedMethods: # 允许的跨域ajax的请求方式
              - "GET"
              - "POST"
              - "DELETE"
              - "PUT"
              - "OPTIONS"
            allowedHeaders: "*" # 允许在请求中携带的头信息
            allowCredentials: true # 是否允许携带cookie
            maxAge: 360000 # 这次跨域检测的有效期
```

## Springcloud04

### MQ简介

##### 1.1.1.同步通讯

我们之前学习**的Feign调用就属于同步方式，虽然调用可以实时得到结果，但存在下面的问题：**

![image-20210717162004285](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717162004285.png)

总结：

同步调用的优点：

- 时效性较强，可以立即得到结果

同步调用的问题：

- 耦合度高
- 性能和吞吐能力下降
- 有额外的资源消耗
- 有级联失败问题

##### 1.1.2.异步通讯

异步调用则可以避免上述问题：

我们以购买商品为例，用户支付后需要调用订单服务完成订单状态修改，调用物流服务，从仓库分配响应的库存并准备发货。在事件模式中，支付服务是事件发布者（publisher），在支付完成后只需要发布一个支付成功的事件（event），事件中带上订单id。订单服务和物流服务是事件订阅者（Consumer），订阅支付成功的事件，监听到事件后完成自己业务即可。

为了解除事件发布者与订阅者之间的耦合，**两者并不是直接通信，而是有一个中间人（Broker）**。**发布者发布事件到Broker，不关心谁来订阅事件。订阅者从Broker订阅事件，不关心谁发来的消息。**

![image-20210422095356088](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210422095356088.png)

Broker 是一个像数据总线一样的东西，所有的服务要接收数据和发送数据都发到这个总线上，这个总线就像协议一样，让服务间的通讯变得标准和可控。

好处：

- 吞吐量提升：无需等待订阅者处理完成，响应更快速

- 故障隔离：服务没有直接调用，不存在级联失败问题
- 调用间没有阻塞，不会造成无效的资源占用
- 耦合度极低，每个服务都可以灵活插拔，可替换
- 流量削峰：不管发布事件的流量波动多大，都由Broker接收，订阅者可以按照自己的速度去处理事件

缺点：

- 架构复杂了，业务没有明显的流程线，不好管理
- 需要依赖于Broker的可靠、安全、性能

好在现在开源软件或云平台上 Broker 的软件是非常成熟的，比较常见的一种就是我们今天要学习的MQ技术。

#### 1.2.技术对比

MQ，**中文是消息队列（MessageQueue），**字面来看就是存放消息的队列。**也就是事件驱动架构中的Broker。**

比较常见的MQ实现：

- ActiveMQ
- RabbitMQ
- RocketMQ
- Kafka

几种常见MQ的对比：

|            | **RabbitMQ**            | **ActiveMQ**                   | **RocketMQ** | **Kafka**  |
| ---------- | ----------------------- | ------------------------------ | ------------ | ---------- |
| 公司/社区  | Rabbit                  | Apache                         | 阿里         | Apache     |
| 开发语言   | Erlang                  | Java                           | Java         | Scala&Java |
| 协议支持   | AMQP，XMPP，SMTP，STOMP | OpenWire,STOMP，REST,XMPP,AMQP | 自定义协议   | 自定义协议 |
| 可用性     | 高                      | 一般                           | 高           | 高         |
| 单机吞吐量 | 一般                    | 差                             | 高           | 非常高     |
| 消息延迟   | 微秒级                  | 毫秒级                         | 毫秒级       | 毫秒以内   |
| 消息可靠性 | 高                      | 一般                           | 高           | 一般       |

追求可用性：Kafka、 RocketMQ 、RabbitMQ

追求可靠性：RabbitMQ、RocketMQ

追求吞吐能力：RocketMQ、Kafka

追求消息低延迟：RabbitMQ、Kafka

### RabbitMQ快速入门

在RabbitMQ中，Connection（连接）和Channel（通道）是两个核心概念，它们之间有以下区别：

1. Connection（连接）：连接是客户端与RabbitMQ Broker（服务器）之间的TCP连接。通常情况下，一个应用程序只需与RabbitMQ建立一个连接。连接负责在客户端和服务器之间建立通信通道，并且在必要时重新建立连接。连接是较重量级的对象，建立和销毁连接都是较为昂贵的操作，因此在应用程序中应该尽可能地复用连接。
2. Channel（通道）：通道是建立在连接之上的逻辑通信信道。每个连接可以拥有多个通道，通道是轻量级的对象。通道负责发送和接收消息，以及定义和维护队列、交换机等实体的操作。使用通道可以在单个TCP连接上复用资源，从而提高了消息传输的效率。通常情况下，一个应用程序会在一个连接上创建多个通道，以实现并发发送和接收消息。

总的来说，Connection负责建立物理连接，而Channel负责在连接上进行逻辑通信。

MQ的基本结构：

![image-20210717162752376](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717162752376.png)



RabbitMQ中的一些角色：

- publisher：生产者
- consumer：消费者
- exchange：交换机，负责消息路由
- queue：队列，存储消息
- virtualHost：虚拟主机，隔离不同租户的exchange、queue、消息的隔离

#### 2.2.RabbitMQ消息模型

RabbitMQ官方提供了5个不同的Demo示例，对应了不同的消息模型：

- basicqueue
- workqueue
- publish\subscribe

![image-20210717163332646](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717163332646.png)

#### 2.3.导入Demo工程

课前资料提供了一个Demo工程，mq-demo:

![image-20210717163253264](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717163253264.png)

导入后可以看到结构如下：

![image-20210717163604330](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717163604330.png)

包括三部分：

- mq-demo：父工程，管理项目依赖
- publisher：消息的发送者
- consumer：消息的消费者

#### 2.4.入门案例

简单队列模式的模型图：

 ![image-20210717163434647](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717163434647.png)

官方的HelloWorld是基于最基础的消息队列模型来实现的，只包括三个角色：

- publisher：消息发布者，将消息发送到队列queue
- queue：消息队列，负责接受并缓存消息
- consumer：订阅队列，处理队列中的消息

##### 2.4.1.publisher实现

思路：

- 建立连接
- 创建Channel
- 声明队列
- 发送消息
- 关闭连接和channel

代码实现：

```java
package cn.itcast.mq.helloworld;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import org.junit.Test;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class PublisherTest {
    @Test
    public void testSendMessage() throws IOException, TimeoutException {
        // 1.建立连接
        ConnectionFactory factory = new ConnectionFactory();
        // 1.1.设置连接参数，分别是：主机名、端口号、vhost、用户名、密码
        factory.setHost("192.168.150.101");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        factory.setUsername("itcast");
        factory.setPassword("123321");
        // 1.2.建立连接
        Connection connection = factory.newConnection();

        // 2.创建通道Channel
        Channel channel = connection.createChannel();

        // 3.创建队列
        String queueName = "simple.queue";
        channel.queueDeclare(queueName, false, false, false, null);

        // 4.发送消息
        String message = "hello, rabbitmq!";
        channel.basicPublish("", queueName, null, message.getBytes());
        System.out.println("发送消息成功：【" + message + "】");

        // 5.关闭通道和连接
        channel.close();
        connection.close();

    }
}
```

##### 2.4.2.consumer实现

代码思路：

- 建立连接
- 创建Channel
- 声明队列
- 订阅消息

代码实现：

```java
package cn.itcast.mq.helloworld;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class ConsumerTest {

    public static void main(String[] args) throws IOException, TimeoutException {
        // 1.建立连接
        ConnectionFactory factory = new ConnectionFactory();
        // 1.1.设置连接参数，分别是：主机名、端口号、vhost、用户名、密码
        factory.setHost("192.168.150.101");
        factory.setPort(5672);
        factory.setVirtualHost("/");
        factory.setUsername("itcast");
        factory.setPassword("123321");
        // 1.2.建立连接
        Connection connection = factory.newConnection();

        // 2.创建通道Channel
        Channel channel = connection.createChannel();

        // 3.创建队列
        String queueName = "simple.queue";
        channel.queueDeclare(queueName, false, false, false, null);

        // 4.订阅消息
        channel.basicConsume(queueName, true, new DefaultConsumer(channel){
            @Override
            public void handleDelivery(String consumerTag, Envelope envelope,
                                       AMQP.BasicProperties properties, byte[] body) throws IOException {
                // 5.处理消息
                String message = new String(body);
                System.out.println("接收到消息：【" + message + "】");
            }
        });
        System.out.println("等待接收消息。。。。");
    }
}
```

#### 2.5.总结

基本消息队列的消息发送流程：

1. 建立connection

2. 创建channel

3. 利用channel声明队列

4. 利用channel向队列发送消息

基本消息队列的消息接收流程：

1. 建立connection

2. 创建channel

3. 利用channel声明队列

4. 定义consumer的消费行为handleDelivery()

5. 利用channel将消费者与队列绑定

### SpringAMQP

**Advanced Meesage Queuing Protocol**

SpringAMQP是基于RabbitMQ封装的一套模板，并且还利用SpringBoot对其实现了自动装配，使用起来非常方便。

<img src="D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717164024967.png" alt="image-20210717164024967" style="zoom: 50%;" />

<img src="D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717164038678.png" alt="image-20210717164038678" style="zoom:50%;" />

SpringAMQP提供了三个功能：

- **自动声明队列、交换机及其绑定关系**
- 基于注解的**监听器模式，异步接收消息**
- 封装**了RabbitTemplate工具，用于发送消息** 

#### 3.1.Basic Queue 简单队列模型

在父工程mq-demo中引入依赖

```xml
<!--AMQP依赖，包含RabbitMQ-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

##### 3.1.1.消息发送

首先配置MQ地址，在publisher服务的application.yml中添加配置：

```yaml
spring:
  rabbitmq:
    host: 192.168.150.101 # 主机名
    port: 5672 # 端口
    virtual-host: / # 虚拟主机
    username: itcast # 用户名
    password: 123321 # 密码
```

然后在publisher服务中编写测试类SpringAmqpTest，并利用RabbitTemplate实现消息发送：

```java
package cn.itcast.mq.spring;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringAmqpTest {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    public void testSimpleQueue() {
        // 队列名称
        String queueName = "simple.queue";
        // 消息
        String message = "hello, spring amqp!";
        // 发送消息
        rabbitTemplate.convertAndSend(queueName, message);
    }
}
```

##### 3.1.2.消息接收

首先配置MQ地址，在consumer服务的application.yml中添加配置：

```yaml
spring:
  rabbitmq:
    host: 192.168.150.101 # 主机名
    port: 5672 # 端口
    virtual-host: / # 虚拟主机
    username: itcast # 用户名
    password: 123321 # 密码
```

然后在consumer服务的`cn.itcast.mq.listener`包中新建一个类SpringRabbitListener，代码如下：

```java
package cn.itcast.mq.listener;

import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

@Component
public class SpringRabbitListener {

    @RabbitListener(queues = "simple.queue")
    public void listenSimpleQueueMessage(String msg) throws InterruptedException {
        System.out.println("spring 消费者接收到消息：【" + msg + "】");
    }
}
```



##### 3.1.3.测试

启动consumer服务，然后在publisher服务中运行测试代码，发送MQ消息

#### 3.2.WorkQueue

Work queues，也被称为（Task queues），任务模型。简单来说就是**让多个消费者绑定到一个队列，共同消费队列中的消息**。

![image-20210717164238910](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717164238910.png)

当消息处理比较耗时的时候，可能生产消息的速度会远远大于消息的消费速度。长此以往，消息就会堆积越来越多，无法及时处理。**此时就可以使用work 模型，多个消费者共同处理消息处理，速度就能大大提高了。**

##### 3.2.1.消息发送

这次我们循环发送，模拟大量消息堆积现象。

在publisher服务中的SpringAmqpTest类中添加一个测试方法：

```java
/**
     * workQueue
     * 向队列中不停发送消息，模拟消息堆积。
     */
@Test
public void testWorkQueue() throws InterruptedException {
    // 队列名称
    String queueName = "simple.queue";
    // 消息
    String message = "hello, message_";
    for (int i = 0; i < 50; i++) {
        // 发送消息
        rabbitTemplate.convertAndSend(queueName, message + i);
        Thread.sleep(20);
    }
}
```

##### 3.2.2.消息接收

要模拟多个消费者绑定同一个队列，我们在consumer服务的SpringRabbitListener中添加2个新的方法：

```java
@RabbitListener(queues = "simple.queue")
public void listenWorkQueue1(String msg) throws InterruptedException {
    System.out.println("消费者1接收到消息：【" + msg + "】" + LocalTime.now());
    Thread.sleep(20);
}

@RabbitListener(queues = "simple.queue")
public void listenWorkQueue2(String msg) throws InterruptedException {
    System.err.println("消费者2........接收到消息：【" + msg + "】" + LocalTime.now());
    Thread.sleep(200);
}
```

注意到这个消费者sleep了1000秒，模拟任务耗时。

##### 3.2.3.测试

启动ConsumerApplication后，在执行publisher服务中刚刚编写的发送测试方法testWorkQueue。可以看到消费者1很快完成了自己的25条消息。消费者2却在缓慢的处理自己的25条消息。也就是说消息是**平均分配给每个消费者，并没有考虑到消费者的处理能力。**这样显然是有问题的。

##### 3.2.4.能者多劳

在spring中有一个简单的配置，可以解决这个问题。我们修改consumer服务的application.yml文件，添加配置：

```yaml
spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1 # 每次只能获取一条消息，处理完成才能获取下一个消息
```

##### 3.2.5.总结

Work模型的使用：

- 多个消费者绑定到一个队列，同一条消息只会被一个消费者处理
- **通过设置prefetch来控制消费者预取的消息数量**

#### 3.3.发布/订阅

发布订阅的模型如图：

![image-20210717165309625](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717165309625.png)



可以看到，在订阅模型中，多了一个exchange角色，而且过程略有变化：

- Publisher：生产者，也就是要发送消息的程序，**但是不再发送到队列中，而是发给X（交换机）**
- Exchange：交换机，图中的X。一方面，接收生产者发送的消息。另一方面，知道如何处理消息**，例如递交给某个特别队列、递交给所有队列、或是将消息丢弃。到底如何操作，取决于Exchange的类型。Exchange有以下3种类型：**
  - **Fanout：广播，将消息交给所有绑定到交换机的队列**
  - **Direct：定向，把消息交给符合指定routing key 的队列**
  - **Topic：通配符，把消息交给符合routing pattern（路由模式） 的队列**
- Consumer：消费者，与以前一样，订阅队列，没有变化
- Queue：消息队列也与以前一样，接收消息、缓存消息。

**Exchange（交换机）只负责转发消息，不具备存储消息的能力**，**因此如果没有任何队列与Exchange绑定，或者没有符合路由规则的队列，那么消息会丢失！**

#### 3.4.Fanout

Fanout，英文翻译是扇出，我觉得在MQ中叫广播更合适。

![image-20210717165438225](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717165438225.png)

在广播模式下，消息发送流程是这样的：

- 1）  可以有多个队列
- 2）  **每个队列都要绑定到Exchange（交换机）**
- 3）  生产者发送的消息，只能发送到交换机，交换机来决定要发给哪个队列，生产者无法决定
- **4）  交换机把消息发送给绑定过的所有队列**
- **5）  订阅队列的消费者都能拿到消息**

我们的计划是这样的：

- 创建一个交换机 itcast.fanout，类型是Fanout
- 创建两个队列fanout.queue1和fanout.queue2，绑定到交换机itcast.fanout

![image-20210717165509466](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717165509466.png)

##### 3.4.1.声明队列和交换机

Spring提供了一个接口Exchange，来表示所有不同类型的交换机：

![image-20210717165552676](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717165552676.png)

在consumer中创建一个类，声明队列和交换机：

```java
package cn.itcast.mq.config;

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.BindingBuilder;
import org.springframework.amqp.core.FanoutExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FanoutConfig {
    /**
     * 声明交换机
     * @return Fanout类型交换机
     */
    @Bean
    public FanoutExchange fanoutExchange(){
        return new FanoutExchange("itcast.fanout");
    }

    /**
     * 第1个队列
     */
    @Bean
    public Queue fanoutQueue1(){
        return new Queue("fanout.queue1");
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue1(Queue fanoutQueue1, FanoutExchange fanoutExchange){
        return BindingBuilder.bind(fanoutQueue1).to(fanoutExchange);
    }

    /**
     * 第2个队列
     */
    @Bean
    public Queue fanoutQueue2(){
        return new Queue("fanout.queue2");
    }

    /**
     * 绑定队列和交换机
     */
    @Bean
    public Binding bindingQueue2(Queue fanoutQueue2, FanoutExchange fanoutExchange){
        return BindingBuilder.bind(fanoutQueue2).to(fanoutExchange);
    }
}
```

##### 3.4.2.消息发送

在publisher服务的SpringAmqpTest类中添加测试方法：

```java
@Test
public void testFanoutExchange() {
    // 队列名称
    String exchangeName = "itcast.fanout";
    // 消息
    String message = "hello, everyone!";
    rabbitTemplate.convertAndSend(exchangeName, "", message);
}
```

##### 3.4.3.消息接收

在consumer服务的SpringRabbitListener中添加两个方法，作为消费者：

```java
@RabbitListener(queues = "fanout.queue1")
public void listenFanoutQueue1(String msg) {
    System.out.println("消费者1接收到Fanout消息：【" + msg + "】");
}

@RabbitListener(queues = "fanout.queue2")
public void listenFanoutQueue2(String msg) {
    System.out.println("消费者2接收到Fanout消息：【" + msg + "】");
}
```

##### 3.4.4.总结

交换机的作用是什么？

- 接收publisher发送的消息
- **将消息按照规则路由到与之绑定的队列**
- 不能缓存消息，路由失败，消息丢失
- FanoutExchange的会将消息路由到每个绑定的队列

声明队列、交换机、绑定关系的Bean是什么？

- Queue
- FanoutExchange
- Binding

#### 3.5.Direct

在Fanout模式中，一条消息，会被所有订阅的队列都消费。但是，在某些场景下，我们希望不同的消息被不同的队列消费。**这时就要用到Direct类型的Exchange。**

![image-20210717170041447](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717170041447.png)

 在Direct模型下：

- **队列与交换机的绑定，不能是任意绑定了，而是要指定一个`RoutingKey`（路由key）**
- **消息的发送方在 向 Exchange发送消息时，也必须指定消息的 `RoutingKey`。**
- Exchange不再把消息交给每一个绑定的队列，而是根据消息的`Routing Key`进行判断，只有队列的`Routingkey`与消息的 `Routing key`完全一致，才会接收到消息

**案例需求如下**：

1. **利用@RabbitListener声明Exchange、Queue、RoutingKey**

2. 在consumer服务中，编写两个消费者方法，分别监听direct.queue1和direct.queue2

3. 在publisher中编写测试方法，向itcast. direct发送消息

![image-20210717170223317](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717170223317.png)

##### 3.5.1.基于注解声明队列和交换机

基于@Bean的方式声明队列和交换机比较麻烦，Spring还提供了基于注解方式来声明。

在consumer的SpringRabbitListener中添加两个消费者，同时基于注解来声明队列和交换机：

```java
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "direct.queue1"),
    exchange = @Exchange(name = "itcast.direct", type = ExchangeTypes.DIRECT),
    key = {"red", "blue"}
))
public void listenDirectQueue1(String msg){
    System.out.println("消费者接收到direct.queue1的消息：【" + msg + "】");
}

@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "direct.queue2"),
    exchange = @Exchange(name = "itcast.direct", type = ExchangeTypes.DIRECT),
    key = {"red", "yellow"}
))
public void listenDirectQueue2(String msg){
    System.out.println("消费者接收到direct.queue2的消息：【" + msg + "】");
}
```

##### 3.5.2.消息发送

在publisher服务的SpringAmqpTest类中添加测试方法：

```java
@Test
public void testSendDirectExchange() {
    // 交换机名称
    String exchangeName = "itcast.direct";
    // 消息
    String message = "红色警报！日本乱排核废水，导致海洋生物变异，惊现哥斯拉！";
    // 发送消息
    rabbitTemplate.convertAndSend(exchangeName, "red", message);
}
```

##### 3.5.3.总结

描述下Direct交换机与Fanout交换机的差异？

- Fanout交换机将消息路由给每一个与之绑定的队列
- Direct交换机根据RoutingKey判断路由给哪个队列
- 如果多个队列具有相同的RoutingKey，则与Fanout功能类似

基于@RabbitListener注解声明队列和交换机有哪些常见注解？

- @Queue
- @Exchange

#### 3.6.Topic

##### 3.6.1.说明

`Topic`类型的`Exchange`与`Direct`相比，都是可以根据`RoutingKey`把消息路由到不同的队列。只不过`Topic`类型`Exchange`可以让队列在绑定`Routing key` 的时候使用通配符！

`Routingkey` 一般都是有一个或多个单词组成，多个单词之间以”.”分割，例如： `item.insert`

 通配符规则：

`#`：匹配一个或多个词

`*`：匹配不多不少恰好1个词

举例：

`item.#`：能够匹配`item.spu.insert` 或者 `item.spu`

`item.*`：只能匹配`item.spu`

图示：

![image-20210717170705380](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717170705380.png)

解释：

- Queue1：绑定的是`china.#` ，因此凡是以 `china.`开头的`routing key` 都会被匹配到。包括china.news和china.weather
- Queue2：绑定的是`#.news` ，因此凡是以 `.news`结尾的 `routing key` 都会被匹配。包括china.news和japan.news

案例需求：

实现思路如下：

1. 并利用@RabbitListener声明Exchange、Queue、RoutingKey

2. 在consumer服务中，编写两个消费者方法，分别监听topic.queue1和topic.queue2

3. 在publisher中编写测试方法，向itcast. topic发送消息

![image-20210717170829229](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210717170829229.png)

##### 3.6.2.消息发送

在publisher服务的SpringAmqpTest类中添加测试方法：

```java
/**
     * topicExchange
     */
@Test
public void testSendTopicExchange() {
    // 交换机名称
    String exchangeName = "itcast.topic";
    // 消息
    String message = "喜报！孙悟空大战哥斯拉，胜!";
    // 发送消息
    rabbitTemplate.convertAndSend(exchangeName, "china.news", message);
}
```

##### 3.6.3.消息接收

在consumer服务的SpringRabbitListener中添加方法：

```java
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "topic.queue1"),
    exchange = @Exchange(name = "itcast.topic", type = ExchangeTypes.TOPIC),
    key = "china.#"
))
public void listenTopicQueue1(String msg){
    System.out.println("消费者接收到topic.queue1的消息：【" + msg + "】");
}

@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "topic.queue2"),
    exchange = @Exchange(name = "itcast.topic", type = ExchangeTypes.TOPIC),
    key = "#.news"
))
public void listenTopicQueue2(String msg){
    System.out.println("消费者接收到topic.queue2的消息：【" + msg + "】");
}
```

##### 3.6.4.总结

描述下Direct交换机与Topic交换机的差异？

- Topic交换机接收的消息RoutingKey必须是多个单词，以 `**.**` 分割
- Topic交换机与队列绑定时的bindingKey可以指定通配符
- `#`：代表0个或多个词
- `*`：代表1个词

#### 3.7.消息转换器

之前说过，Spring会把**你发送的消息序列化为字节发送给MQ，接收消息的时候，还会把字节反序列化为Java对象。**

![image-20200525170410401](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20200525170410401.png)

只不过，默认情况下Spring采用的序列化方式是JDK序列化。众所周知，JDK序列化存在下列问题：

- 数据体积过大 
- 有安全漏洞
- 可读性差

我们来测试一下。

##### 3.7.1.测试默认转换器

我们修改消息发送的代码，发送一个Map对象：

```java
@Test
public void testSendMap() throws InterruptedException {
    // 准备消息
    Map<String,Object> msg = new HashMap<>();
    msg.put("name", "Jack");
    msg.put("age", 21);
    // 发送消息
    rabbitTemplate.convertAndSend("simple.queue","", msg);
}
```

停止consumer服务

发送消息后查看控制台：

![image-20210422232835363](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210422232835363.png)

##### 3.7.2.配置JSON转换器

显然，JDK序列化方式并不合适。我们希望消息体的体积更小、可读性更高，**因此可以使用JSON方式来做序列化和反序列化。**在publisher和consumer两个服务中都引入依赖：

```xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
    <version>2.9.10</version>
</dependency>
```

配置消息转换器。

在启动类中添加一个Bean即可：

```java
@Bean
public MessageConverter jsonMessageConverter(){
    return new Jackson2JsonMessageConverter();
}
```

## Springcloud05

##### 1.1.5.总结

什么是elasticsearch？

- **一个开源的分布式搜索引擎，可以用来实现搜索、日志统计、分析、系统监控等功能**

**什么是elastic stack（ELK）？**

- 是以elasticsearch为核心的技术栈，包括beats、Logstash、kibana、elasticsearch

什么是Lucene？

- **是Apache的开源搜索引擎类库，提供了搜索引擎的核心API**

#### 1.2.倒排索引

倒排索引的概念是基于MySQL这样的正向索引而言的。

##### 1.2.1.正向索引

那么什么是正向索引呢？例如给下表（tb_goods）中的id创建索引：

![image-20210720195531539](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210720195531539.png)

如果是根据id查询，那么直接走索引，查询速度非常快。

但如果是基于title做模糊查询，只能是逐行扫描数据，流程如下：

1）用户搜索数据，条件是title符合`"%手机%"`

2）逐行获取数据，比如id为1的数据

3）判断数据中的title是否符合用户搜索条件

4）如果符合则放入结果集，不符合则丢弃。回到步骤1



逐行扫描，也就是全表扫描，随着数据量增加，其查询效率也会越来越低。当数据量达到数百万时，就是一场灾难。





##### 1.2.2.倒排索引

倒排索引中有两个非常重要的概念：

- 文档（`Document`）：用来搜索的数据，其中的每一条数据就是一个文档。例如一个网页、一个商品信息
- 词条（`Term`）：对文档数据或用户搜索数据，利用某种算法分词，得到的具备含义的词语就是词条。例如：我是中国人，就可以分为：我、是、中国人、中国、国人这样的几个词条

**创建倒排索引**是对正向索引的一种特殊处理，流程如下：

- 将每一个文档的数据利用算法分词，得到一个个词条
- 创建表，每行数据包括词条、词条所在文档id、位置等信息
- 因为词条唯一性，可以给词条创建索引，例如hash表结构索引

如图：

![image-20210720200457207](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210720200457207.png)





倒排索引的**搜索流程**如下（以搜索"华为手机"为例）：

1）用户输入条件`"华为手机"`进行搜索。

2）对用户输入内容**分词**，得到词条：`华为`、`手机`。

3）拿着词条在倒排索引中查找，可以得到包含词条的文档id：1、2、3。

4）拿着文档id到正向索引中查找具体文档。

如图：

![image-20210720201115192](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210720201115192.png)



虽然要先查询倒排索引，再查询倒排索引，但是无论是词条、还是文档id都建立了索引，查询速度非常快！无需全表扫描。



##### 1.2.3.正向和倒排

那么为什么一个叫做正向索引，一个叫做倒排索引呢？

- **正向索引**是最传统的，根据id索引的方式。但根据词条查询时，必须先逐条获取每个文档，然后判断文档中是否包含所需要的词条，是**根据文档找词条的过程**。

- 而**倒排索引**则相反，是先找到用户要搜索的词条，根据词条得到保护词条的文档的id，然后根据id获取文档。是**根据词条找文档的过程**。

是不是恰好反过来了？

那么两者方式的优缺点是什么呢？

**正向索引**：

- 优点：
  - 可以给多个字段创建索引
  - 根据索引字段搜索、排序速度非常快
- 缺点：
  - 根据非索引字段，或者索引字段中的部分词条查找时，只能全表扫描。

**倒排索引**：

- 优点：
  - 根据词条搜索、模糊搜索时，速度非常快
- 缺点：
  - 只能给词条创建索引，而不是字段
  - 无法根据字段做排序

#### 1.3.es的一些概念

elasticsearch中有很多独有的概念，与mysql中略有差别，但也有相似之处。

##### 1.3.1.文档和字段

elasticsearch是面向**文档（Document）**存储的，可以是数据库中的一条商品数据，一个订单信息。文档数据会被序列化为json格式后存储在elasticsearch中：

![image-20210720202707797](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210720202707797.png)



而Json文档中往往包含很多的**字段（Field）**，类似于数据库中的列。



##### 1.3.2.索引和映射

**索引（Index）**，就是相同类型的文档的集合。

例如：

- 所有用户文档，就可以组织在一起，称为用户的索引；
- 所有商品的文档，可以组织在一起，称为商品的索引；
- 所有订单的文档，可以组织在一起，称为订单的索引；

![image-20210720203022172](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210720203022172.png)



因此，我们可以把索引当做是数据库中的表。

数据库的表会有约束信息，用来定义表的结构、字段的名称、类型等信息。因此，索引库中就有**映射（mapping）**，是索引中文档的字段约束信息，类似表的结构约束。



##### 1.3.3.mysql与elasticsearch

我们统一的把mysql与elasticsearch的概念做一下对比：

| **MySQL** | **Elasticsearch** | **说明**                                                     |
| --------- | ----------------- | ------------------------------------------------------------ |
| Table     | Index             | 索引(index)，就是文档的集合，类似数据库的表(table)           |
| Row       | Document          | 文档（Document），就是一条条的数据，类似数据库中的行（Row），文档都是JSON格式 |
| Column    | Field             | 字段（Field），就是JSON文档中的字段，类似数据库中的列（Column） |
| Schema    | Mapping           | Mapping（映射）是索引中文档的约束，例如字段类型约束。类似数据库的表结构（Schema） |
| SQL       | DSL               | DSL是elasticsearch提供的JSON风格的请求语句，用来操作elasticsearch，实现CRUD |

是不是说，我们学习了elasticsearch就不再需要mysql了呢？

并不是如此，两者各自有自己的擅长支出：

- Mysql：擅长事务类型操作，可以确保数据的安全和一致性

- Elasticsearch：擅长海量数据的搜索、分析、计算



因此在企业中，往往是两者结合使用：

- 对安全性要求较高的写操作，使用mysql实现
- 对查询性能要求较高的搜索需求，使用elasticsearch实现
- 两者再基于某种方式，实现数据的同步，保证一致性

![image-20210720203534945](D:\2024\Notes\Typora\Study\Java_Springcloud\springcloud.assets\image-20210720203534945.png)

















































