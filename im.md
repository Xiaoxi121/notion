# 01 数据库和架构设计

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

| **表名**           | **表名（中文）** | **创建时机**   | **说明**                           | 索引                                                  |
| ------------------ | ---------------- | -------------- | ---------------------------------- | ----------------------------------------------------- |
| im_user            | 用户表           | 用户注册       | 记录用户基本信息                   | 索引：id主键索引                                      |
| im_friend          | 好友表           | 添加好友       | 记录好友关系以及好友的昵称、头像   | 索引：id主键索引、user_id和friend_id的联合索引        |
| im_private_message | 私聊消息表       | 发送私聊消息   | 记录与好友之间的聊天消息           | 索引：id主键索引、send_id和receive_id的联合索引       |
| im_group           | 群组表           | 创建群组       | 记录群组信息                       | 索引：id主键索引                                      |
| im_group_member    | 群组成员表       | 邀请好友进群聊 | 记录群组中的成员信息               | 索引：id主键索引、group_id二级索引、member_id二级索引 |
| im_group_message   | 群聊消息表       | 发送群聊消息   | 记录与群聊中的聊天消息             | 索引：id主键索引、receive_id（群聊id）二级索引        |
| im_sensitive_word  | 敏感词           | 后台创建       | 发送消息时如果匹配敏感词会变成'**' |                                                       |

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

|                     key                     |            value             |                             备注                             |
| :-----------------------------------------: | :--------------------------: | :----------------------------------------------------------: |
|     im:cache:friend:[userId]:[friendId]     |          是否为好友          |     减少发送消息时判断是否为好友的次数，缓解数据库的压力     |
|               im:cache:group:               |           群聊消息           |                                                              |
|          im:cache:group_member_ids          |          群聊成员id          |                                                              |
| im:readed:group:position:[groupId]:[userId] | 该用户在该群已读的最大消息id | 群聊消息可能会存在部分用户已读，部分用户未读的情况，无法通过单一字段记录。考虑到写扩散造成的数据库IO读写压力，这里选择了通过redis去记录。记录了用户在某个群的已读的最大消息id后，只要是小于或等于这个id的消息都是已读消息，否则就是已读消息，而不必记录每条消息是否已读。 |
|               userId:serverId               |  用户所连接的netty服务器id   |                                                              |
|                max_server_id                |      最大netty服务器id       |                                                              |

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

# 02 ​实现聊天功能

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

## 接入消息推送（实现聊天功能）

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

![image-20240823213628023](D:\2024\Notes\Typora\项目rpc+im\image-20240823213628023.png)

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

![image-20240823214003841](D:\2024\Notes\Typora\项目rpc+im\image-20240823214003841.png)

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

![image-20240823214742179](D:\2024\Notes\Typora\项目rpc+im\image-20240823214742179.png)

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

![image-20241015222041511](D:\2024\Notes\Typora\项目rpc+im\image-20241015222041511.png)

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



# 03 RabbitMQ

## 什么是RabbitMQ

- 消息队列是一个使用队列来通信的组件。
- RabbitMQ是一个基于AMQP（Advanced Message Queueing Protocol）标准的消息中间件。

## RabbitMQ特点

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

## RabbitMQ核心概念

RabbitMQ 整体上是一个生产者与消费者模型，主要负责接收、存储和转发消息

![图1-RabbitMQ 的整体模型架构](D:\2024\Notes\Typora\项目rpc+im\96388546.jpg)

### Producer 生产者和Consumer 消费者

- 消息由两部分组成

  - 消息头 Label

    由一系列的可选属性组成

    - routing-key 路由键
    - priority 优先级
    - delivery-mode 指出该消息可能需要持久性存储

  - 消息体 payLoad

    不透明

- 生产者把消息交由MQ以后，RabbitMQ会根据消息头把消息发送给感兴趣的消费者

### Exchange 交换器

1. 在 RabbitMQ 中，消息并不是直接被投递到 **Queue(消息队列)** 中的，中间还必须经过 **Exchange(交换器)** 这一层，**Exchange(交换器)** 会把我们的消息分配到对应的 **Queue(消息队列)** 中。
2. **Exchange(交换器)** 用来接收生产者发送的消息并将这些消息路由给服务器中的队列中，如果路由不到，**或许会返回给 Producer(生产者) ，或许会被直接丢弃掉** 。这里可以将 RabbitMQ 中的交换器看作一个简单的实体。
   1. **RabbitMQ 的 Exchange(交换器) 有 4 种类型，不同的类型对应着不同的路由策略**：**direct(默认)**，**fanout**, **topic**, 和 **headers**，不同类型的 Exchange 转发消息的策略有所区别



1. 生产者将消息发给交换器的时候，一般会指定一个Routing Key路由键，用来指定这个消息的路由规则，

   **这个Routingkey需要与交换器类型和绑定键bindkey联合使用才能生效**

2. Exchange交换器与Queue消息队列关联需要通过binding绑定，**在绑定的时候一般会指定一个bindingkey绑定键**，这样RabbitMQ就知道如何正确将消息路由到消息队列里。

3. **一个绑定就是基于路由键将交换器和消息队列连接起来的路由规则**，所以可以将交换器理解成一个由绑定构成的路由表。Exchange和消息队列可以是多对多的关系



1. **生产者将消息发送给交换其实，需要一个RoutingKey**，当BindingKey和RoutingKey匹配时，消息会被路由到对应的队列中
2. 再绑定多个队列到同一个交换器的时候，允许使用相同的BindingKey，BindingKey并不是在所有情况下都生效，依赖于交换机的类型
   1. fanout类型的交换器就会无视，而是将消息路由到所有绑定到该交换器的队列中。

### Queue 消息队列

- Queue(消息队列) 用来保存消息直到发送给消费者。它是消息的容器，也是消息的终点。一个消息可投入一个或多个队列。消息一直在队列里面，等待消费者连接到这个队列将其取走。
- **RabbitMQ 中消息只能存储在 队列 中，**这一点和 Kafka 这种消息中间件相反。Kafka 将消息存储在 topic（主题） 这个逻辑层面，而相对应的队列逻辑只是 topic 实际存储文件中的位移标识。 **RabbitMQ 的生产者生产消息并最终投递到队列中，消费者可以从队列中获取消息并消费。**
- 多个消费者可以订阅同一个队列，这时队列中的消息会被平均分摊（Round-Robin，即轮询）给多个消费者进行处理，而不是每个消费者都收到所有的消息并处理，这样避免消息被重复消费。
  - RabbitMQ不支持队列层面的广播消费

### Broker 消息中间件的服务节点

一个 RabbitMQ Broker 可以简单地看作一个 RabbitMQ 服务节点，或者 RabbitMQ 服务实例。大多数情况下也可以将一个 RabbitMQ Broker 看作一台 RabbitMQ 服务器。

![image-20241007190820073](D:\2024\Notes\Typora\项目rpc+im\image-20241007190820073.png)

### Exchange Types 交换器类型

1. **fanout**

   1. 把所有发送到该 Exchange 的消息路由到所有与它绑定的 Queue 中
   2. 速度最快、用于广播消息

2. **direct**

   1. 把消息路由到那些 Bindingkey 与 RoutingKey 完全匹配的 Queue 中。
   2. 用于处理有优先级的任务，根据任务的优先级把消息发送到对应的队列，这样可以指派更多的资源去处理高优先级的队列。

3. **topic**

   1. 也是将消息路由到 BindingKey 和 RoutingKey 相匹配的队列中
   2. 不同点：
      1. RoutingKey为一个点号“.”分割的字符串
      2. BindingKey和RoutingKey一样也是点号“.”分割的字符串
      3. BindingKey 中可以存在两种特殊字符串“\*”和“#”，用于做模糊匹配。其中“*”用于匹配一个单词，“#”用于匹配多个单词(可以是零个)。

4. **headers**

   根据发送的消息内容中的 headers 属性进行匹配

5. system

6. 自定义

## 什么是AMQP

RabbitMQ 就是 AMQP 协议的 Erlang 的实现(当然 RabbitMQ 还支持 STOMP2、 MQTT3 等协议 ) AMQP 的模型架构 和 RabbitMQ 的模型架构是一样的，生产者将消息发送给交换器，交换器和队列绑定

1. AMQP 协议的三层：
   1. Module Layer:协议最高层，主要定义了一些**客户端调用的命令**，客户端可以用这些命令实现自己的业务逻辑。
   2. Session Layer:中间层，**主要负责客户端命令发送给服务器，再将服务端应答返回客户端，提供可靠性同步机制和错误处理**。
   3. TransportLayer:最底层，**主要传输二进制数据流，提供帧的处理、信道复用、错误检测和数据表示等**。
2. AMQP模型的三大组件
   1. 交换器：消息代理服务器中用于把消息路由到队列的组件
   2. 队列：用来存储消息的数据结构，位于硬盘或者内存中
   3. 绑定：一套规则，告知交换器应该将消息投递到哪个队列中

## 说说生产者Producer和消费者Consumer

**生产者** :

- 消息生产者，就是投递消息的一方。
- 消息一般包含两个部分：消息体（`payload`)和标签(`Label`)。

**消费者**：

- 消费消息，也就是接收消息的一方。
- **消费者连接到 RabbitMQ 服务器，并订阅到队列上。消费消息时只消费消息体，丢弃标签**

## 说说Broker服务节点、Queue队列、Exchange交换器

- Broker：可以看做 RabbitMQ 的服务节点。一般情况下一个 Broker 可以看做一个 RabbitMQ 服务器。
- Queue：RabbitMQ 的内部对象，用于存储消息。多个消费者可以订阅同一队列，这时队列中的消息会被平摊（轮询）给多个消费者进行处理。
- Exchange：生产者将消息发送到交换器，由交换器将消息路由到一个或者多个队列中。当路由不到时，或返回给生产者或直接丢弃。

##  什么是死信队列？如何导致的？

DLX，全称为 Dead-Letter-Exchange，死信交换器，死信邮箱。当消息在一个队列中变成死信 (dead message) 之后，**它能被重新发送到另一个交换器中，这个交换器就是 DLX，绑定 DLX 的队列就称之为死信队列。**

导致的死信的几种原因：

- 消息**被拒**（Basic.Reject /Basic.Nack) 且 requeue = false。
- 消息 **TTL 过期。**
- **队列满了，无法再添加。**

## 什么是延迟队列？RabbitMQ怎么实现延迟队列

延迟队列指的是存储对应的延迟消息，消息被发送以后，并不想让消费者立刻拿到消息，而是**等待特定时间后，消费者才能拿到这个消息进行消费。**

RabbitMQ 本身是没有延迟队列的，要实现延迟消息，一般有两种方式：

1. 通过 RabbitMQ 本身队列的特性来实现，**需要使用 RabbitMQ 的死信交换机（Exchange）和消息的存活时间 TTL（Time To Live）。**
   1. 首先，需要创建一个 DLX，当消息过期时，这些消息将会被发送到 DLX 中。
   2. 接下来，创建一个普通的队列，并设置它的参数，使得当消息过期后，消息会发送到之前创建的 DLX 中
   3. 发送消息时，可以将消息发送到普通队列中。这些消息将在设定的时间后过期，并自动转移到 DLX。
   4. 最后，需要创建另一个队列来监听 DLX，并处理那些过期
2. 在 RabbitMQ 3.5.7 及以上的版本**提供了一个插件**（rabbitmq-delayed-message-exchange）来实现延迟队列功能。同时，插件依赖 Erlang/OPT 18.0 及以上。

也就是说，AMQP 协议以及 RabbitMQ 本身没有直接支持延迟队列的功能，但是可以通过 TTL 和 DLX 模拟出延迟队列的功能。

## 什么是优先级队列

RabbitMQ 自 V3.5.0 有优先级队列实现，优先级高的队列会先被消费。

可以通过`x-max-priority`参数来实现优先级队列。不过，当消费速度大于生产速度且 Broker 没有堆积的情况下，优先级显得没有意义

## RabbitMQ有哪些工作模式

- 简单模式
- work 工作模式
- pub/sub 发布订阅模式
- Routing 路由模式
- Topic 主题模式

## RabbitMQ消息怎么传输

由于 **TCP 链接的创建和销毁开销较大，且并发数受系统资源限制，会造成性能瓶颈**，所以 **RabbitMQ 使用信道的方式来传输数据**。信道（Channel）是生产者、消费者与 RabbitMQ 通信的渠道，信道是**建立在 TCP 链接上的虚拟链接，且每条 TCP 链接上的信道数量没有限制。就是说 RabbitMQ 在一条 TCP 链接上建立成百上千个信道来达到多个线程处理**，这个 **TCP 被多个线程共享，每个信道在 RabbitMQ 都有唯一的 ID，保证了信道私有性，**每个信道对应一个线程使用

## 如何保证消息的可靠性

消息到 MQ 的过程中搞丢，MQ 自己搞丢，MQ 到消费过程中搞丢。

- 生产者到 RabbitMQ：**事务机制和 Confirm 机制，注意：事务机制和 Confirm 机制是互斥的，两者不能共存，会导致 RabbitMQ 报错。**
- RabbitMQ 自身：**持久化、集群、普通模式、镜像模式。**
- RabbitMQ 到消费者：**basicAck 机制、死信队列、消息补偿机制。**

## 如何保证RabbitMQ消息的顺序性

- 拆分多个 queue(消息队列)，每个 queue(消息队列) 一个 consumer(消费者)，就是多一些 queue (消息队列)而已，确实是麻烦点；
- 或者就一个 queue (消息队列)但是对应一个 consumer(消费者)，然后这个 consumer(消费者)内部用内存队列做排队，然后分发给底层不同的 worker 来处理

## 如何保证RabbitMQ高可用的

R**abbitMQ 是比较有代表性的，因为是基于主从（非分布式）做高可用性的，**我们就以 RabbitMQ 为例子讲解第一种 MQ 的高可用性怎么实现。**RabbitMQ 有三种模式：单机模式、普通集群模式、镜像集群模式。**

- 单机模式

  Demo 级别的，一般就是你本地启动了玩玩儿的?，没人生产用单机模式。

- 普通集群模式

  意思就是在多台机器上启动多个 RabbitMQ 实例，每个机器启动一个。**你创建的 queue，只会放在一个 RabbitMQ 实例上，但是每个实例都同步 queue 的元数据（**元数据可以认为是 queue 的一些配置信息，**通过元数据，可以找到 queue 所在实例）。**

  你消费的时候，实际上**如果连接到了另外一个实例，那么那个实例会从 queue 所在实例上拉取数据过来。这方案主要是提高吞吐量的，就是说让集群中多个节点来服务某个 queue 的读写操作。**

- 镜像集群模式

  这种模式，才是所谓的 RabbitMQ 的高可用模式。跟普通集群模式不一样的是**，在镜像集群模式下，你创建的 queue，无论元数据还是 queue 里的消息都会存在于多个实例上**，就是说，每个 RabbitMQ 节点都有这个 queue 的一个完整镜像，包含 queue 的全部数据的意思。**然后每次你写消息到 queue 的时候，都会自动把消息同步到多个实例的 queue 上。**RabbitMQ 有很好的管理控制台，就是在后台新增一个策略，这个策略是镜像集群模式的策略，指定的时候是可以要求数据同步到所有节点的，也可以要求同步到指定数量的节点，再次创建 queue 的时候，应用这个策略，就会自动将数据同步到其他的节点上去了。

  这样的好处在于，你任何一个机器宕机了，没事儿，其它机器（节点）还包含了这个 queue 的完整数据，别的 consumer 都可以到其它节点上去消费数据。坏处在于**，第一，这个性能开销也太大了吧**，消息需要同步到所有机器上，导致网络带宽压力和消耗很重！RabbitMQ 一个 queue 的数据都是放在一个节点里的，镜像集群下，也是每个节点都放这个 queue 的完整数据

## 如何解决消息队列的**延时以及过期失效**问题

RabbtiMQ 是可以设置过期时间的，也就是 TTL。如果**消息在 queue 中积压超过一定的时间就会被 RabbitMQ 给清理掉，**这个数据就没了。那这就是第二个坑了。这就不是说数据会大量积压在 mq 里，而是大量的数据会直接搞丢。我们可以采取一个方案，就是批量重导，这个我们之前线上也有类似的场景干过。就是大量积压的时候，我们当时就直接丢弃数据了，然后等过了高峰期以后，比如大家一起喝咖啡熬夜到晚上 12 点以后，用户都睡觉了。这个时候我们就开始写程序，将丢失的那批数据，写个临时程序，一点一点的查出来，然后重新灌入 mq 里面去，把白天丢的数据给他补回来。也只能是这样了。假设 1 万个订单积压在 mq 里面，没有处理，其中 1000 个订单都丢了，你只能手动写程序把那 1000 个订单给查出来，手动发到 mq 里去再补一次。

## RabbitMQ可靠性

1. 添加消息到队列中，保证消息成功的添加到了队列
2. 保证消息发送出去，一定会被消费者正常消费
3. 消费者正常消费了，生产者或者队列 知道消费者已经成功的消费了消息

## 问题一解决方案-添加消息到队列中，保证消息成功的添加到了队列

添加消息到队列中，保证消息成功的添加到了队列

- 通过AMQP事务机制实现，这也是AMQP协议层面提供的解决方案；（影响性能）
- 通过将channel设置成confirm模式来实现；

### Confirm消息确认模式

confirm机制： 只要放消息到 queue 是成功的，那么队列就一定会反馈，表示发送成功；并且，只要是成功的发送到队列中，就会反馈成功，就算是在队列中，消费者接收失败，也会反馈成功！

#### 实现原理

​        生产者将信道设置成confirm模式，一旦信道进入confirm模式，所有在该信道上面发布的消息都会被指派一个唯一的ID(从1开始)；

​        一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认消息给生产者（包含消息的唯一 ID）,这就使得生产者知道消息已经正确到达目的队列了；

​        如果消息和队列是可持久化的，那么确认消息会将消息写入磁盘之后发出，broker回传给生产者的确认消息中deliver-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。

#### 优点

- 支持异步，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果RabbitMQ因为自身内部错误导致消息丢失，就会发送一条nack消息，生产者应用程序同样可以在回调方法中处理该nack消息。
- 在channel 被设置成 confirm 模式之后，所有被 publish 的后续消息都将被 confirm（即 ack） 或者被nack一次。但是没有对消息被 confirm 的快慢做任何保证，并且同一条消息不会既被 confirm又被nack 。

## 问题二解决方案-保证消息发送出去，一定会被消费者正常消费

使用 RabbitMQ中的 ACK 机制，就是手动确认消息。

消费者在声明队列时，可以指定noAck参数，当noAck=false时，RabbitMQ会等待消费者显式发回ack信号后才从内存(或者磁盘，持久化消息)中移去消息。否则，消息被消费后会被立即删除。

消费者接收每一条消息后都必须进行确认（消息接收和消息确认是两个不同操作）。只有消费者确认了消息，RabbitMQ 才能安全地把消息从队列中删除。

RabbitMQ不会为未ack的消息设置超时时间，它判断此消息是否需要重新投递给消费者的唯一依据是消费该消息的消费者连接是否已经断开。这么设计的原因是RabbitMQ允许消费者消费一条消息的时间可以很长。保证数据的最终一致性；

如果消费者返回ack之前断开了链接，RabbitMQ 会重新分发给下一个订阅的消费者。（可能存在消息重复消费的隐患，需要去重）；

## 问题三解决方案-消费者正常消费了，生产者或者队列 知道消费者已经成功的消费了消息 

和问题2的解决方法一致，在手动签收消息之后做手动告诉生产者已经消费成功

## RabbitMQ持久化

可以进行消息持久化, 即使rabbitMQ挂了，重启后也能恢复数据

如果要进行消息持久化，那么需要对以下3种实体均配置持久化

a) Exchange

声明exchange时设置持久化（durable = true）并且不自动删除(autoDelete = false)

b) Queue

声明queue时设置持久化（durable = true）并且不自动删除(autoDelete = false)

c) message

发送消息时通过设置deliveryMode=2持久化消息

# 04. 除了 Redis，你还知道其他分布式缓存方案吗？
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

   8.心跳机制中可以结合 TCP 的 keepalive 和应用层心跳来一起使用吗？
   如果从实现功能角度看，传输层和应用层的心跳机制没有结合的必要，因为传输层的心跳探测连接可用性，应用层的心跳机制也可以完成探测; 但从debug角度看，应用层的心跳探测机制无法定位是网络的问题还是系统的问题，此时由传输层辅助就非常好，但实现会相对复杂！

   9.二分法来进行心跳探测 的逻辑？
   其实就是下一次动态调整的心跳间隔是：当前已经确认的安全的心跳间隔最大值 和 已经确认的心跳探测过大的最小值 的中间均值。比如上一次心跳间隔是4分钟，而且连续N次都成功ack了，那么当前已经确认的安全的心跳间隔是4分钟，假设已经确认10分钟时心跳间隔过大了，那么下一次调整的心跳就是 （4 + 10） / 2 = 7分钟。

   10.tcp的keepalive怎么开启？
   比如netty下可以通过如下代码开启：

   ServerBootstrap bootstrap = new ServerBootstrap();
   bootstrap.childOption(ChannelOption.SO_KEEPALIVE, true);

   另外tcp keepalive的心跳间隔的配置也需要修改一下系统的/etc/sysctl.conf，类似下面：

   net.ipv4.tcp_keepalive_time=120
   net.ipv4.tcp_keepalive_intvl=30
   net.ipv4.tcp_keepalive_probes=3

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
