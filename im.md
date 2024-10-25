# 01 基于SpringBoot的网页版即时通讯系统

- 项目简述：一个分布式的即时通讯系统，支持好友、群组、私聊、群聊、离线消息拉取、发送文件等功能。
- 项目技术：**SpringBoot + MyBatis-Plus + Netty + MySQL + Redis + RabbitMQ + MinIO**
- 项目内容：

1. 服务器支持集群化部署，每个服务器仅处理自身连接用户的请求，具有良好的横向拓展能力。
   - 每个服务器节点运行相同的软件栈，并且只处理分配给它的用户请求。这通常通过负载均衡器来实现，负载均衡器可以根据IP哈希、轮询或其他策略将用户请求分发到不同的服务器节点上。
   - 为了实现良好的横向扩展能力，系统设计时要考虑无状态化，即每个服务器节点上运行的应用程序不应该保存任何会话状态，所有持久化的数据应该存储在外部存储系统中（如数据库、缓存服务器等）。
   - **04 实现聊天功能-》消息推送底层分析**
2. 通过RabbitMQ的消息队列服务转发消息，进行流量削峰和解耦，并利用结果监听机制来确保消息的可靠投递。
   - **04 实现聊天功能-》接入消息推送**
   - RabbitMQ作为AMQP（Advanced Message Queuing Protocol）的中间件，可以用于发布/订阅模式或工作队列模式来处理消息。
   - 在流量高峰期间，可以通过将消息发送到队列中来削峰填谷，后端服务可以从队列中拉取消息并异步处理，从而缓解瞬时高并发压力。
   - 结果监听机制通常是指确认消息已被正确消费，如果未收到确认，则重新发送消息以确保消息的可靠传递。
3. 使用Redis缓存来减少数据库访问频率，记录好友关系、群组成员关系等，提高了系统响应速度和用户体验。
   - Redis是一个键值存储系统，可以用来缓存频繁访问的数据，例如好友关系或群组成员信息。
   - 当用户请求这些数据时，首先查询Redis，如果命中则直接返回，否则从数据库获取并将结果存储到Redis中供后续请求使用。
   - **02 数据库和架构设计中的缓存设计**
4. 实现用户登录鉴权功能，采用双Token机制确保用户身份的安全验证，提升了用户体验、保证了安全性。
   - **03 用户登录和鉴权**
   - 双Token机制通常指的是访问令牌（Access Token）和刷新令牌（Refresh Token）。用户首次登录时，系统颁发一个访问令牌和一个刷新令牌。
   - 访问令牌用于用户对资源的访问，而刷新令牌用于在访问令牌过期时换取新的访问令牌。这样既减少了每次请求时都需要重新认证的情况，也增加了安全性，因为即使访问令牌被盗用，也可以通过撤销刷新令牌来防止进一步的损害。
5. 使用WebSocket协议建立了客户端与服务端之间的长连接，保证了消息的实时性，并通过心跳机制维持连接的持久性。
   - WebSocket协议允许客户端和服务端之间建立持久连接，支持双向通信。
   - 心跳机制定期发送心跳包来检测连接是否仍然活跃，如果一段时间内没有接收到对方的心跳响应，则认为连接已断开，需要重新建立连接。
   - **04 实现聊天功能-》消息推送底层分析-》消息接收**
6. 使用基于HashMap实现的前缀树和字符串匹配算法，完成敏感词的实时过滤功能；利用MinIO作为文件存储解决方案，支持图片和其他多媒体内容的上传与管理。
   - **敏感词过滤：**
     - 前缀树节点结构定义
       - 每个节点包含一个标记（`isKeyWordsEnd`），用于指示是否为敏感词的结尾。
       - 一个子节点映射表（`subNodes`），用于存储字符到子节点的映射
     - 构建前缀树：
       创建一个空的根节点。
       对每一个敏感词，从第一个字符到最后一个字符，逐个字符地构建树的路径。
       每当遇到一个新的字符，就在当前节点添加一个子节点，并将当前节点移动到新添加的子节点上。
       当构建完一个敏感词后，标记最后一个节点为敏感词结束标志。
     - 文本过滤：
       初始化三个指针：
       begin指向待处理字符串的开始位置，
       position指向当前正在检查的字符，
       tempNode指向前缀树的当前节点（通常是根节点）。
       逐字符检查字符串中的字符是否与前缀树中的路径匹配。
       如果不匹配，那么从begin开始的部分字符串不是敏感词的一部分，可以将这部分字符串输出或保留。
       如果匹配，那么position向前移动，tempNode指向相应的子节点。
       如果到达敏感词的结尾（即tempNode标记为敏感词结束），则替换或处理该敏感词，并重置begin、position和tempNode的状态。
   - **MINIO**：跳转至**05文件、图片、语音消息**

# 02 数据库和架构设计

## 技术选型

框架

springboot、mybatisplus、netty

中间件

mysql（数据库）、redis（缓存）、rabbitmq（消息队列）、minio（文件存储）

## 开源组件

| **组件** | **是否必须** | **主要作用**                       |
| -------- | ------------ | ---------------------------------- |
| mysql    | 是           | 存储用户、群聊、消息等数据         |
| redis    | 是           | 数据缓存、消息队列                 |
| minio    | 否           | 存储文件，如头像、图片、语音、文件 |
| coturn   | 否           | 音视频通话时用于协助打洞和转发     |



## 数据库设计

1.1 VARCHAR
varchar 在 MySQL 中必须满足最大行宽度限制，也就是 65535(64k) 字节，而 varchar 本身是按字符串个数来定义的，在 MySQL 中使用 uft-8 字符集一个字符占用三个字节，所以单表 varchar 实际占用最大长度如下：
1）使用 utf-8 字符编码集 varchar 最大长度是 (65535-2)/3 = 21844 个字符（超过 255 个字节会有 2 字节的额外占用空间开销，所以减 2，如果是 255 以下，则减 1）。
2）使用 utf-8mb4 字符集，MySQL 中使用 utf-8mb4 字符集一个字符占用 4 个字节，所以 varchar 最大长度是 (65535-2)/4 = 16383 个字符（超过 255 个字节会有 2 字节的额外占用空间开销，所以减 2，如果是 255 以下，则减 1）。
注：如果使用 utf-8mb4 字符集时，有些需要存储 utf-8 字符的时候,还是会只占 3 字节，所以有时会比这个计算值能存更多个字符，因为 utf-8mb4 是 utf-8 的超集。
1.2 TEXT
最大限制也是 64k 个字节，但是本质是溢出存储，InnoDB 默认只会存放前 768 字节在数据页中，而剩余的数据则会存储在溢出段中,虽然也受单表 65535 最大行宽度限制，但 MySQL 表中每个 BLOB 和 TEXT 列实际只占其中的 5 至 9 个字节，其他部分将进行溢出存储。所以实际占用表最大行宽度为 9+2 字节，外加的是额外开销，跟表的实际宽度没有关系：
1）如果使用 utf-8 字符集，那么单字段占用最大长度也是 21844 个字符。
2）不过单表可以设置多个 text 字段，这就突破了单表最大行宽度 65535 的限制。
注：如果采用了新的行格式类型 Barracuda (梭子鱼)，该文件格式拥有新的两种行格式（compressed 和 dynamic），两种格式对 blob/text 字段采用完全溢出的方式，数据页中只存放 20 字，,其余的都存放在溢出段中。

| **表名**           | **表名（中文）** | **创建时机**   | **说明**                           | 索引                                                         |
| ------------------ | ---------------- | -------------- | ---------------------------------- | ------------------------------------------------------------ |
| im_user            | 用户表           | 用户注册       | 记录用户基本信息                   | 索引：id主键索引                                             |
| im_friend          | 好友表           | 添加好友       | 记录好友关系以及好友的昵称、头像   | 索引：<br />id主键索引、<br />user_id和friend_id的联合索引   |
| im_private_message | 私聊消息表       | 发送私聊消息   | 记录与好友之间的聊天消息           | 索引：<br />id主键索引、<br />send_id和receive_id的联合索引  |
| im_group           | 群组表           | 创建群组       | 记录群组信息                       | 索引：id主键索引                                             |
| im_group_member    | 群组成员表       | 邀请好友进群聊 | 记录群组中的成员信息               | 索引：<br />id主键索引、<br />group_id二级索引、<br />member_id二级索引 |
| im_group_message   | 群聊消息表       | 发送群聊消息   | 记录与群聊中的聊天消息             | 索引：<br />id主键索引、<br />receive_id（群聊id）二级索引   |
| im_sensitive_word  | 敏感词           | 后台创建       | 发送消息时如果匹配敏感词会变成'**' |                                                              |

```sql
create database box2im;
use box2im;

create table `im_user`(
    `id` bigint not null auto_increment primary key  comment 'id',
    `user_name` varchar(255) not null comment '用户名',
    `nick_name` varchar(255) not null comment '用户昵称',
    `head_image` varchar(255) default '' comment '用户头像',
    `head_image_thumb` varchar(255) default '' comment '用户头像缩略图',
    `password` varchar(255) not null comment '密码',
    `sex`  tinyint(1) default 0 comment '性别 0:男 1:女',
    `is_banned` tinyint(1) default 0 comment '是否被封禁 0:否 1:是',
    `reason` varchar(255) default ''  comment '被封禁原因',
    `type`  smallint default 1 comment '用户类型 1:普通用户 2:审核账户',
    `signature` varchar(1024) default '' comment '个性签名',
    `last_login_time`  datetime DEFAULT null comment '最后登录时间',
    `created_time` datetime DEFAULT CURRENT_TIMESTAMP comment '创建时间',
    unique key `idx_user_name`(user_name),
    key `idx_nick_name`(nick_name)
) ENGINE=InnoDB CHARSET=utf8mb4 comment '用户';

create table `im_friend`(
    `id` bigint not null auto_increment primary key  comment 'id',
    `user_id` bigint not null  comment '用户id',
    `friend_id` bigint not null  comment '好友id',
    `friend_nick_name` varchar(255) not null comment '好友昵称',
    `friend_head_image` varchar(255) default '' comment '好友头像',
    `created_time` datetime DEFAULT CURRENT_TIMESTAMP comment '创建时间',
    key `idx_user_id` (`user_id`),
    key `idx_friend_id` (`friend_id`)
) ENGINE=InnoDB CHARSET=utf8mb4 comment '好友';

create table `im_private_message`(
    `id` bigint not null auto_increment primary key comment 'id',
    `send_id` bigint not null  comment '发送用户id',
    `recv_id` bigint not null  comment '接收用户id',
    `content` text   comment '发送内容',
    `type`  tinyint(1) NOT NULL  comment '消息类型 0:文字 1:图片 2:文件 3:语音 4:视频 21:提示',
    `status` tinyint(1) NOT NULL   comment '状态 0:未读 1:已读 2:撤回 3:已读',
    `send_time` datetime DEFAULT CURRENT_TIMESTAMP comment '发送时间',
    key `idx_send_id` (`send_id`),
    key `idx_recv_id` (`recv_id`)
)ENGINE=InnoDB CHARSET=utf8mb4 comment '私聊消息';

create table `im_group`(
    `id` bigint not null auto_increment primary key comment 'id',
    `name` varchar(255) not null comment '群名字',
    `owner_id` bigint not null  comment '群主id',
    `head_image` varchar(255) default '' comment '群头像',
    `head_image_thumb` varchar(255) default '' comment '群头像缩略图',
    `notice` varchar(1024)  default '' comment '群公告',
    `is_banned` tinyint(1) default 0 comment '是否被封禁 0:否 1:是',
    `reason` varchar(255) default '' comment '被封禁原因',
    `deleted` tinyint(1) default 0   comment '是否已删除',
    `created_time` datetime default CURRENT_TIMESTAMP comment '创建时间'
)ENGINE=InnoDB CHARSET=utf8mb4 comment '群';

create table `im_group_member`(
    `id` bigint not null auto_increment primary key comment 'id',
    `group_id` bigint not null  comment '群id',
    `user_id` bigint not null  comment '用户id',
    `user_nick_name`  varchar(255) DEFAULT '' comment '用户昵称',
    `remark_nick_name` varchar(255) DEFAULT '' comment '显示昵称备注',
    `head_image` varchar(255) DEFAULT '' comment '用户头像',
    `remark_group_name` varchar(255) DEFAULT '' comment '显示群名备注',
    `quit` tinyint(1) DEFAULT 0  comment '是否已退出',
    `quit_time` datetime DEFAULT NULL comment '退出时间',
    `created_time` datetime DEFAULT CURRENT_TIMESTAMP comment '创建时间',
    key `idx_group_id`(`group_id`),
    key `idx_user_id`(`user_id`)
)ENGINE=InnoDB CHARSET=utf8mb4 comment '群成员';

create table `im_group_message`(
    `id` bigint not null auto_increment primary key comment 'id',
    `group_id` bigint not null  comment '群id',
    `send_id` bigint not null  comment '发送用户id',
    `send_nick_name` varchar(255) DEFAULT ''  comment '发送用户昵称',
    `recv_ids` varchar(1024) DEFAULT ''  comment '接收用户id,逗号分隔，为空表示发给所有成员',
    `content` text   comment '发送内容',
    `at_user_ids` varchar(1024) comment '被@的用户id列表，逗号分隔',
    `receipt` tinyint DEFAULT 0  comment '是否回执消息',
    `receipt_ok` tinyint DEFAULT 0  comment '回执消息是否完成',
    `type`  tinyint(1) NOT NULL  comment '消息类型 0:文字 1:图片 2:文件 3:语音 4:视频 21:提示' ,
    `status` tinyint(1) DEFAULT 0 comment '状态 0:未发出  2:撤回 ',
    `send_time` datetime DEFAULT CURRENT_TIMESTAMP comment '发送时间',
    key `idx_group_id` (group_id)
)ENGINE=InnoDB CHARSET=utf8mb4 comment '群消息';

create table `im_sensitive_word`(
    `id` bigint not null auto_increment primary key comment 'id',
    `content` varchar(64) not null  comment '敏感词内容',
    `enabled` tinyint DEFAULT 0 COMMENT '是否启用 0:未启用 1:启用',
    `creator` bigint DEFAULT NULL COMMENT '创建者',
    `create_time` datetime  DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间'
)ENGINE=InnoDB CHARSET=utf8mb4 comment '敏感词';
```

## 缓存设计

|                      key                       |            value             |                             备注                             |
| :--------------------------------------------: | :--------------------------: | :----------------------------------------------------------: |
|     1. im:cache:friend:[userId]:[friendId]     |          是否为好友          |     减少发送消息时判断是否为好友的次数，缓解数据库的压力     |
|                im:cache:group:                 |           群聊消息           |                                                              |
|           im:cache:group_member_ids            |          群聊成员id          |                                                              |
| 2. im:readed:group:position:[groupId]:[userId] | 该用户在该群已读的最大消息id | 群聊消息可能会存在部分用户已读，部分用户未读的情况，无法通过单一字段记录。考虑到写扩散造成的数据库IO读写压力，这里选择了通过redis去记录。记录了用户在某个群的已读的最大消息id后，只要是小于或等于这个id的消息都是已读消息，否则就是已读消息，而不必记录每条消息是否已读。 |
|                userId:serverId                 |  用户所连接的netty服务器id   |                                                              |
|                 max_server_id                  |      最大netty服务器id       |                                                              |

### 数据库和缓存一致性

见面试

## 项目结构

| **模块**    | **功能**     | **可运行** | **说明**                                                     |
| ----------- | ------------ | ---------- | ------------------------------------------------------------ |
| im-platform | 业务平台服务 | 是         | 负责接收前端的http请求，处理盒子IM的所有业务                 |
| im-server   | 消息推送服务 | 是         | 仅实现消息推送，不参与任何业务。其他服务(im-platform)需通过im-client与im-server通信 |
| im-client   | 消息推送sdk  | 否         | 集成到其他服务(im-platform)，使其能够与im-server通信，实现消息推送 |
| im-common   | 公共包       | 否         | 被所有后端模块引用                                           |
| im-web      | web页面      | 是         | web端页面                                                    |
| im-uniapp   | app页面      | 是         | 移动端页面，包括app、h5、微信小程序                          |

![image-20240820155351270](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20240820155351270.png)

# 03 用户登录和鉴权

## 1. 方案选择

- 方案一(session)：整合Spring security,同时将session缓存到redis实现集群化管理
- 方案二(token):  通过jwt生成token,每次请求都携带此token,后端通过拦截器解析token

两种方案各有优缺点，盒子IM早期采用的是方案一，但是遇到了两个问题：

- **同一个浏览器无法同时登陆多个账号：第2个账号登录时，会自动顶掉第一个账号的session**
- **ws连接校验不容易实现： session是通过http的header中的sessionId实现，因此与http协议是强捆绑关系，并不支持ws协议**

所以最后改用了方案二，也就是使用token的方式

## 2. 交互流程

### 2.1. 获取token

![image-20241020182651181](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020182651181.png)



主要步骤代码：

| **步骤** | **类名**        | **方法** |
| -------- | --------------- | -------- |
| 1,2,3,4  | UserServiceImpl | login    |



### 2.2. token校验

token的校验通过拦截器实现，对所有接口(除登录等个别接口)进行统一拦截校验：

![image-20241020182703364](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020182703364.png)

图中只是已请求用户消息接口为例，其他请求亦是如此。

主要步骤代码：

| **步骤** | **类名**        | **方法**  |
| -------- | --------------- | --------- |
| 2        | AuthInterceptor | preHandle |

**实现方式**

**1.1 步骤一：自定义拦截器**

自定义拦截器，即拦截器的实现类，一般有两种自定义方式：

1. 定义一个类，实现`org.springframework.web.servlet.HandlerInterceptor`接口。
2. 定义一个类，继承已实现了HandlerInterceptor接口的类，例如`org.springframework.web.servlet.handler.HandlerInterceptorAdapter`抽象类。

**1.2 步骤二：添加Interceptor拦截器到WebMvcConfigurer配置器中**

自定义配置器，然后实现WebMvcConfigurer配置器。

以前一般继承`org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter`类，不过SrpingBoot 2.0以上WebMvcConfigurerAdapter类就过时了。有以下2中替代方法：

1. 直接实现`org.springframework.web.servlet.config.annotation.WebMvcConfigurer`接口。（推荐）
2. 继承`org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport`类。但是继承WebMvcConfigurationSupport会让SpringBoot对mvc的自动配置失效。不过目前大多数项目是前后端分离，并没有对静态资源有自动配置的需求，所以继承WebMvcConfigurationSupport也未尝不可。



`AuthInterceptor` 会在请求到达目标资源之前被触发。具体来说，它会在如下时机起作用：

- 当一个请求进入控制器（Controller）中的某个方法之前，如果该方法配置了使用`AuthInterceptor`，那么拦截器的`preHandle`方法就会被执行。
- 如果`preHandle`方法返回`true`，则请求将继续执行；如果返回`false`，则请求将被终止，通常会返回一个未认证或未授权的响应。



### 2.3. 刷新token

accessToken的过期时间只有半个小时，而refreshToken7天才会过期。

当accessToken过期时，需要调用/refreshToken接口，换取新的token。

![image-20241020182719418](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020182719418.png)

主要步骤代码：

| **步骤** | **类名**        | **方法**     |
| -------- | --------------- | ------------ |
| 1,2,3,4  | UserServiceImpl | refreshToken |



## 3. 用户无感知刷新token

上一小节中介绍了当token过期时如何去换取新的token,那么问题来了，程序应该如何感知到token已经过期了呢？

- 方案一：前端用定时器对token的过期时间进行检测，如果快过期了，则进行token刷新
- 方案二：通过前端的http拦截器实现，用户发请求时被此拦截器捕获，此时如果发现token过期时，进行刷新token,然后重新发起请求

显然方案二更为优雅，所以盒子IM采用是方案二。

主要实现代码(前端):

| **目录**          | **文件名**     | **方法** |
| ----------------- | -------------- | -------- |
| im-web/src/api/   | httpRequest.js | 所有     |
| im-uniapp/common/ | request.js     | 所有     |

# 04 ​实现聊天功能

## im-client相关API介绍

API列表

| **API**            | **说明**     |
| ------------------ | ------------ |
| sendPrivateMessage | 推送私聊消息 |
| sendGroupMessage   | 推送群聊消息 |
| sendSystemMessage  | 推送系统消息 |
| isOnline           | 用户是否在线 |

私聊消息API参数

| **参数**      | **类型**      | **必填** | **说明**                              |
| ------------- | ------------- | -------- | ------------------------------------- |
| sender        | IMUserInfo    | 是       | 发送方的信息,包括发送方的id和终端类型 |
| recvId        | Long          | 是       | 接收方id                              |
| recvTerminals | List<Integer> | 否       | 接收者的终端类型,默认全部             |
| sendToSelf    | Boolean       | 否       | 是否发送给自己的其他终端,默认true     |
| sendResult    | Boolean       | 否       | 是否需要回推发送结果,默认true         |
| data          | 泛型          | 是       | 消息内容，类型由应用层定义            |

群聊消息API参数

| **参数**      | **类型**      | **必填** | **说明**                                    |
| ------------- | ------------- | -------- | ------------------------------------------- |
| sender        | IMUserInfo    | 是       | 发送方的信息,包括发送方的id和终端类型       |
| recvIds       | List<Long>    | 否       | 接收者id列表(一般填群成员id,为空则不会推送) |
| recvTerminals | List<Integer> | 否       | 接收者终端类型,默认所有类型                 |
| sendToSelf    | Boolean       | 否       | 是否需要同时推送给自己的其他终端,默认true   |
| sendResult    | Boolean       | 否       | 是否需要回推发送结果,默认true               |
| data          | 泛型          | 是       | 消息内容，类型由应用层定义                  |

系统消息API参数

| **参数**      | **类型**      | **必填** | **说明**                                    |
| ------------- | ------------- | -------- | ------------------------------------------- |
| recvIds       | List<Long>    | 否       | 接收者id列表(一般填群成员id,为空则不会推送) |
| recvTerminals | List<Integer> | 否       | 接收者终端类型,默认所有类型                 |
| sendResult    | Boolean       | 否       | 是否需要回推发送结果,默认true               |
| data          | 泛型          | 是       | 消息内容，类型由应用层定义                  |

快速接入

后端接入

1. 在im-platform的pom.xml中引入im-client依赖

```xml
<dependency>
  <groupId>com.bx</groupId>
  <artifactId>im-client</artifactId>
  <version>3.0.0</version>
</dependency>
```

2. 消息推送使用了redis充当MQ进行通信,所以要在application.yml中配置redis地址

```yaml
spring:
  redis:
    host: 127.0.0.1
    port: 6379
```

3. 调用IMClient的API进行消息推送

```java
@Autowired
private IMClient imClient;

public void sendMessage(){
    IMPrivateMessage<PrivateMessageVO> sendMessage = new IMPrivateMessage<>();
    // 发送方的id和终端类型
    sendMessage.setSender(new IMUserInfo(1L, IMTerminalType.APP.code()));
    // 对方的id
    sendMessage.setRecvId(2L);
    // 推送给对方所有终端
    sendMessage.setRecvTerminals(IMTerminalType.codes());
    // 同时推送给自己的其他类型终端
    sendMessage.setSendToSelf(true);
    // 需要回推发送结果，将在IMListener接收发送结果
    sendMessage.setSendResult(true);
    // 推送的内容
    sendMessage.setData(msgInfo);
    // 推送消息
    imClient.sendPrivateMessage(sendMessage);
}
```

4.  监听发送结果

如果需要监听消息推送的结果，需要以下两步：

- 编写消息监听类，实现MessageListener,并加上@IMListener 
- 发送消息时指定sendResult字段为true

监听器类代码样例：

```java
@Slf4j
@IMListener(type = IMListenerType.PRIVATE_MESSAGE)
public class PrivateMessageListener implements MessageListener {

    @Override
    public void process(IMSendResult<PrivateMessageVO> result){
        PrivateMessageVO messageInfo = result.getData();
        if(result.getCode().equals(IMSendCode.SUCCESS.code())){
            log.info("消息发送成功，消息id:{}，发送者:{},接收者:{},终端:{}",messageInfo.getId(),result.getSender().getId(),result.getReceiver().getId(),result.getReceiver().getTerminal());
        }
    }
}
```

前端接入

1. 将wssocket.js放到im-web中的/api目录

2. 接收消息

实现聊天功能

| **类名**                  | **方法**    | **说明**                                        |
| ------------------------- | ----------- | ----------------------------------------------- |
| PrivateMessageServiceImpl | sendMessage | 推送私聊消息                                    |
| GroupMessageServiceImpl   | sendMessage | 推送群聊消息                                    |
| PrivateMessageListener    | process     | 监听私聊消息结果,消息发送成功后，状态修改为送达 |
| GroupMessageListener      | process     | 监听群聊消息结果,暂时没实际作用                 |

## :star:  接入消息推送（实现聊天功能）

消息推送功能由im-server实现，并且已经封装了sdk(im-client)。现在我们把消息推送功能看成一个黑盒，暂时不深究它的具体实现，本小节的目标是在im-platform中集成im-client,集成之后，im-platform只需要调用im-client的api,就能够把消息推送到前端。

发消息

- **implatform**

  1. **消息入库：存进mysql ，此时消息状态为messageStatus的unsend**
  2. **调用imsender中的sendprivatemessage**

- **imclient-sendprivatemessage**

  - **给对方发**

    - **在线**

      **投递消息：把消息存储至redis等待拉取推送**

    - 不在线

      **设置消息为imsendcode中的not_online，messagelistenerMulticaster向业务层的监听器广播消息推送结果：调用privateMessageListener(把成功的消息处理了，失败的消息继续保持unsend)**

  - **给自己发**

    - **终端在线**

      **将数据存储至redis等待拉取推送**

    - 不在线

- **imserver**

  - **通过PullPrivateMessageTask循环从redis中拉取私聊消息，然后将消息交由PrivateMessageProcessor处理**
    - **通过接收方id取出对方的channel,通过channel将消息推送给用户**
      - **送成功设置消息成为imsendcode中的success**
      - **推送失败消息成为imsendcode中的notfindchannel**（对方当前不在线，消息即使投递到了im-server也会由于缺少对方的channel而无法推送。）
    - **无论推送成功还是失败，都会回推消息推送结果到消息结果队列**
      - **privateMessageProcessor向redis回推私聊消息推送结果**
      - **PrivateMessageResultResultTask从redis中定时拉取私聊消息推送结果**
      - **messagelistenerMulticaster向业务层的监听器广播消息推送结果**
        - 推送成功：从unsend变为sended
        - 未成功：继续保持unsend

拉取离线消息

​	从mysql中拉取，调用imsender中的sendprivatemessage，将拉取的所有消息全部重发（**未发送**状态的消息也会在用户登录时，作为离线消息被用户主动拉取。）

## 消息推送底层分析

我们已经通过im-client的sendPrivateMessage接口轻松实现了聊天功能，但是现在这个接口的内部实现对我们来说还是一个未知的"黑盒"。在这一章节中，我们就来打开这个"黑盒"，看看调用sendPrivateMessage之后，具体干了什么事情。

### 消息推送

交互流程

用户发送私聊消息完整流程：

![image-20241020184545556](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020184545556.png)

当消息的发送者和接收者连的不是同一个im-server时，消息是无法直接推送的，所以我们需要设计出能够支持跨节点推送的方案:

- 利用了redis的list类型实现消息推送，其中key为im:message:private:${serverid},每个key的数据可以看做一个队列,每个im-server根据自身的id只消费属于自己的消息队列
- 用户发起ws连接时，redis会记录每个用户所连接的serverid
- 用户发送消息时，im-client将根据接收方的serverid,决定将消息推向响应的队列

图中2~5步都是调用sendPrivateMessage之后的流程，整个流程涉及3个组件:

- **im-client**：客户端部分，负责将消息推送至redis,对应图中橙色部分
- **im-server**:  服务器部分，负责从redis拉取消息，并通过ws推送给用户，对应图中红色部分
- **redis**: 中间角色，充当消息中间件，负责将消息从im-client转发到im-server

客户端部分（im-client）

核心流程逻辑说明：

- 通过redis获取接收方id所绑定的serverid(每个im-server都会生成一个唯一的id)
- 当serverid存在表示用户在线，反之则说明用户离线
- 如果用户在线，则将消息上传到redis，等待im-server拉取。注意上传消息的key是通过serverid生成的，也就是说，不同的im-server使用不同的消息队列，这么做是为了支持im-server集群化部署。
- 如果用户离线，则向应用层回复消息发送失败状态**（消息推送结果监听机制下面有介绍）**

| **类名** | **方法**           | **作用**     |
| -------- | ------------------ | ------------ |
| IMSender | sendPrivateMessage | 推送私聊消息 |
| IMSender | sendGroupMessage   | 推送群聊消息 |

服务器部分（im-server）

核心流程逻辑：

- 循环从redis中拉取私聊消息，然后将消息交由PrivateMessageProcessor处理
- 通过接收方id取出对方的channel,通过channel将消息推送给用户
- 无论推送成功还是失败，都会回推消息推送结果

| **类名**                | **方法**    | **作用**                  |
| ----------------------- | ----------- | ------------------------- |
| PullPrivateMessageTask  | pullMessage | 循环从redis中拉取私聊消息 |
| PrivateMessageProcessor | process     | 处理拉取到的私聊消息      |
| GroupMessageProcessor   | process     | 处理拉取到的群聊消息      |

### 消息接收

im-server已经通过ws向前端推送消息了，那么前端是如何跟im-server交互，并接收到消息呢？

![image-20241020184630812](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020184630812.png)

| **步骤** | **类**                  | **方法**           | **说明**                            |
| -------- | ----------------------- | ------------------ | ----------------------------------- |
| 1        | IMChannelHandler        | handlerAdded       | 用户连接事件，这里只打印日志        |
| 2        | LoginProcessor          | process            | 登录处理，将用户id和serverId绑定    |
| 3        | HeartbeatProcessor      | process            | 心跳处理，绑定关系续命              |
| 3        | IMChannelHandler        | userEventTriggered | 心跳超时事件，关闭channel           |
| 4,5      | PrivateMessageProcessor | process            | 私聊消息发送处理，前面已讲解        |
| 4,5      | GroupMessageProcessor   | process            | 私聊消息发送处理，前面已讲解        |
| 6        | IMChannelHandler        | handlerRemoved     | 连接断开事件，移除channel，解除绑定 |

| 前端**文件** | **方法** | **说明**     |
| ------------ | -------- | ------------ |
| wssocket.js  | 全部     | 前端ws封装   |
| home.vue     | init     | ws应用初始化 |

### 推送结果监听机制

在im-platform调用了sendPrivateMessage之后，是否一定能将成功消息推送给用户呢？

答案是否定的，例如对方当前不在线，消息即使投递到了im-server也会由于缺少对方的channel而无法推送。

作为一个相对完善的消息推送组件，应该能够提供一种机制：不管消息是否成功发送，都应该告知调用消息推送是否成功。一般来说，实现有两种方案：

| **实现方式** | **同步方式**                                     | **异步方式**                                                 |
| ------------ | ------------------------------------------------ | ------------------------------------------------------------ |
| 实现思路     | 接口同步返回结果，发送成功返回true,否则返回false | 消息发送后，回推结果消息，将发送结果异步投递到业务层的监听器。 |
| 优点         | 调用简单                                         | 调用复杂                                                     |
| 缺点         | 性能较低，实现比较复杂                           | 性能好，同时也易于复杂                                       |

目前采用的的是**异步方式，**回推消息结果流程其实与推送消息类似，只是流程"反"过来：

![image-20241020184654000](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020184654000.png)

| **步骤** | **类**                         | **方法**    | **说明**                          |
| -------- | ------------------------------ | ----------- | --------------------------------- |
| 1        | PrivateMessageProcessor        | sendResult  | 向redis回推私聊消息推送结果       |
| 1        | GroupMessageProcessor          | sendResult  | 向redis回推群聊消息推送结果       |
| 2        | PrivateMessageResultResultTask | pullMessage | 从redis中定时拉取私聊消息推送结果 |
| 2        | GroupMessageResultResultTask   | pullMessage | 从redis中定时拉取群聊消息推送结果 |
| 3        | MessageListenerMulticaster     | multicast   | 向业务层的监听器广播消息推送结果  |

## 离线消息和已读未读显示

### 消息状态

消息状态类型

维护消息状态是实现离线消息和已读未读功能的关键**，**参考枚举类型**MessageStatus**，消息共有4个状态

| **状态**       | **状态值** | **状态变更时机**             |
| -------------- | ---------- | ---------------------------- |
| 未发送(UNSEND) | 0          | 消息入库后默认状态就是UNSEND |
| 已发送(SENDED) | 1          | 消息成功推送到客户端后       |
| 已撤回(RECALL) | 2          | 用户撤回消息后               |
| 已读(READED)   | 3          | 用户看到消息后(点击聊天列表) |

消息状态记录

**私聊消息**和**群聊消息**的状态记录采用的是不同的方案。

- **私聊消息**的记录状态相对比较简单，消息表有status字段，直接存数据库中即可。
- **群聊消息**可能会存在部分用户已读，部分用户未读的情况，无法通过单一字段记录。考虑到写扩散造成的数据库IO读写压力，这里我选择了通过redis去记录:

**KEY:**    **im:readed:group:position:{groupId}:{userId}**

**VALUE:   该用户在该群已读的最大消息id**

记录了用户在某个群的已读的最大消息id后，只要是小于或等于这个id的消息都是已读消息，否则就是已读消息，而不必记录每条消息是否已读。

### 离线消息

拉取离线消息

离线消息的实现并不复杂，核心思路是前端通过localstorge缓存了已接收消息的最大id，在用户登录后，**主动向服务器拉取比这个消息id大的所有消息。**

前端相关代码：

| **文件** | **方法**                  | **作用**                 |
| -------- | ------------------------- | ------------------------ |
| home.vue | pullPrivateOfflineMessage | 向服务器拉取私聊离线消息 |
| home.vue | pullGroupOfflineMessage   | 向服务器拉取群聊离线消息 |

后端相关代码：

| **类**                    | **方法**           | **说明**                                    |
| ------------------------- | ------------------ | ------------------------------------------- |
| PrivateMessageServiceImpl | pullOfflineMessage | 拉取最近1个月的离线私聊消息，最多拉取1000条 |
| GroupMessageServiceImpl   | pullOfflineMessage | 拉取最近1个月的离线群聊消息，最多拉取1000条 |

离线消息被拉取后，消息状态修改为**已发送**状态。

### 已读未读显示

已读状态变更

用户在页面进行以下之一操作，都会将当前会话的消息变更为已读状态：

- 点击了某个会话列表，进入会话聊天窗口
- 鼠标在聊天窗口滑动
- 回复了对方消息

前端相关代码：

| **文件**    | **方法**      | **作用**                               |
| ----------- | ------------- | -------------------------------------- |
| ChatBox.vue | readedMessage | 向服务器请求变更消息状态为**已读**状态 |

后端相关代码：

| **类**                    | **方法**      | **说明**                                    |
| ------------------------- | ------------- | ------------------------------------------- |
| PrivateMessageServiceImpl | readedMessage | 将会话中的所有私聊消息都更改为**已读**状态  |
| GroupMessageServiceImpl   | readedMessage | 在redis中记录用户在群聊里的**已读**消息位置 |

离线状态变更

目前消息内容和状态都是缓存到前端的localstorage，这样也带来了一个问题：web端接收到消息后，用户没看到消息就退出了，随后用户在app端登录并看到了消息，此时消息变更为**已读**状态，但是web端的缓存依然记录为未读状态。

为了解决这个问题，在前端加入主动更新状态机制：在用户打开聊天窗口时，主动从服务器拉取最大已读消息id,并将前端缓存中小于这个id的消息更新为**已读**状态

前端相关代码：

| **文件**    | **方法**   | **作用**                 |
| ----------- | ---------- | ------------------------ |
| ChatBox.vue | loadReaded | 向服务器拉取已读最大消息 |

后端相关代码：

| **类**                    | **方法**       | **说明**                       |
| ------------------------- | -------------- | ------------------------------ |
| PrivateMessageServiceImpl | getMaxReadedId | 获取某个会话中已读消息的最大id |



###  回执消息(群聊已读未读)

方案选择

群聊的消息默认不会显示已读人数，只有当用户手动点亮“消息回执”图标后，发送的消息才会显示已读人数。

为什么不像私聊消息那样，每条消息都显示已读人数呢？

假如在一个500人的群发了100条**回执消息**，群里的用户每看到一条消息，都会自动发送一条回执信令，那么消息的扩散量就是100*500x500=2500w。显然如果存在过量的**回执消息，**对系统的消耗是相当大的。

最终是参考了企业微信的设计，通过让用户手动开启的方式，可以明显降低**回执消息**的数量。

 技术实现

群组中存在多个用户，很可能会存在A用户已读，B用户未读的情况，所以群消息的状态，不能像私聊消息一样，简单的用一个消息状态字段存储。

方案一：存到数据库中，im_group_message表增加一个readed_user_ids字段，将已读的用户id全部记录到数据到里面。

方案二：存到redis中，使用HashMap结构。大KEY: **im:readed:group:position:{groupId}**, 小KEY: **{userId}**, 值:  **已读的最大消息id**

根据前面讨论的消息扩散情况，如果采用方案一，每条消息将会对数据库进行500次更新操作，可能会对数据库造成一定压力，所以我们选择了性能更强劲的redis去记录回执消息的已读状态，即方案二。

在方案二中，**为每个群聊分配一个大KEY，该KEY记录了一个群聊中所有用户的已读消息的位置。**

> 1. **大Key** (`im:readed:group:position:{groupId}`)：这是哈希的主键名称，它标识了一个具体的群组。`{groupId}`是一个占位符，实际使用时会被替换为具体的群组ID。
> 2. **小Key** (`{userId}`)：这是哈希内部的一个字段名，用于标识具体的用户。`{userId}`同样是一个占位符，表示用户的唯一标识符。
> 3. **值** (已读的最大消息id)：这是对应于每个用户的小Key的具体数值，表示该用户在这个群组中已读过的消息的最大ID。
>
> 例如，如果你有一个群组ID为`12345`，并且有两个用户，其用户ID分别为`A`和`B`，那么你的Redis哈希结构可能会是这样的：
>
> ```
> > HSET im:readed:group:position:12345 A 100
> (integer) 1
> > HSET im:readed:group:position:12345 B 150
> (integer) 1
> ```
>
> 假如有一个4人小群，redis记录该群的数据如下：
>

| **用户名** | **用户id** | **最大已读消息id** |
| ---------- | ---------- | ------------------ |
| 张三       | 2          | 120                |
| 李四       | 3          | 200                |
| 王五       | 4          | 180                |
| 老六       | 5          | 170                |

当用户点击了一条id为165消息，想查看已读用户列表时，服务器只需取出这个大KEY的数据，筛出最大已读消息id大于等于165的用户列表,即“李四”、“王五”、“老六”

相关代码：

| **类**                  | **方法**        | **说明**                   |
| ----------------------- | --------------- | -------------------------- |
| GroupMessageServiceImpl | readedMessage   | 记录用户在群里已读消息位置 |
| GroupMessageServiceImpl | findReadedUsers | 获取某条消息的已读成员列表 |



## 消息可靠性、顺序性、唯一性

MQ进行消息异步推送有一个绕不开的话题，消息的可靠性、顺序性，还有唯一性如何保证？

- **可靠性**：只要消息推送接口调用成功，用户最终一定能够收到消息。
- **顺序性**：保证用户收到的消息跟推送时是一致的，不会发生乱序。
- **唯一性**：也称幂等性。保证用户不会收到重复的消息。

### 可靠性

盒子IM采用的是"最少一次"的消息推送策略。由于已经实现了推送结果监听机制,消息无论是否推送成功，都会在监听器接收到推送结果。在监听器里，我们把推送成功的消息记录为**已发送**状态。

```java
@Slf4j
@IMListener(type = IMListenerType.PRIVATE_MESSAGE)
public class PrivateMessageListener implements MessageListener<PrivateMessageVO> {
    @Lazy
    @Autowired
    private IPrivateMessageService privateMessageService;
    @Override
    public void process(List<IMSendResult<PrivateMessageVO>> results) {
        Set<Long> messageIds = new HashSet<>();
        for(IMSendResult<PrivateMessageVO> result : results){
            PrivateMessageVO messageInfo = result.getData();
            // 更新消息状态,这里只处理成功消息，失败的消息继续保持未读状态
            if (result.getCode().equals(IMSendCode.SUCCESS.code())) {
                messageIds.add(messageInfo.getId());
                log.info("消息送达，消息id:{}，发送者:{},接收者:{},终端:{}", messageInfo.getId(), result.getSender().getId(), result.getReceiver().getId(), result.getReceiver().getTerminal());
            }
        }
        // 批量修改状态
        if(CollUtil.isNotEmpty(messageIds)){
            UpdateWrapper<PrivateMessage> updateWrapper = new UpdateWrapper<>();
            updateWrapper.lambda().in(PrivateMessage::getId, messageIds)
                    .eq(PrivateMessage::getStatus, MessageStatus.UNSEND.code())
                    .set(PrivateMessage::getStatus, MessageStatus.SENDED.code());
            privateMessageService.update(updateWrapper);
        }
    }
}
```

发起消息推送后一种可能会出现4种情形：

| **序号** | **消息推送** | **结果监听** | **出现情形之一**             | **消息最终状态** |
| -------- | ------------ | ------------ | ---------------------------- | ---------------- |
| 1        | 成功         | 成功         | 正常情况                     | **已发送**       |
| 2        | 失败         | 成功         | 用户离线\掉线                | **未发送**       |
| 3        | 成功         | 失败         | 服务在完成消息推送后立刻宕机 | **未发送**       |
| 4        | 失败         | 失败         | 服务在消息推送前宕机         | **未发送**       |

- **情况1**： 正常情况，消息会在监听器变更为**已发送**状态
- **情况2**：也算正常情况，用户不在线时，消息无法推送，此时监听器也会收到推送失败的结果。从上面的监听器代码可以看到不会处理推送失败的消息，消息依旧保持**未发送**状态
- **情况3**：异常情况, 一般是服务器宕机或重启才会出现。此时消息已完成推送,但是还未来得及回推结果服务器就挂了，监听器不会收到推送结果，所以消息保持默认的**未发送**状态
- **情况4**：异常情况, 一般是服务器宕机或重启才会出现。此时消息在完成推送前服务器就挂了，监听器同样不会收到推送结果，所以消息保持默认的**未发送**状态

除了**情况1**，其他情况的消息最终都是**未发送**状态，**未发送**状态的消息会在用户登录时，作为离线消息被用户主动拉取。也就是说，消息都会最终投递到前端。比较特殊的是**情况3**，因为消息已经推送给用户了，用户在重新登录时，又会作为**离线消息**被用户主动拉取，此时用户收到了重复消息。

对于重复消息的讨论，我们留到唯一性讨论。

![image-20241020184741577](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/image-20241020184741577.png)

### 顺序性

消息的有序性分为**全局有序**和**局部有序。**

- **全局有序：**保证整个系统的所有消息都是有序的，实现比较简单粗暴，通常是将消息放到一个队列里面。但吞吐量比较低，非必要不建议使用。
- **局部有序：**对于IM系统来说，局部有序可以理解为保证单一聊天会话内的消息有序即可。实现方式是在业务层控制相同会话的消息进入相同的队列。局部有序的方案可获得更高的吞吐量。

盒子IM采用的**局部有序**的方案，即保证单一会话内的消息有序。我们可以回顾一下消息推送的流程，以下两点设计是保证消息顺序性的关键：

- 消息投递的队列是由接收方的用户id决定，相同的用户的消息会进入相同的队列

- im-server通过单线程同步方式顺序处理队列的消息

- **步骤1,2:** 出现:“后发先至”情况，即用户后面发的消息被服务器先处理了。

  前端发送消息的请求先放入一个先进先出的队列中，只有当前面的消息发送完成后，才发送下一条消息

- **步骤3.4:** im-server从redis拉取到2,3,4号消息，没有按照顺序推送

  1.同一个会话的消息会进入相同的队列，保证了从redis取出的消息是有序的。

  2.而且必须取出来的消息完成发送后，才会去拉取下一批消息，保证了不会在多个线程同时推送消息

- **步骤5：**有一种极端情况，当"张三"上线时，拉取id为100-200的离线消息，当推送到150号消息时，“李四”发来了201号消息，此时消息顺序变成100...150,201,151...200

  是会有这种情况，所以还需要在客户端做最后一层保证。即根据消息id大小进行判断，如果接受到的消息id<maxId，则需要将消息插入到会话中合适的位置，而不是直接插入到最后

### 唯一性

前面讨论可靠性时提过，盒子IM采用的是"最少一次"的消息推送策略。也就是说，在极端情况下，是有可能出现将重复的消息投递给用户。

其实盒子IM的服务器端并未对消息的唯一性做校验，而是交给了前端进行处理。1前端处理也十分简单：每条消息都携带了唯一的id,接收到消息后，利用id判断消息是否已存在，如果已存在，则丢弃或覆盖即可。

/**利用缓存将消息的messageId存储起来，当重发过后对消息进行去重，来防止消息重复，同时设置一个过期时间，来释放空间**

+布隆过滤器



### 私聊+群聊

当我们的im-platform集成im-client之后，实现私聊和群聊功能就会变得非常简单，通过简单的crud以及调用api即可实现。

以下是聊天相关的业务代码（代码比较简单，小伙伴可以自行阅读）：

| **类名**                  | **方法**    | **说明**                                        |
| ------------------------- | ----------- | ----------------------------------------------- |
| PrivateMessageServiceImpl | sendMessage | 推送私聊消息                                    |
| GroupMessageServiceImpl   | sendMessage | 推送群聊消息                                    |
| PrivateMessageListener    | process     | 监听私聊消息结果,消息发送成功后，状态修改为送达 |
| GroupMessageListener      | process     | 监听群聊消息结果,暂时没实际作用                 |

# 05 文件、图片、语音消息

### 1. 文件上传

#### 1.1. 整合minio

图片、视频、语音、文档本质上都是文件，要实现文件消息的推送，首先我们需要一个文件存储服务器。盒子使用Minio作为文件存储服务。

整合Minio并不复杂，基本上遵循三步走战略即可：导包、配置、调接口，网上也有相当多案例和资料，这里不再重复讲述。

盒子内部对Minio的api作了简单的进一步封装：

| **类**    | **方法**        | **说明**               |
| --------- | --------------- | ---------------------- |
| MinioUtil | bucketExists    | 查看存储bucket是否存在 |
| MinioUtil | makeBucket      | 创建存储bucket         |
| MinioUtil | setBucketPublic | 设置bucket权限为public |
| MinioUtil | upload          | 文件上传               |
| MinioUtil | remove          | 文件删除               |

#### 1.2. 文件上传

文件上传编写了两个接口:

| **类**      | **方法**    | **说明**                     |
| ----------- | ----------- | ---------------------------- |
| FileService | uploadFile  | 上传普通文件                 |
| FileService | uploadImage | 上传图片同时返回原图和缩略图 |

**注：除了在全屏展示大图时使用原图，前端其他所有地方都是展示缩略图**

#### 1.3. 极速秒传

小伙伴们是否体验过百度云的极速秒传功能，几G大的文件瞬间就上传完成了，神奇的不得了。

其实实现起来也非常简单，只需要在上传前对文件的内容生成md5值，后端拿到这个md5去数据库(上传文件后需要入库)查询是否已经存在相同的文件，如果已存在，则直接返回文件原来的url，然后客户端给文件打上"极速秒传"标签。

不过这个功能还是很有实际意义的，能够有效减少重复文件带来的带宽和服务器磁盘压力。

**盒子目前未实现这块功能，建议小伙伴们可以自行实现**

#### 1.4 分片

常见的分片算法有哪些？
分片算法主要解决了数据被水平分片之后，数据究竟该存放在哪个表的问题。
常见的分片算法有：

- 哈希分片：求指定分片键的哈希，然后根据哈希值确定数据应被放置在哪个表中。哈希分片比较适合随机读写的场景，不太适合经常需要范围查询的场景。哈希分片可以使每个表的数据分布相对均匀，但对动态伸缩（例如新增一个表或者库）不友好。
- 范围分片：按照特定的范围区间（比如时间区间、ID 区间）来分配数据，比如 将 id 为 1~299999 的记录分到第一个表， 300000~599999 的分到第二个表。范围分片适合需要经常进行范围查找且数据分布均匀的场景，不太适合随机读写的场景（数据未被分散，容易出现热点数据的问题）。
- 映射表分片：使用一个单独的表（称为映射表）来存储分片键和分片位置的对应关系。映射表分片策略可以支持任何类型的分片算法，如哈希分片、范围分片等。映射表分片策略是可以灵活地调整分片规则，不需要修改应用程序代码或重新分布数据。不过，这种方式需要维护额外的表，还增加了查询的开销和复杂度。
- 一致性哈希分片：将哈希空间组织成一个环形结构，将分片键和节点（数据库或表）都映射到这个环上，然后根据顺时针的规则确定数据或请求应该分配到哪个节点上，解决了传统哈希对动态伸缩不友好的问题。
- 地理位置分片：很多 NewSQL 数据库都支持地理位置分片算法，也就是根据地理位置（如城市、地域）来分配数据。
- 融合算法分片：灵活组合多种分片算法，比如将哈希分片和范围分片组合

#### 1.5 分片键如何选择？

分片键（Sharding Key）是数据分片的关键字段。分片键的选择非常重要，它关系着数据的分布和查询效率。一般来说，分片键应该具备以下特点：

- 具有共性，即能够覆盖绝大多数的查询场景，尽量减少单次查询所涉及的分片数量，降低数据库压力；
- 具有离散性，即能够将数据均匀地分散到各个分片上，避免数据倾斜和热点问题；
- 具有稳定性，即分片键的值不会发生变化，避免数据迁移和一致性问题；
- 具有扩展性，即能够支持分片的动态增加和减少，避免数据重新分片的开销。
- 实际项目中，分片键很难满足上面提到的所有特点，需要权衡一下。并且，分片键可以是表中多个字段的组合，例如取用户 ID 后四位作为订单 ID 后缀。

#### 1.6 分库分表会带来什么问题呢？

引入分库分表之后，会给系统带来什么挑战呢？

- join 操作：同一个数据库中的表分布在了不同的数据库中，导致无法使用 join 操作。这样就导致我们需要手动进行数据的封装，比如你在一个数据库中查询到一个数据之后，再根据这个数据去另外一个数据库中找对应的数据。不过，很多大厂的资深 DBA 都是建议尽量不要使用 join 操作。因为 join 的效率低，并且会对分库分表造成影响。对于需要用到 join 操作的地方，可以采用多次查询业务层进行数据组装的方法。不过，这种方法需要考虑业务上多次查询的事务性的容忍度。
- 事务问题：同一个数据库中的表分布在了不同的数据库中，如果单个操作涉及到多个数据库，那么数据库自带的事务就无法满足我们的要求了。这个时候，我们就需要引入分布式事务了。关于分布式事务常见解决方案总结，网站上也有对应的总结：https://javaguide.cn/distributed-system/distributed-transaction.html 。
- 分布式 ID：分库之后， 数据遍布在不同服务器上的数据库，数据库的自增主键已经没办法满足生成的主键唯一了。我们如何为不同的数据节点生成全局唯一主键呢？这个时候，我们就需要为我们的系统引入分布式 ID 了。关于分布式 ID 的详细介绍&实现方案总结，可以看我写的这篇文章：分布式 ID 介绍&实现方案总结。
- 跨库聚合查询问题：分库分表会导致常规聚合查询操作，如 group by，order by 等变得异常复杂。这是因为这些操作需要在多个分片上进行数据汇总和排序，而不是在单个数据库上进行。为了实现这些操作，需要编写复杂的业务代码，或者使用中间件来协调分片间的通信和数据传输。这样会增加开发和维护的成本，以及影响查询的性能和可扩展性。

[读写分离和分库分表详解 | JavaGuide](https://javaguide.cn/high-performance/read-and-write-separation-and-library-subtable.html#分库分表有没有什么比较推荐的方案)

### 2. 文件、图片、语音消息

我们可以仿照文字消息接口，为文件、图片、语音消息分别编写单独的消息接口。

但盒子采用了更简便的方式：

直接复用原先编写好的的文字消息接口，将文件、图片、语音的url以及其他信息以json的格式存储到content字段中。

文件消息流程如下：

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/1704597862514-7f34272f-6429-4c89-9f78-d90f528708b9.jpeg" alt="img" style="zoom: 33%;" />

由于不同类型的文件展示要求不同，content中包含的内容也不一样：

| **类型** | **content字段**  | **说明**             |
| -------- | ---------------- | -------------------- |
| 文件     | name             | 文件名               |
| size     | 文件大小(字节)   |                      |
| url      | 文件url          |                      |
| 图片     | originUrl        | 原图(大图展示时显示) |
| thumbUrl | 缩略图(默认展示) |                      |
| 语音     | duration         | 语音时长(秒)         |
| url      | 语音文件url      |                      |

### 3. 语音采集

前端的语音采集使用了开源插件[js-audio-recorder](https://www.npmjs.com/package/js-audio-recorder)，感兴趣的小伙伴们可以自行去了解。

前端相关代码：

| 文件          | 方法 | 功能     |
| ------------- | ---- | -------- |
| ChatVoice.vue | 全部 | 录音采集 |

# 06 性能压测和分析

### 1. 压测设备环境

本次压测使用的是我个人的开发电脑，核心配置如下：

| **环境** | **配置**                  |
| -------- | ------------------------- |
| 操作系统 | windows10 专业版          |
| CPU      | AMD 锐龙R5 3500x 6核      |
| 内存     | 金士顿骇客DDR4 3200hz 16G |
| 磁盘     | 西部数据SSD 500G          |

**如果条件允许，建议使用专业的linux企业级服务器压测效果更佳！**



**2.压测前准备**

我们本次压测的目标是服务器的性能，所以应避免前端的干扰。

压测时应由一台独立的设备接收消息，或者像作者这样屏蔽掉前端的处理代码，只打印数量**：**

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/1725108297912-7a3610df-4450-4ab5-8724-37b7af8b41bc.png" alt="img" style="zoom:33%;" />



### 2. 压测私聊接口

#### 2.1. 压测条件

| **条件** | **说明**                              |
| -------- | ------------------------------------- |
| 压测工具 | jmeter                                |
| 压测请求 | 私聊接口（对方web端在线，移动端离线） |
| 压测线程 | 10                                    |
| 压测数量 | 10万                                  |

#### 2.2. 压测脚本

[📎压测私聊消息.jmx](https://www.yuque.com/attachments/yuque/0/2023/jmx/1743561/1703773073690-3f21f485-c0fb-4089-a956-66c782fc8533.jmx)8

#### 2.3. 压测结果

| 请求异常       | 0%                           |
| -------------- | ---------------------------- |
| 数据丢失       | 0%                           |
| 吞吐量         | 1978qps                      |
| 压测时设备状态 | cpu100%,内存37%，io利用率56% |

jmeter压测截图：

![img](D:\2024\Notes\Typora\项目rpc+im\1703773896091-94eb6ffb-4fe3-4e7d-9fcc-0fa68a862fd7.png)

前端收到全部10w条消息：

<img src="D:\2024\Notes\Typora\项目rpc+im\1703773940902-0aa001b3-c94d-489f-8577-8e050f4ced88.png" alt="img" style="zoom:33%;" />

以下是压测时设备状态，其im-platform占用26.3%cpu,im-server占用11.2%cpu：

<img src="https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/1703777055921-b15fe6e0-4461-4404-ade4-d40363648cd9.png" alt="img" style="zoom: 50%;" />

### 3. 压测群聊接口

#### 3.1. 压测条件

| **条件** | **说明**                                       |
| -------- | ---------------------------------------------- |
| 压测工具 | jmeter                                         |
| 压测请求 | 群聊接口（群聊成员10人，其中5人在线，5人离线） |
| 压测线程 | 10                                             |
| 压测数量 | 10万                                           |

#### 3.2. 压测脚本

[📎压测群聊消息.jmx](https://www.yuque.com/attachments/yuque/0/2023/jmx/1743561/1703775694008-c88e2b90-41f6-4b1c-8d23-a5eb3796e171.jmx)

#### 3.3. 压测结果

| 请求异常       | 0%                            |
| -------------- | ----------------------------- |
| 数据丢失       | 0%                            |
| 吞吐量         | 1546qps                       |
| 压测时设备状态 | cpu100%，内存36%，io利用率58% |

jmeter压测截图：

<img src="D:\2024\Notes\Typora\项目rpc+im\1703775146663-198332ff-b7a9-4443-9aed-3b2a7e7c6557.png" alt="img" style="zoom:50%;" />

同样收到了全部消息

<img src="D:\2024\Notes\Typora\项目rpc+im\1703775129553-b41eddb7-0c2c-4a32-862f-0d47acc59bfe.png" alt="img" style="zoom:50%;" />

im-platform进程占用24.7%cpu,im-server占用17.0%：

<img src="D:\2024\Notes\Typora\项目rpc+im\1703776781124-6be4e529-aa49-449b-a478-f8c1206f30ac.png" alt="img" style="zoom:50%;" />

### 4. 分析以及优化

#### 4.1. 分析

由上面的结果不难看出, 无论私聊还是群聊，**"木桶的短板"都在cpu这里，而且是我们的jvm进程占用了主要的cpu资源。**

如何进一步分析是哪里占用这么高的cpu呢，这里推荐使用阿里开源的[arthas](http://arthas.gitee.io/)工具进行分析。

压测时通过arthas执行以下指令，可以得到jvm 30秒的CPU火焰图:

profiler start  -d 30

这是我生成的火焰图：

<img src="D:\2024\Notes\Typora\项目rpc+im\1703779832951-9db95445-2f67-4349-b0b3-ed655b4bc4bd.png" alt="img" style="zoom:50%;" />

从图中可以看到，在业务代码中，大部分的cpu都消耗在lettuce的调用上面，而lettuce最终调用了Unsafe.park。了解并发编程的同学应该知道，Unsafe.park会让线程阻塞，是需要进行内核上下文切换的，是比较消耗cpu的操作。

也就是说，每访问一次redis，都会产生一次上下文切换。那么发送一条私聊消息，需要访问多少次redis呢？

| **操作**             | **访问次数** | **说明**                              |
| -------------------- | ------------ | ------------------------------------- |
| 判断对方是否我的好友 | 1            | 作为mysql缓存，压测时基本都会命中缓存 |
| 判断对方是否在线     | 2            | web端+移动端分别判断一次              |
| 推送消息             | 2            | 一推一拉，共2次                       |
| 回推消息结果         | 2            | 一推一拉，共2次                       |
| 总共                 | 7            |                                       |

按照上面压测的结果，qps接近2000，意味着1秒中需要访问2000x7=14000次redis，cpu上下文也要切换14000次！

#### 4.2. 优化建议

这里有小伙伴可能会问：既然lettuce这么消耗cpu,换成其他redis客户端（如jedis、redission）能解决问题吗？

不行，其他客户端一样会有这个问题，原理都是一样的。

事实上只要是通过网络访问远程服务器，且是同步返回结果的sdk，都需要进行阻塞等待，比如jdbc访问mysql,只是mysql请求量比较小,没有暴露问题而已。

其实代码层面的优化空间已经不大了，如果小伙伴们对性能要求比较高，这里给出我的两个优化建议：

- 采用MQ代替redis推送消息，MQ可以实现异步发送，订阅拉取消息，不需要阻塞等待。个人推荐RocketMQ,其次Kakfa和RabbitMQ。
- 直接化身氪金大佬，升级硬件设备(纵向扩展)或者进行多机部署(横向扩展)

### 5. 总结

#### 5.1. 如何估算qps

不少小伙伴都问过我这样一个问题：

**如果我们要搭建一个可以支撑100万用户的IM系统，qps应该达到多少才能稳定运行呢？**

对于这个问题，我在这里统一谈谈我个人的见解。

首先，我们需要先要对自己系统用户状态进行一个大致的估算,假设以下是估算的结果：

| 最大同时在线人数占比 | 30%  |
| -------------------- | ---- |
| 正在发送消息人数占比 | 20%  |
| 平均发一条消息耗时   | 15s  |

可以计算出: qps = 1000000 x 0.3 x 0.2 / 15 = 4000

但是系统还有其他业务，且系统应留有一定的冗余度应对突发流量，在计算的结果乘以一个系数，比如乘以2，即压测要求达到8000qps，基本能保证系统问题运行

### 6. 最后说明

1.以上是作者使用自己开发电脑压测的结果，在不同的环境下压测的结果可能完全不一样，小伙伴们应该根据自身服务器环境的压测结果进行精准优化。

2.最近发现有些小伙伴直接用作者线上环境进行压测，还请各位不要这样做，因为作者购买的只是一个轻量级服务器，无法承受太高的压力，而且会产生很多垃圾数据。

为满足各位的求知欲，我自己对线上环境简单压测了一遍，结果如下：

| 服务器 | 华为轻量级云服务器 |
| ------ | ------------------ |
| 配置   | 4核8G              |
| QPS    | 约1800             |

如果小伙伴们还是要坚持用作者的环境进行压测，en...我也没招，请自觉v作者50作为数据清理的费用

# 07 RabbitMQ

## 1. 和其他MQ对比

常用的MQ
1️⃣ ActiveMQ
优点：**单机吞吐量万级，**时效性 ms 级，可用性高，基于主从架构实现高可用性，消息可靠性较低的概率丢失数据
缺点：官方社区现在对 ActiveMQ 5.x **维护越来越少**，高吞吐量场景较少使用。
2️⃣ Kafka
大数据的杀手锏，谈到大数据领域内的消息传输，则绕不开 Kafka，这款为大数据而生的消息中间件，以其百万级 TPS 的吞吐量名声大噪，迅速成为大数据领域的宠儿，在数据采集、传输、存储的过程中发挥着举足轻重的作用。目前已经被 **LinkedIn，Uber, Twitter, Netflix 等**大公司所采纳。
**优点：性能卓越，吞吐量高，单机写入 TPS 约在百万条/秒，**时效性 ms 级，可用性非常高；其次 kafka 是分布式的**，一个数据多个副本，少数机器宕机，不会丢失数据导致服务不可用，**消费者采用 Pull 方式获取消息，消息有序，通过控制能够保证所有消息被消费且仅被消费一次。此外 kafka 有优秀的第三方 Kafka Web 管理界面 Kafka-Manager，在日志领域比较成熟，被多家公司和多个开源项目使用；最后 kafka 在功能支持方便面它功能较为简单，主要支持简单的 MQ 功能，在大数据领域的实时计算以及日志采集被大规模使用。
缺点：Kafka 单机超过 64 个队列/分区，Load 会发生明显的飙高现象，队列越多，load 越高，发送消息响应时间变长，使用**短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试**；**支持消息顺序，但是一台代理宕机后，就会产生消息乱序，社区更新较慢；**
选用场景：Kafka 主要特点是基于Pull 的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生大量数据的互联网服务的数据收集业务。大型公司建议可以选用，**如果有日志采集功能，肯定是首选 kafka 了**。
3️⃣ RocketMQ
**RocketMQ 出自阿里巴巴的开源产品，用 Java 语言实现，在设计时参考了 Kafka，**并做出了自己的一些改进。被阿里巴巴广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，binglog 分发等场景。
优点：**单机吞吐量十万级**，可用性非常高，采用分布式架构，消息可以做到 0 丢失，MQ 功能较为完善，扩展性好，支持 10 亿级别的消息堆积，不会因为堆积导致性能下降，采用 java 语言实现。
**缺点：支持的客户端语言不多，目前是 java 及 c++，**其中 c++不成熟；**社区活跃度一般,**没有在MQ核心中去实现 JMS 等接口，有些系统要迁移需要修改大量代码。
选用场景：天生为金融互联网领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。RoketMQ 在稳定性上可能更值得信赖，这些业务场景在阿里双 11 已经经历了多次考验，如果你的业务有上述并发场景，建议可以选择 RocketMQ。
4️⃣ RabbitMQ
2007 年发布，是一个在AMQP(高级消息队列协议)基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一。
优点：由**于 erlang 语言的高并发特性，性能较好**；吞吐量到万级，MQ 功能比较完备、健壮、稳定、易用、跨平台、**支持多种语言如P**ython、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP等，支持 AJAX 文档齐全；开源提供的管理界面非常棒，用起来很好用，社区活跃度高；**更新频率相当高。**
缺点：商业版需要收费，学习成本较高。
选用场景：**结合 erlang 语言本身的并发优势，性能好时效性微秒级，社区活跃度也比较高，管理界面用起来十分方便，如果你的数据量没有那么大，中小型公司优先选择功能比较完备的 RabbitMQ。**

## 2. 什么是RabbitMQ

- 消息队列是一个使用队列来通信的组件。
- RabbitMQ是一个基于AMQP（Advanced Message Queueing Protocol）标准的消息中间件。

## 3. RabbitMQ特点

- 可靠性：使用**持久化、传输确认、发布确认**等机制保证可靠性
- 灵活的路由：在消息进入队列之前，通过交换器路由消息。
  - 典型路由功能：提供内置交换器实现
  - 更复杂的路由功能：将多个交换器绑定在一起，也可以通过插件机制来实现自己的路由
- 扩展性：多个RabbitMQ节点可以组成一个集群，也可以根据实际业务情况动态的扩展集群中的节点。
- 高可用性：队列可以在集群中的机器上设置镜像，使得部分节点出现问题的情况下队列仍然可用。
- 多种协议：原生支持AMQP，还支持STOMP。MQTT等多种消息中间件协议。
- 多语言客户端：几乎支持所有常用语言
- 管理界面：提供一个易用的用户界面，使得用户可以监控和管理消息、集群中的节点等。
- 插件机制：提供多种机制来扩展

## 4. RabbitMQ核心概念

RabbitMQ 整体上是一个生产者与消费者模型，主要负责接收、存储和转发消息

![图1-RabbitMQ 的整体模型架构](https://raw.githubusercontent.com/Xiaoxi121/xiaoxi.github.image/main/img/96388546-1729422070141-27.jpg)

**生产者与消费者模型**，主要负责接收、存储和转发消息。

消息一般由2部分组成：**消息头和消息体**，消息体是**payLoad**，不透明，消息头是由一系列的可选属性组成，包括**routing-key**（路由键）、**priority**（优先级）、**delivery-mode**（持久化标志）等，生产者把消息交给RabbitMQ后，RabbitMQ会根据消息头将消息发送给感兴趣的消费者

* **Producer和 Consumer**

  **生产者** :

  - 消息生产者，就是投递消息的一方。
  - 消息一般包含两个部分：消息体（`payload`)和标签(`Label`)。

  **消费者**：

  - 消费消息，也就是接收消息的一方。
  - **消费者连接到 RabbitMQ 服务器，并订阅到队列上。消费消息时只消费消息体，丢弃标签**

* **Exchange**：

  生产者将消息发送到交换器，由交换器将消息路由到一个或者多个队列中。当路由不到时，或返回给生产者或直接丢弃。

  交换器会将消息分配到对应的消息队列，如果路由不到，可能会返回给生产者，可能会直接丢弃，4种类型，不同类型对应不同路由策略：**direct**（默认）、**fanout**、**topic**、**headers**。不同类型交换器转发消息的策略不同，生产者发送消息给交换器时，一般会指定一个**RoutingKey**（路由键），指定这个消息的路由规则，这个 **RoutingKey 需要与交换器类型和绑定键(BindingKey)联合使用才能最终生效**。RabbitMQ 中通过 **Binding(绑定)** 将 **Exchange(交换器)** 与 **Queue(消息队列)** 关联起来，在绑定的时候一般会指定一个 **BindingKey(绑定建)** ，交换器和队列可以是多对多的关系，生产者将消息发送给交换器时，需要一个 RoutingKey,当 BindingKey 和 RoutingKey 相匹配时，消息会被路由到对应的队列中，在绑定多个队列到同一个交换器的时候，这些绑定允许使用相同的 BindingKey。BindingKey 并不是在所有的情况下都生效，它依赖于交换器类型，比如 fanout 类型的交换器就会无视，而是将消息路由到所有绑定到该交换器的队列中。

  * **fanout**：会把发送到该交换器的消息发送到所有绑定的 Queue种，广播消息，最快

  * **direct**：把消息路由到**BindingKey**和 **RountingKey**完全匹配的**Queue**，常用于处理优先级的任务，根据优先级把消息发送到对应的队列

  * **topic**：将消息路由到**BindingKey**和 **RountingKey** 匹配的队列

    `. `号分隔字符串，每一段独立的字符串称为一个单词，BindingKey和 RountingKey都是点号

    BindingKey可以存在 `*和#`，用于模糊匹配，星号匹配一个单词，井号匹配多个单词（可以是0个）

  * **headers**（不推荐）：不依赖路由键的匹配规则路由消息，而是根据消息内容的headers属性进行匹配。在绑定队列和交换器时指定一组键值对，当发送消息到交换器时，RabbitMQ 会获取到该消息的 headers（也是一个键值对的形式)，对比其中的键值对是否完全匹配队列和交换器绑定时指定的键值对，如果完全匹配则消息会路由到该队列，否则不会路由到该队列。headers 类型的交换器性能会很差，而且也不实用，基本上不会看到它的存在。

* **Queue**：一个消息可投入一个或多个队列。**多个消费者可以订阅同一个队列**，这时队列中的消息会被平均分摊（Round-Robin，即轮询）给多个消费者进行处理，而不是每个消费者都收到所有的消息并处理，**这样避免消息被重复消费。**

* **Broker**：可以将**Broker**看作一台**RabbitMQ**服务器

## 5. 什么是AMQP

RabbitMQ 就是 AMQP 协议的 Erlang 的实现(当然 RabbitMQ 还支持 STOMP2、 MQTT3 等协议 ) AMQP 的模型架构 和 RabbitMQ 的模型架构是一样的，生产者将消息发送给交换器，交换器和队列绑定

1. AMQP 协议的三层：
   1. Module Layer:协议最高层，主要定义了一些**客户端调用的命令**，客户端可以用这些命令实现自己的业务逻辑。
   2. Session Layer:中间层，**主要负责客户端命令发送给服务器，再将服务端应答返回客户端，提供可靠性同步机制和错误处理**。
   3. TransportLayer:最底层，**主要传输二进制数据流，提供帧的处理、信道复用、错误检测和数据表示等**。
2. AMQP模型的三大组件
   1. 交换器：消息代理服务器中用于把消息路由到队列的组件
   2. 队列：用来存储消息的数据结构，位于硬盘或者内存中
   3. 绑定：一套规则，告知交换器应该将消息投递到哪个队列中

## 6. 消息的重复消费

* **每条消息设置一个唯一标识id**
* **幂等方案，分布式锁**
* > **幂等性（Idempotence）**
  > 幂等性是指一个操作执行一次和多次的效果是一样的。在处理消息时，如果能够确保即使消息重复发送，处理结果也是相同的，那么就可以避免因重复消息而导致的问题。实现幂等性的方法包括但不限于以下几种：
  >
  > **消息唯一标识：**给每条消息添加一个唯一的标识符（例如UUID），在接收到消息时检查是否已经处理过相同标识的消息，如果是，则忽略这条消息。
  > **状态校验码：**对于某些业务场景，可以通过计算消息的状态校验码（例如MD5哈希值），然后将这个值存储起来，在接收到新的消息时，先验证其状态校验码是否存在于已处理的消息集合中。
  > **数据库乐观锁/悲观锁**：在更新数据库记录时，通过版本号或时间戳来检查记录是否已经被修改，从而避免并发冲突。
  > **分布式锁**
  > 分布式锁是一种协调工具，用于控制分布式环境下的并发访问。它确保在同一时刻只有一个线程或进程能够执行某个操作，从而防止多个节点同时处理同一任务导致的问题。使用分布式锁可以有效防止消息处理中的竞态条件，尤其是在高并发场景下。
  >
  > 常见的分布式锁实现方式有：
  >
  > **基于Redis的实现：**利用Redis的SETNX命令（设置值仅当键不存在时）或者其他原子操作来实现互斥访问。也可以使用更高级的锁实现，如Redlock算法。
  > **ZooKeeper：**利用ZooKeeper提供的顺序节点和临时节点特性来实现分布式锁。
  > Etcd：使用Etcd提供的分布式锁服务来实现。

## 7. 消息堆积

**生产者发送消息速度超过消费者消费消息速度**，队列中消息堆积，直到上限，**之后消息成为死信，可能被丢弃**

**解决**：

* **增加消费者**
* 在**消费者开启线程池加快处理消费**
* **扩大队列**容积，**声明队列时使用惰性队列**

## 8. 惰性队列

**接收到消息后直接存入磁盘而不是内存**，消费时**才从磁盘加载**，支持百万条消息存储

##  9. 死信队列

DLX，全称为 Dead-Letter-Exchange，死信交换器，死信邮箱。当消息在一个队列中变成死信 (dead message) 之后，**它能被重新发送到另一个交换器中，这个交换器就是 DLX，绑定 DLX 的队列就称之为死信队列。**

导致的死信的几种原因：

- 消息**被拒**（Basic.Reject /Basic.Nack) 且 requeue = false。
- 消息 **TTL 过期。**
- **队列满了，无法再添加。**

进入死信队列后，消息可以根据业务需求进行多种处理方式，|
包括但不限于**消费处理、日志记录、自动重试、人工干预、**转发到**其他队列或系统以及丢弃或忽略**

## 10. 延迟队列

延迟队列指的是存储对应的延迟消息，消息被发送以后，并不想让消费者立刻拿到消息，
而是**等待特定时间后，消费者才能拿到这个消息进行消费。**

RabbitMQ 本身是没有延迟队列的，要实现延迟消息，一般有两种方式：

1. 通过 RabbitMQ 本身队列的特性来实现，
   **需要使用 RabbitMQ 的死信交换机（Exchange）和消息的存活时间 TTL（Time To Live）。**
   1. 首先，需要创建一个 DLX，当消息过期时，这些消息将会被发送到 DLX 中。
   2. 接下来，创建一个普通的队列，并设置它的参数，使得当消息过期后，消息会发送到之前创建的 DLX 中
   3. 发送消息时，可以将消息发送到普通队列中。这些消息将在设定的时间后过期，并自动转移到 DLX。
   4. 最后，需要创建另一个队列来监听 DLX，并处理那些过期
2. 在 RabbitMQ 3.5.7 及以上的版本**提供了一个插件**（rabbitmq-delayed-message-exchange）来实现延迟队列功能。同时，插件依赖 Erlang/OPT 18.0 及以上。

也就是说，AMQP 协议以及 RabbitMQ 本身没有直接支持延迟队列的功能，但是可以通过 TTL 和 DLX 模拟出延迟队列的功能。

## 11. 优先级队列

RabbitMQ 自 V3.5.0 有优先级队列实现，优先级高的队列会先被消费。

可以通过`x-max-priority`参数来实现优先级队列。不过，当消费速度大于生产速度且 Broker 没有堆积的情况下，优先级显得没有意义

## 12. RabbitMQ工作模式

- 简单模式

  只涉及一个生产者和一个消费者。生产者直接将消息发送到队列，消费者从队列中取出消息并处理

- work 工作模式

  允许多个消费者订阅同一个队列，但每次只有一条消息会被发送到一个消费者。
  当一个消费者处理完消息后，下一个消息才会被发送给其他消费者。

- pub/sub 发布订阅模式

  发布订阅模式允许生产者将消息发送到一个Exchange，而不是直接发送到队列。
  多个队列可以绑定到同一个Exchange，当生产者发送消息时，所有绑定到该Exchange的队列都会收到消息

- Routing 路由模式

  路由模式允许生产者发送消息到Exchange，但消息只会被发送到与特定routing key绑定的队列。这种方式允许更细粒度的消息路由。

- Topic 主题模式

  主题模式是Routing模式的扩展，支持模糊匹配的routing key。生产者发送消息时指定一个routing key，而队列绑定时使用一个pattern（模式）作为binding key。如果消息的routing key与队列的binding key模式匹配，则消息会被发送到该队列

## 13. RabbitMQ消息怎么传输

由于 **TCP 链接的创建和销毁开销较大，且并发数受系统资源限制，会造成性能瓶颈**，
所以 **RabbitMQ 使用信道的方式来传输数据**。

信道（Channel）是生产者、消费者与 RabbitMQ 通信的渠道，
信道是**建立在 TCP 链接上的虚拟链接，且每条 TCP 链接上的信道数量没有限制。**

**就是说 RabbitMQ 在一条 TCP 链接上建立成百上千个信道来达到多个线程处理**，
这个 **TCP 被多个线程共享，每个信道在 RabbitMQ 都有唯一的 ID，保证了信道私有性，**每个信道对应一个线程使用

## 14. 消息的可靠性

消息到 MQ 的过程中搞丢，MQ 自己搞丢，MQ 到消费过程中搞丢。

- 生产者到 RabbitMQ：**事务机制和 Confirm 机制，注意：事务机制和 Confirm 机制是互斥的，两者不能共存，会导致 RabbitMQ 报错。**
- RabbitMQ 自身：**持久化、集群、普通模式、镜像模式。**
- RabbitMQ 到消费者：**basicAck 机制、死信队列、消息补偿机制。**

* 生产者到**RabbitMQ**：

  1.使⽤rabbitmq提供是事务功能，就是⽣产者在发送数据之前开启事务，然后发送消息，如果消息没有成功被rabbitmq接收到，那么⽣产者会受到异常报错，这时就可以回滚事 务，然后尝试重新发送;如果收到了消息，那么就可以提交事务。 

  **缺点**:rabbitmq事务已开启，就会变为同步阻塞操作，⽣产者会阻塞等待是否发送成功，太耗性能会造成吞吐量的下降。

  2.可以开启confirm模式。在⽣产者哪⾥设置开启了confirm模式之后，每次写的消息都会分配 ⼀个唯⼀的id， 然后如何写⼊了rabbitmq之中，rabbitmq会给你回传⼀个ack消息，告诉你 这个消息发送OK了;如果 rabbitmq没能处理这个消息，会回调你⼀个nack接⼝，告诉你这 个消息失败了，你可以进⾏重试。⽽且你可以结合这个机制知道⾃⼰在内存⾥维护每个消息 的id，如果超过⼀定时间还没接收到这个消息的回调，那么你可以进⾏重发

  **⼆者不同** 事务机制是同步的，你提交了⼀个事务之后会阻塞住，但是confirm机制是异步的，发送消息 之后可以接着发送下⼀个消息，然后rabbitmq会回调告知成功与否。 ⼀般在⽣产者这块避免丢失，都是⽤confirm机制。

* **RabbitMQ**自身：开启**持久化**交换机、队列、消息、集群

  （1）设置消息持久化到磁盘。设置持久化有两个步骤:

  1.创建queue的时候将其设置为持久化的，这样就可以保证rabbitmq持久化queue的元数据，但是不会持久化queue里面的数据。

  2.发送消息的时候将消息的deliveryMode设置为2，这样消息就会被设为持久化⽅式，此时 rabbitmq就会将消息持久化到磁盘上。

  必须要同时开启这两个才可以。

  （2）⽽且持久化可以跟⽣产的confirm机制配合起来，只有消息持久化到了磁盘之后才会通知生产者ack，这样 就算是在持久化之前rabbitmq挂了，数据丢了，⽣产者收不到ack回调也会进⾏消息重发。

* **RabbitMQ**到消费者：

* 使⽤rabbitmq提供的ack机制，⾸先关闭rabbitmq的⾃动ack，然后每次在确保处理完这个 消息之后，在代码⾥⼿动调⽤ack。这样就可以避免消息还没有处理完就ack

### RabbitMQ可靠性

1. 添加消息到队列中，保证消息成功的添加到了队列
2. 保证消息发送出去，一定会被消费者正常消费
3. 消费者正常消费了，生产者或者队列 知道消费者已经成功的消费了消息

#### 问题一解决方案-添加消息到队列中，保证消息成功的添加到了队列

添加消息到队列中，保证消息成功的添加到了队列

- 通过AMQP事务机制实现，这也是AMQP协议层面提供的解决方案；（影响性能）
- 通过将channel设置成confirm模式来实现；

##### Confirm消息确认模式

confirm机制： 只要放消息到 queue 是成功的，那么队列就一定会反馈，表示发送成功；并且，只要是成功的发送到队列中，就会反馈成功，就算是在队列中，消费者接收失败，也会反馈成功！

###### 实现原理

​        生产者将信道设置成confirm模式，一旦信道进入confirm模式，所有在该信道上面发布的消息都会被指派一个唯一的ID(从1开始)；

​        一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认消息给生产者（包含消息的唯一 ID）,这就使得生产者知道消息已经正确到达目的队列了；

​        如果消息和队列是可持久化的，那么确认消息会将消息写入磁盘之后发出，broker回传给生产者的确认消息中deliver-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。

###### 优点

- 支持异步，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果RabbitMQ因为自身内部错误导致消息丢失，就会发送一条nack消息，生产者应用程序同样可以在回调方法中处理该nack消息。
- 在channel 被设置成 confirm 模式之后，所有被 publish 的后续消息都将被 confirm（即 ack） 或者被nack一次。但是没有对消息被 confirm 的快慢做任何保证，并且同一条消息不会既被 confirm又被nack 。

#### 问题二解决方案-保证消息发送出去，一定会被消费者正常消费

使用 RabbitMQ中的 ACK 机制，就是手动确认消息。

消费者在声明队列时，可以指定noAck参数，当noAck=false时，RabbitMQ会等待消费者显式发回ack信号后才从内存(或者磁盘，持久化消息)中移去消息。否则，消息被消费后会被立即删除。

消费者接收每一条消息后都必须进行确认（消息接收和消息确认是两个不同操作）。只有消费者确认了消息，RabbitMQ 才能安全地把消息从队列中删除。

RabbitMQ不会为未ack的消息设置超时时间，它判断此消息是否需要重新投递给消费者的唯一依据是消费该消息的消费者连接是否已经断开。这么设计的原因是RabbitMQ允许消费者消费一条消息的时间可以很长。保证数据的最终一致性；

如果消费者返回ack之前断开了链接，RabbitMQ 会重新分发给下一个订阅的消费者。（可能存在消息重复消费的隐患，需要去重）；

#### 问题三解决方案-消费者正常消费了，生产者或者队列 知道消费者已经成功的消费了消息 

和问题2的解决方法一致，在手动签收消息之后做手动告诉生产者已经消费成功

## 15. RabbitMQ持久化

可以进行消息持久化, 即使rabbitMQ挂了，重启后也能恢复数据

如果要进行消息持久化，那么需要对以下3种实体均配置持久化

a) Exchange

声明exchange时设置持久化（durable = true）并且不自动删除(autoDelete = false)

b) Queue

声明queue时设置持久化（durable = true）并且不自动删除(autoDelete = false)

c) message

发送消息时通过设置deliveryMode=2持久化消息

## 16. 消息的顺序性

- **拆分多个queue，每个queue⼀个consumer**

  根据hash等操作将生产者发送到一个queue里面，让一个消费者去消费这个queue，这样顺序就有了保障

- **⼀个queue对应⼀个consumer，然后这个consumer内部⽤内存队列做排队，然后分发给底层不同的worker 来处理**

  消费者不去消息队列中直接消费消息，根据hash将一个数据的操作放在一个内存队列中，消费者线程从这个内存队列中消费。

- **设置优先级**

## 17. RabbitMQ高可用

R**abbitMQ 是比较有代表性的，因为是基于主从（非分布式）做高可用性的，**我们就以 RabbitMQ 为例子讲解第一种 MQ 的高可用性怎么实现。**RabbitMQ 有三种模式：单机模式、普通集群模式、镜像集群模式。**

- 单机模式

  Demo 级别的，一般就是你本地启动了玩玩儿的?，没人生产用单机模式。

- 普通集群模式

  意思就是在多台机器上启动多个 RabbitMQ 实例，每个机器启动一个。**你创建的 queue，只会放在一个 RabbitMQ 实例上，但是每个实例都同步 queue 的元数据（**元数据可以认为是 queue 的一些配置信息，**通过元数据，可以找到 queue 所在实例）。**

  你消费的时候，实际上**如果连接到了另外一个实例，那么那个实例会从 queue 所在实例上拉取数据过来。这方案主要是提高吞吐量的，就是说让集群中多个节点来服务某个 queue 的读写操作。**

- 镜像集群模式

  这种模式，才是所谓的 RabbitMQ 的高可用模式。跟普通集群模式不一样的是**，在镜像集群模式下，你创建的 queue，无论元数据还是 queue 里的消息都会存在于多个实例上**，就是说，每个 RabbitMQ 节点都有这个 queue 的一个完整镜像，包含 queue 的全部数据的意思。**然后每次你写消息到 queue 的时候，都会自动把消息同步到多个实例的 queue 上。**RabbitMQ 有很好的管理控制台，就是在后台新增一个策略，这个策略是镜像集群模式的策略，指定的时候是可以要求数据同步到所有节点的，也可以要求同步到指定数量的节点，再次创建 queue 的时候，应用这个策略，就会自动将数据同步到其他的节点上去了。

  这样的好处在于，你任何一个机器宕机了，没事儿，其它机器（节点）还包含了这个 queue 的完整数据，别的 consumer 都可以到其它节点上去消费数据。坏处在于**，第一，这个性能开销也太大了吧**，消息需要同步到所有机器上，导致网络带宽压力和消耗很重！RabbitMQ 一个 queue 的数据都是放在一个节点里的，镜像集群下，也是每个节点都放这个 queue 的完整数据

RabbitMQ基于主从（非分布式）做高可用

* **普通集群模式**：共享交换机、队列元信息（可以有引用）、不包含队列的消息，创建的queue，只会放在一个Rabbitmq实例上，但是每个实例都同步 queue 的元数据。访问集群某节点，如果队列不在该节点，就从数据所在节点传递到当前节点，队列所在节点宕机，队列中消息就丢失

* **镜像集群模式**：本质是主从模式，消息会同步备份，创建队列的节点叫该队列的主节点，备份到其他节点叫做该队列的镜像节点，一个队列的主节点可能是另一个队列的镜像节点，所有操作都是主节点完成，同步给镜像节点，主节点宕机后，镜像节点替代。可能丢失数据，可以使用仲裁队列

* **仲裁队列**：3.8后，替代镜像队列，都是主从同步，基于Raft协议，强一致

## 18. 如何解决消息队列的**延时以及过期失效**问题

RabbtiMQ 是可以设置过期时间的，也就是 TTL。
如果**消息在 queue 中积压超过一定的时间就会被 RabbitMQ 给清理掉，**这个数据就没了。
那这就是第二个坑了。这就不是说数据会大量积压在 mq 里，而是大量的数据会直接搞丢。

我们可以采取一个方案，**就是批量重导，**这个我们之前线上也有类似的场景干过。就是大量积压的时候，我们当时就直接丢弃数据了，然后等过了高峰期以后，比如大家一起喝咖啡熬夜到晚上 12 点以后，用户都睡觉了。这个时候我们就开始写程序，将丢失的那批数据，写个临时程序，一点一点的查出来，然后重新灌入 mq 里面去，把白天丢的数据给他补回来。也只能是这样了。假设 1 万个订单积压在 mq 里面，没有处理，其中 1000 个订单都丢了，你只能手动写程序把那 1000 个订单给查出来，手动发到 mq 里去再补一次。

# 1. 除了 Redis，你还知道其他分布式缓存方案吗？
下面简单聊聊常见的分布式缓存技术选型。

- 分布式缓存的话，使用的比较多的还是 **Memcached 和 Redis。**不过，现在基本没有看过还有项目使用 Memcached 来做缓存，都是直接用 Redis。**Memcached 是分布式缓存最开始兴起的那会，比较常用的。后来，随着 Redis 的发展，大家慢慢都转而使用更加强大的 Redis 了。**
- 有一些大厂也开源了类似于 Redis 的分布式高性能 KV 存储数据库，例如，**腾讯开源的 Tendis** 。Tendis 基于知名开源项目 RocksDB 作为存储引擎 ，100% 兼容 Redis 协议和 Redis4.0 所有数据模型。关于 Redis 和 Tendis 的对比，腾讯官方曾经发过一篇文章：Redis vs Tendis：冷热混合存储版架构揭秘 ，可以简单参考一下。
  - 不过，从 Tendis 这个项目的 Github 提交记录可以看出，**Tendis 开源版几乎已经没有被维护更新了，**加上其关注度并不高，使用的公司也比较少。因此，不建议你使用 Tendis 来实现分布式缓存。

目前，**比较业界认可的 Redis 替代品还是下面这两个开源分布式缓存**（都是通过碰瓷 Redis 火的）：

1. **Dragonfly：**一种针对现代应用程序负荷需求而构建的内存数据库，完全兼容 Redis 和 Memcached 的 API，迁移时无需修改任何代码，号称全世界最快的内存数据库。
2. **KeyDB：** Redis 的一个高性能分支，专注于多线程、内存效率和高吞吐量。

不过，个人还是建议分布式缓存首选 Redis ，毕竟经过这么多年的生考验，**生态也这么优秀，资料也很全面！**

Redis和Memcached区别和共同点

共同点

1. 都是**基于内存**的数据库，一般都用来当做缓存使用。
2. 都有**过期策略。**
3. 两者的性**能都非常高。**

区别

1. 数据类型：**Redis 支持更丰富的数据类型（支持更复杂的应用场景）**。Redis 不仅仅支持简单的 k/v 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储。**Memcached 只支持最简单的 k/v 数据类型**。
2. 数据持久化：**Redis 支持数据的持久化，**可**以将内存中的数据保持在磁盘中**，重启的时候可以再次加载进行使用，**而 Memcached 把数据全部存在内存之中**。也就是说，Redis 有灾难恢复机制而 Memcached 没有。
3. 集群模式支持：Memcached 没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据**；但是 Redis 自 3.0 版本起是原生支持集群模式的。**
4. 线程模型：Memcached 是多线程，非阻塞 IO 复用的网络模型；**Redis 使用单线程的多路 IO 复用模型**。 （Redis 6.0 针对网络数据的读写引入了多线程）
5. 特性支持**：Redis 支持发布订阅模型、Lua 脚本、事务等功能，**而 Memcached 不支持。并且，Redis 支持更多的编程语言。
6. 过期数据删除：Memcached 过期数据的删除策略只用了惰性删除**，而 Redis 同时使用了惰性删除与定期删除。**



# 2. Websocket无法连接是什么原因

- 未修改配置。需修改前端的VUE_APP_WS_URL变量为im-server的服务地址
- ws url前缀不正确。不带ssl证书时前缀是"ws://"，带ssl证书是"wss://"
- 网站配置了ssl证书，但是没有为ws配置证书。

# 3. 生产部署时出现跨域问题如何解决？

- 将前端页面、后端端口、minio文件都通过nginx的同一个端口进行代理，这样可以避免跨域。可以参考上面的nginx配置

# 4.为什么不使用MQ，而是用redis充当消息队列

- redis本身性能不弱，单线程能达到10w qps, 尤其在redis6.2版本之后，支持批量拉取消息。从原理上来说，其实跟一些MQ已经差不多，本质都是短轮训的模式
- 引入MQ之后，依然不能舍弃redis(需要用来做缓存、分布式锁等)，多一个组件，则多一份负担，且主流的MQ(kafak、rocketmq、rabbitmq)均不是轻量级组件，把有限的资源让给redis, 未必不会有会有更好的效果。
- 本项目是一个开源项目，需要考虑普适性。如果盒子IM使用的是rocketmq,如果客户的原有系统用的是rabbitmq，想集成盒子IM的功能，需要同时部署多个MQ。即使用的是相同的MQ，也可能是不同版本。而redis这类组件，我相信90%以上项目都会使用，且redis的sdk版本适配性相当不错。

并不是复杂的架构就是好架构,也不是所有的项目都是大型项目,很多时候，架构越复杂,意味着消耗的资源就却多,排查问题的难度就越大.

# 8.心跳机制中可以结合 TCP 的 keepalive 和应用层心跳来一起使用吗？

如果从实现功能角度看，传输层和应用层的心跳机制没有结合的必要，
因为传输层的心跳探测连接可用性，应用层的心跳机制也可以完成探测; 
但从debug角度看，应用层的心跳探测机制无法定位是网络的问题还是系统的问题，此时由传输层辅助就非常好，但实现会相对复杂！

# 9.二分法来进行心跳探测 的逻辑？

其实就是下一次动态调整的心跳间隔是**：当前已经确认的安全的心跳间隔最大值 和 已经确认的心跳探测过大的最小值** 的中间均值。比如上一次心跳间隔是4分钟，而且连续N次都成功ack了，那么当前已经确认的安全的心跳间隔是4分钟，假设已经确认10分钟时心跳间隔过大了，那么下一次调整的心跳就是 （4 + 10） / 2 = 7分钟。

# 10.tcp的keepalive怎么开启？

比如netty下可以通过如下代码开启：

ServerBootstrap bootstrap = new ServerBootstrap();
bootstrap.childOption(ChannelOption.SO_KEEPALIVE, true);

另外tcp keepalive的心跳间隔的配置也需要修改一下系统的/etc/sysctl.conf，类似下面：

net.ipv4.tcp_keepalive_time=120
net.ipv4.tcp_keepalive_intvl=30
net.ipv4.tcp_keepalive_probes=3

# 11. 表中字段，你主键用自增ID还是UUID，为什么？

**自增 id。**

- 因为 **uuid 相对顺序的自增 id 来说是毫无规律**可言的，**新行的值不一定要比之前的主键的值要大**，所以 innodb **无法做到总是把新行插入到索引的最后，而是需要为新行寻找新的合适的位置从而来分配新的空间。**

- 这个过程需要做很多额外的操作，数据的毫无顺序会导致数据分布散乱，将会导致以下的问题：

  - **写入的目标页很可能已经刷新到磁盘上并且从缓存上移除**，或者还没有被加载到缓存中，innodb 在插入之前**不得不先找到并从磁盘读取目标页到内存中，这将导致大量的随机 IO。**

  - 因为写入是乱序的**，innodb 不得不频繁的做页分裂操作，以便为新的行分配空间，页分裂导致移动大量的数据，影响性能。**

  - 由于**频繁的页分裂，页会变得稀疏并被不规则的填充，最终会导致数据会有碎片。**

  - **UUID 太占用内存。**每个 UUID 由 36 个字符组成，在字符串进行比较时，**需要从前往后比较，字符串越长，性能越差**。另外字符串越长，**占用的内存越大，**由于页的大小是固定的，这样一个页上能存放的**关键字数量就会越少，**这样最终就会导致**索引树的高度越大，**在索引搜索的时候，发生的磁盘 IO 次数越多，性能越差

    结论：使用 InnoDB 应该尽可能的按主键的自增顺序插入，并且尽可能使用单调的增加的聚簇键的值来插入新行



# 12.讲一下缓存设计

# 

1. **消息队列**：Redis 自带的 List 数据结构可以作为一个简单的队列使用。Redis 5.0 中增加的 Stream 类型的数据结构更加适合用来做消息队列。它比较类似于 Kafka，有主题和消费组的概念，支持消息持久化以及 ACK 机制。
   延时队列：Redisson 内置了延时队列（基于 Sorted Set 实现的）。

   因此，我们通常建议不要使用 Redis 来做消息队列，你完全可以选择市面上比较成熟的一些消息队列比如 RocketMQ、Kafka。不过，如果你就是想要用 Redis 来做消息队列的话，那我建议你优先考虑 Stream，这是目前相对最优的 Redis 消息队列实现。

2. Redisson 是一个基于 Redis 的 Java 语言编写的分布式同步工具包，它提供了多种分布式 Redis 对象，使得开发人员可以非常方便地实现分布式环境下的一些复杂功能。Redisson 提供了诸如 Locks（锁）、Semaphores（信号量）、CountdownLatches（倒计时锁存器）、Queues（队列）、Topic（主题）、Maps（映射表）等多种集合操作，并且它支持事务，使得开发者可以像使用本地集合类一样使用这些分布式数据结构。

   Redisson 的主要特点包括：

   - **高性能**：由于它是基于 Redis 实现的，而 Redis 本身是一个高效的键值存储系统，所以 Redisson 也继承了这种高效性。
   - **易于使用**：提供了丰富的 API 接口，使得分布式应用的开发变得更加简单。
   - **集群支持**：支持 Redis 的主从模式及集群模式，增强了其可用性和扩展性。
   - **透明的客户端-服务器模型**：客户端不需要知道具体的服务器地址，只需要连接到任何一个 Redis 服务器节点即可，客户端库会自动发现其他的节点。
   - **事务支持**：支持 Redis 的事务功能，可以确保一系列操作的原子性。

   Redisson 适用于构建需要分布式锁、消息队列等功能的应用场景，尤其是在 Java 生态系统中，它是一个非常有用的工具库。通过使用 Redisson，开发者可以在分布式环境中轻松实现诸如分布式锁、分布式缓存等功能，简化了多线程或多服务实例之间的协调工作。

3. 你的mq用来做什么的

4. 即时通讯系统，万人群怎么解决写扩散问题？读扩散和写扩散？

5. 在线和没在线的用户有没有考虑

6. 如何保证mq顺序消费

7. 1.消息存储中，内容表和索引表如果需要分库处理，应该按什么字段来哈希？ 索引表可以和内容表合并成一个表吗？
   答： 内容表应该按主键消息ID来哈希做分库分表处理，这样便于定位某一条具体的消息；索引表应该按索引的用户UID来哈希做分库分表处理，这样可以使得当前用户的所有联系人都落在一张表上，减少遍历所有表的麻烦。 索引表可以与内容表合成一张表，好处是显而易见的，能减少拉取历史消息时的数据库IO，不好的地方就是消息内容冗余存储，浪费了空间。

   2.能从索引表里获取到最近联系人所需要的信息，为什么还需要单独的联系人表呢？
   答： 如果从索引表中获取一个用户的所有联系人信息（包括最后一条聊天内容和时间）的话，SQL语句中会有分组后取top 1的操作，性能不理想； 另外当前用户与单个联系人之间的未读数需要维护，用联系人表的一个字段来存储，比用索引表方便许多。

   3.TCP 长连接的方式是怎么实现“当有消息需要发送给某个用户时，能够准确找到这个用户对应的网络连接”？
   答： 首先用户有一个登陆的过程： (1)tcp客户端与服务端通过三次握手建立tcp连接；(2)基于该连接客户端发送登陆请求；(3)服务端对登陆请求进行解析和判断，如果合法，就将当前用户的uid和标识当前tcp连接的socket描述符(也就是fd)建立映射关系； (4)这个映射关系一般是保存在本地缓存或分布式缓存中。 然后，当服务端收到要发送给这个用户的消息时，先从缓存中根据uid查找fd，如果找到，就基于fd将消息推送出去。

   4.有了 TCP 协议本身的 ACK 机制，为什么还需要业务层的 ACK 机制？
   答：这个问题从操作系统(linux/windows/android/ios)实现TCP协议的原理角度来说明更合适：
        1 操作系统在TCP发送端创建了一个TCP发送缓冲区，在接收端创建了一个TCP接收缓冲区；
        2 在发送端应用层程序调用send()方法成功后，实际是将数据写入了TCP发送缓冲区；
        3 根据TCP协议的规定，在TCP连接良好的情况下，TCP发送缓冲区的数据是“有序的可靠的”到达TCP接收缓冲区，然后回调接收方应用层程序来通知数据到达；
        4 但是在TCP连接断开的时候，在TCP的发送缓冲区和TCP的接收缓冲区中可能还有数据，那么操作系统如何处理呢？
              首先，对于TCP发送缓冲区中还未发送的数据，操作系统不会通知应用层程序进行处理（试想一下：send()函数已经返回成功了，后面再告诉你失败，这样的系统如何设计？太复杂了...），通常的处理手段就是直接回收TCP发送缓存区及其socket资源；
              对于TCP接收方来说，在还未监测到TCP连接断开的时候，因为TCP接收缓冲区不再写入数据了，所以会有足够的时间进行处理，但若未来得及处理就发现了连接断开，仍然会为了及时释放资源，直接回收TCP接收缓存区和对应的socket资源。

   总结一下就是： 发送方的应用层程序，调用send()方法返回成功的时候，数据实际是写入到了TCP的发送缓冲区，而非已经被接收方的应用层程序处理。怎么办呢？只能借助于应用层的ACK机制。即使数据成功发送到接收方设备了，tcp层再把数据交给应用层时也可能出现异常情况，比如存储客户端的本地db失败，导致消息在业务层实际是没成功收到的。这种情况下，可以通过业务层的ack来提供保障，客户端只有都执行成功才会回ack给服务端。

   5.在即时消息收发场景中，用于保证消息接收时序的序号生成器为什么可以不是全局递增的？
   答： 这是由业务场景决定的，这个群的消息和另一个群的消息在逻辑上是完全隔离的，只要保证消息的序号在群这样的一个局部范围内是递增的即可； 当然如果可以做到全局递增最好，但是会浪费很多的资源，却没有带来更多的收益。

   6.TLS 能识别客户端模拟器仿冒用户真实访问的问题吗？如果不能有什么其他更好的办法？
   答： TLS 是传输层的加密协议，是用来保证消息传输过程中不被截获、篡改和伪造的，但是无法识别仿冒的真实用户。
            客户端模拟器如果像真实用户一样来访问服务端，其实是没有必要去识别的，因为此时模拟器一般是为了帮助真实用户做一些事情，没有恶意行为；如果存在恶意行为，进行识别的办法是通过机器学习的方式进行识别，例如：客户端模拟器会频繁发送消息，针对这一特征，可以对线上访问流量进行甄别。

   7.类似 Redis+Lua 的原子化嵌入脚本的方式，是否真的能够做到“万无一失”的变更一致性？比如，执行过程中机器掉电会出现问题吗？
   redis在执行lua脚本过程中如果发生掉电，是可能会导致两个未读不一致的，因为lua脚本在redis中的执行只能保证多条命令会原子执行，整体执行完成才会同步给从库并写入aof，所以如果执行过程中掉电，会直接导致被中断的后面部分的脚本得不到执行。当然， 实际情况中这种概率非常小。作为兜底的方案，可以在未读变更时如果会话比较少，可以获取一次全量的会话未读来覆盖总未读，从而有机会能得到最终一致。

   

   11.如果用户的离线消息比较多，有没有办法来减少用户上线时离线消息的数据传输量？

   答： 用户所有的离线消息对用户来说，并不都是关心和感兴趣的，用户可能只是看了与某个最近联系人的最近的几条消息后，之前的都不想看了，所以这个时候如果将之前的离线消息都拉到本地是非常浪费资源的。通常的做法是：
   1 将用户的所有离线消息，按联系人进行分开；
   2 用户登录后进入与联系人的聊天窗口时，首先加载与该联系人的最近的10条离线消息；
   3 当用户用手滑动手机屏幕的时候，再分页拉取10条。

   12.通过长连接的接入网关机，缩容时与普通的 Web 服务机器缩容相比有什么区别？
   普通的Web服务器机器提供http的短连接服务，缩容时拿掉机器，会导致前端连接失败，但通过nginx的负载均衡算法，会使重连的客户端连接到另外一台服务器上，这对客户端来说，基本是无感知的； 但是长连接的接入网关机，在缩容拿掉机器时，会导致这台机器上的所有的长连接全部断掉，此时是会影响到所有连接到这台网关机的所有用户，当然通过入口调度服务，客户端可以通过重连连接到新的网关机上，但是用户的体验始终是不好的。

   13.为了避免每条消息都查询用户的在线状态，所有的消息都发送给所有的网关节点，这样也会造成每台网关机器的流量成倍数增长吧。这样，是不是会影响消费者推送消息的速率呢？毕竟，如果有50台网关节点，原来每台网关节点只需要取1条消息，现在却需要取50条消息，其中有49条是无效的。
   所以这个需要一个权衡，如果业务场景大部分都是点对点场景那么使用全局在线状态来精确投递是更好的选择，如果是群聊和直播类似扇出较大的场景推荐使用所有网关来订阅全量消息的方式。

   14.上下线通知好友时,是要先查询好友们的在线状态以取得他们所连接的服务器,然后向这些服务器推送上下线消息吗? 从几亿人的在线状态数据中,查询出几百个在线好友,有什么优化手段吗?
   一个用户的好友是有限的，在线状态如果是通过中央kv型存储的，并发查询几百个好友也并不是个问题，性能上不会太慢，只是存储压力会比较大。如果真要优化，好友数太多的情况下，我个人觉得可以把这个用户的好友查出后，组装成一条特殊消息下发给所有网关机，由各台网关机认领各自本机维护的这些好友中的那些在本机登录连接的，然后push上下线消息就可以。

   15.自动熔断机制中，如何来确认 Fail-fast 时的熔断阈值（比如：当单位时间内访问耗时超过 1s 的比例达到 50% 时，对该依赖进行熔断）
   一方面做压力测试 另外一方面可以做半熔断机制 结合流控 只放行50%的流量。

   16.请问做限流有好的开源代码推介吗？
   单机限流推荐guava的RateLimiter，全局限流直接基于Redis+Lua写一个很简单。
   对，限流阈值需要模拟压测，避免由于阈值设置太宽松导致服务仍然可能被拖死或者阈值太敏感导致一点抖动也会整体熔断

   项目：做的即时通讯系统

   你的mq用来做什么的？

   即时通讯系统，万人群怎么解决写扩散问题？读扩散和写扩散

   为什么要做一个二级缓存框架

   在线和没在线的用户有没有考虑过。
   、即时通讯项目介绍
   3、feign是哪个框架里的东西
   4、群聊消息收发怎么做的
   5、怎么保证的消息有序性
   6、有没有考虑过消息大量堆积的问题
   如何设计一个即时通讯软件的数据库 外键 ，索引

   即时通讯软件怎么实现

   项目实现难点
   了即时通讯，离线消息缓存，项目没答好，g。

   设计即时通讯，实现群聊私聊，查询用户在线信息，服务器注册，离线消息拉取。
   思路：先回答会用到的技术：redis，zookeeper，netty
   1.zookeeper做服务注册，消息发送服务器将ip，端口注册到zookeeper下面
   2.redis保存已上线用户信息和这个用户客户端与消息发送服务器建立的netty连接
   3.服务器分为两类，route服务器，消息发送服务器。
   4.群聊：客户端先将消息发给route服务器，由route服务器去redis中查询，拿到消息发送服务器ip，端口号，由route服务器发送给对应服务器，在有服务器通过netty与客户端通信
   5.私聊与群聊相似，只是由接收端的userId找到服务器地址
   6.离线消息拉取，在redis中用根据userid存储发给这个用户的消息，然后当客户端上线后根据id去redis中查询
   7.用户上线去redis中存储一下，在线用户查询直接查询redis。
   给出大体思路后，面试官又提出的问题：
   1.route服务器挂了怎么办？
   2.离线消息太多，一次拉取太多导致阻塞怎么办？
   3.不需要使用mysql存储吗？
   4.zookeeper挂了怎么办？

   你写了一个即时通讯im项目，什么时候做的？项目中遇到的难题？

   这里你用到了websocket？能简单说下吗？为什么要用他呢？

   你说了基于tcp，为什么不考虑用传输效率更高的udp呢？

   既然你说了考虑了实时性，那这种服务端的推送，客户端和服务端分别需要注意什么？

   那如果我这边请求很多（高并发），可能会打爆服务，那该怎么设计？

   即时通讯，类似于qq这种，大部分时间挂机没有聊天，如何维护tcp请求（在连接数百万量级的时候）

   因为看了你这个即时通讯的项目，你是比较了解网络方面的吗？
