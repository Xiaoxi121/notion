# Redis

## 面试篇

### 2.1 Redis常见面试题

#### 1. 认识Redis

##### 什么是Redis

- Redis 是一种基于内存的数据库，对数据的读写操作都是在内存中完成，因此读写速度非常快，常用于缓存，消息队列、分布式锁等场景。
- Redis 提供了多种数据类型来支持不同的业务场景，比如 String(字符串)、Hash(哈希)、 List (列表)、Set(集合)、Zset(有序集合)、Bitmaps（位图）、HyperLogLog（基数统计）、GEO（地理信息）、Stream（流），并且对数据类型的操作都是原子性的，因为执行命令由单线程负责的，不存在并发竞争的问题。
- Redis 还支持事务 、持久化、Lua 脚本、多种集群方案（主从复制模式、哨兵模式、切片机群模式）、发布/订阅模式，内存淘汰机制、过期删除机制等等

##### Redis和Memcached区别

共同点：

1. 都是基于内存的数据库，一般都用来当做缓存使用。
2. 都有过期策略。
3. 两者的性能都非常高 

区别：

|   区别   |                            Redis                             |                Memcached                 |
| :------: | :----------------------------------------------------------: | :--------------------------------------: |
| 数据类型 |                            更丰富                            |             只支持key-value              |
|  持久化  | 支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用 |   没有持久化功能，数据全部存在内存之中   |
| 集群模式 |                       原生支持集群模式                       | 需要依靠客户端来实现往集群中分片写入数据 |
|   支持   |               支持发布订阅模式、Lua脚本、事务                |                                          |

##### 为什么用Redis作为MySQL的缓存

- 高性能
- 高并发

#### Redis除了缓存还能做什么

1. **分布式锁**：通过 Redis 来做分布式锁是一种比较常见的方式。通常情况下，我们都是基于 Redisson 来实现分布式锁。关于 Redis 实现分布式锁的详细介绍，可以看我写的这篇文章：分布式锁详解 。
2. **限流：**一般是通过 Redis + Lua 脚本的方式来实现限流。如果不想自己写 Lua 脚本的话，也可以直接利用 Redisson 中的 RRateLimiter 来实现分布式限流，其底层实现就是基于 Lua 代码+令牌桶算法。
3. **消息队列**：Redis 自带的 List 数据结构可以作为一个简单的队列使用。Redis 5.0 中增加的 Stream 类型的数据结构更加适合用来做消息队列。它比较类似于 Kafka，有主题和消费组的概念，支持消息持久化以及 ACK 机制。
   延时队列：Redisson 内置了延时队列（基于 Sorted Set 实现的）。
4. **分布式 Session** ：利用 String 或者 Hash 数据类型保存 Session 数据，所有的服务器都可以访问。
5. **复杂业务场景**：通过 Redis 以及 Redis 扩展（比如 Redisson）提供的数据结构，我们可以很方便地完成很多复杂的业务场景比如通过 Bitmap 统计活跃用户、通过 Sorted Set 维护排行榜。

#### 2. Redis的数据结构

|  数据类型   |                           应用场景                           |                     底层数据结构                      | 内部实现                                                     |
| :---------: | :----------------------------------------------------------: | :---------------------------------------------------: | :----------------------------------------------------------- |
|   String    |        缓存对象、常规计数、分布式锁、共享session信息         |                  SDS(简单动态字符串)                  | 1.不仅可以保存文本数据，还可以保持二进制数据（记录len）2. 获取字符串长度的时间复杂度是O（1） 3.API安全，拼接字符串不造成缓冲区溢出 |
|    Hash     |                       缓存对象、购物车                       | Redis3.2以后改成quicklist实现，代替双向链表和压缩列表 | quicklist                                                    |
|    List     | 消息队列（问题1 生产者需要自行实现全局唯一ID 问题2 不能以消费组形式消费数据） | （Redis7.0以后改成listpack实现，代替压缩列表）+哈希表 | 小于512，每个小于64字节，listpack 否则哈希表                 |
|     Set     |           聚合计算场景（点赞、共同关注、抽奖活动）           |                  哈希表或者整数集合                   | 小于512 整数集合 否则哈希表                                  |
|    Zset     |                           排序场景                           |  （Redis7.0以后改成listpack实现，代替压缩列表）+跳表  | 小于128，每个小于64字节，listpack，否则跳表                  |
|   BitMap    | 二值状态统计的场景，比如签到、判断用户登陆状态、连续签到用户总数等 |                                                       |                                                              |
| HyperLogLog |        海量数据基数统计的场景，比如百万级网页 UV 计数        |                                                       |                                                              |
|     GEO     |             存储地理位置信息的场景，比如滴滴叫车             |                                                       |                                                              |
|   Stream    | 消息队列，相比于基于 List 类型实现的消息队列，有这两个特有的特性：<br />自动生成全局唯一消息ID，支持以消费组形式消费数据。 |                                                       |                                                              |

#### 3. Redis线程模式

##### Redis是单线程吗

**Redis 单线程**指的是「**接收客户端请求->解析请求 ->进行数据读写等操作->发送数据给客户端」**这个过程是由一个线程（主线程）来完成的；Redis **程序**并不是单线程的，Redis 在**启动的时候，是会启动后台线程（BIO）的：**

​	后台线程相当于一个消费者，生产者把耗时任务丢到任务队列中，消费者（BIO）不停轮询这个队列，拿出任务就去执行对应的方法即可。

- 后台线程1：关闭文件

  BIO_CLOSE_FILE，关闭文件任务队列：当队列有任务后，后台线程会调用 close(fd) ，将文件关闭；

- 后台线程2：AOF刷盘

  BIO_AOF_FSYNC，AOF刷盘任务队列：当 AOF 日志配置成 everysec 选项后，主线程会把 AOF 写日志操作封装成一个任务，也放到队列中。当发现队列有任务后，后台线程会调用 fsync(fd)，将 AOF 文件刷盘，

- 后台线程3：异步释放Redis内存(Lazy Free)

  BIO_LAZY_FREE，lazy free 任务队列：当队列有任务后，后台线程会 free(obj) 释放对象 / free(dict) 删除数据库所有对象 / free(skiplist) 释放跳表对象；

##### Redis单线程模式是什么样的

1. Redis初始化
   1. 调用epoll_create()创建一个epoll对象；调用socket()创建一个服务端socket
   2. 调用bind()绑定端口，调用listen()监听该socket
   3. 调用epoll()_ctl将listen socket加入到epoll，同时注册连接事件处理函数
2. 主线程进入事件处理函数
   1. 首先调用处理发送队列函数，看发送队列里面有没有任务。有通过write函数将客户端发送那个缓存区里面的数据发送出去，如果这一轮数据没有发送完，就会注册事件处理函数，等待epoll_wait发现可写后在处理。
   2. 接着调用epoll_wait等待事件的到来
      1. 连接事件：连接事件处理函数-》调用accept获取已经连接的socket-》调用epoll_ctl将连接的socket加入到epoll，注册读事件处理函数
      2. 读事件：读事件处理函数-》调用read获取客户端发送的数据-》解析命令-》处理命令-》将客户端对象添加到发送队列-》将执行结果写到发送缓存区等待发送。
      3. 写事件：函数将客户端发送那个缓存区里面的数据发送出去，如果这一轮数据没有发送完，就会注册事件处理函数，等待epoll_wait发现可写后在处理。

Redis 通过 IO 多路复用程序 来监听来自客户端的大量连接（或者说是监听多个 socket），它会将感兴趣的事件及类型（读、写）注册到内核中并监听每个事件是否发生。

''这样的好处非常明显：I/O 多路复用技术的使用让 Redis 不需要额外创建多余的线程来监听客户端的大量连接，降低了资源的消耗（和 NIO 中的 Selector 组件很像）。

文件事件处理器（file event handler）主要是包含 4 个部分：

- 多个 socket（客户端连接）
- IO 多路复用程序（支持多个客户端连接的关键）
- 文件事件分派器（将 socket 关联到相应的事件处理器）
- 事件处理器（连接应答处理器、命令请求处理器、命令回复处理器

##### Redis采用单线程为什么还这么快

1. **Redis 的大部分操作都在内存中完成，并且采用了高效的数据结构，**因此 Redis 瓶颈可能是机器的内存或者网络带宽，而并非 CPU，既然 CPU 不是瓶颈，那么自然就采用单线程的解决方案了；
2. Redis 采用单线程模型可以**避免了多线程之间的竞争，省去了多线程切换带来的时间和性能上的开销，**而且**也不会导致死锁问题。**
3. Redis **采用了 I/O 多路复用机制处理大量的客户端 Socket 请求，IO 多路复用机制是指一个线程处理多个 IO 流，就是我们经常听到的 select/epoll 机制。**简单来说，在 Redis 只运行单线程的情况下，该机制允许内核中，同时存在多个监听 Socket 和已连接 Socket。**内核会一直监听这些 Socket 上的连接请求或数据请求。一旦有请求到达，就会交给 Redis 线程处理，这就实现了一个 Redis 线程处理多个 IO 流的效果**
4. Redis **通信协议实现简单且解析高效**

##### Redis6.0之前为什么使用单线程

上个问题1.2

##### Redis6.0之后为什么引入了多线程

- 网络硬件性能提升，Redis性能瓶颈上有时候会出现在网络IO的处理上，于是引入了多个线程来处理网络请求
- 所以为了提高网络 I/O 的并行度，**Redis 6.0 对于网络 I/O 采用多线程来处理。**但是**对于命令的执行，Redis 仍然使用单线程来处理，**所以大家不要误解 Redis 有多线程同时执行命令。
- **Redis 单线程指的是「接收客户端请求->解析请求 ->进行数据读写等操作->发送数据给客户端」这个过程是由一个线程（主线程）来完成的，**
- Redis6.0 引入多线程主要是为了提高网络 IO 读写性能，因为这个算是 Redis 中的一个性能瓶颈（Redis 的瓶颈主要受限于内存和网络）。
  虽然，Redis6.0 引入了多线程，但是 Redis 的多线程只是在网络数据的读写这类耗时操作上使用了，执行命令仍然是单线程顺序执行。因此，你也不需要担心线程安全问题。
- Redis 6.0 版本之后，**Redis 在启动的时候，默认情况下会额外创建 6 个线程（不包括主线程）**
  - Redis-server ： Redis的主线程，主要负责执行命令；
  - bio_close_file、bio_aof_fsync、bio_lazy_free：三个后台线程，分**别异步处理关闭文件任务、AOF刷盘任务、释放内存任务；**
  - io_thd_1、io_thd_2、io_thd_3：三个 I/O 线程，io-threads 默认是 4 ，所以会启动 3（4-1）个 I/O 多线程，用来分担 Redis 网络 I/O 的压力

##### Redis怎么实现的io多路复用

1. 因为 Redis 是跑在「单线程」中的，所有的操作都是按照顺序线性执行的，但是由于读写操作等待用户输入 或 输出都是阻塞的，所以 I/O 操作在一般情况下往往不能直接返回，这会导致某一文件的 I/O 阻塞导，致整个进程无法对其它客户提供服务
2. I/O 多路复用就是为了解决这个问题而出现的。为了让单线程(进程)的服务端应用同时处理多个客户端的事件，Redis 采用了 IO 多路复用机制
3. I/O 多路复用其实是使用一个线程来检查多个 Socket 的就绪状态，在单个线程中通过记录跟踪每一个 socket（I/O流）的状态来管理处理多个 I/O 流。

对 Redis 的 I/O 多路复用模型进行一下描述说明：

1. 一个 socket 客户端与服务端连接时，会生成对应一个套接字描述符(套接字描述符是文件描述符的一种)，每一个 socket 网络连接其实都对应一个文件描述符。
2. 多个客户端与服务端连接时，Redis 使用 I/O 多路复用程序 将客户端 socket 对应的 FD 注册到监听列表(一个队列)中。当客服端执行 read、write 等操作命令时，I/O 多路复用程序会将命令封装成一个事件，并绑定到对应的 FD 上。
3. 文件事件处理器使用 I/O 多路复用模块同时监控多个文件描述符（fd）的读写情况，当 accept、read、write 和 close 文件事件产生时，文件事件处理器就会回调 FD 绑定的事件处理器进行处理相关命令操作。

例如：以 Redis 的 I/O 多路复用程序 epoll 函数为例。**多个客户端连接服务端时，Redis 会将客户端 socket 对应的 fd 注册进 epoll，然后 epoll 同时监听多个文件描述符(FD)是否有数据到来，如果有数据来了就通知事件处理器赶紧处理，这**样就不会存在服务端一直等待某个客户端给数据的情形。

**整个文件事件处理器是在单线程上运行的，但是通过 I/O 多路复用模块的引入，实现了同时对多个 FD 读写的监控，当其中一个 client 端达到写或读的状态，文件事件处理器就马上执行，从而就不会出现 I/O 堵塞的问题，提高了网络通信的性能**。

Redis 的 I/O 多路复用模式使用的是 **Reactor 设置模式的方式来实现**

#### Redis的网络模型是怎么样的

1. Redis 6.0 版本之前，是用的是单Reactor单线程的模式

   缺点：

   ​	a. 只有一个进程，无法充分利用多核cpu

   ​	b. Handler 对象在业务处理时，整个进程是无法处理其他连接的事件的，如果业务处理耗时比较长，那么就造成响应的延迟；

   不适用计算机密集型的场景，只适用于业务处理非常快速的场景。

2. Redis 6.0 对于网络 I/O 采用多线程来处理。但是对于命令的执行，Redis 仍然使用单线程来处理

#### 4. Redis持久化

##### Redis如何实现数据不丢失

为了保证内存中的数据不会丢失，Redis 实现了数据持久化的机制，这个机制会把数据存储到磁盘，这样在 Redis 重启就能够从磁盘中恢复原有的数据。

三种持久化方式：

- **AOF日志：**每执行一条写**操作命令**，就把该命令以追加的方式写入到一个文件里
- **RDB快照：**将某一时刻的**内存数据**，以二进制方式写入磁盘
- 混合持久化方式：Redis4.0新增的方式，继承了前两种的优点

##### AOF（Append Only File）日志是如何实现的

Reids 是先执行写操作命令后，才将该命令记录到 AOF 日志里的

- 避免额外的检查开销
- 不会阻塞当前写操作命令的执行

- 数据可能会丢失
- 可能阻塞其他的操作

###### 1. AOF写回策略有几种？

- Redis 执行完写操作命令后，会将命令追加到 server.aof_buf 缓冲区；

- 然后通过 write() 系统调用，将 aof_buf 缓冲区的数据写入到 AOF 文件，此时数据并没有写入到硬盘，而是拷贝到了内核缓冲区 page cache，等待内核将数据写入硬盘；

- 具体内核缓冲区的数据什么时候写入到硬盘，由内核决定。

  - Redis.conf 配置文件中的 appendfsync 配置项可以有以下 3 种参数可填。
    这三种策略只是在控制 fsync() 函数的调用时机。

    | 写回策略 | 写回时机           | 优点                           | 缺点                               |
    | -------- | ------------------ | ------------------------------ | ---------------------------------- |
    | Always   | 同步写回           | 可靠性高、最大程度保证数不丢失 | 每个写命令都要写回硬盘，性能开销大 |
    | EverySec | 每秒写回           | 性能适中                       | 宕机时会丢失一秒内的数据           |
    | No       | 由操作系统控制写回 | 性能好                         | 宕机时可能会丢失大量数据           |

###### 2. AOF日志过大会触发什么机制

- 提供了 AOF 重写机制，当 AOF 文件的大小超过所设定的阈值后，启用 AOF 重写机制，来压缩 AOF 文件。
- 读取当前数据库中的所有键值对，然后将每一个键值对用一条命令记录到「新的 AOF 文件」，等到全部记录完后，就将新的 AOF 文件替换掉现有的 AOF 文件。
  - 因为如果 AOF 重写过程中失败了，现有的 AOF 文件就会造成污染，可能无法用于恢复使用。所以 AOF 重写过程，先重写到新的 AOF 文件，重写失败的话，就直接删除这个文件就好，不会对现有的 AOF 文件造成影响

###### 3. 重写AOF日志的过程是怎样的

​	Redis 的重写 AOF 过程是由后台子进程 bgrewriteaof 来完成的，这么做可以达到两个好处

- 避免阻塞主进程
- 父子进程内存以只读的的方式共享，当父子进程任意一方修改了该共享内存，发生写时复制，父子进程有了独立的数据副本，不用加锁来保存副本
  - 主进程在通过 fork 系统调用生成 bgrewriteaof 子进程时，操作系统会把**主进程的「页表」复制一份**给子进程，这个页表记录着虚拟地址和物理地址映射关系，而**不会复制物理内存**，也就是说，两者的虚拟空间不同，但其对应的物理空间是同一个。
  - 当父进程或者子进程在向这个内存发起写操作时，CPU 就会触发**写保护中断**，这个写保护中断是由于违反权限导致的，然后操作系统会在「写保护中断处理函数」里**进行物理内存的复制，并重新设置其内存映射关系，将父子进程的内存读写权限设置为可读写**，最后才会对内存进行写操作，这个过程被称为「写时复制(Copy On Write)」
  - 写时复制顾名思义**，在发生写操作的时候，操作系统才会去复制物理内存**，这样是**为了防止** fork 创建子进程时，**由于物理内存数据的复制时间过长而导致父进程长时间阻塞的问题。**

触发重写机制后，主进程就会创建重写 AOF 的子进程，此时父子进程共享物理内存，重写子进程只会对这个内存进行只读，重写 AOF 子进程会读取数据库里的所有数据，并逐一把内存数据的键值对转换成一条命令，再将命令记录到重写日志（新的 AOF 文件）

 在 bgrewriteaof 子进程执行 AOF 重写期间，主进程需要执行以下三个工作:

- 执行客户端发来的命令；
  - 如果此时主进程修改了已经存在 key-value，就会发生写时复制，注意这里只会复制主进程修改的物理内存数据，没修改物理内存还是与子进程共享的。
- 将执行后的写命令**追加到 「AOF 缓冲区」；**
- 将执行后的写命令**追加到 「AOF 重写缓冲区」；**

子进程完成重写工作，向主进程发信号，主进程收到信号，调用信号处理函数：

- **将 AOF 重写缓冲区中的所有内容追加到新的 AOF 的文件中，使得新旧两个 AOF 文件所保存的数据库状态一致；**
- **新的 AOF 的文件进行改名，覆盖现有的 AOF 文件。**

##### RDB（Redis Database）快照是如何实现的呢

​	RDB 快照就是记录某一个瞬间的内存数据，记录的是实际数据，而 AOF 文件记录的是命令操作的日志，而不是实际的数据。因此在 Redis 恢复数据时， RDB 恢复数据的效率会比 AOF 高些，因为直接将 RDB 文件读入内存就可以，不需要像 AOF 那样还需要额外执行操作命令的步骤才能恢复数据。

​	RDB 快照的缺点：在服务器发生故障时，丢失的数据会比 AOF 持久化的方式更多，因为 RDB 快照是全量快照的方式，因此执行的频率不能太频繁，否则会影响 Redis 性能，而 AOF 日志可以以秒级的方式记录操作命令，所以丢失的数据就相对更少

###### RDB做快照时会阻塞线程吗

- save 在主线程生成RDB文件，会阻塞主线程
- bgsave 创建子线程来生成RDB文件，避免主线程的阻塞
- 也可以通过配置文件的选项自动执行bgsave

###### RDB在执行快照的时候，数据能修改吗

​	执行 bgsave 过程中，Redis 依然可以继续处理操作命令的，也就是数据是能被修改的，关键的技术就在于写时复制技术（Copy-On-Write, COW）。

​	共享同一片内存数据，页表指向的物理内存还是一个，主线程可以直接修改原来的数据。

​	bgsave 快照过程中，如果主线程修改了共享数据，发生了写时复制后，RDB 快照保存的是原本的内存数据，而主线程刚修改的数据，是没办法在这一时间写入 RDB 文件的，只能交由下一次的 bgsave 快照。
​	所以 Redis 在使用 bgsave 快照过程中，如果主线程修改了内存数据，不管是否是共享的内存数据，RDB 快照都无法写入主线程刚修改的数据，因为此时主线程（父进程）的内存数据和子进程的内存数据已经分离了，子进程写入到 RDB 文件的内存数据只能是原本的内存数据
​	如果系统恰好在 RDB 快照文件创建完毕后崩溃了，那么 Redis 将会丢失主线程在快照期间修改的数据。

##### 为什么会有混合持久化

- 混合持久化工作在 AOF 日志重写过程，AOF 文件的前半部分是 RDB 格式的全量数据，后半部分是 AOF 格式的增量数据。
  - 当开启了混合持久化时，在 AOF 重写日志时，fork 出来的重写子进程会先将与主线程共享的内存数据以 RDB 方式写入到 AOF 文件，然后主线程处理的操作命令会被记录在重写缓冲区里，重写缓冲区里的增量命令会以 AOF 方式写入到 AOF 文件，写入完成后通知主进程将新的含有 RDB 格式和 AOF 格式的 AOF 文件替换旧的的 AOF 文件。
- 既保证了 Redis 重启速度，又降低数据丢失风险。
- 添加了 RDB 格式的内容，使得 AOF 文件的可读性变得很差；兼容性差，如果开启混合持久化，那么此混合持久化 AOF 文件，就不能用在 Redis 4.0 之前版本了。

#### 5. Redis集群

##### Redis如何实现服务高可用

想设计一个高可用的 Redis 服务，一定要从 Redis 的多服务节点来考虑，比如 Redis 的主从复制、哨兵模式、切片集群。

1. 主从复制

   一主多从，读写分离。

   主服务器进行读写，写操作自动同步给从服务器。从服务器只读，接收主服务器同步的写操作命令，然后执行。

   主从服务器之间的命令复制是异步进行的。主服务器不会等待从服务器命令执行完成再向客户端返回结果，无法实现强一致性保证

2. 哨兵复制

   当 Redis 的主从服务器出现故障宕机时，需要手动进行恢复。因此增加哨兵模式，可以监控主从服务器，并且提供主从节点故障转移的功能。

3. 切片集群

   当 Redis 缓存数据量大到一台服务器无法缓存时，就需要使用 Redis 切片集群（Redis Cluster ）方案，它将数据分布在不同的服务器上，以此来降低系统对单主节点的依赖，从而提高 Redis 服务的读写性能。

##### 集群脑裂导致数据丢失怎么办？

什么是脑裂？

​	由于网络问题，集群节点之间失去联系。主从数据不同步；重新平衡选举，产生两个主服务。等网络恢复，旧主节点会降级为从节点，再与新主节点进行同步复制的时候，由于会从节点会清空自己的缓冲区，所以导致之前客户端写入的数据丢失了。

解决方案

​	当主节点发现从节点下线或者通信超时的总数量小于阈值时，那么禁止主节点进行写数据，直接把错误返回给客户端。主库连接的从库中至少有 N 个从库，和主库进行数据复制时的 ACK 消息延迟不能超过 T 秒，否则，原主库就会被限制接收客户端写请求，客户端也就不能在原主库中写入新数据了。等到新主库上线时，就只有新主库能接收和处理客户端请求，此时，新写的数据会被直接写到新主库中。而原主库会被哨兵降为从库，即使它的数据被清空了，也不会有新数据丢失。

#### 6. Redis过期删除与内存淘汰

##### 如何设置过期时间

1. 对key设置过期时间的命令
   1. expire <key> <n>：设置 key 在 n 秒后过期
   2. pexpire <key> <n>：设置 key 在 n 毫秒后过期
   3. expireat <key> <n>：设置 key 在某个时间戳（精确到秒）之后过期，
   4. pexpireat <key> <n>：设置 key 在某个时间戳（精确到毫秒）之后过期
2. 设置字符串时，同时对key设置过期时间
   1. set <key> <value> ex <n> ：设置键值对的时候，同时指定过期时间（精确到秒）；
   2. set <key> <value> px <n> ：设置键值对的时候，同时指定过期时间（精确到毫秒）；
   3. setex <key> <n> <valule> ：设置键值对的时候，同时指定过期时间（精确到秒）
3. 如果你想查看某个 key 剩余的存活时间，可以使用 TTL <key> 命令。
4. 如果突然反悔，取消 key 的过期时间，则可以使用 PERSIST <key> 命令。

##### Redis 使用的过期删除策略是什么？

- Redis 是可以对 key 设置过期时间的，因此需要有相应的机制将已过期的键值对删除，而做这个工作的就是过期键值删除策略。

- Redis 会把该 key 带上过期时间存储到一个过期字典（expires dict）中（**过期字典存储在redisDb结构中**），也就是说「过期字典」保存了数据库中所有 key 的过期时间。Redis 选择**「惰性删除+定期删除」这两种策略配和使用**，以求在合理使用 CPU 时间和避免内存浪费之间取得平衡。

  - 过期字典的 key 是一个指针，指向某个键对象；
    过期字典的 value 是一个 long long 类型的整数，这个整数保存了 key 的过期时间；

- 当我们查询一个 key 时，Redis 首先检查该 key 是否存在于过期字典中：

  - 不存在，正常读取
  - 存在，比对时间

- 定时删除策略

  在设置 key 的过期时间时，同时创建一个定时事件，当时间到达时，由事件处理器自动执行 key 的删除操作

  优点：可以保证过期 key 会被尽快删除，也就是内存可以被尽快地释放。因此，定时删除对内存是最友好的。

  缺点：在过期 key 比较多的情况下，**删除过期 key 可能会占用相当一部分 CPU 时间，**在内存不紧张但 CPU 时间紧张的情况下，将 CPU 时间用于删除和当前任务无关的过期键上，无疑会对服务器的响应时间和吞吐量造成影响。所以，定时删除策略对 CPU 不友好。

- **惰性删除策略**

  不主动删除过期键，每次从数据库访问 key 时，都检测 key 是否过期，如果过期则删除该 key。

  优点：惰性删除策略对 CPU 时间最友好。

  缺点：只要这个过期 key 一直没有被访问，它所占用的内存就不会释放，造成了一定的内存空间浪费。

- **定期删除策略**

  每隔一段时间**「随机」从数据库中取出一定数量**的 key 进行检查，并删除其中的过期key。超过25%继续查。

  那 Redis 为了保证定期删除不会出现循环过度，导致线程卡死现象，为此增加了定期删除循环流程的时间上限，默认不会超过 25ms。

  优点：

  ​	通过限制删除操作执行的时长和频率，来减少删除操作对 CPU 的影响。

  ​	同时也能删除一部分过期的数据减少了过期键对空间的无效占用。

  缺点：

  ​	难以确定删除操作执行的时长和频率。如果执行的太频繁，就会对 CPU 不友好；如果执行的太少，那又和惰性删除一样了，过期 key 占用的内存不会及时得到释放。

##### Redis 持久化时，对过期键会如何处理的？

1. RDB文件
   1. 生成阶段：从内存状态持久化成RDB的时候，对key进行过期检查，不会保存到新的RDB文件中
   2. 加载阶段
      1. 主服务器，载入RDB文件时，对文件中保存的key进行检查，不会载入到数据库中
      2. 从服务器，直接载入。但是主从服务器同步时候从服务器数据会被清空，所以也没影响
2. AOF文件
   1. 写入阶段：以AOF模式持久化，过期键被保留，当此过期键被删除后，会向AOF文件追加一条DEL命令来显式地删除该键值
   2. 重写阶段：执行AOF重写，对键进行检查，不会保存到重写的文件中。

##### Redis 主从模式中，对过期键会如何处理？

- 当 Redis 运行在主从模式下时，从库不会进行过期扫描，从库对过期的处理是被动的。也就是即使从库中的 key 过期了，如果有客户端访问从库时，依然可以得到 key 对应的值，像未过期的键值对一样返回。
  - 小林哥，感觉redis有些东西有点老了。主从复制那里，master删除过期键，但是slave不会删除过期键。这句话是redis3.2以前适用的。
    在4.0.11 以上版本，所有命令均已修复，过期 key 在 slave 上查询，均返回不存在了
- 主库在 key 到期时，会在 AOF 文件里增加一条 del 指令，同步到所有的从库，从库通过执行这条 del 指令来删除过期的 key

##### Redis 内存满了，会发生什么？

触发内存淘汰策略

##### Redis 内存淘汰策略有哪些？

八种内存淘汰策略，大体分为不进行数据淘汰和进行数据淘汰两种策略。

1. 不进行数据淘汰的策略

   noeviction（Redis3.0之后，默认的内存淘汰策略） ：它表示当运行内存超过最大设置内存时，不淘汰任何数据，而是不再提供服务，直接返回错误。

2. 进行数据淘汰的策略

   1. 在设置了过期时间的数据中进行淘汰
      1. volatile-random：随机淘汰设置了过期时间的任意键值；
      2. volatile-ttl：优先淘汰更早过期的键值。
      3. volatile-lru（Redis3.0 之前，默认的内存淘汰策略）：淘汰所有设置了过期时间的键值中，最久未使用的键值；
      4. volatile-lfu（Redis 4.0 后新增的内存淘汰策略）：淘汰所有设置了过期时间的键值中，最少使用的键值；
   2. 在所有数据范围内进行淘汰
      1. allkeys-random：随机淘汰任意键值;
      2. allkeys-lru：淘汰整个键值中最久未使用的键值；
      3. allkeys-lfu（Redis 4.0 后新增的内存淘汰策略）：淘汰整个键值中最少使用的键值。

##### LRU 算法和 LFU 算法有什么区别？

1. 传统的LRU算法：最近最少使用 Least Recently Used

   1. 需要用链表管理所有的缓存数据，这会带来额外的空间开销；
   2. 当有数据被访问时，需要在链表上把该数据移动到头端，如果有大量数据被访问，就会带来很多链表移动操作，会很耗时，进而会降低 Redis 缓存性能。

2. Redis实现的LRU算法 

   1. Redis 实现的是一种近似 LRU 算法，目的是为了更好的节约内存，它的实现方式是在 Redis 的对象结构体中添加一个额外的字段，**用于记录此数据的最后一次访问时间。**
   2. 当 Redis 进行内存淘汰时，会使用随机采样的方式来淘汰数据，它是随机取 5 个值（此值可配置），然后淘汰最久没有使用的那个。
   3. 节省了空间占用，提升了缓存性能
   4. 但是 LRU 算法有一个问题，无法解决缓存污染问题，比如应用一次读取了大量的数据，而这些数据只会被读取这一次，那么这些数据会留存在 Redis 缓存中很长一段时间，造成缓存污染。

3. Redis实现的LFU算法 最近最不常用的 Least Frequently Used

   1. LFU 算法相比于 LRU 算法的实现，多记录了**「数据的访问频次」**的信息

   2. 在 LRU 算法中，Redis 对象头的 24 bits 的 lru 字段是用来记录 key 的访问时间戳，因此在 LRU 模式下，**Redis可以根据对象头中的 lru 字段记录的值，来比较最后一次 key 的访问时间长，从而淘汰最久未被使用的 key。**

   3. 在 LFU 算法中，Redis对象头的 24 bits 的 lru 字段被分成两段来存储，高 16bit 存储 ldt(Last Decrement Time)，用来记录 key 的访问时间戳；**低 8bit 存储 logc(Logistic Counter)，用来记录 key 的访问频次。**

      注意，logc 并不是单纯的访问次数，而是**访问频次（访问频率），因为 logc 会随时间推移而衰减的**

#### 7. Redis缓存设计

##### 如何避免缓存雪崩、缓存击穿、缓存穿透？

###### 缓存雪崩

**大量缓存数据在同一时间过期（失效）时**，如果此时有大量的用户请求，都无法在 Redis 中处理，于是**全部请求都直接访问数据库，**从而导致数据库的压力骤增，严重的会造成数据库宕机，从而形成一系列连锁反应，造成整个系统崩溃，这就是缓存雪崩的问题。

发生原因和解决办法

- 大量数据同时过期

  - 随机打散缓存失效时间

  - 互斥锁

    - 当业务线程在处理用户请求时，如果发现访问的数据不在 Redis 里，就**加个互斥锁，保证同一时间内只有一个请求来构建缓存（从数据库读取数据，再将数据更新到 Redis 里），**当缓存构建完成后，再释放锁。未能获取互斥锁的请求，要么等待锁释放后重新读取缓存，要么就返回空值或者默认值。
    - 实现互斥锁的时候，**最好设置超时时间**，不然第一个请求拿到了锁，然后这个请求发生了某种意外而一直阻塞，一直不释放锁，这时其他请求也一直拿不到锁，整个系统就会出现无响应的现象。

  - 后台更新缓存

    - 让缓存“永久有效”，并**将更新缓存的工作交由后台线程定时更新。**

      - 但是**这并不意味着数据一直能在内存里**，当系统**内存紧张的时候，有些缓存数据会被“淘汰”，**而在缓存被“淘汰”到下一次后台定时更新缓存的这段时间内，业务线程**读取缓存失败就返回空值**，业务的视角就以为是数据丢失

        解决方法：

        1. 后台线程**不仅负责定时更新缓存，而且也负责频繁地检测缓存是否有效**，检测到缓存失效了，原因可能是系统紧张而被淘汰的，于是就要**马上从数据库读取数据，并更新到缓存**。
        2. 在业务线程**发现缓存数据失效后（缓存数据被淘汰），通过消息队列发送一条消息通知后台线程更新缓存**，后台线程收到消息后，在更新缓存前可以判断缓存是否存在，存在就不执行更新缓存操作**；不存在就读取数据库数据，并将数据加载到缓存。**

      - 业务刚上线的时候，我们·最好提前把数据缓起来，而不是等待用户访问才来触发缓存构建，这就是所谓的**缓存预热**，后台更新缓存的机制刚好也适合干这个事情。

- Redis故障宕机

  - 服务熔断机制或者请求限流机制
    - 服务熔断机制：**暂停业务应用对缓存服务的访问，直接返回错误，不用再继续访问数据库**，从而降低对数据库的访问压力，保证数据库系统的正常运行，然后等到 **Redis 恢复正常后，再允许业务应用访问缓存服务。**
    - 请求限流机制：**只将少部分请求发送到数据库进行处理，再多的请求就在入口直接拒绝服务，**等到 Redis **恢复正常并把缓存预热完**后，再解除请求限流的机制。
  - 构建Redis缓存高可靠集群
    - 通过**主从节点的方式构建 Redis 缓存高可靠集群**。如果 Redis 缓存的主节点故障宕机，从节点可以切换成为主节点，继续提供缓存服务，避免了由于 Redis 故障宕机而导致的缓存雪崩问题。

###### 缓存击穿

如果缓存中的**某个热点数据过期了**，此时**大量的请求访问了该热点数据**，就无法从缓存中读取，**直接访问数据库**，数据库很容易就被高并发的请求冲垮，这就是缓存击穿的问题。

- **互斥锁方案**（Redis 中使用 setNX 方法设置一个状态位，表示这是一种锁定状态），**保证同一时间只有一个业务线程请求缓存**，未能获取互斥锁的请求，要么等待锁释放后重新读取缓存，要么就返回空值或者默认值
- **不给热点数据设置过期时间，由后台异步更新缓存**，或者在热点数据**准备要过期前，提前通知后台线程**更新缓存以及重新设置过期时间；

###### 缓存穿透

当用户访问的数据，**既不在缓存中，也不在数据库中**，导致请求在访问缓存时，发现缓存缺失，再**去访问数据库时，发现数据库中也没有要访问的数据**，没办法构建缓存数据，来服务后续的请求。那么当有大量这样的请求到来时，数据库的压力骤增，这就是缓存穿透的问题。

- 业务误操作，缓存中的数据和数据库中的数据都被误删除了，所以导致缓存和数据库中都没有数据；

- 黑客恶意攻击，故意大量访问某些读取不存在数据的业务；

  

- 限制非法请求

- 设置空值或者默认值

- 使用布隆过滤器判断数据是否存在，避免通过查询数据库来判断数据是否存在。

  - 查询布隆过滤器说数据存在，并不一定证明数据库中存在这个数据，但是查询到数据不存在，数据库中一定就不存在这个数据


##### 如何设计一个缓存策略，可以动态缓存热点数据呢？

​	热点数据动态缓存的策略总体思路：**通过数据最新访问时间来做排名，并过滤掉不常访问的数据**，只留下经常访问的数据。在 Redis 中可以用 zadd 方法和 zrange 方法来完成排序队列和获取 200 个商品的操作。

##### 说说常见的缓存更新策略？

1. Cache Aside（旁路缓存）策略；（实际开发Redis 和 MySQL 的更新策略用的是 Cache Aside，另外用不了。）
2. Read/Write Through（读穿 / 写穿）策略；
3. Write Back（写回）策略；

###### 1. 旁路缓存

**应用程序直接与数据库、缓存交互，并负责对缓存的交互**

- 写策略：**先更新数据库中数据，再更新缓存**

  **写策略的步骤的顺序不能倒过来，否则「读+写」并发时，会出现缓存和数据库的数据不一致性的问题。**

  - 无论是「先更新数据库，再更新缓存」，还是「先更新缓存，再更新数据库」，这两个方案都存在并发问题，当两个请求并发更新同一条数据的时候，可能会出现缓存和数据库中的数据不一致的现象（无解）

  - 数据库更新成功，更新缓存失败，出现数据不一致问题

    解决方案（都是异步操作缓存）

    - **消息队列重试机制**
      - 将删除缓存要操作的数据加入到消息队列，由消费者来操作数据
        - 如果删除缓存失败，从消息队列里再次读取，并重新删除。超过一定次数，向业务层报错
        - 如果删除缓存成功，把数据从消息队列中移除，避免重复操作。
      - 优点：保证缓存一致性问题
      - 缺点：对代码入侵性比较强，因为需要改造原本业务的代码
    - **订阅MySQL binlog**+消息队列+重试缓存
      - **更新数据库成功，就会产生一条变更日志，记录在 binlog 里。** **订阅binlog日志，拿到具体要操作的数据，然后再执行缓存删除**，阿里巴巴开源的 Canal 中间件就是基于这个实现的。
      - **将binlog日志采集发送到MQ队列里面，然后编写一个简单的缓存删除消息者订阅binlog日志，根据更新log删除缓存，并且通过ACK机制确认处理这条更新log**，保证数据缓存一致性。**必须是删除缓存成功，再回 ack 机制给消息队列，否则可能会造成消息丢失的问题**，比如消费服务从消息队列拿到事件之后，直接回了 ack，然后再执行删除缓存操作的话，如果删除缓存的操作还是失败了，那么因为提前给消息队列回 ack了，就没办重试了。
      - 优点：规避了代码入侵的问题，保证缓存一致性问题
      - 缺点：引入组件较多，对运维有较高要求

- 读策略：

  - 如果读取的数据命中了缓存，则直接返回数据；
  - 如果读取的数据没有命中缓存，则从数据库中读取数据，然后将数据写入到缓存，并且返回给用户。

- 适合读多写少，不适合写多，否则会频繁的更新缓存数据，对缓存命中率有影响。

- 如果要提高缓存命中率

  - 一种做法是**在更新数据时也更新缓存**，**在更新缓存前先加一个分布式锁，因为这样在同一时间只允许一个线程更新缓存，**就不会产生并发问题了。当然这么做对于写入的性能会有一些影响；
  - 另一种做法同样也是**在更新数据时更新缓存，**只是给缓存加一个较短的过期时间，**这样即使出现缓存不一致的情况，缓存的数据也会很快过期，对业务的影响也是可以接受。

###### 2. 读穿/写穿策略

**应用程序只和缓存交互，不和数据库交互，由缓存和数据库交互。**

###### 读穿

先看能否命中缓存，没有命中，由缓存组件从数据库中读取数据，将数据写入缓存组件，最后缓存组件将数据返回给应用

###### 写穿

- 如果缓存中数据不存在，直接更新数据库，然后返回
- 如果缓存中数据已经存在，则更新缓存中的数据，并且由缓存组件同步更新到数据库中，然后缓存组件告知应用程序更新完成。

###### 3. 写回策略

Write Back（写回）策略在更新数据的时候，**只更新缓存，同时将缓存数据设置为脏的**，然后立马返回，并不会更新数据库。对于**数据库的更新，会通过批量异步更新**的方式进行。

- Write Back 策略特别适合**写多**的场景，因为发**生写操作的时候， 只需要更新缓存，就立马返回了**。
- 但是带来的问题是，数据不是强一致性的，而且会有数据丢失的风险，

#### 8. Redis实战

##### Redis 如何实现延迟队列？

使用有序集合（ZSet）的方式来实现延迟消息队列的，ZSet 有一个 Score 属性可以用来存储延迟执行的时间。

##### Redis 的大 key 如何处理？

指的是对应的value很大

- String 类型的值大于 10 KB；
- Hash、List、Set、ZSet 类型的元素的个数超过 5000个；

- 造成：
  - **客户端超时阻塞**。由于 Redis 执行命令是单线程处理，然后在操作大 key 时会比较耗时，那么就会阻塞 Redis，从客户端这一视角看，就是很久很久都没有响应。
  - **引发网络阻塞**。每次获取大 key 产生的网络流量较大，如果一个 key 的大小是 1 MB，每秒访问量为 1000，那么每秒会产生 1000MB 的流量，这对于普通千兆网卡的服务器来说是灾难性的。
  - **阻塞工作线程**。如果使用 del 删除大 key 时，会阻塞工作线程，这样就没办法处理后续的命令。
  - **内存分布不均。**集群模型在 slot 分片均匀情况下，会出现数据和查询倾斜情况，部分有大 key 的 Redis 节点占用内存多。

##### Redis 管道有什么用？

- 使用管道技术可以解决多个命令执行时的网络等待，它是把多个命令整合到一起发送给服务器端处理之后统一返回给客户端，这样就免去了每条命令执行后都要等待的情况，从而有效地提高了程序的执行效率。
- 技术也要注意避免发送的命令过大，或管道内的数据太多而导致的网络阻塞。
- 管道技术本质上是客户端提供的功能，而非 Redis 服务器端的功能

##### Redis 事务支持回滚吗？

​	**没有回滚机制；Redis并不一定保证原子性**。这里不支持事务回滚，指的是不支持事务运行时错误的事务回滚。

#### 如何实现redis原子性？

- redis 执行一条命令的时候是具备原子性的，因为 redis 执行命令的时候是单线程来处理的，不存在多线程安全的问题。
- **如果要保证 2 条命令的原子性的话，可以考虑用 lua 脚本，将多个操作写到一个 Lua 脚本中，Redis 会把整个 Lua 脚本作为一个整体执行，在执行的过程中不会被其他命令打断，从而保证了 Lua 脚本中操作的原子性**

#### 除了lua脚本有没有什么也能保证redis的原子性

- redis 事务也可以保证多个操作的原子性。
- 如果 redis 事务正常执行，没有发生任何错误，那么使用 MULTI 和 EXEC 配合使用，就可以保证多个操作都完成。
- 但是，如果事务执行发生错误了，就没办法保证原子性了。比如说 2 个操作，第一个操作执行成果了，但是第二个操作执行的时候，命令出错了，那事务并不会回滚，因为Redis 中并没有提供回滚机制。

因此，Redis 对事务原子性属性的保证情况：

- **Redis 事务正常执行，可以保证原子性；**
- **Redis 事务执行中某一个操作执行失败，不保证原子性**

##### 如何用 Redis 实现分布式锁的？

- **分布式锁是用于分布式环境下并发控制的一种机制，用于控制某个资源在同一时刻只能被一个应用所使用**

- Redis 本身可以被多个客户端共享访问，正好就是一个共享存储系统，可以用来保存分布式锁，而且 Redis 的读写性能高，可以应对高并发的锁操作场景。

- Redis 的 SET 命令有个 NX 参数可以实现「key不存在才插入」，可以用来实现分布式锁

- 加锁满足的三个条件:

  - Set命令带上nx选项来实现加锁 ——包含读取锁变量、检查锁变量值、设置锁变量值三个操作，需要以原子形式完成
  - set命令执行时加上ex/px选项设置过期时间——避免客户端拿到锁以后发生异常导致锁一直无法释放
  - 使用色图命令设置锁变量值时，每个客户端设置的值是一个唯一值，用于标识客户端——锁变量的值需要能区分来自不同客户端的加锁操作，以免在释放锁时，出现误释放操作

- 而解锁的过程就是将 lock_key 键删除（del lock_key），但不能乱删，要保证执行操作的客户端就是加锁的客户端。所以，解锁的时候，我们要先判断锁的 unique_value 是否为加锁客户端，是的话，才将 lock_key 键删除。

  - 解锁是有两个操作，这时就需要 Lua 脚本来保证解锁的原子性，因为 Redis 在执行 Lua 脚本时，可以以原子性的方式执行，保证了锁释放操作的原子性。

- 优缺点

  - 优点：性能高效、实现方便、避免单点故障

  - 缺点：超时事件难以选择、Redis 主从复制模式中的数据是异步复制的，这样导致分布式锁的不可靠

    - 如何解决集群情况下分布式锁的可靠性

      Redis 官方已经设计了一个分布式锁算法 Redlock（红锁）。它是基于多个 Redis 节点的分布式锁，即使有节点发生了故障，锁变量仍然是存在的，客户端还是可以完成锁操作。是让客户端和多个独立的 Redis 节点依次请求申请加锁，如果客户端能够和半数以上的节点成功地完成加锁操作，那么我们就认为，客户端成功地获得分布式锁，否则加锁失败。

      加锁成功要同时满足两个条件：

      - 客户端从超过半数（大于等于 N/2+1）的 Redis 节点上成功获取到了锁；
      - 客户端从大多数节点获取锁的总耗时（t2-t1）小于锁设置的过期时间。

​						释放锁和在单节点上释放锁的操作一样，只要执行释放锁的lua脚本就可以了。

## 数据类型篇

### 3.1 Redis常见数据类型和应用场景

#### String

- String 类型的底层的数据结构实现主要是 int 和 SDS（简单动态字符串）。

- 内部编码（encoding）有 3 种 ：int、raw和 embstr。

- embstr和raw编码都会使用SDS来保存值，但不同之处在**于embstr会通过一次内存分配函数**来分配一块连续的内存空间来保存redisObject和SDS，而**raw编码会通过调用两次内存分配函**数来分别分配两块空间来保存redisObject和SDS

  ​		embstr好处如下：

  - 内存分配次数降低

  - 释放只需要调用一次内存释放函数

  - 一块连续的内存，更好的利用CPU缓存提升性能

    缺点

  - 如果字符串的长度增加需要重新分配内存时，整个redisObject和sds都需要重新分配空间，所以embstr编码的字符串对象实际上是只读的，redis没有为embstr编码的字符串对象编写任何相应的修改程序。当我们对embstr编码的字符串对象执行任何修改命令（例如append）时，程序会先将对象的编码从embstr转换成raw，然后再执行修改命令。

##### String还是Hash？

- 对象存储方式**：String 存储的是序列化后的对象数据，存放的是整个对象，**操作简单直接。**Hash 是对对象的每个字段单独存储，可以获取部分字段的信息，**也可以修改或者添加部分字段，节省网络流量。如果对象中某些字段需要经常变动或者经常需要单独查询对象中的个别字段信息，Hash 就非常适合。
- 内存消耗**：Hash 通常比 String 更节省内存，特**别是在字段较多且字段长度较短时。Redis 对小型 Hash 进行优化（如使用 ziplist 存储），进一步降低内存占用。
- 复杂对象存储**：String 在处理多层嵌套或复杂结构的对象时更方便，因为无需处理每个字段的独立存储和操作**。
- 性能：**String 的操作通常具有 O(1) 的时间复杂度，因为它存储的是整个对象，**操作简单直接，整体读写的性能较好。**Hash 由于需要处理多个字段的增删改查操作，在字段较多且经常变动的情况下，可能会带来额外的性能开销。**

- 在绝大多数情况下，**String** 更适合存储对象数据，尤其是当对象结构简单且整体读写是主要操作时。
- 如果你需要频繁操作对象的部分字段或节省内存，**Hash** 可能是更好的选择。

##### 应用场景

###### 缓存对象

- 直接缓存整个对象的 JSON，命令例子： SET user:1 '{"name":"xiaolin", "age":18}'。
- 采用将 key 进行分离为 user:ID:属性，采用 MSET 存储，用 MGET 获取各属性值，命令例子： MSET user:1:name xiaolin user:1:age 18 user:2:name xiaomei user:2:age 20。

###### 常规计数

- Redis 处理命令是单线程，所以执行命令的过程是原子的。因此 String 数据类型适合计数场景，比如计算访问次数、点赞、转发、库存数量等等。

###### 分布式锁

- SET 命令有个 NX 参数可以实现「key不存在才插入」，可以用它来实现分布式锁。
- 通过使用 SET 命令和 Lua 脚本在 Redis 单节点上完成了分布式锁的加锁和解锁。

###### 共享session信息

- 借助 Redis 对这些 Session 信息进行统一的存储和管理，这样无论请求发送到那台服务器，服务器都会去同一个 Redis 获取相关的 Session 信息，这样就解决了分布式系统下 Session 存储的问题。

#### List

- List 列表是简单的字符串列表，按照插入顺序排序，可以从头部或尾部向 List 列表添加元素。
- 在 Redis 3.2 版本之后，List 数据类型底层数据结构就只由 quicklist 实现了，替代了双向链表和压缩列表

##### 消息队列

1. 消息保序
   1. List 可以使用 LPUSH + RPOP （或者反过来，RPUSH+LPOP）命令实现消息队列。（反之也可
   2. 问题：即使没有新消息写入List，消费者也要不停地调用 RPOP 命令，这就会导致消费者程序的 CPU 一直消耗在执行 RPOP 命令上，带来不必要的性能损失
      1. ，Redis提供了 BRPOP 命令。BRPOP命令也称为阻塞式读取，客户端在没有读到队列数据时，自动阻塞，直到有新的数据写入队列，再开始读取新数据
2. 处理重复的消息
   1. 每个消息要有一个全局id
   2. 消费者要记录已经处理过的消息id
3. 保证消息可靠性
   1. List 类型提供了 BRPOPLPUSH 命令，这个命令的作用是让消费者程序从一个 List 中读取消息，同时，Redis 会把这个消息再插入到另一个 List（可以叫作备份 List）留存。
4. 缺陷
   1. List 不支持多个消费者消费同一条消息，因为一旦消费者拉取一条消息后，这条消息就从 List 中删除了，无法被其它消费者再次消费。
      要实现一条消息可以被多个消费者消费，那么就要将多个消费者组成一个消费组，使得多个消费者可以消费同一条消息，但是 List 类型并不支持消费组的实现。
   2. Stream 同样能够满足消息队列的三大需求，而且它还支持「消费组」形式的消息读取

#### Hash

- Hash 是一个键值对（key - value）集合，其中 value 的形式如： value=[{field1，value1}，...{fieldN，valueN}]。Hash 特别适合用于存储对象。
  - 一般对象用 String + Json 存储，对象中某些频繁变化的属性可以考虑抽出来用 Hash 类型存储。
- 在 Redis 7.0 中，压缩列表数据结构已经废弃了，交由 listpack 数据结构来实现了

##### 应用场景

- 缓存对象

- 购物车

  - 用户 id 为 key

  - 商品 id 为 field，商品数量为 value

  - 用户添加商品就是往 Hash 里面增加新的 field 与 value；

    查询购物车信息就是遍历对应的 Hash；

    更改商品数量直接修改对应的 value 值（直接 set 或者做运算皆可）；

    删除商品就是删除 Hash 中对应的 field；

    清空购物车直接删除对应的 key 即可。

#### Set

- Set 类型是一个无序并唯一的键值集合，它的存储顺序不会按照插入的先后顺序进行存储。
- set\list区别
  - List 可以存储重复元素，Set 只能存储非重复元素；
    List 是按照元素的先后顺序存储元素的，而 Set 则是无序方式存储元素的
- Set 类型的底层数据结构是由哈希表或整数集合实现的
- Set 的差集、并集和交集的计算复杂度较高，在数据量较大的情况下，如果直接执行这些计算，会导致 Redis 实例阻塞。主从集群中，为了避免主库因为 Set 做聚合计算（交集、差集、并集）时导致主库被阻塞，我们可以选择一个从库完成聚合统计，或者把数据返回给客户端，由客户端来完成聚合统计

##### 应用场景

- 点赞

- 共同关注

- 抽奖活动

  - 如果想要使用 Set 实现一个简单的抽奖系统的话，直接使用下面这几个命令就可以了：
    SADD key member1 member2 ...：向指定集合添加一个或多个元素。
    SPOP key count：随机移除并获取指定集合中一个或多个元素，适合不允许重复中奖的场景。
    SRANDMEMBER key count : 随机获取指定集合中指定数量的元素，适合允许重复中奖的场景。

  

#### ZSet

- Zset 类型（有序集合类型）相比于 Set 类型多了一个排序属性 score（分值），对于有序集合 ZSet 来说，每个存储元素相当于有两个值组成的，一个是有序集合的元素值，一个是排序值。Zset 类型（Sorted Set，有序集合） 可以根据元素的权重来排序。
  - 有序集合保留了集合不能有重复成员的特性（分值可以重复），但有序集合中的元素可以排序
- 在 Redis 7.0 中，压缩列表数据结构已经废弃了，交由 listpack 数据结构来实现了。
- Zset 运算操作（相比于 Set 类型，ZSet 类型没有支持差集运算）

##### 底层为什么用跳表，而不用平衡树、红黑树或者b+树

这道面试题很多大厂比较喜欢问，难度还是有点大的。

- 平衡树 vs 跳表：平衡树的插入、删除和查询的时间复杂度和跳表一样都是 **O(log n)**。对于范围查询来说，平衡树也可以通过中序遍历的方式达到和跳表一样的效果。但是它的每一次插入或者删除操作都需要保证整颗树左右节点的绝对平衡，只要不平衡就要通过旋转操作来保持平衡，这个过程是比较耗时的。跳表诞生的初衷就是为了克服平衡树的一些缺点。跳表使用概率平衡而不是严格强制的平衡，因此，跳表中的插入和删除算法比平衡树的等效算法简单得多，速度也快得多。
- 红黑树 vs 跳表：相比较于红黑树来说，跳表的实现也更简单一些，不需要通过旋转和染色（红黑变换）来保证黑平衡。并且，按照区间来查找数据这个操作，红黑树的效率没有跳表高。
- B+树 vs 跳表：B+树更适合作为数据库和文件系统中常用的索引结构之一，它的核心思想是通过可能少的 IO 定位到尽可能多的索引来获得查询数据。对于 Redis 这种内存数据库来说，它对这些并不感冒，因为 Redis 作为内存数据库它不可能存储大量的数据，所以对于索引不需要通过 B+树这种方式进行维护，只需按照概率进行随机维护即可，节约内存。而且使用跳表实现 zset 时相较前者来说更简单一些，在进行插入时只需通过索引将数据插入到链表中合适的位置再随机维护一定高度的索引即可，也不需要像 B+树那样插入时发现失衡时还需要对节点分裂与合并。

##### 应用场景

排行榜、电话姓名排序

使用有序集合的 ZRANGEBYLEX 或 ZREVRANGEBYLEX 可以帮助我们实现电话号码或姓名的排序，我们以 ZRANGEBYLEX （返回指定成员区间内的成员，按 key 正序排列，分数必须相同）为例。**不要在分数不一致的 SortSet 集合中去使用 ZRANGEBYLEX和 ZREVRANGEBYLEX 指令，因为获取的结果会不准确。**

#### BitMap

- Bitmap，即位图，是一串连续的二进制数组（0和1），可以通过偏移量（offset）定位元素。BitMap通过最小的单位bit来进行0|1的设置，表示某个元素的值或者状态，时间复杂度为O(1)。
- Bitmap 本身是用 String 类型作为底层数据结构实现的一种统计二值状态的数据类型。String 类型是会保存为二进制的字节数组，所以，Redis 就把字节数组的每个 bit 位利用起来，用来表示一个元素的二值状态，你可以把 Bitmap 看作是一个 bit 数组。

##### 应用场景

- 签到统计：Redis 提供了 BITPOS key bitValue [start] [end]指令，返回数据表示 Bitmap 中第一个值为 bitValue 的 offset 位置。
- 判断用户登录态
- 连续签到用户总数

#### HyperlogLog

- 简单来说 HyperLogLog 提供不精确的去重计数。
- Redis HyperLogLog 优势在于只需要花费 12 KB 内存，就可以计算接近 2^64 个元素的基数，和元素越多就越耗费内存的 Set 和 Hash 类型相比，HyperLogLog 就非常节省空间。

##### 应用场景

百万级网页uv计数

- `PFADD key element1 element2 ...`：添加一个或多个元素到 HyperLogLog 中。
- `PFCOUNT key1 key2`：获取一个或者多个 HyperLogLog 的唯一计数。

1、将访问指定页面的每个用户 ID 添加到 `HyperLogLog` 中。

```
PFADD PAGE_1:UV USER1 USER2 ...... USERn
```

2、统计指定页面的 UV。

```
PFCOUNT PAGE_1:UV
```

#### GEO

- 主要用于存储地理位置信息，并对存储的信息进行操作。
- GEO 本身并没有设计新的底层数据结构，而是直接使用了 Sorted Set 集合类型。
  - GEO 类型使用 GeoHash 编码方法实现了经纬度到 Sorted Set 中元素权重分数的转换，这其中的两个关键机制就是「对二维地图做区间划分」和「对区间进行编码」。一组经纬度落在某个区间后，就用区间的编码值来表示，并把编码值作为 Sorted Set 元素的权重分数。

#### Stream

- 之前消息队列的实现方式的缺陷
  - 发布订阅模式，不能持久化也就无法可靠的保存消息，并且对于离线重连的客户端不能读取历史消息的缺陷；
  - List 实现消息队列的方式不能重复消费，一个消息消费完就会被删除，而且生产者需要自行实现全局唯一 ID。
- stream完美地实现消息队列，它**支持消息的持久化、支持自动生成全局唯一 ID、支持 ack 确认消息的模式、支持消费组模式**等，让消息队列更加的稳定和可靠

##### 常见命令

```sql
XADD：插入消息，保证有序，可以自动生成全局唯一 ID；
XLEN ：查询消息长度；
XREAD：用于读取消息，可以按 ID 读取数据；
XDEL ： 根据消息 ID 删除消息；
DEL ：删除整个 Stream；
XRANGE ：读取区间消息
XREADGROUP：按消费组形式读取消息；
XPENDING 和 XACK：
	XPENDING 命令可以用来查询每个消费组内所有消费者「已读取、但尚未确认」的消息；
	XACK 命令用于向消息队列确认消息处理已完成
```

##### 应用场景：消息队列

1. 消息保序：XADD/XREAD
2. 阻塞读取：XREAD block
3. 重复消息处理：Stream 在使用 XADD 命令，会自动生成全局唯一 ID；
4. 消息可靠性：内部使用 PENDING List 自动保存消息，使用 XPENDING 命令查看消费组已经读取但是未被确认的消息，消费者使用 XACK 确认消息；
5. 支持消费组形式消费数据

- stream基础方法，使用 xadd 存入消息和 xread 循环阻塞读取消息的方式可以实现简易版的消息队列

  - **生产者通过xadd命令插入消息**，返回消息id，id由两部分组成
    - 第一部分：当前服务器时间
    - 第二部分：当前毫秒内的消息序号
  - **消费者通过XREAD 命令从消息队列中读取消**息时，可以指定一个消息 ID，并从这个消息 ID 的**下一条消息**开始进行读取
  - 实现阻塞读（无消息时阻塞），使用XREAD设定BLOCK配置项

- stream特有功能

  - **Stream 可以以使用 XGROUP 创建消费组**，创建消费组之后，Stream 可以使用 XREADGROUP 命令让消费组内的消费者读取消息。

    - 同一个消费组里的消费者不能消费同一条消息
    - 不同消费组的消费者可以消费同一条消息
      - 但是有前提条件，创建消息组的时候，**不同消费组指定了相同位置开始读取消息**

  - 如何保证消费者在发生故障或宕机再次重启后，仍然可以读取未处理完的消息？

    - **Streams 会自动使用内部队列（也称为 PENDING List）留存消费组里每个消费者读取的消息，直到消费者使用 XACK 命令通知 Streams“消息已经处理完成”。**

      消费者可以在重启后，用 XPENDING 命令查看已读取、但尚未确认处理完成的消息。

- Redis 基于 Stream 消息队列与专业的消息队列有哪些差距？

  - 专业消息队列特点

    - 消息不丢失

      - 消息生产阶段：生产者 

        只要**处理好mq返回的ack确认响应**；**对返回异常进行消息重发**，不会出现丢失。

      - 消息消费阶段：消费者

        不会，因为 Stream （ MQ 中间件）会自动使用内部队列（也称为 PENDING List）**留存消费组里每个消费者读取的消息，但是未被确认的消息**。消费者可以在重启后，用 XPENDING 命令查看已读取、但尚未确认处理完成的消息。**等到消费者执行完业务逻辑后，再发送消费确认 XACK 命令，**也能保证消息的不丢失。

      - 消息存储阶段：消息中间件

        会，两种场景

        1. **AOF 持久化配置为每秒写盘，但这个写盘过程是异步的**，Redis 宕机时会存在数据丢失的可能
        2. **主从复制也是异步的**，**主从切换时，也存在丢失数据的可能**

      - 像 RabbitMQ 或 Kafka 这类专业的队列中间件，在**使用时是部署一个集群，生产者在发布消息时，队列中间件通常会写「多个节点」，也就是有多个副本，**即便其中一个节点挂了，也能保证集群的数据不丢失。

    - 消息可堆积

      - **Redis消息存储在内存中，这就意味着一旦发生消息积压，则会导致 Redis 的内存持续增长，**如果超过机器内存上限，就会面临被 OOM 的风险。
      - 提供了可以指定队列最大长度的功能，**超过上限后，旧消息会被删除**，只保留固定长度的新消息。这么来看，**Stream 在消息积压时，如果指定了最大长度，还是有可能丢失。**
      - 专业的消息队列它们的数据都是**存储在磁盘上，**当消息积压时，无非就是多占用一些磁盘空间。

  - Redis队列使用的两个问题：

    - Redis 本身可能会丢数据；
    - 面对消息挤压，内存资源会紧张；

- Redis 发布/订阅机制为什么不可以作为消息队列？

  发布订阅机制存在以下缺点，都是跟丢失数据有关

  - **没有基于任何数据类型实现，所以不具备「数据持久化」的能力，**也就是发布/订阅机制的相关操作，**不会写入到 RDB 和 AOF 中**，当 Redis 宕机重启，发布/订阅机制的数据也会全部丢失
  - “发后既忘”的工作模式，如果有订阅者**离线重连之后不能消费之前的历史消息**
  - 当消费端有一定的消息积压时，也就是**生产者发送的消息，消费者消费不过来时，如果超过 32M 或者是 60s 内持续保持在 8M 以上，消费端会被强行断开**

  发布/订阅机制只适合即时通讯的场景，比如**构建哨兵集群 (opens new window)的场景采用了发布/订阅机制。**

### 3.2  Redis数据结构

- String_SDS
- list_quicklist
- hash_listpack、哈希表
- set_哈希表、整数集合
- zset_listpack、跳表

#### 键值对数据库是怎么实现的

#### SDS

#### 链表

#### 压缩列表

#### 哈希表

#### 整数集合

#### 跳表

Redis 只有 Zset 对象的底层实现用到了跳表，**跳表的优势是能支持平均 O(logN) 复杂度的节点查找。**
zset 结构体里有两个数据结构：**一个是跳表，一个是哈希表**。这样的好处是既能进行高效的范围查询，也能进行高效单点查询。

```java
typedef struct zset {
dict *dict;
zskiplist *zsl;
} zset;
```

- Zset 对象在执行数据插入或是数据更新的过程中，会依次在跳表和哈希表中插入或更新相应的数据，从而保证了跳表和哈希表中记录的信息一致。
- Zset 对象能支持范围查询（如 ZRANGEBYSCORE 操作），这是因为它的数据结构设计采用了跳表，而又能以常数复杂度获取元素权重（如 ZSCORE 操作），这是因为它同时采用了哈希表进行索引。
- 可能很多人会奇怪，为什么我开头说 Zset 对象的底层数据结构是「压缩列表」或者「跳表」，而没有说哈希表呢？
- Zset 对象在使用跳表作为数据结构的时候，是使用由「哈希表+跳表」组成的 struct zset，但是我们讨论的时候，都会说跳表是 Zset 对象的底层数据结构，而不会提及哈希表，是因为 struct zset 中的哈希表只是用于以常数复杂度获取元素权重，大部分操作都是跳表实现的。

接下来，详细的说下跳表







#### quicklist

#### listpack

## 持久化篇

### 4.3 Redis大key对持久化有什么影响

#### 大key对AOF日志的影响

- 在使用 Always 策略的时候，主线程在执行完命令后，会把数据写入到 AOF 日志文件，然后会调用 fsync() 函数，将内核缓冲区的数据直接写入到硬盘，等到硬盘写操作完成后，该函数才会返回。
  - 如果写入是一个大 Key，**主线程在执行 fsync() 函数的时候，阻塞的时间会比较久**，因为当写入的数据量很大的时候，数据同步到硬盘这个过程是很耗时的。
- 当使用 **Everysec 策略**的时候，由于是**异步**执行 fsync() 函数，所以大 Key 持久化的过程（数据同步磁盘）**不会影响**主线程。
- 当使用 No 策略的时候，由于**永不执**行 fsync() 函数，所以大 Key 持久化的过程**不会影响主线程。**

#### 大key对AOF重写和RDB的影响

**AOF 重写机制和 RDB 快照（bgsave 命令）的过程，都会分别通过 fork() 函数创建一个子进程来处理任务**。会有两个阶段会导致阻塞父进程（主线程）：

1. 创建子进程的途中，由于要复制父进程的**页表**等数据结构，阻塞的时间跟页表的大小有关，页表越大，阻塞的时间也越长；
2. 创建完子进程后，如**果父进程修改了共享数据中的大 Key**，就会发**生写时复制**，**这期间会拷贝物理内存，由于大 Key 占用的物理内存会很大，**那么在复制物理内存这一过程，就会比较耗时，所以有可能会阻塞父进程

#### 如何避免大key

- 在设计阶段，就把大 key **拆分成一个一个小 key。**
- 或者，**定时检查** Redis 是否存在大 key ，如果该大 key 是可以删除的，不要使用 DEL 命令删除，因为该命令删除过程会阻塞主线程，**而是用 unlink 命令**（Redis 4.0+）删除大 key，**因为该命令的删除过程是异步的，不会阻塞主线程。**

## 高可用篇

### 6.1 主从复制是怎么实现的

- 两个持久化技术保证了即使在服务器重启的情况下也不会丢失数据（或少量损失）。
- 由于数据都是存储在一台服务器上，如果出事就完犊子了，比如：
  - 如果服务器发生了宕机，由于数据恢复是需要点时间，那么这个期间是**无法服务新的请求的；**
  - 如果这台服务器的硬盘出现了故障，可能**数据就都丢失了。**
- 要避免这种单点故障，最好的办法是将数据备份到其他服务器上，让这些服务器也可以对外提供服务，这样即使有一台服务器出现了故障，其他服务器依然可以继续提供服务

主从复制共有三种模式：**全量复制、基于长连接的命令传播、增量复制。**

#### 第一次同步

- 使用replicaof（Redis 5.0 之前使用 slaveof）命令形成主服务器和从服务器的关系。a replicaof b  a为b的从服务器
- 主从服务器间的第一次同步的过程可分为三个阶段：
  - 第一阶段是**建立链接、协商同步；**
    - 1.1 从服务器执行replica of
    - 1.2 从服务器给主服务器发送psync命令，请求进行数据同步，包含两个参数
      - 主服务器的runId 第一次为？
      - 复制进度offset 第一次为-1

    - 1.3 主服务器用fullresync作为响应命令返回给对方（全量复制）
      - 主服务器的runid
      - 目前的复制进度offset

    - 1.4 从服务器保存主服务器的信息

  - 第二阶段是**主服务器同步数据给从服务器**；
    - 2.1 执行bgsave命令，生成RDB文件
    - 2.2 传输RDB文件给从服务器
    - 2.3 从服务器清空当前数据，载入RDB
    - 这期间的写操作没有写入RDB文件中，导致主从服务器数据不一致，为了保证，将上面三个时间间隙中收到的写操作命令，**写入到replication buffer缓冲区里**

  - 第三阶段是**主服务器发送新写操作命令给从服务器**
    - 3.1 主服务器发送新的写操作命令
    - 3.2 从服务器接收写操作命令并执行


#### 命令传播

- 第一次同步之后，双方维护一个TCP连接，且是长连接。主服务器可以继续发送写操作命令传播给从服务器并令其执行，维护数据库状态一致。
- **基于长连接的命令传播，通过这种方式来保证第一次同步后的主从服务器的数据一致性。**

#### 分摊主服务器的压力

两个耗时操作：生成RDB文件和传输RDB文件

- 由于是通过 bgsave 命令来生成 RDB 文件的，那么主服务器就会**忙于使用 fork() 创建子进程，**如果主服务器的内存数据非大，在执行 fork() 函数时是会阻塞主线程的，从而使得 Redis 无法正常处理请求；
- 传输 RDB 文件**会占用主服务器的网络带宽**，会对主服务器响应命令请求产生影响。

解决方案：从服务器自己不仅可以接收主服务器的同步数据，自**己也可以同时作为主服务器的形式将数据同步给其他从服务器**。主服务器生成 RDB 和传输 RDB 的压力可以分摊到充当经理角色的从服务器。

#### 增量复制

网络连接断开，无法保证主从数据库一致。网络断开又恢复后，**主从服务器会采用增量复制的方式继续同步，也就是只会把网络断开期间主服务器接收到的写操作命令，同步给从服务器。**

1. 从服务器在恢复网络后，会发送 **psync 命令**给主服务器，此时的 psync 命令里的 offset 参数不是 -1；

2. 主服务器收到该命令后，然后用 CONTINUE 响应命令告诉从服务器接下来采用增量复制的方式同步数据；

3. 然后主服务**将主从服务器断线期间，所执行的写命令发送给从服务器，**然后从服务器执行这些命令。

   1. repl_backlog_buffer，是一个「环形」缓冲区，用于主从服务器断连后，从中找到差异的数据；

      1. 在主服务器**进行命令传播时，不仅会将写命令发送给从服务器，还会将写命令写入到 repl_backlog_buffer 缓冲区里**，因此 这个缓冲区里会保存着最近传播的写命令。

      2. 网络断开后，当从服务器重新连上主服务器时，从服务器会**通过 psync 命令将自己的复制偏移量 slave_repl_offset 发送给主服务器**，主服务器根据自己的 master_repl_offset 和 slave_repl_offset 之间的差距，然后来决定对从服务器执行哪种同步操作：

         1. 如果判断出**从服务器要读取的数据还在 repl_backlog_buffer 缓冲区里，那么主服务器将采用增量同步的方式；**
         2. 相反，如果判断出**从服务器要读取的数据已经不存在 repl_backlog_buffer 缓冲区**里，那么主服务器将采用全量同步的方式。

      3. 当主服务器在 repl_backlog_buffer 中找到主从服务器差异（增量）的数据后，就会将增量的数据写入到 replication buffer 缓冲区。

      4. repl_backlog_buffer 缓行缓冲区的默认大小是 1M，并且由于它是一个环形缓冲区，所以当缓冲区写满后，主服务器继续写入的话，就会覆盖之前的数据。因此，当主服务器的写入速度远超于从服务器的读取速度，缓冲区的数据一下就会被覆盖。

      5. 那么在网络恢复时，如果从服务器想读的数据已经被覆盖了，主服务器就会采用全量同步，这个方式比增量同步的性能损耗要大很。

      6. 所以要尽量避免全量同步-》调大repl_backlog_buffer的空间 最小的大小=second*write_size_per_second。

         second 为从服务器断线后重新连接上主服务器所需的平均 时间(以秒计算)。
         write_size_per_second 则是主服务器平均每秒产生的写命令数据量大小

   2. replication offset，**标记上面那个缓冲区的同步进度，主从服务器都有各自的偏移量**，主服务器使用 master_repl_offset 来记录自己「写」到的位置，从服务器使用 slave_repl_offset 来记录自己「读」到的位置。

#### 面试题

##### 怎么判断Redis某个节点是否能正常工作

Redis 判断节点是否正常工作，基本都是通过互相的 ping-pong 心态检测机制，如果有一半以上的节点去 ping 一个节点的时候没有 pong 回应，集群就会认为这个节点挂掉了，会断开与这个节点的连接。

Redis 主从节点发送的心态间隔是不一样的，而且作用也有一点区别：

- **Redis 主节点默认每隔 10 秒**对从节点发送 ping 命令，**判断从节点的存活性和连接状态**，可通过参数repl-ping-slave-period控制发送频率。
- **Redis 从节点每隔 1 秒**发送 replconf ack{offset} 命令，给主节点上报自身当前的复制偏移量，目的是为了：
- 实时**监测主从节点网络状态；**
- 上报自身复制偏移量， **检查复制数据是否丢失，** 如果从节点数据丢失， 再从主节点的复制缓冲区中拉取丢失数据

##### 主从复制架构中，过期key如何处理？

主节点处理了一个key或者通过淘汰算法淘汰了一个key，这个时间主节点模**拟一条del命令发送给从节点，从节点收到该命令后，就进行删除key的操作。**

#####  Redis 是同步复制还是异步复制？

Redis 主节点每次收到写命令之后，先写到内部的缓冲区，然后**异步发送给从节点**

##### 主从复制中两个 Buffer(replication buffer 、repl backlog buffer)有什么区别？

replication buffer 、repl backlog buffer 区别如下：

- 出现的阶段不一样：
  - repl backlog buffer 是在增量复制阶段出现，**一个主节点只分配一个 repl backlog buffer；**
  - replication buffer 是在全量复制阶段和增量复制阶段都会出现，**主节点会给每个新连接的从节点，分配一个 replication buffer；**
- 这两个 Buffer 都有大小限制的，当缓冲区满了之后，发生的事情不一样：
  - 当 repl backlog buffer 满了，因为是**环形结构，会直接覆盖起始位置数据;**
  - 当 **replication buffer 满了，会导致连接断开，删除缓存，从节点重新连接，重新开始全量复制**

##### 如何应对主从数据不一致？

为什么会主从数据不一致？

- 主从节点之间的命令复制是**异步**进行的，所以无法实现强一致性保证。
- 在主从节点命令传播阶段，主节点收到新的写命令后发送给从节点。主节点在自己本地执行完命令后，就会向客户端返回结果，**不会等从节点实际执行完命令后**。因此如果从节点还没有执行主节点同步过来的命令，主从节点间的数据就不一致了。

如何应对主从数据不一致？

- 尽量保证主从节点间的**网络连接状况**良好，避免主从节点在不同的机房
- 开发一个外部程序来**监控主从节点间的复制进度**
  - 如果某个从节点的**进度差值大**于我们预设的阈值，我们可以让客户端**不再和这个从节点连接进行数据读取**，这样就可以减少读到不一致数据的情况。不过，为了避免出现客户端和所有从节点都不能连接的情况，我们需要把复制进度差值的阈值设置得大一些
  - **Redis 的 INFO replication 命令可以查看主节点接收写命令的进度信息（master_repl_offset）和从节点复制写命令的进度信息（slave_repl_offset）**，所以，我们就可以开发一个监控程序，先用 INFO replication 命令查到主、从节点的进度，然后，我们用 master_repl_offset 减去 slave_repl_offset，这样就能得到从节点和主节点间的复制进度差值了

#####  主从切换如何减少数据丢失？

- 异步复制同步丢失

  Redis 配置里有一个参数 min-slaves-max-lag，表示一旦所有的从节点数据复制和同步的延迟都超过了 min-slaves-max-lag 定义的值，那么主节点就会拒绝接收任何请求。
  假设将 min-slaves-max-lag 配置为 10s 后，根据目前 master->slave 的复制速度，如果数据同步完成所需要时间超过10s，就会认为 master 未来宕机后损失的数据会很多，master 就拒绝写入新请求。这样就能将 master 和 slave 数据差控制在10s内，即使 master 宕机也只是这未复制的 10s 数据。
  那么对于客户端，当客户端发现 master 不可写后，我们可以采取降级措施，将数据暂时写入本地缓存和磁盘中，在一段时间（等 master 恢复正常）后重新写入 master 来保证数据不丢失，也可以将数据写入 kafka 消息队列，等 master 恢复正常，再隔一段时间去消费 kafka 中的数据，让将数据重新写入 master

- 集群脑裂产生数据丢失

  当主节点发现**「从节点下线的数量太多」**，或者**「网络延迟太大」**的时候，那么主节点会禁止写操作，直接把错误返回给客户端。

  当主节点发现从节点下线或者通信超时的总数量小于阈值时，那么禁止主节点进行写数据，直接把错误返回给客户端。主库连接的从库中至少有 N 个从库，和主库进行数据复制时的 ACK 消息延迟不能超过 T 秒，否则，原主库就会被限制接收客户端写请求，客户端也就不能在原主库中写入新数据了。等到新主库上线时，就只有新主库能接收和处理客户端请求，此时，新写的数据会被直接写到新主库中。而原主库会被哨兵降为从库，即使它的数据被清空了，也不会有新数据丢失。

##### 主从如何做到故障自动切换？

- 主节点挂了 ，从节点是无法自动升级为主节点的，这个过程需要**人工处理，在此期间 Redis 无法对外提供写操作。**
- 此时**，Redis 哨兵机制**就登场了，哨兵在发现主节点出现故障时，由哨兵**自动完成故障发现和故障转移，**并通知给应用方，从而实现高可用性

### 6.2 为什么要有哨兵

#### 为什么要有哨兵机制

哨兵（Sentinel）机制，它的作用是**实现主从节点故障转移**。它会监测主节点是否存活，如果发现主节点挂了，它就会**选举一个从节点切换为主节点**，并且**把新主节点的相关信息通知给从节点和客户端。**

#### 哨兵机制是如何工作的

哨兵其实是一个运行在特殊模式下的 **Redis 进程，所以它也是一个节点。**

1. **监控**
2. **选主**
3. **通知**

#### 如何判断主节点真的故障了

- 哨兵会每隔 1 秒给所有**主从节点**发送 PING 命令，当主从节点收到 PING 命令后，会发送一个响应命令给哨兵，这样就可以判断它们是否在正常运行。没收到就标记为**主观下线**
- 为了避免因为主节点系统压力大或者网络阻塞没有PING，一个哨兵判断为主观下线以后，向其他哨兵发起命令进行投票。当这个哨兵的赞同票数达到哨兵配置文件中的 quorum 配置项设定的值后，这时主节点就会被该哨兵标记为「**客观下线**」。quorum 的值一般设置为哨兵个数的二分之一加 1，例如 3 个哨兵就设置 2。

#### 由哪个哨兵进行主从故障转移

哪个哨兵节点**判断主节点为「客观下线」**，这个哨兵节点就是候选者，所谓的候选者就是**想当 Leader 的哨兵。**候选者会向其他哨兵发送命令，表明希望成为 Leader 来执行主从切换，并让所有其他哨兵对它进行投票。

#### 主从故障转移的过程是怎么样的

1. 第一步：在已下线主节点（旧主节点）属下的所有「从节点」里面，挑选出一个从节点，并将其转换为主节点。

   1. **过滤掉已经离线的从节点；**

   2. **过滤掉历史网络连接状态不好的从节点；**

   3. 将剩下的从节点，进行三轮考察：**优先级、复制进度、ID 号。**在**每一轮考察过程中，如果找到了一个胜出的从节点**，就将其作为新主节点。

      1. 第一轮考察：哨兵首先会根据从节点的优先级来进行排序，**优先级越小越高排名越靠前，**
      2. 第二轮考察：如果优先级相同，则查看复制的下标，哪个**从「主节点」接收的复制数据多，哪个就靠前。**
      3. 第三轮考察：如果优先级和下标都相同，就选择从节点 **ID 较小的那个**

      在发送 SLAVEOF no one 命令之后，哨兵 leader 会以每秒一次的频率向被升级的从节点发送 INFO 命令（没进行故障转移之前，INFO 命令的频率是每十秒一次），并观察命令回复中的角色信息，**当被升级节点的角色信息从原来的 slave 变为 master 时，哨兵 leader 就知道被选中的从节点已经顺利升级为主节点了**。

2. 第二步：让已下线主节点属下的所有「从节点」**修改复制目标**，修改为复制「新主节点」；

3. 第三步：**将新主节点的 IP 地址和信息，通过「发布者/订阅者机制」通知**给客户端；

   1. 这主要通过 Redis 的发布者/订阅者机制来实现的。**每个哨兵节点提供发布者/订阅者机制，客户端可以从哨兵订阅消息。**
   2. 客户端和哨兵建立连接后，客户端会订阅哨兵提供的频道。主从切换完成后，哨兵就会向 +switch-master 频道发布新主节点的 **IP 地址和端口的消息**，这个时候客户端就可以收到这条信息，然后用这里面的新主节点的 IP 地址和端口进行通信了。

4. 第四步：继续监视旧主节点，当这个旧主节点**重新上线时，将它设置为新主节点的从节点**

#### 哨兵集群是如何组成的

**哨兵节点之间是通过 Redis 的发布者/订阅者机制来相互发现的**

- 哨兵 A 把自己的 IP 地址和端口的信息发布到__sentinel__:hello 频道上，哨兵 B 和 C 订阅了该频道。那么此时，哨兵 B 和 C 就可以从这个频道直接获取哨兵 A 的 IP 地址和端口号。然后，哨兵 B、C 可以和哨兵 A 建立网络连接

哨兵集群会对「从节点」的运行状态进行监控，那哨兵集群如何知道「从节点」的信息？

- 主节点知道所有「从节点」的信息，所以哨兵会每 10 秒一次的频率向主节点发送 INFO 命令来获取所有「从节点」的信息。