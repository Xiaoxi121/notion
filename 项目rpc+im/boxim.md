# -----cakeim功能模块-----

友联[聊天室 (yuque.com)](https://www.yuque.com/bless-8rrm7/pqswof/lgp3g8ui0htzv6ai?singleDoc#)

# 01 基础介绍

- 盒子IM是一个仿微信实现的开源聊天软件，支持内网部署，不依赖任何收费SDK或组件
- 支持web端和移动端同时在线以及消息同步
- 后端服务支持集群化部署，具有良好的横向扩展能力
- 消息推送功能已进行SDK封装，可快速接入企业项目

<img src="D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240820103853536.png" alt="image-20240820103853536" style="zoom: 67%;" />

# 02 本地环境搭建

1. docker pull mysql:5.7

2. docker pull redis:3.0

3. docker pull minio/minio

4. docker pull node:14.21.3

5. 查看minio账号密码

   1. 启动minio

      ```shell
      docker run -d \
        --name=minio \
        -p 9000:9000 \
        -p 9001:9001 \
        -e MINIO_ROOT_USER=admin \
        -e MINIO_ROOT_PASSWORD=admin123456 \
        -e TZ=Asia/Shanghai \
        -v /home/minio/data:/data \
        minio/minio server /data \
        --console-address :9001 \
        --address :9000
      ```

      (开放端口)

      ```shell
      firewall-cmd --zone=public --add-port=9000/tcp --permanent #开放9000端口
      firewall-cmd --reload #配置立即生效
      firewall-cmd --zone=public --list-ports #查看防火墙所有开放的端口
      ```

      <img src="D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240820105200003.png" alt="image-20240820105200003" style="zoom: 200%;" />

   2. 查看账号密码

      docker ps -a

       docker logs [CONTAINER ID]

      ![image-20240820105244169](D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240820105244169.png)

6. 建立redis容器

   ```shell
   docker run -d \
     --name redis \
     -p 6379:6379 \
     -e TZ=Asia/Shanghai \ 
     redis
   ```

7. 启动mysql redis minio 

   `docker start mysql redis minio`

8. 打开im-platform中的application-dev.yml文件 将数据库和minio的账号及密码修改为自己的

   1. 数据库密码:123456

   2. minio账号密码修改

      minio:

      endpoint: http://192.168.5.154:9001

      public:http://192.168.5.154:9001

      accessKey:admin

      secretKey:admin123456

   3. 修改数据库地址

      `url: jdbc:mysql://localhost:3306/box2im?useSSL=false&useUnicode=true&characterEncoding=utf-8&allowPublicKeyRetrieval=true`

9. 连接数据库 生成数据库 `create database box2im;`使用数据库 `use box2im;`

10. 将模块依赖版本换成自己的jdk17

11. 启动web端

    ```shell
    cd im-web
    npm install
    npm run serve
    ```

12. ws无法连接是什么原因？

    - 未修改配置。需修改前端的VUE_APP_WS_URL变量为im-server的服务地址
    - ws url前缀不正确。不带ssl证书时前缀是"ws://"，带ssl证书是"wss://"
    - 网站配置了ssl证书，但是没有为ws配置证书。

13. 生产部署时出现跨域问题如何解决？

    将前端页面、后端端口、minio文件都通过nginx的同一个端口进行代理，这样可以避免跨域。可以参考上面的nginx配置

# 03 数据库和架构设计

## 技术选型

### 框架

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
|                                             |                              |                                                              |

## 项目结构

| **模块**    | **功能**     | **可运行** | **说明**                                                     |
| ----------- | ------------ | ---------- | ------------------------------------------------------------ |
| im-platform | 业务平台服务 | 是         | 负责接收前端的http请求，处理盒子IM的所有业务                 |
| im-server   | 消息推送服务 | 是         | 仅实现消息推送，不参与任何业务。其他服务(im-platform)需通过im-client与im-server通信 |
| im-client   | 消息推送sdk  | 否         | 集成到其他服务(im-platform)，使其能够与im-server通信，实现消息推送 |
| im-common   | 公共包       | 否         | 被所有后端模块引用                                           |
| im-web      | web页面      | 是         | web端页面                                                    |
| im-uniapp   | app页面      | 是         | 移动端页面，包括app、h5、微信小程序                          |

![image-20240820155351270](D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240820155351270.png)

# 04 用户登录和鉴权

## 4.1 LoginController

```java
@Tag(name = "注册登陆")
@RestController
@RequiredArgsConstructor
public class LoginController {
    private final UserService userService;
    1.2.3.4
}
```

restful api的在线文档生成工具

1. knife4j

   knife4j是为Java MVC框架集成Swagger生成Api文档的增强解决方案,前身是swagger-bootstrap-ui,取名kni4j是希望她能像一把匕首一样小巧,轻量,并且功能强悍。
   优点：基于swagger生成实时在线文档，支持在线调试，全局参数、国际化、访问权限控制等，功能非常强大。
   缺点：界面有一点点丑，需要依赖额外的jar包
   个人建议：如果公司对ui要求不太高，可以使用这个文档生成工具，比较功能还是比较强大的。

2. yapi

   yapi是去哪儿前端团队自主研发并开源的，主要支持以下功能：
   可视化接口管理
   数据mock
   自动化接口测试
   数据导入（包括swagger、har、postman、json、命令行）
   权限管理
   支持本地化部署
   支持插件
   支持二次开发
   优点：功能非常强大，支持权限管理、在线调试、接口自动化测试、插件开发等，BAT等大厂等在使用，说明功能很好。
   缺点：在线调试功能需要安装插件，用户体检稍微有点不好，主要是为了解决跨域问题，可能有安全性问题。不过要解决这个问题，可以自己实现一个插件，应该不难。
   个人建议：如果不考虑插件安全的安全性问题，这个在线文档工具还是非常好用的，可以说是一个神器，笔者在这里强烈推荐一下。

3. Open API专用注解

   1. @Operation

      @Operation 注解则用于描述一个具体的API操作。每个HTTP请求路径上的特定HTTP方法（如GET、POST、PUT、DELETE等）都可以使用@Operation注解来详细说明它的行为。@Operation可以用来描述操作的摘要、详细描述、响应类型、响应状态码以及其他相关信息

   2. @Schema

      @Schema 是一个用于描述数据模型的注解，它也是OpenAPI规范的一部分。在OpenAPI 3.x中，@Schema 注解用于定义数据结构，包括请求体和响应体的数据格式。它可以帮助描述API中的数据模型，包括字段的类型、是否必须、默认值、示例值等信息。

4. **OpenAPI 是一个用于描述 API 的标准规范，而 Knife4j 和 YAPI 分别是对这一规范的应用和扩展。Knife4j 主要用于在基于 Spring Boot 的项目中生成和美化 API 文档，而 YAPI 是一个更为全面的 API 管理平台，适用于需要协作和管理的场景。**

### @RestController

[【SpringBoot】带你一文彻底搞懂RestController和Controller的关系与区别-CSDN博客](https://blog.csdn.net/miles067/article/details/132567377)

​	=@Controller+@ResponseBody，用于创建RESTful风格的API。每个方法的返回值序列化为特定格式（如JSON或者XML的形式）直接写入HTTP响应体中，相当于每个方法都添加了@ResponseBody注解。传统的springMVC一般就需要直接返回视图，而现在新兴的前端技术vue在项目中为前后端分离的架构，前端框架负责处理数据和渲染页面，而后端 API 则负责提供数据即可，所以对返回视图的要求也就比较少了

@Controller

​	适用于传统的MVC架构，标记的类是传统的控制器类，用于处理客户端发起的请求，并负责返回适当的视图（View）作为响应。（@RestController下的方法默认返回的是数据格式）

@ResponseBody

​	指示该方法的返回值要作为响应的主体内容，而不是解析为视图（视图->返回的结果会被解析为一个HTML页面或者模板引擎所需要的数据）。

### mvc架构

model view controller

1. controller 控制层

   **Controller**层的主要作用是接收客户端请求，将请求参数传递给**Service**层进行处理，并将处理结果以适当的形式返回给客户端。

2. service 业务逻辑层

   它接收来自**Controller**层的请求，调用**Mapper**层的方法进行数据操作，并将结果返回给**Controller**层。

3. mapper数据访问层（dao层或者repository层）

   **Mapper**层的主要作用是访问[数据库](https://cloud.baidu.com/solution/database.html)，执行数据的增删改查操作。它通常包含一些基本的SQL语句或使用ORM框架提供的API来执行数据库操作。

4. entity层（实体层）

   **Entity**层是存放实体的类，类中定义了多个属性，并且与数据库表的字段保持一致。Entity层的定义主要用于定义与数据库对象对应的属性，提供get/set方法、toString方法以及有参无参构造函数等

### @RequiredArgsConstructor

[@NoArgsConstructor、@AllArgsConstructor、@RequiredArgsConstructor的区别以及在springboot常用地方-CSDN博客](https://blog.csdn.net/xueyijin/article/details/124618309?ops_request_misc=%7B%22request%5Fid%22%3A%22172414254416800211515306%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172414254416800211515306&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-124618309-null-null.142^v100^control&utm_term=requiredargsconstructor&spm=1018.2226.3001.4187)
@NoArgsConstructor：生成无参的构造方法。
@AllArgsConstructor：生成该类下全部属性的构造方法。
@RequiredArgsConstructor：生成该类下被final修饰或者non-null字段生成一个构造方法。
场景：
在springboot中，对于一个bean类，注入其他bean的时候，常见的是使用@Autowired，实际上也可以使用构造函数注入，这个时候就可以使用@AllArgsConstructor或者@RequiredArgsConstructor来代替。

```java
@AllArgsConstructor
public class demo {

    private String name;

    // 被final修饰
    private final String age;

    @NonNull
    private String sex;
}
//反编译后
public class demo
{
	// 默认 只要是该类下的字段，无论什么修饰，都会被参与构造
    public demo(String name, String age, String sex)
    {
        if(sex == null)
        {
            throw new NullPointerException("sex is marked non-null but is null");
        } else
        {
            this.name = name;
            this.age = age;
            this.sex = sex;
            return;
        }
    }

    private String name;
    private final String age;
    private String sex;
}

```

```java
@RequiredArgsConstructor
public class demo {

    private String name;

    // 被final修饰
    private final String age;

    @NonNull
    private String sex;
}

//反编译后
public class demo
{
	// 只构造了有final或者no-null修饰的字段
    public demo(String age, String sex)
    {
        if(sex == null)
        {
            throw new NullPointerException("sex is marked non-null but is null");
        } else
        {
            this.age = age;
            this.sex = sex;
            return;
        }
    }

    private String name;
    private final String age;
    private String sex;
}

```

#### 注入Bean的方式有哪些？

依赖注入的常见方式：

- **构造函数注入**：通过类的构造函数来注入依赖项

  ```java
  @Service
  public class UserService {
  
      private final UserRepository userRepository;
  
      public UserService(UserRepository userRepository) {
          this.userRepository = userRepository;
      }
  
      //...
  }
  ```

- **Setter注入**：通过类的Setter方法来注入依赖项

  ```java
  @Service
  public class UserService {
  
      private UserRepository userRepository;
  
      // 在 Spring 4.3 及以后的版本，特定情况下 @Autowired 可以省略不写
      @Autowired
      public void setUserRepository(UserRepository userRepository) {
          this.userRepository = userRepository;
      }
  
      //...
  }
  ```

- **Field（字段）注入**：直接在类的字段上使用注解（如@Autowired或@Resource来注入依赖项）

  ```java
  @Service
  public class UserService {
  
      @Autowired
      private UserRepository userRepository;
  
      //...
  }
  ```

#### 构造函数注入还是Setter注入？

Spring 官方推荐构造函数注入，这种注入方式的优势如下：

- **依赖完整性：**确保所有必需依赖在对象创建时就被注入，避免了空指针异常的风险。
- **不可变性：**有助于创建不可变对象，提高了线程安全性。
- **初始化保证：**组件在使用前已完全初始化，减少了潜在的错误。
- **测试便利性：**在单元测试中，可以直接通过构造函数传入模拟的依赖项，而不必依赖 Spring 容器进行注入。

**构造函数注入适合处理必需的依赖项，而 Setter 注入 则更适合可选的依赖项，**这些依赖项可以有默认值或在对象生命周期中动态设置。虽然 @Autowired 可以用于 Setter 方法来处理必需的依赖项，但构造函数注入仍然是更好的选择。

### final

[java中final关键字_java写一个程序演示被final关键字修饰的变量只能赋值一次,在没有对他赋初值时,-CSDN博客](https://blog.csdn.net/MTCya/article/details/107327387?ops_request_misc=%7B%22request%5Fid%22%3A%22172414322416800211530028%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172414322416800211530028&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-107327387-null-null.142^v100^control&utm_term=final&spm=1018.2226.3001.4187)

final修饰的类不能被继承

```java
final class A{//A 是没有子孙的}
class B extends A{
    //错误的, 无法从最终类A进行继承
    //B类继承A类 相当于对A类功能进行扩展
    //如果不希望别人对A类进行扩展，可以给A类加final关键字，如String类
}
```

- 修饰方法 **该方法是最终方法不能被重写**
- 修饰类     **该类是最终类 不能被继承**
- 修饰变量 **叫做常量 只能被赋值一次**

## 4.2 UserServiceImpl

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class UserServiceImpl extends ServiceImpl<UserMapper, User> implements UserService {
    private final PasswordEncoder passwordEncoder;
    private final GroupMemberService groupMemberService;
    private final FriendService friendService;
    private final JwtProperties jwtProperties;
    private final IMClient imClient;
}
```

### @Service

@Service 注解是 Spring Framework 中的一种注解，它标识了这个类是一个**业务逻辑层**的服务 Bean。这意味着当 Spring 应用启动时，该 Bean 会被自动创建并加入到 Spring 应用上下文中。@Service 注解是一种用于标记服务层 Bean 的注解，是在 Spring Boot 应用中实现业务逻辑复用的重要方法之一。不需要自己手动管理对象的创建和销毁，也不需要自己手动维护对象之间的依赖关系。提高代码的可维护性。

### IService

[Mybatis-plus中IService接口的使用_mybatisplus iservice-CSDN博客](https://blog.csdn.net/weixin_46751741/article/details/129043556?ops_request_misc=%7B%22request%5Fid%22%3A%22172414373116800213032358%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172414373116800213032358&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-129043556-null-null.142^v100^control&utm_term=IService&spm=1018.2226.3001.4187)

IService的使用需要另外两个接口的配合：[baseMapper](https://so.csdn.net/so/search?q=baseMapper&spm=1001.2101.3001.7020)和ServiceImpl

1. 实现basemapper接口

   ```java
   public interface UserMapper extends BaseMapper<User> {}
   ```

2. 编写service类

   ```java
   public interface UserService extends IService<User> {}
   ```

3. 编写serviceImpl，ServiceImpl里面是各种的方法实现，好奇的可以点进源码看下，两个泛型需要注意的，第一个是继承basemapper的(UserMapper)，第二个是实体类(User)。

   ```java
   public class UserServiceImpl extends ServiceImpl<UserMapper,User> 
       						  implements UserService {}
   ```

   ```java
   //查看源码后发现ServiceImpl类使用到了BaseMapper接口T和实现类M，并实现了IService接口
   public class ServiceImpl<M extends BaseMapper<T>, T> implements IService<T> {...}
   ```

1、从分层角度来解释，BaseMapper是DAO层的CRUD封装，而IService是业务业务逻辑层的CRUD封装，所以多了批量增、删、改的操作封装，这也比较符合官方指南中的阐述；
2、IService是对BaseMapper的扩展，从BaseMapper、IService、ServiceImpl三者的类关系以及源码可以看出；
此外，个人认为应该还有一个原因，就是IService和BaseMapper提供的是两种实现方式：
如果继承BaseMapper，则不需要去实现其内部方法，依靠mybatis的动态代理即可实现CRUD操作；
而如果自定义IBaseService去继承IService，则需要去实现IService中的方法；

## 4.3 用户注册

### register

```java
    @PostMapping("/register")
    @Operation(summary = "用户注册", description = "用户注册")
    public Result register(@Valid @RequestBody RegisterDTO dto) {
        userService.register(dto);
        return ResultUtils.success();
    }
```

#### @PostMapping

[Spring Boot中的RESTful API：@GetMapping, @PostMapping, @PutMapping, 和 @DeleteMapping详解_springboot getmapping postmapping-CSDN博客](https://blog.csdn.net/weixin_61034310/article/details/135324203?ops_request_misc=&request_id=&biz_id=102&utm_term=postmapping&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-1-135324203.142^v100^control&spm=1018.2226.3001.4187)

#### @Valid

[@Valid和@Validated注解校验以及异常处理_valid注解-CSDN博客](https://blog.csdn.net/m0_58680865/article/details/127817779?ops_request_misc=%7B%22request%5Fid%22%3A%22172420462016800186527608%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172420462016800186527608&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-127817779-null-null.142^v100^control&utm_term=@valid&spm=1018.2226.3001.4187)

@Valid注解可以作用于：方法、属性（包括枚举中的常量）、构造函数、方法的形参上。

1. 空校验

   注解										应用
   @Null			用于基本类型上，限制只能为null
   @NotNull		 用在基本类型上；不能为null，但可以为empty,没有Size的约束
   @NotEmpty	     用在集合类上；不能为null，而且长度必须大于0
   @NotBlank	      只能作用在String上，不能为null，而且调用trim()后，长度必须大于0

   > [!TIP]
   >
   > 插播一条小内容！！！null和empty有何区别？
   >
   > String a = new String
   > String b = ""
   > String c = null
   > 此时a是分配了内存空间，但值为空，是绝对的空，是一种有值（值存在为空而已）
   > 此时b是分配了内存空间，值为空字符串，是相对的空，是一种有值（值存在为空字串）
   > 此时c是未分配内存空间，无值，是一种无值(值不存在)

2. Boolean校验

   | 注解         | 应用            |
   | ------------ | --------------- |
   | @AssertFalse | 限制必须为false |
   | @AssertTrue  | 限制必须为true  |

3. 长度校验

   | 注解                | 应用                                                         |
   | ------------------- | ------------------------------------------------------------ |
   | @Size(max,min)      | 验证对象（Array,[Collection](https://so.csdn.net/so/search?q=Collection&spm=1001.2101.3001.7020),Map,String）长度是否在给定的范围之内 |
   | @Length(min=, max=) | 验证`字符串`长度是否在给定的范围之内                         |

4. 日期校验

   | 注解            | 应用                                              |
   | --------------- | ------------------------------------------------- |
   | @Past           | 限制必须是一个过去的日期,并且类型为java.util.Date |
   | @Future         | 限制必须是一个将来的日期,并且类型为java.util.Date |
   | @Pattern(value) | 限制必须符合指定的正则表达式                      |

5. 数值校验

   注解	应用
   @Min(value)	验证 Number 和 String 对象必须为一个不小于指定值的数字
   @Max(value)	验证 Number 和 String 对象必须为一个不大于指定值的数字
   @DecimalMax(value)	限制必须为一个不大于指定值的数字,小数存在精度
   @DecimalMin(value)	限制必须为一个不小于指定值的数字,小数存在精度
   @Digits(integer,fraction)	限制必须为一个小数，且整数部分的位数不能超过integer，小数部分的位数不能超过fraction
   @Digits	验证 Number 和 String 的构成是否合法
   @Range(max =3 , min =1 , message = " ")	Checks whether the annotated value lies between (inclusive) the specified minimum and maximum

   > [!TIP]
   >
   > 建议使用在Stirng,Integer类型，不建议使用在int类型上，因为表单值为“”时无法转换为int，但可以转换为Stirng为"",Integer为null。
   >
   > Max和Min是对你填的“数字”是否大于或小于指定值，这个“数字”可以是number或者string类型。长度限制用length

6. 其他校验

   注解应用@Email验证注解的元素值是Email，也可以通过正则表达式和flag指定自定义的email

具体使用

1. 实体类中添加 @Valid 相关注解
2. 接口类中添加 @Valid 注解
3. 全局异常处理类中处理 @Valid 抛出的异常

用户访问接口，然后进行参数效验，因为 @Valid 不支持平面的参数效验（直接写在参数中字段的效验）所以基于 GET 请求的参数还是按照原先方式进行效验，而 POST 则可以以实体对象为参数，可以使用 @Valid 方式进行效验。如果效验通过，则进入业务逻辑，否则抛出异常，交由全局异常处理器进行处理。
![在这里插入图片描述](D:\2024\Notes\Typora\项目rpc+im\boxim.assets\92107f1074a38f6a6fcc1665e49f419e.png)

#### @RequestBody

[@RequestBody、@RequestParam 、@PathVariable、@RequestPart-CSDN博客](https://blog.csdn.net/qq_35341203/article/details/108877579?ops_request_misc=%7B%22request%5Fid%22%3A%22172420546616800180673267%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172420546616800180673267&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-3-108877579-null-null.142^v100^control&utm_term=RequetBody&spm=1018.2226.3001.4187)

@RequestBody（一个方法中能使用多次，但是建议只使用一次）

@RequestParam 以及 @PathVariable：@PathVariable 用于获取路径参数，@RequestParam 用于获取查询参数。

```java
@GetMapping("/klasses/{klassId}/teachers")
public List<Teacher> getKlassRelatedTeachers(
         @PathVariable("klassId") Long klassId,
         @RequestParam(value = "type", required = false) String type ) {
...
}
```

### register-Impl

```java
    @Override
    public void register(RegisterDTO dto) {
        User user = this.findUserByUserName(dto.getUserName());
        if (!Objects.isNull(user)) {
            throw new GlobalException(ResultCode.USERNAME_ALREADY_REGISTER);
        }
        user = BeanUtils.copyProperties(dto, User.class);
        user.setPassword(passwordEncoder.encode(user.getPassword()));
        this.save(user);
        log.info("注册用户，用户id:{},用户名:{},昵称:{}", user.getId(), dto.getUserName(), dto.getNickName());
    }
```

```java
    @Override
    public User findUserByUserName(String username) {
        LambdaQueryWrapper<User> queryWrapper = Wrappers.lambdaQuery();
        queryWrapper.eq(User::getUserName, username);
        return this.getOne(queryWrapper);
    }
```

#### java中常见判空方式

[Java 判空的常见方法以及工具类的总结_java判空-CSDN博客](https://blog.csdn.net/weixin_49171365/article/details/130543354)

1. 判断对象obj是否为null
   1. obj==null
   2. Objects.isNull(obj)
2. 判断字符串str是否为空或为null
   1. 为null  str==null
   2. 为空字符串str.isEmpty()
   3. 只包含空格str.isBlank()
   4. 也可以用StringUtils工具类来判断
3. 判断集合list是否为空或为null
   1. 为null list==null       或者  Objects.isNull(list)
   2. 为空 list.isEmpty()
4. 判断数组arr为空或为null
   1. 为null arr = null
   2. 为空arr.length = 0

#### this

[【Java】还不懂this关键字？一分钟彻底弄懂this关键字_java this-CSDN博客](https://blog.csdn.net/Yaoyao2024/article/details/128753927?ops_request_misc=%7B%22request%5Fid%22%3A%22172420682616800185832384%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172420682616800185832384&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-128753927-null-null.142^v100^control&utm_term=this&spm=1018.2226.3001.4187)

1. this相当于对象的一个隐藏属性 属性的值就是对象在堆内存中地址，即this指向该对象
2. 使用this可以访问本类的实例属性、实例方法、构造器（实例指的是non-static修饰的）

#### save、getOne

- `save`、`getOne`、`queryMapper`等方法名确实是MyBatis Plus框架中提供的常用方法。MyBatis Plus是一个MyBatis的增强工具，旨在简化MyBatis的使用，提供了一系列便捷的方法来处理常见的CRUD
- `save` 方法用于插入一条记录。如果记录的主键存在，则会先调用 `updateById` 方法尝试更新，否则会插入一条新记录。
- `getOne` 方法用于根据给定的条件获取一条记录。此方法返回一个 `Optional<T>` 对象，如果找不到对应的记录，则返回空的 `Optional`。
- `queryMapper` 并不是MyBatis Plus中的标准方法名，通常情况下，你可能会看到 `query` 相关的方法或者使用 `QueryWrapper` 构建查询条件。`QueryWrapper` 是一个灵活的查询构造器，可以用来构建复杂的查询条件

#### Passwordencoder 

PasswordEncoder是一个密码解析器
Spring Security封装了如bcrypt, PBKDF2, scrypt, Argon2等主流适应性单向加密方法（ adaptive one-way functions），用以进行密码存储和校验。单向校验安全性高，但开销很大，单次密码校验耗时可能高达1秒，故针对高并发性能要求较强的大型信息系统，Spring Security更推荐选择如：session, OAuth，Token等开销很小的短期加密策略（short term credential）实现系统信息安全。

### 实现过程

根据传入dto中的用户名字在数据库中查用户，如果用户已存在抛出异常，如果不存在的话，将dto中的数据拷贝到user类中，对密码进行加密，然后保存到数据库中。

## 4.4 :star:用户登录和鉴权交互流程

- 方案一(session)：整合Spring security,同时将session缓存到redis实现集群化管理
  - 同一个浏览器无法同时登陆多个账号：第2个账号登录时，会自动顶掉第一个账号的session
  - ws连接校验不容易实现： session是通过http的header中的sessionId实现，因此与http协议是强捆绑关系，并不支持ws协议
- 方案二(token):  通过jwt生成token,每次请求都携带此token,后端通过拦截器解析token

#### 获取token

<img src="D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240821105918820.png" alt="image-20240821105918820" style="zoom:67%;" />

#### token校验

token校验通过拦截器实现，对所有接口（除登陆等个别接口）进行统一拦截校验

<img src="D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240821110139568.png" alt="image-20240821110139568" style="zoom:67%;" />

#### 刷新token

accessToken过期时间：半个小时，refreshToken过期时间：7天。

当accessToken过期时，需要调用/refreshToken接口，换取新的Token

<img src="D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240821110339131.png" alt="image-20240821110339131" style="zoom:67%;" />

#### 用户无感刷新token

- 方案一：前端用定时器对token的过期时间进行检测，如果快过期了，则进行token刷新
- 方案二：通过前端的http拦截器实现，用户发请求时被此拦截器捕获，此时如果发现token过期时，进行刷新token,然后重新发起请求

<img src="D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240821110737460.png" alt="image-20240821110737460" style="zoom:67%;" />

## 4.5:star:获取token

### login

```java
    @PostMapping("/login")
    @Operation(summary = "用户登陆", description = "用户登陆")
    public Result<LoginVO> login(@Valid @RequestBody LoginDTO dto) {
        LoginVO vo = userService.login(dto);
        return ResultUtils.success(vo);
    }
```

#### vo、dto区别

1、entity 里的每一个字段，与数据库相对应，

2、vo 里的每一个字段，是和你前台 html 页面相对应，

3、dto 这是用来转换从 entity 到 vo，或者从 vo 到 entity 的中间的东西 。（DTO中拥有的字段应该是entity中或者是vo中的一个子集）

- VO (View Object)，用于表示一个与前端进行交互的视图对象，它的作用是把某个指定页面(或组件)的所有数据封装起来。实际上，这里的 VO 只包含前端需要展示的数据，对于前端不需要的数据，比如数据创建和修改的时间等字段，出于减少传输数据量大小和保护数据库结构不外泄的目的，不应该在 VO 中体现出来。
- DTO(Data Transfer Object)，用于表示一个数据传输对象，DTO 通常用于展示层(Controller)和服务层(Service)之间的数据传输对象。DTO 与 VO 概念相似，并且通常情况下字段也基本一致。但 DTO 与 VO 又有一些不同，这个不同主要是设计理念上的，比如 API 服务需要使用的 DTO 就可能与 VO 存在差异。
- DO(Data Object) ，持久化对象，它跟持久层(Dao)的数据结构形成一一对应的映射关系。如果持久层是关系型数据库，那么数据库表中的每个字段就对应PO的一个属性，常是entity实体类。
- BO（Business Object）：业务对象，就是从现实世界中抽象出来的有形或无形的业务实体。

![阿里巴巴Java开发手册](D:\2024\Notes\Typora\项目rpc+im\boxim.assets\f62264a407ae0a08e2c7a1f8b519056f.png)

### login-Impl

```java
    @Override
    public LoginVO login(LoginDTO dto) {
        User user = this.findUserByUserName(dto.getUserName());
        if (Objects.isNull(user)) {
            throw new GlobalException("用户不存在");
        }
        if (user.getIsBanned()) {
            String tip = String.format("您的账号因'%s'已被管理员封禁,请联系客服!",user.getReason());
            throw new GlobalException(tip);
        }
        if (!passwordEncoder.matches(dto.getPassword(), user.getPassword())) {
            throw new GlobalException(ResultCode.PASSWOR_ERROR);
        }
        // 生成token
        UserSession session = BeanUtils.copyProperties(user, UserSession.class);
        session.setUserId(user.getId());
        session.setTerminal(dto.getTerminal());
        String strJson = JSON.toJSONString(session);
        String accessToken = JwtUtil.sign(user.getId(), strJson, jwtProperties.getAccessTokenExpireIn(),jwtProperties.getAccessTokenSecret());
        String refreshToken = JwtUtil.sign(user.getId(), strJson, jwtProperties.getRefreshTokenExpireIn(),jwtProperties.getRefreshTokenSecret());
        LoginVO vo = new LoginVO();
        vo.setAccessToken(accessToken);
        vo.setAccessTokenExpiresIn(jwtProperties.getAccessTokenExpireIn());
        vo.setRefreshToken(refreshToken);
        vo.setRefreshTokenExpiresIn(jwtProperties.getRefreshTokenExpireIn());
        return vo;
    }
}
```

### 实现过程

​	用户存在、未被禁用、密码正确的情况下，生成token，其中：

- accessToken、refreshToken，由jwtutil签发，包含uid、session转为的字符串、过期时间和密钥
- accessTokenExpiresIn，refreshTokenExpiresIn

## 4.6  :star:token校验

### 拦截器

[SpringBoot之HandlerInterceptor拦截器的使用-阿里云开发者社区 (aliyun.com)](https://developer.aliyun.com/article/897450)

#### **1.实现方式**

##### **1.1 步骤一：自定义拦截器**

自定义拦截器，即拦截器的实现类，一般有两种自定义方式：

1. 定义一个类，实现`org.springframework.web.servlet.HandlerInterceptor`接口。
2. 定义一个类，继承已实现了HandlerInterceptor接口的类，例如`org.springframework.web.servlet.handler.HandlerInterceptorAdapter`抽象类。

##### **1.2 步骤二：添加Interceptor拦截器到WebMvcConfigurer配置器中**

自定义配置器，然后实现WebMvcConfigurer配置器。

以前一般继承`org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter`类，不过SrpingBoot 2.0以上WebMvcConfigurerAdapter类就过时了。有以下2中替代方法：

1. 直接实现`org.springframework.web.servlet.config.annotation.WebMvcConfigurer`接口。（推荐）
2. 继承`org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport`类。但是继承WebMvcConfigurationSupport会让SpringBoot对mvc的自动配置失效。不过目前大多数项目是前后端分离，并没有对静态资源有自动配置的需求，所以继承WebMvcConfigurationSupport也未尝不可。

#### **2.HandlerInterceptor方法介绍**

```java
	boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception;

	void postHandle(
			HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)
			throws Exception;

	void afterCompletion(
			HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
			throws Exception;

```

- preHandle：在**业务处理器处理请求之前**被调用。预处理，可以进行编码、安全控制、权限校验等处理；
- postHandle：在业务处理器处理**请求执行完成后，生成视图之前**执行。后处理（调用了Service并返回ModelAndView，但未进行页面渲染），有机会修改ModelAndView ，这个较少用；
- afterCompletion：在DispatcherServlet**完全处理完请求后**被调用，可用于清理资源等。返回处理（已经渲染了页面）；

### AuthInterceptor

`AuthInterceptor` 会在请求到达目标资源之前被触发。具体来说，它会在如下时机起作用：

- 当一个请求进入控制器（Controller）中的某个方法之前，如果该方法配置了使用`AuthInterceptor`，那么拦截器的`preHandle`方法就会被执行。
- 如果`preHandle`方法返回`true`，则请求将继续执行；如果返回`false`，则请求将被终止，通常会返回一个未认证或未授权的响应。

```java
@Slf4j
@Component
@AllArgsConstructor
public class AuthInterceptor implements HandlerInterceptor {

    private final JwtProperties jwtProperties;

    @Override
    public boolean preHandle(@NotNull HttpServletRequest request, @NotNull HttpServletResponse response, @NotNull Object handler) throws Exception {
        //如果不是映射到方法直接通过
        if (!(handler instanceof HandlerMethod)) {
            return true;
        }
        //从 http 请求头中取出 token
        String token = request.getHeader("accessToken");
        if (StrUtil.isEmpty(token)) {
            log.error("未登陆，url:{}", request.getRequestURI());
            throw new GlobalException(ResultCode.NO_LOGIN);
        }
        String strJson = JwtUtil.getInfo(token);
        UserSession userSession = JSON.parseObject(strJson, UserSession.class);
        // 验证 token
        if (!JwtUtil.checkSign(token, jwtProperties.getAccessTokenSecret())) {
            log.error("token已失效，用户:{}", userSession.getUserName());
            log.error("token:{}", token);
            throw new GlobalException(ResultCode.INVALID_TOKEN);
        }
        // 存放session
        request.setAttribute("session", userSession);
        return true;
    }
}
```

#### @Component

[一文掌握SpringBoot注解之@Component 知识文集(1)_springboot component-CSDN博客](https://blog.csdn.net/m0_50308467/article/details/135772279?ops_request_misc=%7B%22request%5Fid%22%3A%22172423118516800182181751%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172423118516800182181751&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-135772279-null-null.142^v100^control&utm_term=@component&spm=1018.2226.3001.4187)

@Component 注解是 Spring 框架中的一个基本注解，它的作用是将一个 Java 类标识为 Spring 的组件（Bean）。被 @Component 注解标注的类会被 Spring 自动扫描并注册到应用上下文中，可以通过应用上下文获取并使用这些组件。

具体来说，@Component 注解可以用于标记任何一个普通的 Java 类，并将其纳入 Spring 管理。这样，它们就可以享受到 Spring 提供的各种依赖注入、自动装配、AOP 等特性。

需要注意的是，@Component 注解通常作为其他更具体的注解（如 @Service、@Repository、@Controller 等）的基础，用于创建具有特定用途的组件。但是，@Component 注解本身并没有明确定义 Bean 的角色或用途。

### MvcConfig

```java
@Configuration
@AllArgsConstructor
public class MvcConfig implements WebMvcConfigurer {

    private final AuthInterceptor authInterceptor;
    private final XssInterceptor xssInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(xssInterceptor).addPathPatterns("/**").excludePathPatterns("/error");
        registry.addInterceptor(authInterceptor).addPathPatterns("/**")
            .excludePathPatterns("/login", "/logout", "/register", "/refreshToken", "/swagger/**", "/v3/api-docs/**",
                "/swagger-resources/**", "/swagger-ui.html", "/swagger-ui/**", "/doc.html");
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        // 使用BCrypt加密密码
        return new BCryptPasswordEncoder();
    }
}
```

#### @Configuration

[@Configuration注解使用-CSDN博客](https://blog.csdn.net/weixin_43808717/article/details/118155699?ops_request_misc=%7B%22request%5Fid%22%3A%22172423159416800180695340%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172423159416800180695340&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-118155699-null-null.142^v100^control&utm_term=@Configuration&spm=1018.2226.3001.4187)

Spring的注解，不是SpringBoot的！  

@Configuration注解的作用：声明一个类为配置类，用于取代bean.xml配置文件注册bean对象

### 实现过程

拦截器需要：
1.自定义拦截器，继承HandleInterceptor类，重写preHandle方法，确定是映射到方法上，然后从http请求头中取出token，进行验证，并把session放到request中
2.添加拦截器到配置器中，排除登录、登出、注册、刷新令牌等界面。

## 4.7  :star:刷新token

### refreshToken

```java
    @PutMapping("/refreshToken")
    @Operation(summary = "刷新token", description = "用refreshtoken换取新的token")
    public Result refreshToken(@RequestHeader("refreshToken") String refreshToken) {
        LoginVO vo = userService.refreshToken(refreshToken);
        return ResultUtils.success(vo);
    }
```

#### @RequestHeader

[请求参数获取：@RequestParam、@PathVariable、@RequestHeader、@CookieValue、@RequestBody、@RequestAttribute注解详细分析-CSDN博客](https://blog.csdn.net/weixin_52536274/article/details/130649782?ops_request_misc=&request_id=&biz_id=102&utm_term=@RequestHeader&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-2-130649782.nonecase&spm=1018.2226.3001.4187)

 @RequestHeader 是获取请求头中的数据，通过指定参数 value 的值来获取请求头中指定的参数值。其他参数用法和 @RequestParam 完全一样。

### refreshToken-Impl

```java

    @Override
    public LoginVO refreshToken(String refreshToken) {
        //验证 token
        if (!JwtUtil.checkSign(refreshToken, jwtProperties.getRefreshTokenSecret())) {
            throw new GlobalException("refreshToken无效或已过期");
        }
        String strJson = JwtUtil.getInfo(refreshToken);
        Long userId = JwtUtil.getUserId(refreshToken);
        User user = this.getById(userId);
        if (Objects.isNull(user)) {
            throw new GlobalException("用户不存在");
        }
        if (user.getIsBanned()) {
            String tip = String.format("您的账号因'%s'被管理员封禁,请联系客服!",user.getReason());
            throw new GlobalException(tip);
        }
        String accessToken =
            JwtUtil.sign(userId, strJson, jwtProperties.getAccessTokenExpireIn(), jwtProperties.getAccessTokenSecret());
        String newRefreshToken = JwtUtil.sign(userId, strJson, jwtProperties.getRefreshTokenExpireIn(),jwtProperties.getRefreshTokenSecret());
        LoginVO vo = new LoginVO();
        vo.setAccessToken(accessToken);
        vo.setAccessTokenExpiresIn(jwtProperties.getAccessTokenExpireIn());
        vo.setRefreshToken(newRefreshToken);
        vo.setRefreshTokenExpiresIn(jwtProperties.getRefreshTokenExpireIn());
        return vo;
    }
```

### 实现过程

验证refreshtoken是否有效后验证用户信息，重新签发token

## 4.8 修改密码

### modifyPwd

```java
    @PutMapping("/modifyPwd")
    @Operation(summary = "修改密码", description = "修改用户密码")
    public Result modifyPassword(@Valid @RequestBody ModifyPwdDTO dto) {
        userService.modifyPassword(dto);
        return ResultUtils.success();
    }
```

### modifyPwd-Impl

```java
    @Override
    public void modifyPassword(ModifyPwdDTO dto) {
        UserSession session = SessionContext.getSession();
        User user = this.getById(session.getUserId());
        if (!passwordEncoder.matches(dto.getOldPassword(), user.getPassword())) {
            throw new GlobalException("旧密码不正确");
        }
        user.setPassword(passwordEncoder.encode(dto.getNewPassword()));
        this.updateById(user);
        log.info("用户修改密码，用户id:{},用户名:{},昵称:{}", user.getId(), user.getUserName(), user.getNickName());
    }
```

#### Session

[Session详解，学习Session，这篇文章就够了(包含底层分析和使用)_session级别-CSDN博客](https://blog.csdn.net/m0_51545690/article/details/123384986?ops_request_misc=%7B%22request%5Fid%22%3A%22172420839116800175721716%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172420839116800175721716&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-123384986-null-null.142^v100^control&utm_term=session&spm=1018.2226.3001.4187)

Session是存储于服务器端的特殊对象，服务器会为每一个客户端创建一个唯一的session，这个session是服务器端共享，客户端独享的，我们可以在session存储数据，实现数据共享。

### 实现过程

通过获取session中的userid查用户，重设密码

## 4.10 其他代码

### ThreadPoolExecutorFactory

```java

@Slf4j
public final class ThreadPoolExecutorFactory {
    /**
     * 机器的CPU核数:Runtime.getRuntime().availableProcessors()
     * corePoolSize 池中所保存的线程数，包括空闲线程。
     * CPU 密集型：核心线程数 = CPU核数 + 1
     * IO 密集型：核心线程数 = CPU核数 * 2
     */
    private static final int CORE_POOL_SIZE =
        Math.min(ThreadPoolExecutorFactory.MAX_IMUM_POOL_SIZE, Runtime.getRuntime().availableProcessors() * 2);
    /**
     * maximumPoolSize - 池中允许的最大线程数(采用LinkedBlockingQueue时没有作用)。
     */
    private static final int MAX_IMUM_POOL_SIZE = 100;
    /**
     * keepAliveTime -当线程数大于核心时，此为终止前多余的空闲线程等待新任务的最长时间，线程池维护线程所允许的空闲时间
     */
    private static final int KEEP_ALIVE_TIME = 1000;
    /**
     * 等待队列的大小。默认是无界的，性能损耗的关键
     */
    private static final int QUEUE_SIZE = 200;

    /**
     * 线程池对象
     */
    private static volatile ScheduledThreadPoolExecutor threadPoolExecutor = null;

    /**
     * 构造方法私有化
     */
    private ThreadPoolExecutorFactory() {
        if (null == threadPoolExecutor) {
            threadPoolExecutor = ThreadPoolExecutorFactory.getThreadPoolExecutor();
        }
    }


    /**
     * 双检锁创建线程安全的单例
     */
    public static ScheduledThreadPoolExecutor getThreadPoolExecutor() {
        if (null == threadPoolExecutor) {
            synchronized (ThreadPoolExecutorFactory.class) {
                if (null == threadPoolExecutor) {
                    threadPoolExecutor = new ScheduledThreadPoolExecutor(
                            //核心线程数
                            CORE_POOL_SIZE,
                            //拒绝策略
                            new ThreadPoolExecutor.CallerRunsPolicy()
                    );
                }
            }
        }
        return threadPoolExecutor;
    }

    /**
     * 关闭线程池
     */
    public static void shutDown() {
        if (threadPoolExecutor != null) {
            threadPoolExecutor.shutdown();
        }
    }

    public static void execute(Runnable runnable) {
        if (runnable == null) {
            return;
        }
        threadPoolExecutor.execute(runnable);
    }

}
```

### UserService

```java
public interface UserService extends IService<User> {

    /**
     * 用户登录
     *
     * @param dto 登录dto
     * @return 登录token
     */
    LoginVO login(LoginDTO dto);

    /**
     * 修改用户密码
     *
     * @param dto 修改密码dto
     */
    void modifyPassword(ModifyPwdDTO dto);

    /**
     * 用refreshToken换取新 token
     *
     * @param refreshToken 刷新token
     * @return 登录token
     */
    LoginVO refreshToken(String refreshToken);

    /**
     * 用户注册
     *
     * @param dto 注册dto
     */
    void register(RegisterDTO dto);

    /**
     * 根据用户名查询用户
     *
     * @param username 用户名
     * @return 用户信息
     */
    User findUserByUserName(String username);

    /**
     * 更新用户信息，好友昵称和群聊昵称等冗余信息也会更新
     *
     * @param vo 用户信息vo
     */
    void update(UserVO vo);

    /**
     * 根据用户昵id查询用户以及在线状态
     *
     * @param id 用户id
     * @return 用户信息
     */
    UserVO findUserById(Long id);

    /**
     * 根据用户昵称查询用户，最多返回20条数据
     *
     * @param name 用户名或昵称
     * @return 用户列表
     */
    List<UserVO> findUserByName(String name);

    /**
     * 获取用户在线的终端类型
     *
     * @param userIds 用户id，多个用‘,’分割
     * @return 在线用户终端
     */
    List<OnlineTerminalVO> getOnlineTerminals(String userIds);
}
```

### User

```java
@Data
@TableName("im_user")
public class User {

    /**
     * id
     */
    @TableId
    private Long id;

    /**
     * 用户名
     */
    private String userName;

    /**
     * 用户昵称
     */
    private String nickName;

    /**
     * 用户头像
     */
    private String headImage;

    /**
     * 用户头像缩略图
     */
    private String headImageThumb;

    /**
     * 密码(明文)
     */
    private String password;

    /**
     * 性别 0:男 1::女
     */
    private Integer sex;

    /**
     * 个性签名
     */
    private String signature;

    /**
     * 账号是否被封禁
     */
    private Boolean isBanned;

    /**
     * 账号被封禁原因
     */
    private String reason;

    /**
     * 最后登录时间
     */
    private Date lastLoginTime;

    /**
     * 创建时间(注册时间)
     */
    private Date createdTime;

    /**
     *  账号类型 1:普通用户 2:wx小程序审核账户
     */
    private Integer type;
}
```

#### @TableName

[MYBatis-Plus常用注解@TableName、@TableId、@TableField、@TableLogic_tableid注解-CSDN博客](https://blog.csdn.net/weixin_51351637/article/details/127044796?ops_request_misc=%7B%22request%5Fid%22%3A%22172423308616800188576327%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172423308616800188576327&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-127044796-null-null.142^v100^control&utm_term=@TableName&spm=1018.2226.3001.4187)

注解作用：设置实体类对应的表名。value作用：value指定数据库中的表名。如果我们不设置这个注解，我们操作的数据库的表就由BaseMapper<Book> 泛型决定（Book）

### LoginDTO

```java
@Data
@Schema(description = "用户登录DTO")
public class LoginDTO {

    @Max(value = 2, message = "登录终端类型取值范围:0,2")
    @Min(value = 0, message = "登录终端类型取值范围:0,2")
    @NotNull(message = "登录终端类型不可为空")
    @Schema(description = "登录终端 0:web 1:app 2:pc")
    private Integer terminal;

    @NotEmpty(message = "用户名不可为空")
    @Schema(description = "用户名")
    private String userName;

    @NotEmpty(message = "用户密码不可为空")
    @Schema(description = "用户密码")
    private String password;

}
```

### LoginVO

```java
@Data
@Schema(description = "用户登录VO")
public class LoginVO {

    @Schema(description = "每次请求都必须在header中携带accessToken")
    private String accessToken;

    @Schema(description = "accessToken过期时间(秒)")
    private Integer accessTokenExpiresIn;

    @Schema(description = "accessToken过期后，通过refreshToken换取新的token")
    private String refreshToken;

    @Schema(description = "refreshToken过期时间(秒)")
    private Integer refreshTokenExpiresIn;

}
```

### Result

```java
@Data
public class Result<T> {

    private int code;

    private String message;

    private T data;

}
```

#### @Data

[@Data注解的理解和详细说明以及使用场景-CSDN博客](https://blog.csdn.net/weixin_47128494/article/details/134706882?ops_request_misc=%7B%22request%5Fid%22%3A%22172423320116800175759767%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172423320116800175759767&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-134706882-null-null.142^v100^control&utm_term=@Data&spm=1018.2226.3001.4187)

在类上添加 @Data 注解后，Lombok 会自动生成以下方法：

- 所有字段的 getter 和 setter 方法。
- equals 和 hashCode 方法，用于比较两个对象是否相等。
- toString 方法，用于将对象转换为字符串表示形式。

### ResultUtils

```java
public final class ResultUtils {

    private ResultUtils() {
    }

    public static <T> Result<T> success() {
        Result<T> result = new Result<>();
        result.setCode(ResultCode.SUCCESS.getCode());
        result.setMessage(ResultCode.SUCCESS.getMsg());
        return result;
    }

    public static <T> Result<T> success(T data) {
        Result<T> result = new Result<>();
        result.setCode(ResultCode.SUCCESS.getCode());
        result.setMessage(ResultCode.SUCCESS.getMsg());
        result.setData(data);
        return result;
    }

    public static <T> Result<T> success(T data, String messsage) {
        Result<T> result = new Result<>();
        result.setCode(ResultCode.SUCCESS.getCode());
        result.setMessage(messsage);
        result.setData(data);
        return result;
    }

    public static <T> Result<T> success(String messsage) {
        Result<T> result = new Result<>();
        result.setCode(ResultCode.SUCCESS.getCode());
        result.setMessage(messsage);
        return result;
    }

    public static <T> Result<T> error(Integer code, String messsage) {
        Result<T> result = new Result<>();
        result.setCode(code);
        result.setMessage(messsage);
        return result;
    }


    public static <T> Result<T> error(ResultCode resultCode, String messsage) {
        Result<T> result = new Result<>();
        result.setCode(resultCode.getCode());
        result.setMessage(messsage);
        return result;
    }

    public static <T> Result<T> error(ResultCode resultCode) {
        Result<T> result = new Result<>();
        result.setCode(resultCode.getCode());
        result.setMessage(resultCode.getMsg());
        return result;
    }
}
```

### ResultCode

```java
@Getter
@AllArgsConstructor
public enum ResultCode {
    /**
     * 成功
     */
    SUCCESS(200, "成功"),
    /**
     * 未登录
     */
    NO_LOGIN(400, "未登录"),
    /**
     * token无效或已过期
     */
    INVALID_TOKEN(401, "token无效或已过期"),
    /**
     * 系统繁忙，请稍后再试
     */
    PROGRAM_ERROR(500, "系统繁忙，请稍后再试"),
    /**
     * 密码不正确
     */
    PASSWOR_ERROR(10001, "密码不正确"),
    /**
     * 该用户名已注册
     */
    USERNAME_ALREADY_REGISTER(10003, "该用户名已注册"),
    /**
     * 请不要输入非法内容
     */
    XSS_PARAM_ERROR(10004, "请不要输入非法内容");
    
    private final int code;
    private final String msg;

}
```

### GlobalException

```java
@Data
public class GlobalException extends RuntimeException implements Serializable {
    private static final long serialVersionUID = 8134030011662574394L;
    private Integer code;
    private String message;

    public GlobalException(Integer code, String message) {
        this.code = code;
        this.message = message;
    }

    public GlobalException(ResultCode resultCode, String message) {
        this.code = resultCode.getCode();
        this.message = message;
    }

    public GlobalException(ResultCode resultCode) {
        this.code = resultCode.getCode();
        this.message = resultCode.getMsg();
    }

    public GlobalException(String message) {
        this.code = ResultCode.PROGRAM_ERROR.getCode();
        this.message = message;
    }

}
```

### GlobalExceptionHandler

```java

@ControllerAdvice
@ResponseBody
@Slf4j
public class GlobalExceptionHandler {

    @ExceptionHandler(value = Exception.class)
    public Result handleException(Exception e) {
        if (e instanceof GlobalException) {
            GlobalException ex = (GlobalException) e;
            // token过期是正常情况,不打印
            if(!ex.getCode().equals(ResultCode.INVALID_TOKEN.getCode())){
                log.error("全局异常捕获:msg:{},log:{},{}", ex.getMessage(), e);
            }
            return ResultUtils.error(ex.getCode(), ex.getMessage());
        } else if (e instanceof UndeclaredThrowableException) {
            GlobalException ex = (GlobalException) e.getCause();
            log.error("全局异常捕获:msg:{},log:{},{}", ex.getMessage(), e);
            return ResultUtils.error(ex.getCode(), ex.getMessage());
        } else {
            log.error("全局异常捕获:msg:{},{}", e.getMessage(), e);
            return ResultUtils.error(ResultCode.PROGRAM_ERROR);
        }
    }


    /**
     * 数据解析错误
     **/
    @ExceptionHandler(value = HttpMessageNotReadableException.class)
    public Result handleMessageNotReadableException(HttpMessageNotReadableException e) {
        log.error("全局异常捕获:msg:{}", e.getMessage());
        Throwable t = e.getCause();
        if (t instanceof JSONException) {
            t = t.getCause();
            if (t instanceof DateTimeParseException) {
                return ResultUtils.error(ResultCode.PROGRAM_ERROR, "日期格式不正确");
            }
            return ResultUtils.error(ResultCode.PROGRAM_ERROR, "数据格式不正确");
        }
        return ResultUtils.error(ResultCode.PROGRAM_ERROR);
    }

    /**
     * 处理请求参数格式错误 @RequestBody上validate失败后抛出的异常
     *
     * @param exception
     * @return
     */
    @ExceptionHandler(value = {MethodArgumentNotValidException.class})
    @ResponseStatus(HttpStatus.OK)
    public Result handleValidationExceptionHandler(MethodArgumentNotValidException exception) {
        BindingResult bindResult = exception.getBindingResult();
        String msg;
        if (bindResult.hasErrors()) {
            msg = bindResult.getAllErrors().get(0).getDefaultMessage();
            if (msg.contains("NumberFormatException")) {
                msg = "参数类型错误！";
            }
        } else {
            msg = "系统繁忙，请稍后重试...";
        }
        return ResultUtils.error(ResultCode.PROGRAM_ERROR, msg);
    }


    @ExceptionHandler(BindException.class)
    @ResponseStatus(HttpStatus.OK)
    public Result handleBindException(BindException e) {
        //抛出异常可能不止一个 输出为一个List集合
        List<ObjectError> errors = e.getAllErrors();
        //取第一个异常
        ObjectError error = errors.get(0);
        //获取异常信息
        String errorMsg = error.getDefaultMessage();
        return ResultUtils.error(ResultCode.PROGRAM_ERROR, errorMsg);
    }

}

```

### BeanUtils

```java

public final class BeanUtils {

    private BeanUtils() {
    }

    private static void handleReflectionException(Exception e) {
        ReflectionUtils.handleReflectionException(e);
    }


    /**
     * 属性拷贝
     *
     * @param orig      源对象
     * @param destClass 目标
     * @return T
     */
    public static <T> T copyProperties(Object orig, Class<T> destClass) {
        try {
            T target = destClass.newInstance();
            if (orig == null) {
                return null;
            }
            copyProperties(orig, target);
            return target;
        } catch (Exception e) {
            handleReflectionException(e);
            return null;
        }
    }


    public static void copyProperties(Object orig, Object dest) {
        try {
            org.springframework.beans.BeanUtils.copyProperties(orig, dest);
        } catch (Exception e) {
            handleReflectionException(e);
        }
    }

}
```

### UserSession

```java
@EqualsAndHashCode(callSuper = true)
@Data
public class UserSession extends IMSessionInfo {

    /**
     * 用户名称
     */
    private String userName;

    /**
     * 用户昵称
     */
    private String nickName;
}

```

### IMSessionInfo

```java
@Data
public class IMSessionInfo {
    /**
     * 用户id
     */
    private Long userId;

    /**
     * 终端类型
     */
    private Integer terminal;

}
```

### IMLoginInfo

```java
@Data
public class IMLoginInfo {

    private String accessToken;
}

```

### IMSendInfo

```java
@Data
public class IMSendInfo<T> {

    /**
     * 命令
     */
    private Integer cmd;

    /**
     * 推送消息体
     */
    private T data;

}
```

### IMCmdType

```java

@AllArgsConstructor
public enum IMCmdType {

    /**
     * 登陆
     */
    LOGIN(0, "登陆"),
    /**
     * 心跳
     */
    HEART_BEAT(1, "心跳"),
    /**
     * 强制下线
     */
    FORCE_LOGUT(2, "强制下线"),
    /**
     * 私聊消息
     */
    PRIVATE_MESSAGE(3, "私聊消息"),
    /**
     * 群发消息
     */
    GROUP_MESSAGE(4, "群发消息"),
    /**
     * 系统消息
     */
    SYSTEM_MESSAGE(5,"系统消息");


    private final Integer code;

    private final String desc;


    public static IMCmdType fromCode(Integer code) {
        for (IMCmdType typeEnum : values()) {
            if (typeEnum.code.equals(code)) {
                return typeEnum;
            }
        }
        return null;
    }


    public Integer code() {
        return this.code;
    }

}
```

### JwtProperties

```java
@Data
@Component
public class JwtProperties {

    @Value("${jwt.accessToken.expireIn}")
    private Integer accessTokenExpireIn;

    @Value("${jwt.accessToken.secret}")
    private String accessTokenSecret;

    @Value("${jwt.refreshToken.expireIn}")
    private Integer refreshTokenExpireIn;

    @Value("${jwt.refreshToken.secret}")
    private String refreshTokenSecret;
}
```

### JwtUtil

```java

public final class JwtUtil {

    private JwtUtil() {
    }

    /**
     * 生成jwt字符串  JWT(json web token)
     *
     * @param userId   用户id
     * @param info     用户细腻系
     * @param expireIn 过期时间
     * @param secret   秘钥
     * @return token
     */
    public static String sign(Long userId, String info, long expireIn, String secret) {
        try {
            Date date = new Date(System.currentTimeMillis() + expireIn * 1000);
            Algorithm algorithm = Algorithm.HMAC256(secret);
            return JWT.create()
                    //将userId保存到token里面
                    .withAudience(userId.toString())
                    //存放自定义数据
                    .withClaim("info", info)
                    //过期时间
                    .withExpiresAt(date)
                    //token的密钥
                    .sign(algorithm);
        } catch (Exception e) {
            return null;
        }
    }

    /**
     * 根据token获取userId
     *
     * @param token 登录token
     * @return 用户id
     */
    public static Long getUserId(String token) {
        try {
            String userId = JWT.decode(token).getAudience().get(0);
            return Long.parseLong(userId);
        } catch (JWTDecodeException e) {
            return null;
        }
    }

    /**
     * 根据token获取用户数据
     *
     * @param token 用户登录token
     * @return 用户数据
     */
    public static String getInfo(String token) {
        try {
            return JWT.decode(token).getClaim("info").asString();
        } catch (JWTDecodeException e) {
            return null;
        }
    }

    /**
     * 校验token
     *
     * @param token  用户登录token
     * @param secret 秘钥
     * @return true/false
     */
    public static Boolean checkSign(String token, String secret) {
        try {
            Algorithm algorithm = Algorithm.HMAC256(secret);
            JWTVerifier verifier = JWT.require(algorithm).build();
            verifier.verify(token);
            return true;
        } catch (JWTVerificationException e) {
            return false;
        }
    }
}

```

### RegisterDTO

```java
@Data
@Schema(description = "用户注册DTO")
public class RegisterDTO {

    @Length(max = 64, message = "用户名不能大于64字符")
    @NotEmpty(message = "用户名不可为空")
    @Schema(description = "用户名")
    private String userName;

    @Length(min = 5, max = 20, message = "密码长度必须在5-20个字符之间")
    @NotEmpty(message = "用户密码不可为空")
    @Schema(description = "用户密码")
    private String password;

    @Length(max = 64, message = "昵称不能大于64字符")
    @NotEmpty(message = "用户昵称不可为空")
    @Schema(description = "用户昵称")
    private String nickName;


}

```

### ModifyPwdDTO

```java
@Data
@Schema(description = "修改密码DTO")
public class ModifyPwdDTO {

    @NotEmpty(message = "旧用户密码不可为空")
    @Schema(description = "旧用户密码")
    private String oldPassword;

    @NotEmpty(message = "新用户密码不可为空")
    @Schema(description = "新用户密码")
    private String newPassword;

}
```



# 05 用户信息模块

## 5.1 UserController

```java
@Tag(name = "用户相关")
@RestController
@RequestMapping("/user")
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;
}
```

## 5.2 UserServiceImpl

同04 UserserviceImpl

## 5.3 判断用户哪个终端在线

### getOnlineTerminal

```java
    @GetMapping("/terminal/online")
    @Operation(summary = "判断用户哪个终端在线", description = "返回在线的用户id的终端集合")
    public Result<List<OnlineTerminalVO>> getOnlineTerminal(@NotNull @RequestParam("userIds") String userIds) {
        return ResultUtils.success(userService.getOnlineTerminals(userIds));
    }
```

### getOnlineTerminal-Impl

```java
    @Override
    public List<OnlineTerminalVO> getOnlineTerminals(String userIds) {
        List<Long> userIdList = Arrays.stream(userIds.split(",")).map(Long::parseLong).collect(Collectors.toList());
        // 查询在线的终端
        Map<Long, List<IMTerminalType>> terminalMap = imClient.getOnlineTerminal(userIdList);
        // 组装vo
        List<OnlineTerminalVO> vos = new LinkedList<>();
        terminalMap.forEach((userId, types) -> {
            List<Integer> terminals = types.stream().map(IMTerminalType::code).collect(Collectors.toList());
            vos.add(new OnlineTerminalVO(userId, terminals));
        });
        return vos;
    }
```

### IMClient

```java
@Configuration
@AllArgsConstructor
public class IMClient {}
```

###  IMSender-getOnlineTerminal

```java
@Service
@RequiredArgsConstructor
public class IMSender {

    @Autowired
    private RedisMQTemplate redisMQTemplate;

    @Value("${spring.application.name}")
    private String appName;

    private final MessageListenerMulticaster listenerMulticaster;
    
      public Map<Long,List<IMTerminalType>> getOnlineTerminal(List<Long> userIds){
        if(CollUtil.isEmpty(userIds)){
            return Collections.emptyMap();
        }
        // 把所有用户的key都存起来
        Map<String,IMUserInfo> userMap = new HashMap<>();
        for(Long id:userIds){
            for (Integer terminal : IMTerminalType.codes()) {
                String key = String.join(":", IMRedisKey.IM_USER_SERVER_ID, id.toString(), terminal.toString());
                userMap.put(key,new IMUserInfo(id,terminal));
            }
        }
        // 批量拉取
        List<Object> serverIds = redisMQTemplate.opsForValue().multiGet(userMap.keySet());
        int idx = 0;
        Map<Long,List<IMTerminalType>> onlineMap = new HashMap<>();
        for (Map.Entry<String,IMUserInfo> entry : userMap.entrySet()) {
            // serverid有值表示用户在线
            if(serverIds.get(idx++) != null){
                IMUserInfo userInfo = entry.getValue();
                List<IMTerminalType> terminals = onlineMap.computeIfAbsent(userInfo.getId(), o -> new LinkedList<>());
                terminals.add(IMTerminalType.fromCode(userInfo.getTerminal()));
            }
        }
        // 去重并返回
        return onlineMap;
    }
    
}
```

### RedisMQTemplate

```java

public class RedisMQTemplate extends RedisTemplate<String, Object> {
    private String version = Strings.EMPTY;

    public String getVersion() {
        if (version.isEmpty()) {
            RedisConnection redisConnection = this.getConnectionFactory().getConnection();
            Properties properties = redisConnection.info();
            version = properties.getProperty("redis_version");
        }
        return version;
    }

    /**
     * 是否支持批量拉取，redis版本大于6.2支持批量拉取
     * @return
     */
    Boolean isSupportBatchPull() {
        String version = getVersion();
        String[] arr = version.split("\\.");
        if (arr.length < 2) {
            return false;
        }
        Integer firVersion = Integer.valueOf(arr[0]);
        Integer secVersion = Integer.valueOf(arr[1]);
        return firVersion > 6 || (firVersion == 6 && secVersion >= 2);
    }


    Boolean isClose(){
        try {
            return  getConnectionFactory().getConnection().isClosed();
        }catch (Exception e){
            return true;
        }
    }
}
```

- 这个类继承自`RedisTemplate<String, Object>`，这意味着它是一个用于操作Redis数据库的模板类，使用`String`类型的键和`Object`类型的值。
- 这个方法用于获取Redis服务器的版本号。它首先检查`version`变量是否为空，如果为空，则创建一个新的`RedisConnection`对象，并通过调用`info()`方法获取Redis服务器的信息。这些信息被存储在一个`Properties`对象中，然后从中提取出版本号，并将其存储在`version`变量中，以便下次直接返回而不再重新获取。
- 这个方法用于判断Redis服务器是否支持批量拉取功能。根据Redis官方文档，从6.2版本开始支持批量拉取。因此，该方法首先调用`getVersion()`方法获取Redis版本号，然后解析版本号字符串，提取主要版本号和次要版本号。如果主要版本号大于6，或者主要版本号等于6且次要版本号大于等于2，则认为支持批量拉取功能。
- 这个方法用于检查Redis连接是否已经关闭。它试图通过`getConnectionFactory().getConnection()`获取一个连接，并调用`isClosed()`方法来检查连接状态。如果在获取连接过程中抛出了异常，则假定连接已关闭。

### 实现过程

接收用户id列表作为请求参数，一层层调用到imsender中的getOnlineTerminal，根据用户Id列表构建Redis键，批量查询Redis，获取在线状态，返回。

## 5.4 获取当前用户信息

### findSelfInfo

```java
    @GetMapping("/self")
    @Operation(summary = "获取当前用户信息", description = "获取当前用户信息")
    public Result<UserVO> findSelfInfo() {
        UserSession session = SessionContext.getSession();
        User user = userService.getById(session.getUserId());
        UserVO userVO = BeanUtils.copyProperties(user, UserVO.class);
        return ResultUtils.success(userVO);
    }
```

### SessionContext

```java
public class SessionContext {
    public static UserSession getSession() {
        // 从请求上下文里获取Request对象
        ServletRequestAttributes requestAttributes = 
            (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = requestAttributes.getRequest();
        return (UserSession) request.getAttribute("session");
    }
}
```

这段代码是从 Spring Web 应用程序中的当前线程获取请求上下文，并从中提取 `HttpServletRequest` 对象，进而获取存储在请求中的 `UserSession` 对象。

### 实现过程

获取session中的userId，找到user,将信息拷贝到UserVo中。

## 5.5 根据id查找用户

### findById

```java
    @GetMapping("/find/{id}")
    @Operation(summary = "查找用户", description = "根据id查找用户")
    public Result<UserVO> findById(@NotNull @PathVariable("id") Long id) {
        return ResultUtils.success(userService.findUserById(id));
    }
```

### findById-Impl

```java
    @Override
    public UserVO findUserById(Long id) {
        User user = this.getById(id);
        UserVO vo = BeanUtils.copyProperties(user, UserVO.class);
        vo.setOnline(imClient.isOnline(id));
        return vo;
    }
```

### IMSender-isOnline

```java
    public Boolean isOnline(Long userId) {
        String key = String.join(":", IMRedisKey.IM_USER_SERVER_ID, userId.toString(), "*");
        return !Objects.requireNonNull(redisMQTemplate.keys(key)).isEmpty();
    }
```

### 实现过程

查询、拷贝、返回

## 5.6 修改用户信息

### update

```java
  @PutMapping("/update")
  @Operation(summary = "修改用户信息", description = "修改用户信息，仅允许修改登录用户信息")
    public Result update(@Valid @RequestBody UserVO vo) {
        userService.update(vo);
        return ResultUtils.success();
    }
```

### update-Impl

```java
    @Transactional(rollbackFor = Exception.class)
    @Override
    public void update(UserVO vo) {
        UserSession session = SessionContext.getSession();
        if (!session.getUserId().equals(vo.getId())) {
            throw new GlobalException("不允许修改其他用户的信息!");
        }
        User user = this.getById(vo.getId());
        if (Objects.isNull(user)) {
            throw new GlobalException("用户不存在");
        }

        if (!user.getNickName().equals(vo.getNickName()) || !user.getHeadImageThumb().equals(vo.getHeadImageThumb())) {
            // 更新好友昵称和头像
            LambdaUpdateWrapper<Friend> wrapper1 = Wrappers.lambdaUpdate();
            wrapper1.eq(Friend::getFriendId, session.getUserId());
            wrapper1.set(Friend::getFriendNickName,vo.getNickName());
            wrapper1.set(Friend::getFriendHeadImage,vo.getHeadImageThumb());
            friendService.update(wrapper1);
            // 更新群聊中的昵称和头像
            LambdaUpdateWrapper<GroupMember> wrapper2 = Wrappers.lambdaUpdate();
            wrapper2.eq(GroupMember::getUserId, session.getUserId());
            wrapper2.set(GroupMember::getHeadImage,vo.getHeadImageThumb());
            wrapper2.set(GroupMember::getUserNickName,vo.getNickName());
            groupMemberService.update(wrapper2);
        }
        // 更新用户信息
        user.setNickName(vo.getNickName());
        user.setSex(vo.getSex());
        user.setSignature(vo.getSignature());
        user.setHeadImage(vo.getHeadImage());
        user.setHeadImageThumb(vo.getHeadImageThumb());
        this.updateById(user);
        log.info("用户信息更新，用户:{}}", user);
    }

```

### 实现过程

根据session查找到用户，更新好友、群聊、用户信息

## 5.7 根据用户名或昵称查找用户

### findByName

```java
   @GetMapping("/findByName")
    @Operation(summary = "查找用户", description = "根据用户名或昵称查找用户")
    public Result<List<UserVO>> findByName(@RequestParam String name) {
        return ResultUtils.success(userService.findUserByName(name));
    }
```

### findByName-Impl

```java
    @Override
    public List<UserVO> findUserByName(String name) {
        LambdaQueryWrapper<User> queryWrapper = Wrappers.lambdaQuery();
        queryWrapper.like(User::getUserName, name).or().like(User::getNickName, name).last("limit 20");
        List<User> users = this.list(queryWrapper);
        List<Long> userIds = users.stream().map(User::getId).collect(Collectors.toList());
        List<Long> onlineUserIds = imClient.getOnlineUser(userIds);
        return users.stream().map(u -> {
            UserVO vo = BeanUtils.copyProperties(u, UserVO.class);
            vo.setOnline(onlineUserIds.contains(u.getId()));
            return vo;
        }).collect(Collectors.toList());
    }
```

这段代码使用了 MyBatis Plus 的 `QueryWrapper` 类来构建查询条件。这段代码的作用是查询数据库中用户名（`userName`）或昵称（`nickName`）包含指定字符串 `name` 的用户，并限制查询结果的数量为最多 20 条记录。

### IMSender-getOnlineUser

```java
public List<Long> getOnlineUser(List<Long> userIds){
        return new LinkedList<>(getOnlineTerminal(userIds).keySet());
    }
```

### 实现过程

在数据库中根据名字查出user信息，查出userId，调用imsender中的getonlineuser查出onlinuserId（调用getOnlineTerminal查出在线终端的userId），然后拷贝信息，返回userVo

## 5.8 其他代码

### OnlineTerminalVO

```java
@Data
@AllArgsConstructor
public class OnlineTerminalVO {

    @Schema(description = "用户id")
    private Long userId;

    @Schema(description = "在线终端类型")
    private List<Integer> terminals;

}
```

### IMClient

```java
@Configuration
@AllArgsConstructor
public class IMClient {

    private final IMSender imSender;

    /**
     * 判断用户是否在线
     *
     * @param userId 用户id
     */
    public Boolean isOnline(Long userId){
        return imSender.isOnline(userId);
    }

    /**
     * 判断多个用户是否在线
     *
     * @param userIds 用户id列表
     * @return 在线的用户列表
     */
    public List<Long> getOnlineUser(List<Long> userIds){
        return imSender.getOnlineUser(userIds);
    }


    /**
     * 判断多个用户是否在线
     *
     * @param userIds 用户id列表
     * @return 在线的用户终端
     */
    public Map<Long,List<IMTerminalType>> getOnlineTerminal(List<Long> userIds){
        return imSender.getOnlineTerminal(userIds);
    }

    /**
     * 发送系统消息（发送结果通过MessageListener接收）
     *
     * @param message 私有消息
     */
    public<T> void sendSystemMessage(IMSystemMessage<T> message){
        imSender.sendSystemMessage(message);
    }


    /**
     * 发送私聊消息（发送结果通过MessageListener接收）
     *
     * @param message 私有消息
     */
    public<T> void sendPrivateMessage(IMPrivateMessage<T> message){
        imSender.sendPrivateMessage(message);
    }

    /**
     * 发送群聊消息（发送结果通过MessageListener接收）
     *
     * @param message 群聊消息
     */
    public<T> void sendGroupMessage(IMGroupMessage<T> message){
        imSender.sendGroupMessage(message);
    }
}
```

#### Boolean和boolean

- boolean是八大基本数据类型之一 , Boolean是它的封装类

- Boolean类有属性 , 有方法 , 可以Boolean flag = new Boolean("true") , boolean则不可以!!!

- Boolean是boolean的实例化对象类 , 类比Integer对应int

- 区分 :
  - 自JDK1.5.0以上的版本以后(现在很低很多都是1.8以后所以不用担心自己使用的问题, 我现在默认是1.8自己有的是JDK11 和·JDK13)Boolean在"赋值"和判断上和boolean一样 , 意思就是
  - 可以这样Boolean b1 = true ; 
  - 也可这样boolean b2 = true 
    ![image-20240822151242498](D:\2024\Notes\Typora\im\boxim.assets\image-20240822151242498.png)

### UserVO

```java
@Data
@Schema(description = "用户信息VO")
public class UserVO {

    @NotNull(message = "用户id不能为空")
    @Schema(description = "id")
    private Long id;

    @NotEmpty(message = "用户名不能为空")
    @Length(max = 64, message = "用户名不能大于64字符")
    @Schema(description = "用户名")
    private String userName;

    @NotEmpty(message = "用户昵称不能为空")
    @Length(max = 64, message = "昵称不能大于64字符")
    @Schema(description = "用户昵称")
    private String nickName;

    @Schema(description = "性别")
    private Integer sex;

    @Schema(description = "用户类型 1:普通用户 2:审核账户")
    private Integer type;

    @Length(max = 1024, message = "个性签名不能大于1024个字符")
    @Schema(description = "个性签名")
    private String signature;

    @Schema(description = "头像")
    private String headImage;

    @Schema(description = "头像缩略图")
    private String headImageThumb;

    @Schema(description = "是否在线")
    private Boolean online;

}
```

## IMRedisKey

```java

public final class IMRedisKey {


    /**
     * im-server最大id,从0开始递增
     */
    public static final String  IM_MAX_SERVER_ID = "im:max_server_id";
    /**
     * 用户ID所连接的IM-server的ID
     */
    public static final String  IM_USER_SERVER_ID = "im:user:server_id";
    /**
     * 系统消息队列
     */
    public static final String IM_MESSAGE_SYSTEM_QUEUE = "im:message:system";
    /**
     * 私聊消息队列
     */
    public static final String IM_MESSAGE_PRIVATE_QUEUE = "im:message:private";
    /**
     * 群聊消息队列
     */
    public static final String IM_MESSAGE_GROUP_QUEUE = "im:message:group";

    /**
     * 系统消息发送结果队列
     */
    public static final String IM_RESULT_SYSTEM_QUEUE = "im:result:system";
    /**
     * 私聊消息发送结果队列
     */
    public static final String IM_RESULT_PRIVATE_QUEUE = "im:result:private";
    /**
     * 群聊消息发送结果队列
     */
    public static final String IM_RESULT_GROUP_QUEUE = "im:result:group";

}

```

# 6 好友功能模块

## 6.1 FriendController

```java
@Tag(name = "好友")
@RestController
@RequestMapping("/friend")
@RequiredArgsConstructor
public class FriendController {
 private final FriendService friendService;
}
```

## 6.2 FriendServiceImpl

```java
@Slf4j
@Service
@RequiredArgsConstructor
@CacheConfig(cacheNames = RedisKey.IM_CACHE_FRIEND)
public class FriendServiceImpl extends ServiceImpl<FriendMapper, Friend> implements FriendService {
	private final UserMapper userMapper;
}
```

### @CacheConfig

 Spring Cache 注解 `@CacheConfig`，它用于在类级别上配置缓存相关的属性。`@CacheConfig` 注解通常与 `@Cacheable`、`@CachePut` 和 `@CacheEvict` 等注解一起使用，以实现方法级别的缓存功能。

[Spring缓存注解@Cacheable、@CacheEvict、@CachePut使用_cacheevict cacheable-CSDN博客](https://blog.csdn.net/zhangdaiscott/article/details/130499908?ops_request_misc=%7B%22request%5Fid%22%3A%22172431432416800211543285%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172431432416800211543285&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-130499908-null-null.142^v100^control&utm_term=cacheEvict&spm=1018.2226.3001.4187)

Spring Cache是作用在方法上的，其核心思想是这样的：当我们在调用一个缓存方法时会把该**方法参数和返回结果**作为一**个键值对存放在缓存**中，等到下次利用同样的参数来调用该方法时将不再执行该方法，而是直接从缓存中获取结果进行返回。所以在使用Spring Cache的时候我们要保证我们缓存的方法对于相同的方法参数要有相同的返回结果。

Spring为我们提供了几个注解来支持Spring Cache。其核心主要是@[Cacheable](https://so.csdn.net/so/search?q=Cacheable&spm=1001.2101.3001.7020)和@CacheEvict。

- 使用**@Cacheable标记**的方法在执行后Spring Cache将**缓存其返回结果。**

   @Cacheable可以标记在一个方法上，也可以标记在一个类上。当标记在一个方法上时表示该方法是支持缓存的，当标记在一个类上时则表示该类所有的方法都是支持缓存的

  - 需要注意的是当一个支持缓存的方法**在对象内部被调用时是不会触发缓存功能**的。
  - **@Cacheable可以指定三个属性，value、key和condition。**

- 使用**@CacheEvict标记**的方法会在方法**执行前或者执行后移除S**pring Cache中的某些元素

   @CacheEvict是用来标注在需要清除缓存元素的方法或类上的。当标记在一个类上时表示其中所有的方法的执行都会触发缓存的清除操作

  - **@CacheEvict可以指定的属性有value、key、condition、allEntries和beforeInvocation。其中value、key和condition的语义与@Cacheable对应的属性类似。**
    - **即value表示清除操作是发生在哪些Cache上的（对应Cache的名称）；**
    - **key表示需要清除的是哪个key，如未指定则会使用默认策略生成的key；**
    - **condition表示清除操作发生的条件。**

## 6.3 获取好友列表

### findFriends

```java
    @GetMapping("/list")
    @Operation(summary = "好友列表", description = "获取好友列表")
    public Result<List<FriendVO>> findFriends() {
        List<Friend> friends = friendService.findFriendByUserId(SessionContext.getSession().getUserId());
        List<FriendVO> vos = friends.stream().map(f -> {
            FriendVO vo = new FriendVO();
            vo.setId(f.getFriendId());
            vo.setHeadImage(f.getFriendHeadImage());
            vo.setNickName(f.getFriendNickName());
            return vo;
        }).collect(Collectors.toList());
        return ResultUtils.success(vos);
    }
```

### findFriends-Impl

```java
    @Override
    public List<Friend> findFriendByUserId(Long userId) {
        LambdaQueryWrapper<Friend> queryWrapper = Wrappers.lambdaQuery();
        queryWrapper.eq(Friend::getUserId, userId);
        return this.list(queryWrapper);
    }
```

### 实现过程

根据session中存储的用户id找到friend，返回friendvo；

## 6.4 添加好友

### addFriend

```java
    @PostMapping("/add")
    @Operation(summary = "添加好友", description = "双方建立好友关系")
    public Result addFriend(@NotNull(message = "好友id不可为空") @RequestParam Long friendId) {
        friendService.addFriend(friendId);
        return ResultUtils.success();
    }
```

### addFriend-Impl

```java
   @Transactional(rollbackFor = Exception.class)
    @Override
    public void addFriend(Long friendId) {
        long userId = SessionContext.getSession().getUserId();
        if (friendId.equals(userId)) {
            throw new GlobalException("不允许添加自己为好友");
        }
        // 互相绑定好友关系
        FriendServiceImpl proxy = (FriendServiceImpl) AopContext.currentProxy();
        proxy.bindFriend(userId, friendId);
        proxy.bindFriend(friendId, userId);
        log.info("添加好友，用户id:{},好友id:{}", userId, friendId);
    }
```

### 内部调用导致事务失效

[spring中@Transactional注解的作用，使用场景举例_transactional注解作用-CSDN博客](https://blog.csdn.net/lianjian6534/article/details/112434905#:~:text=一，spring中管)

[SpringBoot 内部方法调用，事务不起作用的原因及解决办法_spring boot 通过this调用方法上的transactional 不生效-CSDN博客](https://blog.csdn.net/CoderBruis/article/details/112308884#:~:text=问题源于内部方法使用`this`调用，未经过AOP代理，导致事务失效。 解决方法包括：1)

![image-20240822170202493](D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240822170202493.png)

[Spring事务总结(一) 内部调用事务失效、异常回滚 - _Gradually - 博客园 (cnblogs.com)](https://www.cnblogs.com/gss128/p/12121303.html)

### Aop和代理模式

[一文搞懂代理模式-CSDN博客](https://blog.csdn.net/weixin_43953283/article/details/125783249?ops_request_misc=%7B%22request%5Fid%22%3A%22172431209916800182779257%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172431209916800182779257&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-125783249-null-null.142^v100^control&utm_term=代理模式&spm=1018.2226.3001.4187)

[AOP实现原理（二）CGLIB动态代理_aop原理cglib限制-CSDN博客](https://blog.csdn.net/bicheng4769/article/details/80030266?ops_request_misc=%7B%22request%5Fid%22%3A%22172431383616800184149192%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172431383616800184149192&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-80030266-null-null.142^v100^control&utm_term=cglibAop&spm=1018.2226.3001.4187)

### bindFriend

```java
    @CacheEvict(key = "#userId+':'+#friendId")
    public void bindFriend(Long userId, Long friendId) {
        QueryWrapper<Friend> queryWrapper = new QueryWrapper<>();
        queryWrapper.lambda()
                .eq(Friend::getUserId, userId)
                .eq(Friend::getFriendId, friendId);
        if (this.count(queryWrapper) == 0) {
            Friend friend = new Friend();
            friend.setUserId(userId);
            friend.setFriendId(friendId);
            User friendInfo = userMapper.selectById(friendId);
            friend.setFriendHeadImage(friendInfo.getHeadImage());
            friend.setFriendNickName(friendInfo.getNickName());
            this.save(friend);
        }
    }
```

### 实现过程

获取用户id，和好友id进行绑定

## 6.5 查找好友信息

### findFriend

```java
    @GetMapping("/find/{friendId}")
    @Operation(summary = "查找好友信息", description = "查找好友信息")
    public Result<FriendVO> findFriend(@NotNull(message = "好友id不可为空") @PathVariable Long friendId) {
        return ResultUtils.success(friendService.findFriend(friendId));
    }
```

### findFriend-Impl

```java
    @Override
    public FriendVO findFriend(Long friendId) {
        UserSession session = SessionContext.getSession();
        QueryWrapper<Friend> wrapper = new QueryWrapper<>();
        wrapper.lambda()
                .eq(Friend::getUserId, session.getUserId())
                .eq(Friend::getFriendId, friendId);
        Friend friend = this.getOne(wrapper);
        if (Objects.isNull(friend)) {
            throw new GlobalException("对方不是您的好友");
        }
        FriendVO vo = new FriendVO();
        vo.setId(friend.getFriendId());
        vo.setHeadImage(friend.getFriendHeadImage());
        vo.setNickName(friend.getFriendNickName());
        return vo;
    }
```

### 实现过程

根据session中userid和friendid查库，返回vo

## 6.6 删除好友

### delFriend

```java
    @DeleteMapping("/delete/{friendId}")
    @Operation(summary = "删除好友", description = "解除好友关系")
    public Result delFriend(@NotNull(message = "好友id不可为空") @PathVariable Long friendId) {
        friendService.delFriend(friendId);
        return ResultUtils.success();
    }

```

### delFriend-Impl

```java
    @Transactional(rollbackFor = Exception.class)
    @Override
    public void delFriend(Long friendId) {
        long userId = SessionContext.getSession().getUserId();
        // 互相解除好友关系，走代理清理缓存
        FriendServiceImpl proxy = (FriendServiceImpl) AopContext.currentProxy();
        proxy.unbindFriend(userId, friendId);
        proxy.unbindFriend(friendId, userId);
        log.info("删除好友，用户id:{},好友id:{}", userId, friendId);
    }
```

### unbindFriend

```java
    @CacheEvict(key = "#userId+':'+#friendId")
    public void unbindFriend(Long userId, Long friendId) {
        QueryWrapper<Friend> queryWrapper = new QueryWrapper<>();
        queryWrapper.lambda()
                .eq(Friend::getUserId, userId)
                .eq(Friend::getFriendId, friendId);
        List<Friend> friends = this.list(queryWrapper);
        friends.forEach(friend -> this.removeById(friend.getId()));
    }
```

### 实现过程

与添加好友相反

## 6.7 更新好友信息

### modifyFriend

```java
    @PutMapping("/update")
    @Operation(summary = "更新好友信息", description = "更新好友头像或昵称")
    public Result modifyFriend(@Valid @RequestBody FriendVO vo) {
        friendService.update(vo);
        return ResultUtils.success();
    }
```

modifyFriend-Impl

```java
    @Override
    public void update(FriendVO vo) {
        long userId = SessionContext.getSession().getUserId();
        QueryWrapper<Friend> queryWrapper = new QueryWrapper<>();
        queryWrapper.lambda()
                .eq(Friend::getUserId, userId)
                .eq(Friend::getFriendId, vo.getId());
        Friend f = this.getOne(queryWrapper);
        if (Objects.isNull(f)) {
            throw new GlobalException("对方不是您的好友");
        }
        f.setFriendHeadImage(vo.getHeadImage());
        f.setFriendNickName(vo.getNickName());
        this.updateById(f);
    }
```

### 实现过程

查找好友信息，进行修改，updatebyid

## 6.8 其他代码

### FriendService

```java

public interface FriendService extends IService<Friend> {

    /**
     * 判断用户2是否用户1的好友
     *
     * @param userId1 用户1的id
     * @param userId2 用户2的id
     * @return true/false
     */
    Boolean isFriend(Long userId1, Long userId2);


    /**
     * 查询用户的所有好友
     *
     * @param userId 用户id
     * @return 好友列表
     */
    List<Friend> findFriendByUserId(Long userId);

    /**
     * 添加好友，互相建立好友关系
     *
     * @param friendId 好友的用户id
     */
    void addFriend(Long friendId);

    /**
     * 删除好友，双方都会解除好友关系
     *
     * @param friendId 好友的用户id
     */
    void delFriend(Long friendId);

    /**
     * 更新好友信息，主要是头像和昵称
     *
     * @param vo 好友vo
     */
    void update(FriendVO vo);

    /**
     * 查询指定的某个好友信息
     *
     * @param friendId 好友的用户id
     * @return 好友信息
     */
    FriendVO findFriend(Long friendId);
}
```

### Friend

```java
@Data
@TableName("im_friend")
public class Friend{


    /**
     * id
     */
    @TableId
    private Long id;

    /**
     * 用户id
     */
    private Long userId;

    /**
     * 好友id
     */
    private Long friendId;

    /**
     * 用户昵称
     */
    private String friendNickName;

    /**
     * 用户头像
     */
    private String friendHeadImage;

    /**
     * 创建时间
     */
    private Date createdTime;

}
```

#### @TableId

@TableId注解是专门用在主键上的注解，如果数据库中的主键字段名和实体中的属性名，不一样且不是驼峰之类的对应关系，可以在实体中表示主键的属性上加@Tableid注解，并指定@Tableid注解的value属性值为表中主键的字段名既可以对应上。

### FriendMapper

```java
public interface FriendMapper extends BaseMapper<Friend> {}
```

### RedisKey

```java
public final class RedisKey {

    /**
     *  用户状态 无值:空闲  1:正在忙
     */
    public static final String IM_USER_STATE = "im:user:state";
    /**
     * 已读群聊消息位置(已读最大id)
     */
    public static final String IM_GROUP_READED_POSITION = "im:readed:group:position";
    /**
     * webrtc 单人通话
     */
    public static final String IM_WEBRTC_PRIVATE_SESSION = "im:webrtc:private:session";
    /**
     * webrtc 群通话
     */
    public static final String IM_WEBRTC_GROUP_SESSION = "im:webrtc:group:session";

    /**
     * 用户被封禁消息队列
     */
    public static final String IM_QUEUE_USER_BANNED = "im:queue:user:banned";

    /**
     * 群聊被封禁消息队列
     */
    public static final String IM_QUEUE_GROUP_BANNED = "im:queue:group:banned";

    /**
     * 群聊解封消息队列
     */
    public static final String IM_QUEUE_GROUP_UNBAN = "im:queue:group:unban";

    /**
     * 缓存是否好友：bool
     */
    public static final String IM_CACHE_FRIEND = "im:cache:friend";
    /**
     * 缓存群聊信息
     */
    public static final String IM_CACHE_GROUP =  "im:cache:group";
    /**
     * 缓存群聊成员id
     */
    public static final String IM_CACHE_GROUP_MEMBER_ID = "im:cache:group_member_ids";
    /**
     * 分布式锁前缀
     */
    public static final String IM_LOCK_RTC_GROUP =  "im:lock:rtc:group";

}
```

### FriendVO

```java
@Data
@Schema(description = "好友信息VO")
public class FriendVO {

    @NotNull(message = "好友id不可为空")
    @Schema(description = "好友id")
    private Long id;

    @NotNull(message = "好友昵称不可为空")
    @Schema(description = "好友昵称")
    private String nickName;


    @Schema(description = "好友头像")
    private String headImage;
}
```

# 07 文件上传模块

## 7.1 文件消息

### 整合minio

使用minio作为文件存储服务。

| **类**    | **方法**        | **说明**               |
| --------- | --------------- | ---------------------- |
| MinioUtil | bucketExists    | 查看存储bucket是否存在 |
| MinioUtil | makeBucket      | 创建存储bucket         |
| MinioUtil | setBucketPublic | 设置bucket权限为public |
| MinioUtil | upload          | 文件上传               |
| MinioUtil | remove          | 文件删除               |

### 文件上传

编写了两个接口，除了在全屏展示大图时使用原图，前端其他所有地方都是展示缩略图。

| **类**      | **方法**    | **说明**                     |
| ----------- | ----------- | ---------------------------- |
| FileService | uploadFile  | 上传普通文件                 |
| FileService | uploadImage | 上传图片同时返回原图和缩略图 |

### 实现文件消息

直接复用原先编写好的的文字消息接口，将文件、图片、语音的url以及其他信息以json的格式存储到content字段中。

<img src="D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240822210200822.png" alt="image-20240822210200822" style="zoom:67%;" />

不同类型的文件展示要求不同，content中包含的内容也不一样：

| **类型** | **content字段** | **说明**         |
| -------- | --------------- | ---------------- |
| 文件     | name            | 文件名           |
|          | size            | 文件大小（字节） |
|          | url             | 文件url          |
| 图片     | originUrl       | 原图             |
|          | thumbUrl        | 缩略图           |

## 7.2 FileController

```java
@Slf4j
@RestController
@Tag(name = "文件上传")
@RequiredArgsConstructor
public class FileController {

    private final FileService fileService;
}
```

## 7.3 FileService

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class FileService {
    private final MinioUtil minioUtil;
    @Value("${minio.public}")
    private String minIoServer;
    @Value("${minio.bucketName}")
    private String bucketName;
    @Value("${minio.imagePath}")
    private String imagePath;
    @Value("${minio.filePath}")
    private String filePath;
    @Value("${minio.videoPath}")
    private String videoPath;


    @PostConstruct
    public void init() {
        if (!minioUtil.bucketExists(bucketName)) {
            // 创建bucket
            minioUtil.makeBucket(bucketName);
            // 公开bucket
            minioUtil.setBucketPublic(bucketName);
        }
    }
}
```

### @Value

[@Value的用法-CSDN博客](https://blog.csdn.net/weixin_43888891/article/details/127505506?ops_request_misc=%7B%22request%5Fid%22%3A%22172441227116800182755609%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172441227116800182755609&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-127505506-null-null.142^v100^control&utm_term=@value&spm=1018.2226.3001.4187)

`@Value`属于`spring`的注解，在`spring-beans`包下，可以在 字段 或 方法参数 或 构造函数参数 上使用，通常用于属性注入

### @PostConstruct

[@Value的用法-CSDN博客](https://blog.csdn.net/weixin_43888891/article/details/127505506?ops_request_misc=%7B%22request%5Fid%22%3A%22172441227116800182755609%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172441227116800182755609&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-127505506-null-null.142^v100^control&utm_term=@value&spm=1018.2226.3001.4187)

被注解的方法，在对象加载完依赖注入后执行。

- 要在**依赖加载后，对象使用前执行**，而且只执行一次
- 所有支持依赖注入的类都要支持此方法
- 除了拦截器这个特殊情况以外，**其他情况都不允许有参数**，否则spring框架会报IllegalStateException；而且返回值要是void，但实际也可以有返回值，至少不会报错，只会忽略
- 方法随便你用什么权限来修饰，public、protected、private都可以，反正功能是由反射来实现
- 方法**不可以是static的，但可以是final的**
- 在一个bean的初始化过程中，方法执行先后顺序为@**Constructor > @Autowired > @PostConstruct**,先执行完构造方法，再注入依赖，最后执行初始化操作

## 7.4 MinioUtil

```java
@Slf4j
@Component
@RequiredArgsConstructor
public class MinioUtil {

    private final MinioClient minioClient;

    /**
     * 查看存储bucket是否存在
     *
     * @return boolean
     */
    public Boolean bucketExists(String bucketName) {
        try {
            return minioClient.bucketExists(BucketExistsArgs.builder().bucket(bucketName).build());
        } catch (Exception e) {
            log.error("查询bucket失败", e);
            return false;
        }
    }

    /**
     * 创建存储bucket
     */
    public void makeBucket(String bucketName) {
        try {
            minioClient.makeBucket(MakeBucketArgs.builder()
                    .bucket(bucketName)
                    .build());
        } catch (Exception e) {
            log.error("创建bucket失败,", e);
        }
    }

    /**
     * 设置bucket权限为public
     */
    public void setBucketPublic(String bucketName) {
        try {
            // 设置公开
            String sb = "{\"Version\":\"2012-10-17\"," +
                    "\"Statement\":[{\"Effect\":\"Allow\",\"Principal\":" +
                    "{\"AWS\":[\"*\"]},\"Action\":[\"s3:ListBucket\",\"s3:ListBucketMultipartUploads\"," +
                    "\"s3:GetBucketLocation\"],\"Resource\":[\"arn:aws:s3:::" + bucketName +
                    "\"]},{\"Effect\":\"Allow\",\"Principal\":{\"AWS\":[\"*\"]},\"Action\":[\"s3:PutObject\",\"s3:AbortMultipartUpload\",\"s3:DeleteObject\",\"s3:GetObject\",\"s3:ListMultipartUploadParts\"],\"Resource\":[\"arn:aws:s3:::" +
                    bucketName +
                    "/*\"]}]}";
            minioClient.setBucketPolicy(
                    SetBucketPolicyArgs.builder()
                            .bucket(bucketName)
                            .config(sb)
                            .build());
        } catch (Exception e) {
            log.error("创建bucket失败,", e);
        }
    }
```

## 7.5 MinioClientConfig

```java

@Configuration
public class MinIoClientConfig {

    @Value("${minio.endpoint}")
    private String endpoint;
    @Value("${minio.accessKey}")
    private String accessKey;
    @Value("${minio.secretKey}")
    private String secretKey;


    @Bean
    public MinioClient minioClient() {
        // 注入minio 客户端
        return MinioClient.builder()
                .endpoint(endpoint)
                .credentials(accessKey, secretKey)
                .build();
    }
}
```

## 7.6 上传图片

### uploadImage

```java
    @Operation(summary = "上传图片", description = "上传图片,上传后返回原图和缩略图的url")
    @PostMapping("/image/upload")
    public Result<UploadImageVO> uploadImage(@RequestParam("file") MultipartFile file) {
        return ResultUtils.success(fileService.uploadImage(file));
    }
```

#### MultipartFile

[深入理解 MultipartFile 处理文件-CSDN博客](https://blog.csdn.net/tengyuxin/article/details/127987074?ops_request_misc=%7B%22request%5Fid%22%3A%22172441307816800222844493%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172441307816800222844493&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-127987074-null-null.142^v100^control&utm_term=multipartfile&spm=1018.2226.3001.4187)

一种可以接收使用多种请求方式来进行上传文件的代表形式.

 首先MultipartFile是一个接口，并继承自InputStreamSource，且在InputStreamSource接口中封装了getInputStream方法，该方法的返回类型为InputStream类型，这也就是为什么MultipartFile文件可以转换为输入流

### uploadImage-FileService

```java
    public UploadImageVO uploadImage(MultipartFile file) {
        try {
            Long userId = SessionContext.getSession().getUserId();
            // 大小校验
            if (file.getSize() > Constant.MAX_IMAGE_SIZE) {
                throw new GlobalException(ResultCode.PROGRAM_ERROR, "图片大小不能超过20M");
            }
            // 图片格式校验
            if (!FileUtil.isImage(file.getOriginalFilename())) {
                throw new GlobalException(ResultCode.PROGRAM_ERROR, "图片格式不合法");
            }
            // 上传原图
            UploadImageVO vo = new UploadImageVO();
            String fileName = minioUtil.upload(bucketName, imagePath, file);
            if (StringUtils.isEmpty(fileName)) {
                throw new GlobalException(ResultCode.PROGRAM_ERROR, "图片上传失败");
            }
            vo.setOriginUrl(generUrl(FileType.IMAGE, fileName));
            // 大于30K的文件需上传缩略图
            if (file.getSize() > 30 * 1024) {
                byte[] imageByte = ImageUtil.compressForScale(file.getBytes(), 30);
                fileName = minioUtil.upload(bucketName, imagePath, Objects.requireNonNull(file.getOriginalFilename()), imageByte, file.getContentType());
                if (StringUtils.isEmpty(fileName)) {
                    throw new GlobalException(ResultCode.PROGRAM_ERROR, "图片上传失败");
                }
            }
            vo.setThumbUrl(generUrl(FileType.IMAGE, fileName));
            log.info("文件图片成功，用户id:{},url:{}", userId, vo.getOriginUrl());
            return vo;
        } catch (IOException e) {
            log.error("上传图片失败，{}", e.getMessage(), e);
            throw new GlobalException(ResultCode.PROGRAM_ERROR, "图片上传失败");
        }
    }

    public String generUrl(FileType fileTypeEnum, String fileName) {
        String url = minIoServer + "/" + bucketName;
        switch (fileTypeEnum) {
            case FILE:
                url += "/" + filePath + "/";
                break;
            case IMAGE:
                url += "/" + imagePath + "/";
                break;
            case VIDEO:
                url += "/" + videoPath + "/";
                break;
            default:
                break;
        }
        url += fileName;
        return url;
    }

```

### MinioUtil

```java
/**
     * 文件上传
     *
     * @param bucketName bucket名称
     * @param path       路径
     * @param file       文件
     * @return Boolean
     */
    public String upload(String bucketName, String path, MultipartFile file) {
        String originalFilename = file.getOriginalFilename();
        if (StringUtils.isBlank(originalFilename)) {
            throw new RuntimeException();
        }
        String fileName = System.currentTimeMillis() + "";
        if (originalFilename.lastIndexOf(".") >= 0) {
            fileName += originalFilename.substring(originalFilename.lastIndexOf("."));
        }
        String objectName = DateTimeUtils.getFormatDate(new Date(), DateTimeUtils.PARTDATEFORMAT) + "/" + fileName;
        try {
            InputStream stream = new ByteArrayInputStream(file.getBytes());
            PutObjectArgs objectArgs = PutObjectArgs.builder().bucket(bucketName).object(path + "/" + objectName)
                .stream(stream, file.getSize(), -1).contentType(file.getContentType()).build();
            //文件名称相同会覆盖
            minioClient.putObject(objectArgs);
        } catch (Exception e) {
            log.error("上传图片失败,", e);
            return null;
        }
        return objectName;
    }
```

```java
/**
 * 文件上传
 *
 * @param bucketName  bucket名称
 * @param path        路径
 * @param name        文件名
 * @param fileByte    文件内容
 * @param contentType contentType
 * @return objectName
 */
public String upload(String bucketName, String path, String name, byte[] fileByte, String contentType) {

    String fileName = System.currentTimeMillis() + name.substring(name.lastIndexOf("."));
    String objectName = DateTimeUtils.getFormatDate(new Date(), DateTimeUtils.PARTDATEFORMAT) + "/" + fileName;
    try {
        InputStream stream = new ByteArrayInputStream(fileByte);
        PutObjectArgs objectArgs = PutObjectArgs.builder().bucket(bucketName).object(path + "/" + objectName)
                .stream(stream, fileByte.length, -1).contentType(contentType).build();
        //文件名称相同会覆盖
        minioClient.putObject(objectArgs);
    } catch (Exception e) {
        log.error("上传文件失败,", e);
        return null;
    }
    return objectName;
}
```

### 实现过程

上传图像，先进行大小和图片格式校验，然后上传原图，大于30k需要上传缩略图，然后返回vo
    上传原图：获得源文件名，然后获得拓展名，加入时间，上传

## 7.7 上传文件

### uploadFile

```java
    @CrossOrigin
    @Operation(summary = "上传文件", description = "上传文件，上传后返回文件url")
    @PostMapping("/file/upload")
    public Result<String> uploadFile(@RequestParam("file") MultipartFile file) {
        return ResultUtils.success(fileService.uploadFile(file), "");
    }
```

#### @CrossOrigin

[注解@CrossOrigin详解-CSDN博客](https://blog.csdn.net/qq_18671415/article/details/109275495?ops_request_misc=%7B%22request%5Fid%22%3A%22172441358316800185882287%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172441358316800185882287&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-5-109275495-null-null.142^v100^control&utm_term=    @CrossOrigin&spm=1018.2226.3001.4187)

#### 跨域问题

[JAVA | Java 解决跨域问题 - 双鬼带单 - 博客园 (cnblogs.com)](https://www.cnblogs.com/zyndev/p/13697313.html#:~:text=跨域，指的是浏览器不)

跨域（CORS）是指不同域名之间相互访问。跨域，指的是浏览器不能执行其他网站的脚本，它是由浏览器的同源策略所造成的，是浏览器对于JavaScript所定义的安全限制策略。

什么情况下会跨域：

1. **同一协议， 如http或https**
2. **同一IP地址, 如127.0.0.1**
3. **同一端口, 如8080**
4. 以上三个条件中有一个条件不同就会产生跨域问题

解决方案：

1. 前端

   1. 使用JSONP方式实现跨域调用；
   2. 使用NodeJS服务器做为服务代理，前端发起请求到NodeJS服务器， NodeJS服务器代理转发请求到后端服务器

2. 后端

   1. **nginx反向代理**解决跨域

      ```BASH
      location / {
         add_header Access-Control-Allow-Origin *;
         add_header Access-Control-Allow-Headers X-Requested-With;
         add_header Access-Control-Allow-Methods GET,POST,PUT,DELETE,OPTIONS;
       
         if ($request_method = 'OPTIONS') {
           return 204;
         }
      }
      ```

   2. **服务端设置Response Header(响应头部)**的Access-Control-Allow-Origin

      使用Filter过滤器来过滤服务请求，向请求端设置Response Header(响应头部)的Access-Control-Allow-Origin属性声明允许跨域访问。

      ```java
      @WebFilter
      public class CorsFilter implements Filter {  
       
          @Override
          public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) throws IOException, ServletException {  
              HttpServletResponse response = (HttpServletResponse) res;  
              response.setHeader("Access-Control-Allow-Origin", "*");  
              response.setHeader("Access-Control-Allow-Methods", "*");  
              response.setHeader("Access-Control-Max-Age", "3600");  
              response.setHeader("Access-Control-Allow-Headers", "*");
              response.setHeader("Access-Control-Allow-Credentials", "true");
              chain.doFilter(req, res);  
          }  
      }
      ```

      CrossInterceptor类属于服务端设置Response Header(响应头部)的Access-Control-Allow-Origin这一种方式来解决跨域问题。具体来说，这种解决方案是在Spring MVC或Spring Boot应用中，通过拦截器（Interceptor）的方式，在HTTP响应头中添加CORS相关的头信息，从而允许来自不同源的请求能够成功访问你的服务端资源。

      ```java
      @Component
      public class CrossInterceptor extends HandlerInterceptorAdapter {
       
          @Override
          public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
              response.setHeader("Access-Control-Allow-Origin", "*");
              response.setHeader("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE, OPTIONS");
              response.setHeader("Access-Control-Max-Age", "3600");
              response.setHeader("Access-Control-Allow-Headers", "*");
              response.setHeader("Access-Control-Allow-Credentials", "true");
              return true;
          }
      }
       
      ```

      Spring Cloud Gateway跨域配置

      ```yaml
      spring: 
        cloud:
          gateway:
            globalcors:
              cors-configurations:
                '[/**]':
                  # 允许跨域的源(网站域名/ip)，设置*为全部
                  # 允许跨域请求里的head字段，设置*为全部
                  # 允许跨域的method， 默认为GET和OPTIONS，设置*为全部
                  allow-credentials: true
                  allowed-origins:
                    - "http://xb.abc.com"
                    - "http://sf.xx.com"
                  allowed-headers: "*"
                  allowed-methods:
                    - OPTIONS
                    - GET
                    - POST
                    - DELETE
                    - PUT
                    - PATCH
                  max-age: 3600
      ```

       通过`gateway` 转发的其他项目，不要进行配置跨域配置

   3. **在需要跨域访问的类和方法中设置允许跨域访问**（如Spring中使用@CrossOrigin注解）；

      如果只是想部分接口跨域，且不想使用配置来管理的话，可以使用这种方式

      1. 在controller使用

         ```java
         @CrossOrigin
         @RestController
         @RequestMapping("/user")
         public class UserController {
          
         	@GetMapping("/{id}")
         	public User get(@PathVariable Long id) {
         		
         	}
          
         	@DeleteMapping("/{id}")
         	public void remove(@PathVariable Long id) {
          
         	}
         }
         ```

      2. 在具体接口上使用

         ```java
         @RestController
         @RequestMapping("/user")
         public class UserController {
          
         	@CrossOrigin
         	@GetMapping("/{id}")
         	public User get(@PathVariable Long id) {
         		
         	}
          
         	@DeleteMapping("/{id}")
         	public void remove(@PathVariable Long id) {
          
         	}
         }
         ```

   4. 继承使用Spring Web的CorsFilter（适用于Spring MVC、Spring Boot）

   5. **实现WebMvcConfigurer接口**（适用于Spring Boot）

      ```java
      @Configuration
      @SuppressWarnings("SpringJavaAutowiredFieldsWarningInspection")
      public class AppConfig implements WebMvcConfigurer {
       
          @Override
          public void addCorsMappings(CorsRegistry registry) {
              registry.addMapping("/**")  // 拦截所有的请求
                      .allowedOrigins("http://www.abc.com")  // 可跨域的域名，可以为 *
                      .allowCredentials(true)
                      .allowedMethods("*")   // 允许跨域的方法，可以单独配置
                      .allowedHeaders("*");  // 允许跨域的请求头，可以单独配置
          }
      }
       
      ```

###  uploadFile-FileService

```java
    public String uploadFile(MultipartFile file) {
        Long userId = SessionContext.getSession().getUserId();
        // 大小校验
        if (file.getSize() > Constant.MAX_FILE_SIZE) {
            throw new GlobalException(ResultCode.PROGRAM_ERROR, "文件大小不能超过20M");
        }
        // 上传
        String fileName = minioUtil.upload(bucketName, filePath, file);
        if (StringUtils.isEmpty(fileName)) {
            throw new GlobalException(ResultCode.PROGRAM_ERROR, "文件上传失败");
        }
        String url = generUrl(FileType.FILE, fileName);
        log.info("文件文件成功，用户id:{},url:{}", userId, url);
        return url;
    }
```

### 实现过程

进行大小校验、上传。

## 7.8 其他代码

### UploadImageVO

```java
@Data
@Schema(description = "图片上传VO")
public class UploadImageVO {

    @Schema(description = "原图")
    private String originUrl;

    @Schema(description = "缩略图")
    private String thumbUrl;
}
```

### Constant

```java
public final class Constant {

    /**
     * 系统用户id
     */
    public static final Long SYS_USER_ID = 0L;
    /**
     * 最大图片上传大小
     */
    public static final Long MAX_IMAGE_SIZE = 20 * 1024 * 1024L;
    /**
     * 最大上传文件大小
     */
    public static final Long MAX_FILE_SIZE = 20 * 1024 * 1024L;
    /**
     * 群聊最大人数
     */
    public static final Long MAX_GROUP_MEMBER = 500L;

}
```

### FileUtil

```java
public final class FileUtil {
    /**
     * 获取文件后缀
     *
     * @param fileName 文件名
     * @return boolean
     */
    public static String getFileExtension(String fileName) {
        return fileName.substring(fileName.lastIndexOf(".") + 1);
    }

    /**
     * 判断文件是否图片类型
     *
     * @param fileName 文件名
     * @return boolean
     */
    public static boolean isImage(String fileName) {
        String extension = getFileExtension(fileName);
        String[] imageExtension = new String[]{"jpeg", "jpg", "bmp", "png", "webp", "gif"};
        for (String e : imageExtension) {
            if (extension.toLowerCase().equals(e)) {
                return true;
            }
        }
        return false;
    }
}
```

### FileType

```java
@AllArgsConstructor
public enum FileType {

    /**
     * 文件
     */
    FILE(0, "文件"),
    /**
     * 图片
     */
    IMAGE(1, "图片"),
    /**
     * 视频
     */
    VIDEO(2, "视频"),
    /**
     * 声音
     */
    AUDIO(3, "声音");

    private final Integer code;

    private final String desc;

    public Integer code() {
        return this.code;
    }
}
```

### ImageUtil

```java
@Slf4j
public final class ImageUtil {

    //以下是常量,按照阿里代码开发规范,不允许代码中出现魔法值
    private static final Integer ZERO = 0;
    private static final Integer ONE_ZERO_TWO_FOUR = 1024;
    private static final Integer NINE_ZERO_ZERO = 900;
    private static final Integer THREE_TWO_SEVEN_FIVE = 3275;
    private static final Integer TWO_ZERO_FOUR_SEVEN = 2047;
    private static final Double ZERO_EIGHT_FIVE = 0.85;
    private static final Double ZERO_SIX = 0.6;
    private static final Double ZERO_FOUR_FOUR = 0.44;
    private static final Double ZERO_FOUR = 0.4;

    /**
     * 根据指定大小压缩图片
     *
     * @param imageBytes  源图片字节数组
     * @param desFileSize 指定图片大小，单位kb
     * @return 压缩质量后的图片字节数组
     */
    public static byte[] compressForScale(byte[] imageBytes, long desFileSize) {
        if (imageBytes == null || imageBytes.length <= ZERO || imageBytes.length < desFileSize * ONE_ZERO_TWO_FOUR) {
            return imageBytes;
        }
        long srcSize = imageBytes.length;
        double accuracy = getAccuracy(srcSize / ONE_ZERO_TWO_FOUR);
        try {
            while (imageBytes.length > desFileSize * ONE_ZERO_TWO_FOUR) {
                ByteArrayInputStream inputStream = new ByteArrayInputStream(imageBytes);
                ByteArrayOutputStream outputStream = new ByteArrayOutputStream(imageBytes.length);
                Thumbnails.of(inputStream)
                        .scale(accuracy)
                        .outputQuality(accuracy)
                        .toOutputStream(outputStream);
                imageBytes = outputStream.toByteArray();
            }
            log.info("图片原大小={}kb | 压缩后大小={}kb",
                    srcSize / ONE_ZERO_TWO_FOUR, imageBytes.length / ONE_ZERO_TWO_FOUR);
        } catch (Exception e) {
            log.error("【图片压缩】msg=图片压缩失败!", e);
        }
        return imageBytes;
    }

    /**
     * 自动调节精度(经验数值)
     *
     * @param size 源图片大小
     * @return 图片压缩质量比
     */
    private static double getAccuracy(long size) {
        double accuracy;
        if (size < NINE_ZERO_ZERO) {
            accuracy = ZERO_EIGHT_FIVE;
        } else if (size < TWO_ZERO_FOUR_SEVEN) {
            accuracy = ZERO_SIX;
        } else if (size < THREE_TWO_SEVEN_FIVE) {
            accuracy = ZERO_FOUR_FOUR;
        } else {
            accuracy = ZERO_FOUR;
        }
        return accuracy;
    }

}
```

### DateTimeUtils

```java
/**
 * 日期处理工具类
 *
 * @version 1.0
 */
public final class DateTimeUtils extends DateUtils {

    public static final String FULL_DATE_FORMAT = "yyyy-MM-dd HH:mm:ss";
    public static final String PARTDATEFORMAT = "yyyyMMdd";


    /**
     * 将日期类型转换为字符串
     *
     * @param date    日期
     * @param xFormat 格式
     * @return 日期
     */
    public static String getFormatDate(Date date, String xFormat) {
        date = date == null ? new Date() : date;
        xFormat = StringUtils.isNotEmpty(xFormat) ? xFormat : FULL_DATE_FORMAT;
        SimpleDateFormat sdf = new SimpleDateFormat(xFormat);
        return sdf.format(date);
    }
}
```

# 08 :star: 实现聊天功能

## 8.1 :star: 接入消息推送（实现聊天功能）

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

### im-client相关API介绍

#### API列表

| **API**            | **说明**     |
| ------------------ | ------------ |
| sendPrivateMessage | 推送私聊消息 |
| sendGroupMessage   | 推送群聊消息 |
| sendSystemMessage  | 推送系统消息 |
| isOnline           | 用户是否在线 |

#### 私聊消息API参数

| **参数**      | **类型**      | **必填** | **说明**                              |
| ------------- | ------------- | -------- | ------------------------------------- |
| sender        | IMUserInfo    | 是       | 发送方的信息,包括发送方的id和终端类型 |
| recvId        | Long          | 是       | 接收方id                              |
| recvTerminals | List<Integer> | 否       | 接收者的终端类型,默认全部             |
| sendToSelf    | Boolean       | 否       | 是否发送给自己的其他终端,默认true     |
| sendResult    | Boolean       | 否       | 是否需要回推发送结果,默认true         |
| data          | 泛型          | 是       | 消息内容，类型由应用层定义            |

#### 群聊消息API参数

| **参数**      | **类型**      | **必填** | **说明**                                    |
| ------------- | ------------- | -------- | ------------------------------------------- |
| sender        | IMUserInfo    | 是       | 发送方的信息,包括发送方的id和终端类型       |
| recvIds       | List<Long>    | 否       | 接收者id列表(一般填群成员id,为空则不会推送) |
| recvTerminals | List<Integer> | 否       | 接收者终端类型,默认所有类型                 |
| sendToSelf    | Boolean       | 否       | 是否需要同时推送给自己的其他终端,默认true   |
| sendResult    | Boolean       | 否       | 是否需要回推发送结果,默认true               |
| data          | 泛型          | 是       | 消息内容，类型由应用层定义                  |

#### 系统消息API参数

| **参数**      | **类型**      | **必填** | **说明**                                    |
| ------------- | ------------- | -------- | ------------------------------------------- |
| recvIds       | List<Long>    | 否       | 接收者id列表(一般填群成员id,为空则不会推送) |
| recvTerminals | List<Integer> | 否       | 接收者终端类型,默认所有类型                 |
| sendResult    | Boolean       | 否       | 是否需要回推发送结果,默认true               |
| data          | 泛型          | 是       | 消息内容，类型由应用层定义                  |

### 快速接入

#### 后端接入

##### 1. 在im-platform的pom.xml中引入im-client依赖

```xml
<dependency>
  <groupId>com.bx</groupId>
  <artifactId>im-client</artifactId>
  <version>3.0.0</version>
</dependency>
```

##### 2. 消息推送使用了redis充当MQ进行通信,所以要在application.yml中配置redis地址

```yaml
spring:
  redis:
    host: 127.0.0.1
    port: 6379
```

##### 3. 调用IMClient的API进行消息推送

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

##### 4.  监听发送结果

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

#### 前端接入

##### 1. 将wssocket.js放到im-web中的/api目录

##### 2. 接收消息

### 实现聊天功能

| **类名**                  | **方法**    | **说明**                                        |
| ------------------------- | ----------- | ----------------------------------------------- |
| PrivateMessageServiceImpl | sendMessage | 推送私聊消息                                    |
| GroupMessageServiceImpl   | sendMessage | 推送群聊消息                                    |
| PrivateMessageListener    | process     | 监听私聊消息结果,消息发送成功后，状态修改为送达 |
| GroupMessageListener      | process     | 监听群聊消息结果,暂时没实际作用                 |

## 8.2 :star: 消息推送底层分析

我们已经通过im-client的sendPrivateMessage接口轻松实现了聊天功能，但是现在这个接口的内部实现对我们来说还是一个未知的"黑盒"。在这一章节中，我们就来打开这个"黑盒"，看看调用sendPrivateMessage之后，具体干了什么事情。

### 消息推送

#### 交互流程

用户发送私聊消息完整流程：

![image-20240823213628023](D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240823213628023.png)

当消息的发送者和接收者连的不是同一个im-server时，消息是无法直接推送的，所以我们需要设计出能够支持跨节点推送的方案:

- 利用了redis的list类型实现消息推送，其中key为im:message:private:${serverid},每个key的数据可以看做一个队列,每个im-server根据自身的id只消费属于自己的消息队列
- 用户发起ws连接时，redis会记录每个用户所连接的serverid
- 用户发送消息时，im-client将根据接收方的serverid,决定将消息推向响应的队列

图中2~5步都是调用sendPrivateMessage之后的流程，整个流程涉及3个组件:

- **im-client**：客户端部分，负责将消息推送至redis,对应图中橙色部分
- **im-server**:  服务器部分，负责从redis拉取消息，并通过ws推送给用户，对应图中红色部分
- **redis**: 中间角色，充当消息中间件，负责将消息从im-client转发到im-server

#### 客户端部分（im-client）

核心流程逻辑说明：

- 通过redis获取接收方id所绑定的serverid(每个im-server都会生成一个唯一的id)
- 当serverid存在表示用户在线，反之则说明用户离线
- 如果用户在线，则将消息上传到redis，等待im-server拉取。注意上传消息的key是通过serverid生成的，也就是说，不同的im-server使用不同的消息队列，这么做是为了支持im-server集群化部署。
- 如果用户离线，则向应用层回复消息发送失败状态**（消息推送结果监听机制下面有介绍）**

| **类名** | **方法**           | **作用**     |
| -------- | ------------------ | ------------ |
| IMSender | sendPrivateMessage | 推送私聊消息 |
| IMSender | sendGroupMessage   | 推送群聊消息 |

#### 服务器部分（im-server）

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

![image-20240823214003841](D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240823214003841.png)

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

![image-20240823214742179](D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20240823214742179.png)

| **步骤** | **类**                         | **方法**    | **说明**                          |
| -------- | ------------------------------ | ----------- | --------------------------------- |
| 1        | PrivateMessageProcessor        | sendResult  | 向redis回推私聊消息推送结果       |
| 1        | GroupMessageProcessor          | sendResult  | 向redis回推群聊消息推送结果       |
| 2        | PrivateMessageResultResultTask | pullMessage | 从redis中定时拉取私聊消息推送结果 |
| 2        | GroupMessageResultResultTask   | pullMessage | 从redis中定时拉取群聊消息推送结果 |
| 3        | MessageListenerMulticaster     | multicast   | 向业务层的监听器广播消息推送结果  |

## 8.3 :star:离线消息和已读未读显示

### 消息状态

#### 消息状态类型

维护消息状态是实现离线消息和已读未读功能的关键**，**参考枚举类型**MessageStatus**，消息共有4个状态

| **状态**       | **状态值** | **状态变更时机**             |
| -------------- | ---------- | ---------------------------- |
| 未发送(UNSEND) | 0          | 消息入库后默认状态就是UNSEND |
| 已发送(SENDED) | 1          | 消息成功推送到客户端后       |
| 已撤回(RECALL) | 2          | 用户撤回消息后               |
| 已读(READED)   | 3          | 用户看到消息后(点击聊天列表) |

#### 消息状态记录

**私聊消息**和**群聊消息**的状态记录采用的是不同的方案。

- **私聊消息**的记录状态相对比较简单，消息表有status字段，直接存数据库中即可。
- **群聊消息**可能会存在部分用户已读，部分用户未读的情况，无法通过单一字段记录。考虑到写扩散造成的数据库IO读写压力，这里我选择了通过redis去记录:

**KEY:**    **im:readed:group:position:{groupId}:{userId}**

**VALUE:   该用户在该群已读的最大消息id**

记录了用户在某个群的已读的最大消息id后，只要是小于或等于这个id的消息都是已读消息，否则就是已读消息，而不必记录每条消息是否已读。

### 离线消息

#### 拉取离线消息

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

#### 已读状态变更

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

#### 离线状态变更

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

## 8.4 :star:消息可靠性、顺序性、唯一性

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

![image-20241015222041511](D:\2024\Notes\Typora\项目rpc+im\boxim.assets\image-20241015222041511.png)



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



## 8.5 可靠性

1. 添加消息到队列中，保证消息成功的添加到了队列
2. 保证消息发送出去，一定会被消费者正常消费
3. 消费者正常消费了，生产者或者队列 知道消费者已经成功的消费了消息

### 问题一解决方案-添加消息到队列中，保证消息成功的添加到了队列

添加消息到队列中，保证消息成功的添加到了队列

- 通过AMQP事务机制实现，这也是AMQP协议层面提供的解决方案；（影响性能）
- 通过将channel设置成confirm模式来实现；

#### Confirm消息确认模式

confirm机制： 只要放消息到 queue 是成功的，那么队列就一定会反馈，表示发送成功；并且，只要是成功的发送到队列中，就会反馈成功，就算是在队列中，消费者接收失败，也会反馈成功！

##### 实现原理

​        生产者将信道设置成confirm模式，一旦信道进入confirm模式，所有在该信道上面发布的消息都会被指派一个唯一的ID(从1开始)；

​        一旦消息被投递到所有匹配的队列之后，broker就会发送一个确认消息给生产者（包含消息的唯一 ID）,这就使得生产者知道消息已经正确到达目的队列了；

​        如果消息和队列是可持久化的，那么确认消息会将消息写入磁盘之后发出，broker回传给生产者的确认消息中deliver-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。

##### 优点

- 支持异步，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果RabbitMQ因为自身内部错误导致消息丢失，就会发送一条nack消息，生产者应用程序同样可以在回调方法中处理该nack消息。
- 在channel 被设置成 confirm 模式之后，所有被 publish 的后续消息都将被 confirm（即 ack） 或者被nack一次。但是没有对消息被 confirm 的快慢做任何保证，并且同一条消息不会既被 confirm又被nack 。

### 问题二解决方案-保证消息发送出去，一定会被消费者正常消费

使用 RabbitMQ中的 ACK 机制，就是手动确认消息。

消费者在声明队列时，可以指定noAck参数，当noAck=false时，RabbitMQ会等待消费者显式发回ack信号后才从内存(或者磁盘，持久化消息)中移去消息。否则，消息被消费后会被立即删除。

消费者接收每一条消息后都必须进行确认（消息接收和消息确认是两个不同操作）。只有消费者确认了消息，RabbitMQ 才能安全地把消息从队列中删除。

RabbitMQ不会为未ack的消息设置超时时间，它判断此消息是否需要重新投递给消费者的唯一依据是消费该消息的消费者连接是否已经断开。这么设计的原因是RabbitMQ允许消费者消费一条消息的时间可以很长。保证数据的最终一致性；

如果消费者返回ack之前断开了链接，RabbitMQ 会重新分发给下一个订阅的消费者。（可能存在消息重复消费的隐患，需要去重）；

### 问题三解决方案-消费者正常消费了，生产者或者队列 知道消费者已经成功的消费了消息 

和问题2的解决方法一致，在手动签收消息之后做手动告诉生产者已经消费成功

## 持久化

可以进行消息持久化, 即使rabbitMQ挂了，重启后也能恢复数据

如果要进行消息持久化，那么需要对以下3种实体均配置持久化

a) Exchange

声明exchange时设置持久化（durable = true）并且不自动删除(autoDelete = false)

b) Queue

声明queue时设置持久化（durable = true）并且不自动删除(autoDelete = false)

c) message

发送消息时通过设置deliveryMode=2持久化消息

# 09 私聊功能模块

## im-platform

- 消息推送：前端调用im-platform的controller，im-platform中需要集成im-client，继承之后，调用im-client的api 进行消息推送
- 监听发送结果：编写消息监听类，实现MessageListener，监听私聊消息结果，消息发送成功后状态修改为送达
- 拉取离线消息：拉取最近1个月的离线私聊消息，最多拉取1000条，离线消息被拉取后，消息状态修改为**已发送**状态。
- 已读未读显示：
  - 已读状态变更：将会话中的所有私聊消息都更改为**已读**状态
  - 离线状态变更：获取某个会话中已读消息的最大id

## im-client

- 消息推送：使用sendPrivateMessage推送私聊消息，将消息推送至redis
  - 通过redis获取接收方id所绑定的serverid(每个im-server都会生成一个唯一的id)
  - 当serverid存在表示用户在线，反之则说明用户离线
  - 如果用户在线，则将消息上传到redis，等待im-server拉取。注意上传消息的key是通过serverid生成的，也就是说，不同的im-server使用不同的消息队列，这么做是为了支持im-server集群化部署。
  - 如果用户离线，则向应用层回复消息发送失败状态**（消息推送结果监听机制下面有介绍）**
- 监听发送结果：编写MessageListener接口，加上@IMListener
- 推送结果监听机制:

  - | **步骤** | **类**                         | **方法**    | **说明**                          |
    | -------- | ------------------------------ | ----------- | --------------------------------- |
    | 2        | PrivateMessageResultResultTask | pullMessage | 从redis中定时拉取私聊消息推送结果 |
    | 2        | GroupMessageResultResultTask   | pullMessage | 从redis中定时拉取群聊消息推送结果 |
    | 3        | MessageListenerMulticaster     | multicast   | 向业务层的监听器广播消息推送结果  |

## im-server

- 消息推送：从redis拉取消息，并且通过ws推送给用户

  - 循环从redis中拉取私聊消息，然后将消息交由PrivateMessageProcessor处理

  - 通过接收方id取出对方的channel,通过channel将消息推送给用户

  - 无论推送成功还是失败，都会回推消息推送结果

  - | PullPrivateMessageTask  | pullMessage | 循环从redis中拉取私聊消息 |
    | ----------------------- | ----------- | ------------------------- |
    | PrivateMessageProcessor | process     | 处理拉取到的私聊消息      |
    | GroupMessageProcessor   | process     | 处理拉取到的群聊消息      |

    ### 消息接收

- 消息接收：

  - im-server已经通过ws向前端推送消息了，那么前端是如何跟im-server交互，并接收到消息呢？

  - | **步骤** | **类**                  | **方法**           | **说明**                            |
    | -------- | ----------------------- | ------------------ | ----------------------------------- |
    | 1        | IMChannelHandler        | handlerAdded       | 用户连接事件，这里只打印日志        |
    | 2        | LoginProcessor          | process            | 登录处理，将用户id和serverId绑定    |
    | 3        | HeartbeatProcessor      | process            | 心跳处理，绑定关系续命              |
    | 3        | IMChannelHandler        | userEventTriggered | 心跳超时事件，关闭channel           |
    | 4,5      | PrivateMessageProcessor | process            | 私聊消息发送处理，前面已讲解        |
    | 4,5      | GroupMessageProcessor   | process            | 私聊消息发送处理，前面已讲解        |
    | 6        | IMChannelHandler        | handlerRemoved     | 连接断开事件，移除channel，解除绑定 |

- 推送结果监听机制:

  - | **步骤** | **类**                  | **方法**   | **说明**                    |
    | -------- | ----------------------- | ---------- | --------------------------- |
    | 1        | PrivateMessageProcessor | sendResult | 向redis回推私聊消息推送结果 |
    | 1        | GroupMessageProcessor   | sendResult | 向redis回推群聊消息推送结果 |

## 9.1 platform消息推送

### PrivateMessageController

```java
@Tag(name = "私聊消息")
@RestController
@RequestMapping("/message/private")
@RequiredArgsConstructor
public class PrivateMessageController {
    private final PrivateMessageService privateMessageService;
    
    @PostMapping("/send")
    @Operation(summary = "发送消息", description = "发送私聊消息")
    public Result<PrivateMessageVO> sendMessage(@Valid @RequestBody PrivateMessageDTO vo) {
        return ResultUtils.success(privateMessageService.sendMessage(vo));
    }
}
```

### sendMessage-Impl

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class PrivateMessageServiceImpl extends ServiceImpl<PrivateMessageMapper, PrivateMessage> implements PrivateMessageService {

    private final FriendService friendService;
    private final IMClient imClient;
    private final SensitiveFilterUtil sensitiveFilterUtil;

    @Override
    public PrivateMessageVO sendMessage(PrivateMessageDTO dto) {
        UserSession session = SessionContext.getSession();
        Boolean isFriends = friendService.isFriend(session.getUserId(), dto.getRecvId());
        if (Boolean.FALSE.equals(isFriends)) {
            throw new GlobalException("您已不是对方好友，无法发送消息");
        }
        // 保存消息
        PrivateMessage msg = BeanUtils.copyProperties(dto, PrivateMessage.class);
        msg.setSendId(session.getUserId());
        msg.setStatus(MessageStatus.UNSEND.code());
        msg.setSendTime(new Date());
        this.save(msg);
        // 过滤内容中的敏感词
        if (MessageType.TEXT.code().equals(dto.getType())) {
            msg.setContent(sensitiveFilterUtil.filter(dto.getContent()));
        }
        // 推送消息
        PrivateMessageVO msgInfo = BeanUtils.copyProperties(msg, PrivateMessageVO.class);
        IMPrivateMessage<PrivateMessageVO> sendMessage = new IMPrivateMessage<>();
        sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
        sendMessage.setRecvId(msgInfo.getRecvId());
        sendMessage.setSendToSelf(true);
        sendMessage.setData(msgInfo);
        sendMessage.setSendResult(true);
        imClient.sendPrivateMessage(sendMessage);
        log.info("发送私聊消息，发送id:{},接收id:{}，内容:{}", session.getUserId(), dto.getRecvId(), dto.getContent());
        return msgInfo;
    }
}
```

```java
    @Cacheable(key = "#userId1+':'+#userId2")
    @Override
    public Boolean isFriend(Long userId1, Long userId2) {
        QueryWrapper<Friend> queryWrapper = new QueryWrapper<>();
        queryWrapper.lambda()
                .eq(Friend::getUserId, userId1)
                .eq(Friend::getFriendId, userId2);
        return this.count(queryWrapper) > 0;
    }
```

### 实现过程

获取当前session，判断session的userId和dto中的rcvId是否是好友，是的话保存信息到PrivateMessage，存储到数据库，过滤内容中的敏感词，并且调用imClient的sendPrivateMessage发送消息

## 9.2 client消息推送

### IMSender

```java
@Service
@RequiredArgsConstructor
public class IMSender {

    @Autowired
    private RedisMQTemplate redisMQTemplate;

    @Value("${spring.application.name}")
    private String appName;

    private final MessageListenerMulticaster listenerMulticaster;

    
    public<T> void sendPrivateMessage(IMPrivateMessage<T> message) {
        List<IMSendResult> results = new LinkedList<>();
        if(!Objects.isNull(message.getRecvId())){
            for (Integer terminal : message.getRecvTerminals()) {
                // 获取对方连接的channelId
                String key = String.join(":", IMRedisKey.IM_USER_SERVER_ID, message.getRecvId().toString(), terminal.toString());
                Integer serverId = (Integer)redisMQTemplate.opsForValue().get(key);
                // 如果对方在线，将数据存储至redis，等待拉取推送
                if (serverId != null) {
                    String sendKey = String.join(":", IMRedisKey.IM_MESSAGE_PRIVATE_QUEUE, serverId.toString());
                    IMRecvInfo recvInfo = new IMRecvInfo();
                    recvInfo.setCmd(IMCmdType.PRIVATE_MESSAGE.code());
                    recvInfo.setSendResult(message.getSendResult());
                    recvInfo.setServiceName(appName);
                    recvInfo.setSender(message.getSender());
                    recvInfo.setReceivers(Collections.singletonList(new IMUserInfo(message.getRecvId(), terminal)));
                    recvInfo.setData(message.getData());
                    redisMQTemplate.opsForList().rightPush(sendKey, recvInfo);
                } else {
                    IMSendResult result = new IMSendResult();
                    result.setSender(message.getSender());
                    result.setReceiver(new IMUserInfo(message.getRecvId(), terminal));
                    result.setCode(IMSendCode.NOT_ONLINE.code());
                    result.setData(message.getData());
                    results.add(result);
                }
            }
        }

        // 推送给自己的其他终端
        if(message.getSendToSelf()){
            for (Integer terminal : IMTerminalType.codes()) {
                if (message.getSender().getTerminal().equals(terminal)) {
                    continue;
                }
                // 获取终端连接的channelId
                String key = String.join(":", IMRedisKey.IM_USER_SERVER_ID, message.getSender().getId().toString(), terminal.toString());
                Integer serverId = (Integer)redisMQTemplate.opsForValue().get(key);
                // 如果终端在线，将数据存储至redis，等待拉取推送
                if (serverId != null) {
                    String sendKey = String.join(":", IMRedisKey.IM_MESSAGE_PRIVATE_QUEUE, serverId.toString());
                    IMRecvInfo recvInfo = new IMRecvInfo();
                    // 自己的消息不需要回推消息结果
                    recvInfo.setSendResult(false);
                    recvInfo.setCmd(IMCmdType.PRIVATE_MESSAGE.code());
                    recvInfo.setSender(message.getSender());
                    recvInfo.setReceivers(Collections.singletonList(new IMUserInfo(message.getSender().getId(), terminal)));
                    recvInfo.setData(message.getData());
                    redisMQTemplate.opsForList().rightPush(sendKey, recvInfo);
                }
            }
        }
        // 对离线用户回复消息状态
        if(message.getSendResult() && !results.isEmpty()){
            listenerMulticaster.multicast(IMListenerType.PRIVATE_MESSAGE, results);
        }

    }
}
```

### 实现过程

- List<IMSendResult> results接收未发送的消息集合
- 如果接收id不为空
  - 遍历每一个接收信息的终端
    - 获取对方连接的channelId
    - 如果对方在线，将数据存储至redis，等待拉取推送
    - 如果不在线，设置IMSendResult为NOT_ONLINE并存入results中
- 如果需要推送消息给自己的其他终端
  - 遍历其他终端
    - 获取连接的channelId
    - 如果终端在线，存储至redis，等待拉取推送
    - 如果不在线
- 对离线用户回复消息状态
  - 如果用户离线，对结果进行监听。

## 9.3 server消息推送

### RedisMQListener

```java
/**
 * redis 队列消费监听注解
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface RedisMQListener {

    /**
     * 队列，也是redis的key
     */
    String queue();

    /**
     * 一次性拉取的数据数量
     */
    int batchSize() default 1;

    /**
     * 拉取间隔周期，单位:ms
     */
    int period() default 100;
}

```

#### @Rentention

@Rentention:保存期的意思

- Rentention(RententionPolicy.SOURCE) ——注解只在源代码级别保留，编译的时候被忽略
  Rentention(RententionPolicy.CLASS)——注解将被编译器在类文件中记录，但是运行时不需要JVM保留
  Rentention(RententionPolicy.RUNTIME)——注解将被编译器记录在类文件，在运行保留JVM，因此可以反读
- 可以按照三个级别进行区分：
  - 编译的时候
  - class文件是否存在
  - JVM是否忽略

#### @Target

@Target:注解作用的目标

- @Target(ElementType.TYPE) ——接口，类，枚举，注解
- @Target(ElementType.FIELD)——字段，枚举常量
- @Target(ElementType.METHOD)——方法
- @Target(ElementType.PARAMETER)——方法参数
- @Target(ElementType.CONSTRUCTOR)——构造函数
- @Target(ElementType.LOCAL_VARIABLE)——局部变量
- @Target(ElementType.ANNOTATION_TYPE)——注解
- @Target(ElementType.PACKAGE)——包

#### @Document:

注解被包含在javadoc中

#### @Inherited:

说明子类可以继承父类中的该注解

### RedisMQConsumer

```java
/**
 * redis 队列消费者抽象类
 */
public abstract class RedisMQConsumer<T> {

     /**
      * 消费消息回调(单条)
      */
     public void onMessage(T data){}

     /**
      * 消费消息回调(批量)
      */
     public void onMessage(List<T> datas){}

     /**
      * 生成redis队列完整key
      */
     public String generateKey(){
          // 默认队列名就是redis的key
          RedisMQListener annotation = this.getClass().getAnnotation(RedisMQListener.class);
          return annotation.queue();
     }

     /**
      * 队列是否就绪，返回true才会开始消费
      */
     public Boolean isReady(){
          return true;
     }
}
```

### RedisMQPullTask

```java
/**
 * reids 队列拉取定时任务
 */
@Slf4j
@Component
public class RedisMQPullTask implements CommandLineRunner {

    private static final ScheduledThreadPoolExecutor EXECUTOR = ThreadPoolExecutorFactory.getThreadPoolExecutor();

    @Autowired(required = false)
    private List<RedisMQConsumer> consumers = Collections.emptyList();

    @Autowired
    private RedisMQTemplate redisMQTemplate;

    @Override
    public void run(String... args) {
        consumers.forEach((consumer -> {
            // 注解参数
            RedisMQListener annotation = consumer.getClass().getAnnotation(RedisMQListener.class);
            String queue = annotation.queue();
            int batchSize = annotation.batchSize();
            int period = annotation.period();
            // 获取泛型类型
            Type superClass = consumer.getClass().getGenericSuperclass();
            Type type = ((ParameterizedType)superClass).getActualTypeArguments()[0];
            EXECUTOR.execute(new Runnable() {
                @Override
                public void run() {
                    List<Object> datas = new LinkedList<>();
                    try {
                        if(redisMQTemplate.isClose()){
                            // 如果redis未初始化或已断开，3s后再重新尝试消费
                            EXECUTOR.schedule(this, 3, TimeUnit.SECONDS);
                            return;
                        }
                        if (consumer.isReady()) {
                            String key = consumer.generateKey();
                            // 拉取一个批次的数据
                            List<Object> objects = pullBatch(key, batchSize);
                            for (Object obj : objects) {
                                if (obj instanceof JSONObject) {
                                    JSONObject jsonObject = (JSONObject)obj;
                                    Object data = jsonObject.toJavaObject(type);
                                    consumer.onMessage(data);
                                    datas.add(data);
                                }
                            }
                            if (!datas.isEmpty()) {
                                consumer.onMessage(datas);
                            }
                        }
                    } catch (Exception e) {
                        log.error("数据消费异常,队列:{}", queue, e);
                        return;
                    }
                    // 继续消费数据
                    if (!EXECUTOR.isShutdown()) {
                        if (datas.size() < batchSize) {
                            // 数据已经消费完，等待下一个周期继续拉取
                            EXECUTOR.schedule(this, period, TimeUnit.MILLISECONDS);
                        } else {
                            // 数据没有消费完，直接开启下一个消费周期
                            EXECUTOR.execute(this);
                        }
                    }
                }
            });
        }));
    }

    private List<Object> pullBatch(String key, Integer batchSize) {
        List<Object> objects = new LinkedList<>();
        if (redisMQTemplate.isSupportBatchPull()) {
            // 版本大于6.2，支持批量拉取
            objects = redisMQTemplate.opsForList().leftPop(key, batchSize);
        } else {
            // 版本小于6.2，只能逐条拉取
            Object obj = redisMQTemplate.opsForList().leftPop(key);
            while (!Objects.isNull(obj) && objects.size() < batchSize) {
                objects.add(obj);
                obj = redisMQTemplate.opsForList().leftPop(key);
            }
            if (!Objects.isNull(obj)){
                objects.add(obj);
            }
        }
        return objects;
    }

    @PreDestroy
    public void destory() {
        log.info("消费线程停止...");
        ThreadPoolExecutorFactory.shutDown();
    }
}
```

#### CommandLineRunner

### AbstractPullMessageTask

```java
@Slf4j
public abstract class AbstractPullMessageTask<T> extends RedisMQConsumer<T> {

    @Autowired
    private IMServerGroup serverGroup;

    @Override
    public String generateKey() {
        return String.join(":",  super.generateKey(), IMServerGroup.serverId + "");
    }

    @Override
    public Boolean isReady() {
        return serverGroup.isReady();
    }
}

```

### PullPrivateMessageTask

```java
@Slf4j
@Component
@RequiredArgsConstructor
@RedisMQListener(queue = IMRedisKey.IM_MESSAGE_PRIVATE_QUEUE, batchSize = 10)
public class PullPrivateMessageTask extends AbstractPullMessageTask<IMRecvInfo> {

    @Override
    public void onMessage(IMRecvInfo recvInfo) {
        AbstractMessageProcessor processor = ProcessorFactory.createProcessor(IMCmdType.PRIVATE_MESSAGE);
        processor.process(recvInfo);
    }

}

```

### ProcessorFactory

```java
public class ProcessorFactory {

    public static AbstractMessageProcessor createProcessor(IMCmdType cmd) {
        AbstractMessageProcessor processor = null;
        switch (cmd) {
            case LOGIN:
                processor = SpringContextHolder.getApplicationContext().getBean(LoginProcessor.class);
                break;
            case HEART_BEAT:
                processor = SpringContextHolder.getApplicationContext().getBean(HeartbeatProcessor.class);
                break;
            case PRIVATE_MESSAGE:
                processor = SpringContextHolder.getApplicationContext().getBean(PrivateMessageProcessor.class);
                break;
            case GROUP_MESSAGE:
                processor = SpringContextHolder.getApplicationContext().getBean(GroupMessageProcessor.class);
                break;
            case SYSTEM_MESSAGE:
                processor = SpringContextHolder.getApplicationContext().getBean(SystemMessageProcessor.class);
                break;
            default:
                break;
        }
        return processor;
    }

}
```

#### SpringContextHolder

[使用SpringContextHolder获取 Spring 容器中的 Bean-CSDN博客](https://blog.csdn.net/Jactil/article/details/136541926?ops_request_misc=%7B%22request%5Fid%22%3A%22172455415516800207052529%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172455415516800207052529&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-136541926-null-null.142^v100^control&utm_term=springContextHolder&spm=1018.2226.3001.4187)

SpringContextHolder 是一个用于在任何地方获取 [Spring](https://so.csdn.net/so/search?q=Spring&spm=1001.2101.3001.7020) 容器中的 Bean 的工具类。它通常用于在非 Spring 管理的类（如普通的 Java 类、工具类等）中获取 Spring 容器中的 Bean，以方便进行依赖注入和调用 Bean 的方法。

### AbstractMessageProcessor

```java
public abstract class AbstractMessageProcessor<T> {

    public void process(ChannelHandlerContext ctx, T data) {
    }

    public void process(T data) {
    }

    public T transForm(Object o) {
        return (T) o;
    }

}
```

#### ChannelHandlerContext

**保存 Channel 相关的所有上下文信息，**同时关联一个 ChannelHandler 对象。即 ChannelHandlerContext 中包含一个具体的事件处理器 ChannelHandler，同时 ChannelHandlerContext 中也绑定了对应的 pipeline 和 Channel 的信息，方便对 ChannelHandler 进行调用。

### PrivateMessageProcessor

```java

@Slf4j
@Component
@RequiredArgsConstructor
public class PrivateMessageProcessor extends AbstractMessageProcessor<IMRecvInfo> {

    private final RedisTemplate<String, Object> redisTemplate;

    @Override
    public void process(IMRecvInfo recvInfo) {
        IMUserInfo sender = recvInfo.getSender();
        IMUserInfo receiver = recvInfo.getReceivers().get(0);
        log.info("接收到私聊消息，发送者:{},接收者:{}，内容:{}", sender.getId(), receiver.getId(), recvInfo.getData());
        try {
            ChannelHandlerContext channelCtx = UserChannelCtxMap.getChannelCtx(receiver.getId(), receiver.getTerminal());
            if (!Objects.isNull(channelCtx)) {
                // 推送消息到用户
                IMSendInfo<Object> sendInfo = new IMSendInfo<>();
                sendInfo.setCmd(IMCmdType.PRIVATE_MESSAGE.code());
                sendInfo.setData(recvInfo.getData());
                channelCtx.channel().writeAndFlush(sendInfo);
                // 消息发送成功确认
                sendResult(recvInfo, IMSendCode.SUCCESS);
            } else {
                // 消息推送失败确认
                sendResult(recvInfo, IMSendCode.NOT_FIND_CHANNEL);
                log.error("未找到channel，发送者:{},接收者:{}，内容:{}", sender.getId(), receiver.getId(), recvInfo.getData());
            }
        } catch (Exception e) {
            // 消息推送失败确认
            sendResult(recvInfo, IMSendCode.UNKONW_ERROR);
            log.error("发送异常，发送者:{},接收者:{}，内容:{}", sender.getId(), receiver.getId(), recvInfo.getData(), e);
        }

    }

    private void sendResult(IMRecvInfo recvInfo, IMSendCode sendCode) {
        if (recvInfo.getSendResult()) {
            IMSendResult<Object> result = new IMSendResult<>();
            result.setSender(recvInfo.getSender());
            result.setReceiver(recvInfo.getReceivers().get(0));
            result.setCode(sendCode.code());
            result.setData(recvInfo.getData());
            // 推送到结果队列
            String key = StrUtil.join(":",IMRedisKey.IM_RESULT_PRIVATE_QUEUE,recvInfo.getServiceName());
            redisTemplate.opsForList().rightPush(key, result);
        }
    }
}
```

### 实现过程

- 循环从redis中拉取消息
  - RedisMQListener注解 redis消费监听注解 包括 queue(redis的key) batchSize(一次性拉取的数据数量) period(拉取间隔周期)
  - RedisMQConsumer抽象类 redis队列消费者抽象类 方法包括消费消息单条、批量回调、生成redis队列完整key、队列是否就绪
  - RedisMQPullTask 从redis中不断拉取数据
- 将消息经由PrivateMessageProcoessor处理
  - AbstractPullMessageTask继承RedisMQConsumer抽象类 
  - PullPrivateMessageTask继承AbstractPullMessageTask抽象类 并在类上加RedisMQListener注解
    - 在类中调用ProcessorFactory的createProcessor方法创建私聊消息的类 PrivateMessageProcessor，该类继承了AbstractMessageProcessor这一抽象类，实现了里面的process方法
      - 如果获取到通道，推送消息给用户、并将消息成功结果推送到结果队列
      - 否则将消息推送失败结果发送到结果队列
  
- RedisMQPullTask

- 好的，让我们分块讲解这段代码。这段代码定义了一个名为`RedisMQPullTask`的类，它实现`CommandLineRunner`接口，以便在Spring Boot应用启动时执行一些初始化任务。这个类的主要职责是从Redis队列中拉取消息，并将消息传递给相应的消费者进行处理。

  ### 1. 类定义和依赖注入

  ```java
  @Slf4j
  @Component
  public class RedisMQPullTask implements CommandLineRunner {
  
      private static final ScheduledThreadPoolExecutor EXECUTOR = ThreadPoolExecutorFactory.getThreadPoolExecutor();
  
      @Autowired(required = false)
      private List<RedisMQConsumer> consumers = Collections.emptyList();
  
      @Autowired
      private RedisMQTemplate redisMQTemplate;
  
      ...
  }
  ```

  - **类定义**：`@Slf4j`注解引入了Lombok插件，用于简化日志记录。
  - **组件注解**：`@Component`标记此类为Spring容器管理的组件。
  - **线程池**：`EXECUTOR`是一个静态成员变量，用于执行异步任务。
  - **依赖注入**：通过`@Autowired`注解注入`List<RedisMQConsumer>`和`RedisMQTemplate`，前者是消息消费者列表，后者用于操作Redis队列。

  ### 2. 实现`CommandLineRunner`接口

  ```java
  @Override
  public void run(String... args) {
      ...
  }
  ```

  - **run方法**：这是`CommandLineRunner`接口要求实现的方法，它将在Spring Boot应用启动完成后执行。

  ### 3. 初始化消费者并启动消费任务

  ```java
  consumers.forEach((consumer -> {
      // 注解参数
      RedisMQListener annotation = consumer.getClass().getAnnotation(RedisMQListener.class);
      String queue = annotation.queue();
      int batchSize = annotation.period();
      int period = annotation.period();
      // 获取泛型类型
      Type superClass = consumer.getClass().getGenericSuperclass();
      Type type = ((ParameterizedType)superClass).getActualTypeArguments()[0];
      EXECUTOR.execute(new Runnable() {
          @Override
          public void run() {
              ...
          }
      });
  }));
  ```

  - **迭代消费者**：遍历注入的消费者列表。
  - **获取注解信息**：从消费者的类中提取`RedisMQListener`注解的信息，包括队列名称、批处理大小和消费间隔。
  - **获取泛型类型**：获取消费者类的泛型参数类型，用于后续的消息转换。
  - **启动消费任务**：为每个消费者启动一个新的线程任务。

  ### 4. 消费消息逻辑

  ```java
  List<Object> datas = new LinkedList<>();
  try {
      if(redisMQTemplate.isClose()){
          // 如果redis未初始化或已断开，3s后再重新尝试消费
          EXECUTOR.schedule(this, 3, TimeUnit.SECONDS);
          return;
      }
      if (consumer.isReady()) {
          String key = consumer.generateKey();
          // 拉取一个批次的数据
          List<Object> objects = pullBatch(key, batchSize);
          for (Object obj : objects) {
              if (obj instanceof JSONObject) {
                  JSONObject jsonObject = (JSONObject)obj;
                  Object data = jsonObject.toJavaObject(type);
                  consumer.onMessage(data);
                  datas.add(data);
              }
          }
          if (!datas.isEmpty()) {
              consumer.onMessage(datas);
          }
      }
  } catch (Exception e) {
      log.error("数据消费异常,队列:{}", queue, e);
      return;
  }
  // 继续消费数据
  if (!EXECUTOR.isShutdown()) {
      if (datas.size() < batchSize) {
          // 数据已经消费完，等待下一个周期继续拉取
          EXECUTOR.schedule(this, period, TimeUnit.MILLISECONDS);
      } else {
          // 数据没有消费完，直接开启下一个消费周期
          EXECUTOR.execute(this);
      }
  }
  ```

  - **检查Redis连接状态**：如果Redis连接有问题，则延迟重试。
  - **检查消费者是否就绪**：调用`isReady()`方法确认消费者是否准备好消费消息。
  - **拉取消息**：调用`pullBatch()`方法从Redis队列中拉取一批消息。
  - **处理消息**：遍历拉取到的消息，转换为相应类型，并调用`onMessage()`方法进行处理。
  - **批量处理**：如果存在消息，则调用批量处理的`onMessage()`方法。
  - **异常处理**：记录消费过程中可能出现的异常。
  - **调度下一次消费**：根据情况决定是立即消费还是等待一段时间后再消费。

  ### 5. 批量拉取消息逻辑

  ```java
  private List<Object> pullBatch(String key, Integer batchSize) {
      List<Object> objects = new LinkedList<>();
      if (redisMQTemplate.isSupportBatchPull()) {
          // 版本大于6.2，支持批量拉取
          objects = redisMQTemplate.opsForList().leftPop(key, batchSize);
      } else {
          // 版本小于6.2，只能逐条拉取
          Object obj = redisMQTemplate.opsForList().leftPop(key);
          while (!Objects.isNull(obj) && objects.size() < batchSize) {
              objects.add(obj);
              obj = redisMQTemplate.opsForList().leftPop(key);
          }
          if (!Objects.isNull(obj)){
              objects.add(obj);
          }
      }
      return objects;
  }
  ```

  - **检查Redis版本**：判断是否支持批量拉取。
  - **批量拉取**：如果支持批量拉取，则一次性拉取多条消息。
  - **逐条拉取**：如果不支持批量拉取，则逐条拉取消息，直到达到批量大小或队列为空。

  ### 6. 销毁线程池

  ```java
  @PreDestroy
  public void destory() {
      log.info("消费线程停止...");
      ThreadPoolExecutorFactory.shutDown();
  }
  ```

  - **销毁方法**：当Spring容器销毁此Bean时，会调用此方法来关闭线程池。

  ### 总结

  这段代码主要实现了从Redis队列中拉取消息，并通过相应的消费者类处理这些消息。它使用了线程池来异步处理消息，同时也考虑了异常处理和线程池的销毁。通过合理的注释和结构化的代码，使得每个部分的功能都非常清晰。

## 9.4 server消息接收

### IMChannelHandler-handlerAdded

用户连接事件，这里只打印日志

```java
@Slf4j
public class IMChannelHandler extends SimpleChannelInboundHandler<IMSendInfo> {
     /**
     * 读取到消息后进行处理
     *
     * @param ctx      channel上下文
     * @param sendInfo 发送消息
     */
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, IMSendInfo sendInfo) {
        // 创建处理器进行处理
        AbstractMessageProcessor processor = ProcessorFactory.createProcessor(IMCmdType.fromCode(sendInfo.getCmd()));
        processor.process(ctx, processor.transForm(sendInfo.getData()));
    }

    /**
     * 出现异常的处理 打印报错日志
     *
     * @param ctx   channel上下文
     * @param cause 异常信息
     */
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) {
        log.error(cause.getMessage());
        //关闭上下文
        //ctx.close();
    }
    
    /**
     * 监控浏览器上线
     *
     * @param ctx channel上下文
     */
    @Override
    public void handlerAdded(ChannelHandlerContext ctx) {
        log.info(ctx.channel().id().asLongText() + "连接");
    }
}
```

### LoginProcessor-process

进行登陆处理，将用户id和serverId绑定

```java
@Slf4j
@Component
@RequiredArgsConstructor
public class LoginProcessor extends AbstractMessageProcessor<IMLoginInfo> {

    private final RedisTemplate<String, Object> redisTemplate;

    @Value("${jwt.accessToken.secret}")
    private String accessTokenSecret;
    
    
    @Override
    public synchronized void process(ChannelHandlerContext ctx, IMLoginInfo loginInfo) {
        if (!JwtUtil.checkSign(loginInfo.getAccessToken(), accessTokenSecret)) {
            ctx.channel().close();
            log.warn("用户token校验不通过，强制下线,token:{}", loginInfo.getAccessToken());
        }
        String strInfo = JwtUtil.getInfo(loginInfo.getAccessToken());
        IMSessionInfo sessionInfo = JSON.parseObject(strInfo, IMSessionInfo.class);
        Long userId = sessionInfo.getUserId();
        Integer terminal = sessionInfo.getTerminal();
        log.info("用户登录，userId:{}", userId);
        ChannelHandlerContext context = UserChannelCtxMap.getChannelCtx(userId, terminal);
        if (context != null && !ctx.channel().id().equals(context.channel().id())) {
            // 不允许多地登录,强制下线
            IMSendInfo<Object> sendInfo = new IMSendInfo<>();
            sendInfo.setCmd(IMCmdType.FORCE_LOGUT.code());
            sendInfo.setData("您已在其他地方登陆，将被强制下线");
            context.channel().writeAndFlush(sendInfo);
            log.info("异地登录，强制下线,userId:{}", userId);
        }
        // 绑定用户和channel
        UserChannelCtxMap.addChannelCtx(userId, terminal, ctx);
        // 设置用户id属性
        AttributeKey<Long> userIdAttr = AttributeKey.valueOf(ChannelAttrKey.USER_ID);
        ctx.channel().attr(userIdAttr).set(userId);
        // 设置用户终端类型
        AttributeKey<Integer> terminalAttr = AttributeKey.valueOf(ChannelAttrKey.TERMINAL_TYPE);
        ctx.channel().attr(terminalAttr).set(terminal);
        // 初始化心跳次数
        AttributeKey<Long> heartBeatAttr = AttributeKey.valueOf("HEARTBEAt_TIMES");
        ctx.channel().attr(heartBeatAttr).set(0L);
        // 在redis上记录每个user的channelId，15秒没有心跳，则自动过期
        String key = String.join(":", IMRedisKey.IM_USER_SERVER_ID, userId.toString(), terminal.toString());
        redisTemplate.opsForValue().set(key, IMServerGroup.serverId, IMConstant.ONLINE_TIMEOUT_SECOND, TimeUnit.SECONDS);
        // 响应ws
        IMSendInfo<Object> sendInfo = new IMSendInfo<>();
        sendInfo.setCmd(IMCmdType.LOGIN.code());
        ctx.channel().writeAndFlush(sendInfo);
    }
    
    @Override
    public IMLoginInfo transForm(Object o) {
        HashMap map = (HashMap) o;
        return BeanUtil.fillBeanWithMap(map, new IMLoginInfo(), false);
    }
}    
```

#### RedisTemplate

[RedisTemplate 的基本使用-CSDN博客](https://blog.csdn.net/weixin_42001592/article/details/124054738?ops_request_misc=%7B%22request%5Fid%22%3A%22172441767016800185830334%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172441767016800185830334&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-124054738-null-null.142^v100^control&utm_term=redisTemplate&spm=1018.2226.3001.4187)

API	                                                      返回值类型	                     说明
redisTemplate.opsForValue()	 ValueOperations	            操作 String 类型数据
redisTemplate.opsForHash()	  HashOperations	             操作 Hash 类型数据
redisTemplate.opsForList()	     ListOperations		       操作 List 类型数据
redisTemplate.opsForSet()	      SetOperations			操作 Set 类型数据
redisTemplate.opsForZSet()	   ZSetOperations		      操作 SortedSet 类型数据
redisTemplate		                                                                           通用的命令

####  synchronized

[【多线程与高并发】- synchronized锁的认知_synchronized能否锁住多台-CSDN博客](https://blog.csdn.net/qq_43843951/article/details/129107202?ops_request_misc=%7B%22request%5Fid%22%3A%22172441795116800222850602%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172441795116800222850602&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-129107202-null-null.142^v100^control&utm_term= synchronized&spm=1018.2226.3001.4187)

synchronized 是 Java 语言的一个关键字，它允许多个线程同时访问共享的资源，以避免多线程编程中的竞争条件和死锁问题。synchronized可以用来给对象或者方法进行加锁，当对某个对象或者代码块加锁时，同时就只能有一个线程去执行。这种就是互斥关系，被加锁的区域称为临界区，而里面的资源就是临界资源。当一个线程进入临界区的时候，另一个线程就必须等待。

#### AttributeKey

[Netty中的AttributeMap属性(AttributeKey、AttributeMap、Attribute)_netty attributekey-CSDN博客](https://blog.csdn.net/weixin_42030357/article/details/110006946?ops_request_misc=%7B%22request%5Fid%22%3A%22172456567216800184186815%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=172456567216800184186815&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-110006946-null-null.142^v100^control&utm_term=AttributeKey&spm=1018.2226.3001.4187)

#### IMServerGroup

```java
@Slf4j
@Component
@AllArgsConstructor
public class IMServerGroup implements CommandLineRunner {

    public static volatile long serverId = 0;

    private final  RedisTemplate<String, Object> redisTemplate;

    private final List<IMServer> imServers;

    /***
     * 判断服务器是否就绪
     *
     * @return
     **/
    public boolean isReady() {
        for (IMServer imServer : imServers) {
            if (!imServer.isReady()) {
                return false;
            }
        }
        return true;
    }

    @Override
    public void run(String... args) {
        // 初始化SERVER_ID
        String key = IMRedisKey.IM_MAX_SERVER_ID;
        serverId = redisTemplate.opsForValue().increment(key, 1);
        // 启动服务
        for (IMServer imServer : imServers) {
            imServer.start();
        }
    }

    @PreDestroy
    public void destroy() {
        // 停止服务
        for (IMServer imServer : imServers) {
            imServer.stop();
        }
    }
}
```

### HeartbeatProcessor-process  

心跳处理，绑定关系续命

```java
@Slf4j
@Component
@RequiredArgsConstructor
public class HeartbeatProcessor extends AbstractMessageProcessor<IMHeartbeatInfo> {

    private final RedisTemplate<String, Object> redisTemplate;

    @Override
    public void process(ChannelHandlerContext ctx, IMHeartbeatInfo beatInfo) {
        // 响应ws
        IMSendInfo sendInfo = new IMSendInfo();
        sendInfo.setCmd(IMCmdType.HEART_BEAT.code());
        ctx.channel().writeAndFlush(sendInfo);
        ;
        // 设置属性
        AttributeKey<Long> heartBeatAttr = AttributeKey.valueOf(ChannelAttrKey.HEARTBEAT_TIMES);
        Long heartbeatTimes = ctx.channel().attr(heartBeatAttr).get();
        ctx.channel().attr(heartBeatAttr).set(++heartbeatTimes);
        if (heartbeatTimes % 10 == 0) {
            // 每心跳10次，用户在线状态续一次命
            AttributeKey<Long> userIdAttr = AttributeKey.valueOf(ChannelAttrKey.USER_ID);
            Long userId = ctx.channel().attr(userIdAttr).get();
            AttributeKey<Integer> terminalAttr = AttributeKey.valueOf(ChannelAttrKey.TERMINAL_TYPE);
            Integer terminal = ctx.channel().attr(terminalAttr).get();
            String key = String.join(":", IMRedisKey.IM_USER_SERVER_ID, userId.toString(), terminal.toString());
            redisTemplate.expire(key, IMConstant.ONLINE_TIMEOUT_SECOND, TimeUnit.SECONDS);
        }
        AttributeKey<Long> userIdAttr = AttributeKey.valueOf(ChannelAttrKey.USER_ID);
        Long userId = ctx.channel().attr(userIdAttr).get();
        log.info("心跳,userId:{},{}",userId,ctx.channel().id().asLongText());
    }

    @Override
    public IMHeartbeatInfo transForm(Object o) {
        HashMap map = (HashMap) o;
        return BeanUtil.fillBeanWithMap(map, new IMHeartbeatInfo(), false);
    }
}
```

###   IMChannelHandler-userEventTriggered  

心跳超时事件，关闭channel

```java
    @Override
    public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
        if (evt instanceof IdleStateEvent) {
            IdleState state = ((IdleStateEvent) evt).state();
            if (state == IdleState.READER_IDLE) {
                // 在规定时间内没有收到客户端的上行数据, 主动断开连接
                AttributeKey<Long> attr = AttributeKey.valueOf("USER_ID");
                Long userId = ctx.channel().attr(attr).get();
                AttributeKey<Integer> terminalAttr = AttributeKey.valueOf(ChannelAttrKey.TERMINAL_TYPE);
                Integer terminal = ctx.channel().attr(terminalAttr).get();
                log.info("心跳超时，即将断开连接,用户id:{},终端类型:{} ", userId, terminal);
                ctx.channel().close();
            }
        } else {
            super.userEventTriggered(ctx, evt);
        }

    }
```

### IMChannelHandler-handlerRemoved  

连接断开事件，移除channel，解除绑定

```java
    @Override
    public void handlerRemoved(ChannelHandlerContext ctx) {
        AttributeKey<Long> userIdAttr = AttributeKey.valueOf(ChannelAttrKey.USER_ID);
        Long userId = ctx.channel().attr(userIdAttr).get();
        AttributeKey<Integer> terminalAttr = AttributeKey.valueOf(ChannelAttrKey.TERMINAL_TYPE);
        Integer terminal = ctx.channel().attr(terminalAttr).get();
        ChannelHandlerContext context = UserChannelCtxMap.getChannelCtx(userId, terminal);
        // 判断一下，避免异地登录导致的误删
        if (context != null && ctx.channel().id().equals(context.channel().id())) {
            // 移除channel
            UserChannelCtxMap.removeChannelCtx(userId, terminal);
            // 用户下线
            RedisTemplate<String, Object> redisTemplate = SpringContextHolder.getBean(RedisTemplate.class);
            String key = String.join(":", IMRedisKey.IM_USER_SERVER_ID, userId.toString(), terminal.toString());
            redisTemplate.delete(key);
            log.info("断开连接,userId:{},终端类型:{},{}", userId, terminal, ctx.channel().id().asLongText());
        }
    }
```

### 实现过程

- 通过IMChannelHandler-handlerAdded，进行用户连接事件的日志打印
- 通过LoginProcessor-process，进行登陆处理，将用户id和serverid绑定
  - 校验用户token，判断并处理是否有多端同时登陆的情况
  - 绑定用户和channel，设置用户id属性、终端类型、初始化心跳次数、在redis上记录心跳
  - 响应ws，发送sendInfo
- 通过HeartbeatProcessor-process，进行心跳处理，绑定关系续命
  -   响应ws
  -  发送心跳，进行续命
- 通过  IMChannelHandler-userEventTriggered  ，处理心跳超时事件，关闭channel
- 通过 IMChannelHandler-handlerRemoved  ，连接断开事件，移除channel，解除绑定

## 9.5 server推送监听结果

### 实现过程

- 通过 PrivateMessageProcessor  sendResult  向redis回推私聊消息推送结果

## 9.6 client监听发送结果

### AbstractMessageResultlTask

```java
@Slf4j
public abstract class AbstractMessageResultTask<T> extends RedisMQConsumer<T> {

    @Value("${spring.application.name}")
    private String appName;

    @Override
    public String generateKey() {
        return StrUtil.join(":", super.generateKey(), appName);
    }
}
```

### PrivateMessageResultTask

```java
@Component
@RequiredArgsConstructor
@RedisMQListener(queue = IMRedisKey.IM_RESULT_PRIVATE_QUEUE, batchSize = 100)
public class PrivateMessageResultResultTask extends AbstractMessageResultTask<IMSendResult> {

    private final MessageListenerMulticaster listenerMulticaster;

    @Override
    public void onMessage(List<IMSendResult> results) {
        listenerMulticaster.multicast(IMListenerType.PRIVATE_MESSAGE, results);
    }

}
```

### IMListener

```java
@Target({ElementType.TYPE,ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Component
public @interface IMListener {

    IMListenerType type();

}
```

### MessageListenerMulticaster

```java
@Component
public class MessageListenerMulticaster {

    @Autowired(required = false)
    private List<MessageListener>  messageListeners  = Collections.emptyList();

    public  void multicast(IMListenerType listenerType, List<IMSendResult> results){
        if(CollUtil.isEmpty(results)){
            return;
        }
        for(MessageListener listener:messageListeners){
            IMListener annotation = listener.getClass().getAnnotation(IMListener.class);
            if(annotation!=null && (annotation.type().equals(IMListenerType.ALL) || annotation.type().equals(listenerType))){
                results.forEach(result->{
                    // 将data转回对象类型
                    if(result.getData() instanceof JSONObject){
                        Type superClass = listener.getClass().getGenericInterfaces()[0];
                        Type type = ((ParameterizedType) superClass).getActualTypeArguments()[0];
                        JSONObject data = (JSONO bject)result.getData();
                        result.setData(data.toJavaObject(type));
                    }
                });
                // 回调到调用方处理
                listener.process(results);
            }
        }
    }
}
```

### 实现过程

- 通过PrivateMessageResultTask中的pullMessage方法，从redis中定时拉取群聊消息推送结果
- 通过MessageListenerMulticaster中的multicast方法，向业务层的监听器广播消息推送结果

## 9.7 platform监听发送结果

### PrivateMessageListener

```java
@Slf4j
@IMListener(type = IMListenerType.PRIVATE_MESSAGE)
public class PrivateMessageListener implements MessageListener<PrivateMessageVO> {
    @Lazy
    @Autowired
    private PrivateMessageService privateMessageService;
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

- 对方在线，输出日志
- 所有消息改成已发送过状态

## 9.8 PrivateMessageController

```java
@Tag(name = "私聊消息")
@RestController
@RequestMapping("/message/private")
@RequiredArgsConstructor
public class PrivateMessageController {

    private final PrivateMessageService privateMessageService;}
```

## 9.9 PrivateMessageControlleServiceImpl

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class PrivateMessageServiceImpl extends ServiceImpl<PrivateMessageMapper, PrivateMessage>
    implements PrivateMessageService {
    private final FriendService friendService;
    private final IMClient imClient;
    private final SensitiveFilterUtil sensitiveFilterUtil;}
```

## 9.10 发送消息

```java
    @PostMapping("/send")
    @Operation(summary = "发送消息", description = "发送私聊消息")
    public Result<PrivateMessageVO> sendMessage(@Valid @RequestBody PrivateMessageDTO vo) {
        return ResultUtils.success(privateMessageService.sendMessage(vo));
    }
```

```java
	@Override
    public PrivateMessageVO sendMessage(PrivateMessageDTO dto) {
        UserSession session = SessionContext.getSession();
        Boolean isFriends = friendService.isFriend(session.getUserId(), dto.getRecvId());
        if (Boolean.FALSE.equals(isFriends)) {
            throw new GlobalException("您已不是对方好友，无法发送消息");
        }
        // 保存消息
        PrivateMessage msg = BeanUtils.copyProperties(dto, PrivateMessage.class);
        msg.setSendId(session.getUserId());
        msg.setStatus(MessageStatus.UNSEND.code());
        msg.setSendTime(new Date());
        this.save(msg);
        // 过滤内容中的敏感词
        if (MessageType.TEXT.code().equals(dto.getType())) {
            msg.setContent(sensitiveFilterUtil.filter(dto.getContent()));
        }
        // 推送消息
        PrivateMessageVO msgInfo = BeanUtils.copyProperties(msg, PrivateMessageVO.class);
        IMPrivateMessage<PrivateMessageVO> sendMessage = new IMPrivateMessage<>();
        sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
        sendMessage.setRecvId(msgInfo.getRecvId());
        sendMessage.setSendToSelf(true);
        sendMessage.setData(msgInfo);
        sendMessage.setSendResult(true);
        imClient.sendPrivateMessage(sendMessage);
        log.info("发送私聊消息，发送id:{},接收id:{}，内容:{}", session.getUserId(), dto.getRecvId(), dto.getContent());
        return msgInfo;
    }
```

## 9.11 撤回消息

```java
    @DeleteMapping("/recall/{id}")
    @Operation(summary = "撤回消息", description = "撤回私聊消息")
    public Result<Long> recallMessage(@NotNull(message = "消息id不能为空") @PathVariable Long id) {
        privateMessageService.recallMessage(id);
        return ResultUtils.success();
    }
```

```java
    @Override
    public void recallMessage(Long id) {
        UserSession session = SessionContext.getSession();
        PrivateMessage msg = this.getById(id);
        if (Objects.isNull(msg)) {
            throw new GlobalException("消息不存在");
        }
        if (!msg.getSendId().equals(session.getUserId())) {
            throw new GlobalException("这条消息不是由您发送,无法撤回");
        }
        if (System.currentTimeMillis() - msg.getSendTime().getTime() > IMConstant.ALLOW_RECALL_SECOND * 1000) {
            throw new GlobalException("消息已发送超过5分钟，无法撤回");
        }
        // 修改消息状态
        msg.setStatus(MessageStatus.RECALL.code());
        this.updateById(msg);
        // 推送消息
        PrivateMessageVO msgInfo = BeanUtils.copyProperties(msg, PrivateMessageVO.class);
        msgInfo.setType(MessageType.RECALL.code());
        msgInfo.setSendTime(new Date());
        msgInfo.setContent("对方撤回了一条消息");

        IMPrivateMessage<PrivateMessageVO> sendMessage = new IMPrivateMessage<>();
        sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
        sendMessage.setRecvId(msgInfo.getRecvId());
        sendMessage.setSendToSelf(false);
        sendMessage.setData(msgInfo);
        sendMessage.setSendResult(false);
        imClient.sendPrivateMessage(sendMessage);

        // 推给自己其他终端
        msgInfo.setContent("你撤回了一条消息");
        sendMessage.setSendToSelf(true);
        sendMessage.setRecvTerminals(Collections.emptyList());
        imClient.sendPrivateMessage(sendMessage);
        log.info("撤回私聊消息，发送id:{},接收id:{}，内容:{}", msg.getSendId(), msg.getRecvId(), msg.getContent());
    }

```

### 实现过程

消息存在、由自己发送、在撤回时间内：修改消息状态更新数据库、推送消息、发送给自己的其他终端，并且不需要回推消息结果。

## 9.12 拉取离线消息

拉取最近1个月的离线私聊消息，最多拉取1000条。离线消息被拉取后，消息状态修改为**已发送**状态。

```java
    @GetMapping("/pullOfflineMessage")
    @Operation(summary = "拉取离线消息", description = "拉取离线消息,消息将通过webscoket异步推送")
    public Result pullOfflineMessage(@RequestParam Long minId) {
        privateMessageService.pullOfflineMessage(minId);
        return ResultUtils.success();
    }
```

```java
	@Override
    public void pullOfflineMessage(Long minId) {
        UserSession session = SessionContext.getSession();
        if (!imClient.isOnline(session.getUserId())) {
            throw new GlobalException("网络连接失败，无法拉取离线消息");
        }
        // 查询用户好友列表
        List<Friend> friends = friendService.findFriendByUserId(session.getUserId());
        if (friends.isEmpty()) {
            // 关闭加载中标志
            this.sendLoadingMessage(false);
            return;
        }
        // 开启加载中标志
        this.sendLoadingMessage(true);
        List<Long> friendIds = friends.stream().map(Friend::getFriendId).collect(Collectors.toList());
        // 获取当前用户的消息
        LambdaQueryWrapper<PrivateMessage> queryWrapper = Wrappers.lambdaQuery();
        // 只能拉取最近3个月的消息,移动端只拉取一个月消息
        int months = session.getTerminal().equals(IMTerminalType.APP.code()) ? 1 : 3;
        Date minDate = DateUtils.addMonths(new Date(), -months);
        queryWrapper.gt(PrivateMessage::getId, minId).ge(PrivateMessage::getSendTime, minDate)
            .ne(PrivateMessage::getStatus, MessageStatus.RECALL.code()).and(wrap -> wrap.and(
                    wp -> wp.eq(PrivateMessage::getSendId, session.getUserId()).in(PrivateMessage::getRecvId, friendIds))
                .or(wp -> wp.eq(PrivateMessage::getRecvId, session.getUserId()).in(PrivateMessage::getSendId, friendIds)))
            .orderByAsc(PrivateMessage::getId);
        List<PrivateMessage> messages = this.list(queryWrapper);
        // 推送消息
        for (PrivateMessage m : messages) {
            PrivateMessageVO vo = BeanUtils.copyProperties(m, PrivateMessageVO.class);
            IMPrivateMessage<PrivateMessageVO> sendMessage = new IMPrivateMessage<>();
            sendMessage.setSender(new IMUserInfo(m.getSendId(), IMTerminalType.WEB.code()));
            sendMessage.setRecvId(session.getUserId());
            sendMessage.setRecvTerminals(List.of(session.getTerminal()));
            sendMessage.setSendToSelf(false);
            sendMessage.setData(vo);
            sendMessage.setSendResult(true);
            imClient.sendPrivateMessage(sendMessage);
        }
        // 关闭加载中标志
        this.sendLoadingMessage(false);
        log.info("拉取私聊消息，用户id:{},数量:{}", session.getUserId(), messages.size());
    }
```

## 9.13 消息已读

已读状态变更

```java
    @PutMapping("/readed")
    @Operation(summary = "消息已读", description = "将会话中接收的消息状态置为已读")
    public Result readedMessage(@RequestParam Long friendId) {
        privateMessageService.readedMessage(friendId);
        return ResultUtils.success();
    }
```

```java
	@Transactional(rollbackFor = Exception.class)
    @Override
    public void readedMessage(Long friendId) {
        UserSession session = SessionContext.getSession();
        // 推送消息给自己，清空会话列表上的已读数量
        PrivateMessageVO msgInfo = new PrivateMessageVO();
        msgInfo.setType(MessageType.READED.code());
        msgInfo.setSendId(session.getUserId());
        msgInfo.setRecvId(friendId);
        IMPrivateMessage<PrivateMessageVO> sendMessage = new IMPrivateMessage<>();
        sendMessage.setData(msgInfo);
        sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
        sendMessage.setSendToSelf(true);
        sendMessage.setSendResult(false);
        imClient.sendPrivateMessage(sendMessage);
        // 推送回执消息给对方，更新已读状态
        msgInfo = new PrivateMessageVO();
        msgInfo.setType(MessageType.RECEIPT.code());
        msgInfo.setSendId(session.getUserId());
        msgInfo.setRecvId(friendId);
        sendMessage = new IMPrivateMessage<>();
        sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
        sendMessage.setRecvId(friendId);
        sendMessage.setSendToSelf(false);
        sendMessage.setSendResult(false);
        sendMessage.setData(msgInfo);
        imClient.sendPrivateMessage(sendMessage);
        // 修改消息状态为已读
        LambdaUpdateWrapper<PrivateMessage> updateWrapper = Wrappers.lambdaUpdate();
        updateWrapper.eq(PrivateMessage::getSendId, friendId).eq(PrivateMessage::getRecvId, session.getUserId())
            .eq(PrivateMessage::getStatus, MessageStatus.SENDED.code())
            .set(PrivateMessage::getStatus, MessageStatus.READED.code());
        this.update(updateWrapper);
        log.info("消息已读，接收方id:{},发送方id:{}", session.getUserId(), friendId);
    }
```

## 9.14 获取最大已读消息的id

离线状态变更

```java
    @GetMapping("/maxReadedId")
    @Operation(summary = "获取最大已读消息的id", description = "获取某个会话中已读消息的最大id")
    public Result<Long> getMaxReadedId(@RequestParam Long friendId) {
        return ResultUtils.success(privateMessageService.getMaxReadedId(friendId));
    }

```

```java
@Override
public Long getMaxReadedId(Long friendId) {
    UserSession session = SessionContext.getSession();
    LambdaQueryWrapper<PrivateMessage> wrapper = Wrappers.lambdaQuery();
    wrapper.eq(PrivateMessage::getSendId, session.getUserId()).eq(PrivateMessage::getRecvId, friendId)
        .eq(PrivateMessage::getStatus, MessageStatus.READED.code()).orderByDesc(PrivateMessage::getId)
        .select(PrivateMessage::getId).last("limit 1");
    PrivateMessage message = this.getOne(wrapper);
    if (Objects.isNull(message)) {
        return -1L;
    }
    return message.getId();
}
```

## 9.15 查询聊天记录

```java
   @GetMapping("/history")
    @Operation(summary = "查询聊天记录", description = "查询聊天记录")
    public Result<List<PrivateMessageVO>> recallMessage(
        @NotNull(message = "好友id不能为空") @RequestParam Long friendId,
        @NotNull(message = "页码不能为空") @RequestParam Long page,
        @NotNull(message = "size不能为空") @RequestParam Long size) {
        return ResultUtils.success(privateMessageService.findHistoryMessage(friendId, page, size));
    }

```

```java
    @Override
    public List<PrivateMessageVO> findHistoryMessage(Long friendId, Long page, Long size) {
        page = page > 0 ? page : 1;
        size = size > 0 ? size : 10;
        Long userId = SessionContext.getSession().getUserId();
        long stIdx = (page - 1) * size;
        QueryWrapper<PrivateMessage> wrapper = new QueryWrapper<>();
        wrapper.lambda().and(
                wrap -> wrap.and(wp -> wp.eq(PrivateMessage::getSendId, userId).eq(PrivateMessage::getRecvId, friendId))
                    .or(wp -> wp.eq(PrivateMessage::getRecvId, userId).eq(PrivateMessage::getSendId, friendId)))
            .ne(PrivateMessage::getStatus, MessageStatus.RECALL.code()).orderByDesc(PrivateMessage::getId)
            .last("limit " + stIdx + "," + size);

        List<PrivateMessage> messages = this.list(wrapper);
        List<PrivateMessageVO> messageInfos =
            messages.stream().map(m -> BeanUtils.copyProperties(m, PrivateMessageVO.class))
                .collect(Collectors.toList());
        log.info("拉取聊天记录，用户id:{},好友id:{}，数量:{}", userId, friendId, messageInfos.size());
        return messageInfos;
    }

```

## 9.16 其他代码

### PrivateMessageService

```java

public interface PrivateMessageService extends IService<PrivateMessage> {

    /**
     * 发送私聊消息(高并发接口，查询mysql接口都要进行缓存)
     *
     * @param dto 私聊消息
     * @return 消息id
     */
    PrivateMessageVO sendMessage(PrivateMessageDTO dto);


    /**
     * 撤回消息
     *
     * @param id 消息id
     */
    void recallMessage(Long id);

    /**
     * 拉取历史聊天记录
     *
     * @param friendId 好友id
     * @param page     页码
     * @param size     页码大小
     * @return 聊天记录列表
     */
    List<PrivateMessageVO> findHistoryMessage(Long friendId, Long page, Long size);


    /**
     * 拉取离线消息，只能拉取最近1个月的消息，最多拉取1000条
     *
     * @param minId 消息起始id
     */
    void pullOfflineMessage(Long minId);

    /**
     * 消息已读,将整个会话的消息都置为已读状态
     *
     * @param friendId 好友id
     */
    void readedMessage(Long friendId);

    /**
     *  获取某个会话中已读消息的最大id
     *
     * @param friendId 好友id
     */
    Long getMaxReadedId(Long friendId);
}

```

### PrivateMessage

```java
@Data
@TableName("im_private_message")
public class PrivateMessage {

    /**
     * id
     */
    private Long id;

    /**
     * 发送用户id
     */
    private Long sendId;

    /**
     * 接收用户id
     */
    private Long recvId;

    /**
     * 发送内容
     */
    private String content;

    /**
     * 消息类型 MessageType
     */
    private Integer type;

    /**
     * 状态
     */
    private Integer status;


    /**
     * 发送时间
     */
    private Date sendTime;


}
```

### PrivateMessageMapper

```java
public interface PrivateMessageMapper extends BaseMapper<PrivateMessage> {}
```

### PrivateMessageDTO

```java
@Data
@Schema(description = "私聊消息DTO")
public class PrivateMessageDTO {

    @NotNull(message = "接收用户id不可为空")
    @Schema(description = "接收用户id")
    private Long recvId;


    @Length(max = 1024, message = "内容长度不得大于1024")
    @NotEmpty(message = "发送内容不可为空")
    @Schema(description = "发送内容")
    private String content;

    @NotNull(message = "消息类型不可为空")
    @Schema(description = "消息类型 0:文字 1:图片 2:文件 3:语音 4:视频")
    private Integer type;

}
```

### PrivateMessageVO

```java
@Data
@Schema(description = "私聊消息VO")
public class PrivateMessageVO {

    @Schema(description = " 消息id")
    private Long id;

    @Schema(description = " 发送者id")
    private Long sendId;

    @Schema(description = " 接收者id")
    private Long recvId;

    @Schema(description = " 发送内容")
    private String content;

    @Schema(description = "消息内容类型 IMCmdType")
    private Integer type;

    @Schema(description = " 状态")
    private Integer status;

    @Schema(description = " 发送时间")
    @JsonSerialize(using = DateToLongSerializer.class)
    private Date sendTime;
}
```

### SensitiveFilterUtil

```java

@Slf4j
@Component
@RequiredArgsConstructor
public final class SensitiveFilterUtil {

    /**
     * 替换符
     */
    private static final String REPLACE_MENT = "***";

    /**
     * 根节点
     */
    private static final TrieNode ROOT_NODE = new TrieNode();

    /**
     * 线程池
     */
    private static final ScheduledThreadPoolExecutor EXECUTOR_SERVICE =
        ThreadPoolExecutorFactory.getThreadPoolExecutor();

    private final SensitiveWordService sensitiveWordService;

    /**
     * 1、 前缀树  前缀树某一个节点
     *
     * @author NXY
     * @date 2023/12/4 11:17
     */
    private static class TrieNode {
        // 关键词结束标识
        private boolean isKeywordEnd = false;

        // 子节点(key是下级字符,value是下级节点)
        // 当前节点的子节点
        private final Map<Character, TrieNode> subNodes = new HashMap<>();

        public boolean isKeywordEnd() {
            return isKeywordEnd;
        }

        public void setKeywordEnd(boolean keywordEnd) {
            isKeywordEnd = keywordEnd;
        }

        // 添加子节点
        public void addSubNode(Character c, TrieNode node) {
            subNodes.put(c, node);
        }

        // 获取子节点
        public TrieNode getSubNode(Character c) {
            return subNodes.get(c);
        }

    }

    /**
     * 2、初始化方法，服务器启动时初始化
     *
     * @author NXY
     * @date 2023/12/4 11:18
     */
    @PostConstruct
    public void init() {
        // 每120s装载一次敏感词
        EXECUTOR_SERVICE.scheduleAtFixedRate(() -> {
            List<String> keywords = sensitiveWordService.findAllEnabledWords();
            keywords.forEach(keyword->{
                if(StrUtil.isNotEmpty(keyword)){
                    // 添加到前缀树
                    addKeyword(keyword);
                }
            });
        },0,120, TimeUnit.SECONDS);
    }

    /**
     * 3、将一个敏感词添加到前缀树中
     *
     * @param keyword
     * @author NXY
     * @date 2023/12/4 11:15
     */
    private void addKeyword(String keyword) {
        TrieNode tempNode = ROOT_NODE;
        for (int i = 0; i < keyword.length(); i++) {
            char c = keyword.charAt(i);
            TrieNode subNode = tempNode.getSubNode(c);
            if (subNode == null) {
                // 初始化子节点
                subNode = new TrieNode();
                tempNode.addSubNode(c, subNode);
            }
            // 指向子节点,进入下一轮循环
            tempNode = subNode;
            // 设置结束标识
            if (i == keyword.length() - 1) {
                tempNode.setKeywordEnd(true);
            }
        }
    }

    /**
     * 过滤敏感词
     *
     * @param text 待过滤的文本
     * @return 过滤后的文本
     */
    public String filter(String text) {
        if (StringUtils.isBlank(text)) {
            return null;
        }
        // 结果
        StringBuilder sb = new StringBuilder();
        try {
            // 指针1
            TrieNode tempNode = ROOT_NODE;
            // 指针2
            int begin = 0;
            // 指针3
            int position = 0;
            while (begin < text.length()) {
                if (position < text.length()) {
                    char c = text.charAt(position);
                    // 跳过符号
                    if (isSymbol(c)) {
                        // 若指针1处于根节点,将此符号计入结果,让指针2向下走一步
                        if (tempNode == ROOT_NODE) {
                            sb.append(c);
                            begin++;
                        }
                        // 无论符号在开头或中间,指针3都向下走一步
                        position++;
                        continue;
                    }
                    // 检查下级节点
                    tempNode = tempNode.getSubNode(c);
                    if (tempNode == null) {
                        // 以begin开头的字符串不是敏感词
                        sb.append(text.charAt(begin));
                        // 进入下一个位置
                        position = ++begin;
                        // 重新指向根节点
                        tempNode = ROOT_NODE;
                    } else if (tempNode.isKeywordEnd()) {
                        // 发现敏感词,将begin~position字符串替换掉
                        sb.append(REPLACE_MENT);
                        // 进入下一个位置
                        begin = ++position;
                        // 重新指向根节点
                        tempNode = ROOT_NODE;
                    } else {
                        // 检查下一个字符
                        position++;
                    }
                }
                // position遍历越界仍未匹配到敏感词
                else {
                    sb.append(text.charAt(begin));
                    position = ++begin;
                    tempNode = ROOT_NODE;
                }
            }
        } catch (Exception e) {
            sb = new StringBuilder(text);
        }
        return sb.toString();
    }

    /**
     * 判断是否为符号 ——特殊符号
     *
     * @author NXY
     * @date 2023/12/4 11:17
     * @return boolean
     */
    private boolean isSymbol(Character c) {
        // 0x2E80~0x9FFF 是东亚文字范围
        return !CharUtils.isAsciiAlphanumeric(c) && (c < 0x2E80 || c > 0x9FFF);
    }
}
```

#### 敏感词过滤

Trie 树 也称为字典树、单词查找树，哈希树的一种变种，通常被用于字符串匹配，用来解决在一组字符串集合中快速查找某个字符串的问题。像浏览器搜索的关键词提示就可以基于 Trie 树来做的。

Trie 树的核心原理其实很简单，就是通过公共前缀来提高字符串匹配效率

Trie 树是一种利用空间换时间的数据结构，占用的内存会比较大。也正是因为这个原因，实际工程项目中都是使用的改进版 Trie 树例如双数组 Trie 树（Double-Array Trie，DAT

1. 导入必要的包：
2. 类定义及依赖注入：
3. 常量定义：
   1. 定义替换符REPLACE_MENT。
   2. 定义前缀树的根节点ROOT_NODE。
   3. 定义线程池EXECUTOR_SERVICE。
4. 内部类TrieNode：
   1. 定义前缀树节点，包含是否为关键词结尾的标志和子节点的映射表。
5. 初始化方法：
   1. 在服务器启动时初始化敏感词，并每隔120秒从SensitiveWordService加载一次启用的敏感词，然后添加到前缀树中。
6. 添加关键词到前缀树：
   1. 遍历关键词的每个字符，依次添加到前缀树中。如果当前节点不存在，则新建一个节点，并将其添加到父节点的子节点列表中。最后设置关键词结尾标志。
7. 过滤敏感词：
   1. 遍历输入文本的每个字符，使用两个指针begin和position来分别跟踪文本的起始位置和当前处理的位置。
   2. 判断字符是否为符号，如果是符号且当前节点为根节点，则跳过该字符；否则继续向下查找。
   3. 如果当前节点不存在，则将该字符添加到结果中，并重置指针和当前节点；如果当前节点是关键词结尾，则替换敏感词；否则继续向下查找。
   4. 异常处理：捕获异常，确保即使出现错误也能返回原始文本。
8. 判断是否为符号：
   1. 判断字符是否为ASCII字母数字字符之外的符号，或者是不在东亚文字范围内的字符。

### UserChannelCtxMap

```java
 
public class UserChannelCtxMap {

    /**
     *  维护userId和ctx的关联关系，格式:Map<userId,map<terminal，ctx>>
     */
    private static Map<Long, Map<Integer, ChannelHandlerContext>> channelMap = new ConcurrentHashMap();

    public static void addChannelCtx(Long userId, Integer channel, ChannelHandlerContext ctx) {
        channelMap.computeIfAbsent(userId, key -> new ConcurrentHashMap()).put(channel, ctx);
    }

    public static void removeChannelCtx(Long userId, Integer terminal) {
        if (userId != null && terminal != null && channelMap.containsKey(userId)) {
            Map<Integer, ChannelHandlerContext> userChannelMap = channelMap.get(userId);
            userChannelMap.remove(terminal);
            if (userChannelMap.isEmpty()) {
                channelMap.remove(userId);
            }
        }
    }

    public static ChannelHandlerContext getChannelCtx(Long userId, Integer terminal) {
        if (userId != null && terminal != null && channelMap.containsKey(userId)) {
            Map<Integer, ChannelHandlerContext> userChannelMap = channelMap.get(userId);
            if (userChannelMap.containsKey(terminal)) {
                return userChannelMap.get(terminal);
            }
        }
        return null;
    }

    public static Map<Integer, ChannelHandlerContext> getChannelCtx(Long userId) {
        if (userId == null) {
            return null;
        }
        return channelMap.get(userId);
    }

}
```

### ChannelAttrKey

```java
public final class ChannelAttrKey {

    private ChannelAttrKey() {
    }

    /**
     * 用户ID
     */
    public static final String USER_ID = "USER_ID";
    /**
     * 终端类型
     */
    public static final String TERMINAL_TYPE = "TERMINAL_TYPE";
    /**
     * 心跳次数
     */
    public static final String HEARTBEAT_TIMES = "HEARTBEAT_TIMES";

}

```

### IMConstant

```java
public final class IMConstant {

    private IMConstant() {
    }

    /**
     * 在线状态过期时间 600s
     */
    public static final long ONLINE_TIMEOUT_SECOND = 600;
    /**
     * 消息允许撤回时间 300s
     */
    public static final long ALLOW_RECALL_SECOND = 300;

}
```

### IMPrivateMessage

```java
@Data
public class IMPrivateMessage<T> {

    /**
     * 发送方
     */
    private IMUserInfo sender;

    /**
     * 接收者id
     */
    private Long recvId;


    /**
     * 接收者终端类型,默认全部
     */
    private List<Integer> recvTerminals = IMTerminalType.codes();

    /**
     * 是否同步消息给自己的其他终端,默认true
     */
    private Boolean sendToSelf = true;

    /**
     * 是否需要回推发送结果,默认true
     */
    private Boolean sendResult = true;

    /**
     * 消息内容
     */
    private T data;


}
```

### IMSendResult

```java
@Data
public class IMSendResult<T> {

    /**
     * 发送方
     */
    private IMUserInfo sender;

    /**
     * 接收方
     */
    private IMUserInfo receiver;

    /**
     * 发送状态编码 IMSendCode
     */
    private Integer code;

    /**
     * 消息内容
     */
    private T data;

}
```

### IMSessionInfo

```java

@Data
public class IMSessionInfo {
    /**
     * 用户id
     */
    private Long userId;

    /**
     * 终端类型
     */
    private Integer terminal;

}
```

### IMRcvInfo

```java
@Data
public class IMRecvInfo {

    /**
     * 命令类型 IMCmdType
     */
    private Integer cmd;

    /**
     * 发送方
     */
    private IMUserInfo sender;

    /**
     * 接收方用户列表
     */
    List<IMUserInfo> receivers;

    /**
     * 是否需要回调发送结果
     */
    private Boolean sendResult;

    /**
     * 当前服务名（回调发送结果使用）
     */
    private String serviceName;
    /**
     * 推送消息体
     */
    private Object data;
}
```

### IMSendCode

```java
@AllArgsConstructor
public enum IMSendCode {

    /**
     * 发送成功
     */
    SUCCESS(0, "发送成功"),
    /**
     * 对方当前不在线
     */
    NOT_ONLINE(1, "对方当前不在线"),
    /**
     * 未找到对方的channel
     */
    NOT_FIND_CHANNEL(2, "未找到对方的channel"),
    /**
     * 未知异常
     */
    UNKONW_ERROR(9999, "未知异常");

    private final Integer code;
    private final String desc;

    public Integer code() {
        return this.code;
    }

}
```

### IMHeartbeatInfo

```java
@Data
public class IMHeartbeatInfo {
}
```

### IMListenerType

```java

@AllArgsConstructor
public enum IMListenerType {
    /**
     * 全部消息
     */
    ALL(0, "全部消息"),
    /**
     * 私聊消息
     */
    PRIVATE_MESSAGE(1, "私聊消息"),
    /**
     * 群聊消息
     */
    GROUP_MESSAGE(2, "群聊消息"),
    /**
     * 系统消息
     */
    SYSTEM_MESSAGE(3, "群聊消息");



    private final Integer code;

    private final String desc;

    public Integer code() {
        return this.code;
    }

}

```

# 10 群组功能模块

## 10.1 GroupController

```java
@Tag(name = "群聊")
@RestController
@RequestMapping("/group")
@RequiredArgsConstructor
public class GroupController {private final GroupService groupService;}
```

## 10.2 GroupServiceImpl

```java
@Slf4j
@Service
@RequiredArgsConstructor
@CacheConfig(cacheNames = RedisKey.IM_CACHE_GROUP)
public class GroupServiceImpl extends ServiceImpl<GroupMapper, Group> implements GroupService {
    private final UserService userService;
    private final GroupMemberService groupMemberService;
    private final GroupMessageMapper groupMessageMapper;
    private final FriendService friendsService;
    private final IMClient imClient;
    private final RedisTemplate<String, Object> redisTemplate;}
```

## 10.3 创建群聊

### createGroup

```java
   @Operation(summary = "创建群聊", description = "创建群聊")
    @PostMapping("/create")
    public Result<GroupVO> createGroup(@Valid @RequestBody GroupVO vo) {
        return ResultUtils.success(groupService.createGroup(vo));
    }
```

### createGroup-ServiceImpl

```java
  @Override
    public GroupVO createGroup(GroupVO vo) {
        UserSession session = SessionContext.getSession();
        User user = userService.getById(session.getUserId());
        // 保存群组数据
        Group group = BeanUtils.copyProperties(vo, Group.class);
        group.setOwnerId(user.getId());
        this.save(group);
        // 把群主加入群
        GroupMember member = new GroupMember();
        member.setGroupId(group.getId());
        member.setUserId(user.getId());
        member.setHeadImage(user.getHeadImageThumb());
        member.setRemarkNickName(vo.getRemarkNickName());
        member.setRemarkGroupName(vo.getRemarkGroupName());
        groupMemberService.save(member);
        // 返回
        vo.setId(group.getId());
        vo.setShowNickName(member.getShowNickName());
        vo.setShowGroupName(StrUtil.blankToDefault(member.getRemarkGroupName(), group.getName()));
        log.info("创建群聊，群聊id:{},群聊名称:{}", group.getId(), group.getName());
        return vo;
    }
```

### 实现过程

通过session中的userid获取user，将vo信息保存到group中，保存到数据库，将群主设置为群成员比能保存相关信息，保存到数据库，返回vo

## 10.4 修改群聊消息

### modifyGroup

```java
    @Operation(summary = "修改群聊信息", description = "修改群聊信息")
    @PutMapping("/modify")
    public Result<GroupVO> modifyGroup(@Valid @RequestBody GroupVO vo) {
        return ResultUtils.success(groupService.modifyGroup(vo));
    }
```

### modifyGroup-GroupServiceImpl

```java
    @CacheEvict(key = "#vo.getId()")
    @Transactional(rollbackFor = Exception.class)
    @Override
    public GroupVO modifyGroup(GroupVO vo) {
        UserSession session = SessionContext.getSession();
        // 校验是不是群主，只有群主能改信息
        Group group = this.getAndCheckById(vo.getId());
        // 更新成员信息
        GroupMember member = groupMemberService.findByGroupAndUserId(vo.getId(), session.getUserId());
        if (Objects.isNull(member) || member.getQuit()) {
            throw new GlobalException("您不是群聊的成员");
        }
        member.setRemarkNickName(vo.getRemarkNickName());
        member.setRemarkGroupName(vo.getRemarkGroupName());
        groupMemberService.updateById(member);
        // 群主有权修改群基本信息
        if (group.getOwnerId().equals(session.getUserId())) {
            group = BeanUtils.copyProperties(vo, Group.class);
            this.updateById(group);
        }
        vo.setShowNickName(member.getShowNickName());
        vo.setShowGroupName(StrUtil.blankToDefault(member.getRemarkGroupName(), group.getName()));
        log.info("修改群聊，群聊id:{},群聊名称:{}", group.getId(), group.getName());
        return vo;
    }
```

### findByGroupAndUserId-GroupMemeberServiceImpl

```java
    @Override
    public GroupMember findByGroupAndUserId(Long groupId, Long userId) {
        QueryWrapper<GroupMember> wrapper = new QueryWrapper<>();
        wrapper.lambda().eq(GroupMember::getGroupId, groupId).eq(GroupMember::getUserId, userId);
        return this.getOne(wrapper);
    }
```

### 实现过程

更新成员信息，校验是否是群主-更新群消息，返回vo，并将群id和返回的vo作为缓存存到redis中名为im_cache_group队列中。

## 10.5 解散群聊

### deleteGroup

```java
    @Operation(summary = "解散群聊", description = "解散群聊")
    @DeleteMapping("/delete/{groupId}")
    public Result deleteGroup(@NotNull(message = "群聊id不能为空") @PathVariable Long groupId) {
        groupService.deleteGroup(groupId);
        return ResultUtils.success();
    }
```

### deleteGroup-GroupServiceImpl

```java
    @Transactional(rollbackFor = Exception.class)
    @CacheEvict(key = "#groupId")
    @Override
    public void deleteGroup(Long groupId) {
        UserSession session = SessionContext.getSession();
        Group group = this.getById(groupId);
        if (!group.getOwnerId().equals(session.getUserId())) {
            throw new GlobalException("只有群主才有权限解除群聊");
        }
        // 群聊用户id
        List<Long> userIds = groupMemberService.findUserIdsByGroupId(groupId);
        // 逻辑删除群数据
        group.setDeleted(true);
        this.updateById(group);
        // 删除成员数据
        groupMemberService.removeByGroupId(groupId);
        // 清理已读缓存  
        String key = StrUtil.join(":", RedisKey.IM_GROUP_READED_POSITION, groupId);
        redisTemplate.delete(key);
        // 推送解散群聊提示
        this.sendTipMessage(groupId, userIds, String.format("'%s'解散了群聊", session.getNickName()));
        log.info("删除群聊，群聊id:{},群聊名称:{}", group.getId(), group.getName());
    }

```

### findUserIdsByGroupId-GroupMemberServiceImpl

```java
    @Cacheable(key = "#groupId")
    @Override
    public List<Long> findUserIdsByGroupId(Long groupId) {
        LambdaQueryWrapper<GroupMember> memberWrapper = Wrappers.lambdaQuery();
        memberWrapper.eq(GroupMember::getGroupId, groupId).eq(GroupMember::getQuit, false)
            .select(GroupMember::getUserId);
        List<GroupMember> members = this.list(memberWrapper);
        return members.stream().map(GroupMember::getUserId).collect(Collectors.toList());
    }
```

### removeByGroupId-GroupMemberServiceImpl

```java
    @CacheEvict(key = "#groupId")
    @Override
    public void removeByGroupId(Long groupId) {
        LambdaUpdateWrapper<GroupMember> wrapper = Wrappers.lambdaUpdate();
        wrapper.eq(GroupMember::getGroupId, groupId).set(GroupMember::getQuit, true)
            .set(GroupMember::getQuitTime, new Date());
        this.update(wrapper);
    }
```

### sendTipMessage

```java
    private void sendTipMessage(Long groupId, List<Long> recvIds, String content) {
        UserSession session = SessionContext.getSession();
        // 消息入库
        GroupMessage message = new GroupMessage();
        message.setContent(content);
        message.setType(MessageType.TIP_TEXT.code());
        message.setStatus(MessageStatus.UNSEND.code());
        message.setSendTime(new Date());
        message.setSendNickName(session.getNickName());
        message.setGroupId(groupId);
        message.setSendId(session.getUserId());
        message.setRecvIds(CommaTextUtils.asText(recvIds));
        groupMessageMapper.insert(message);
        // 推送
        GroupMessageVO msgInfo = BeanUtils.copyProperties(message, GroupMessageVO.class);
        IMGroupMessage<GroupMessageVO> sendMessage = new IMGroupMessage<>();
        sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
        if (CollUtil.isEmpty(recvIds)) {
            // 为空表示向全体发送
            List<Long> userIds = groupMemberService.findUserIdsByGroupId(groupId);
            sendMessage.setRecvIds(userIds);
        } else {
            sendMessage.setRecvIds(recvIds);
        }
        sendMessage.setData(msgInfo);
        sendMessage.setSendResult(false);
        sendMessage.setSendToSelf(false);
        imClient.sendGroupMessage(sendMessage);
    }
```

### 实现过程

确认群主权限，逻辑删除群数据、群成员数据、清除已读缓存、推送解散聊天提示：设置GroupMessage，拷贝到GroupMessageVo中，设置IMGroupMessage，对全体成员发送消息

## 10.6 查询单个群聊消息

### findGroup

```java
    @Operation(summary = "查询群聊", description = "查询单个群聊信息")
    @GetMapping("/find/{groupId}")
    public Result<GroupVO> findGroup(@NotNull(message = "群聊id不能为空") @PathVariable Long groupId) {
        return ResultUtils.success(groupService.findById(groupId));
    }

```

### findGroup-GroupServiceImpl

```java
    @Override
    public GroupVO findById(Long groupId) {
        UserSession session = SessionContext.getSession();
        Group group = super.getById(groupId);
        if (Objects.isNull(group)) {
            throw new GlobalException("群组不存在");
        }
        GroupMember member = groupMemberService.findByGroupAndUserId(groupId, session.getUserId());
        if (Objects.isNull(member)) {
            throw new GlobalException("您未加入群聊");
        }
        GroupVO vo = BeanUtils.copyProperties(group, GroupVO.class);
        vo.setRemarkGroupName(member.getRemarkGroupName());
        vo.setRemarkNickName(member.getRemarkNickName());
        vo.setShowNickName(member.getShowNickName());
        vo.setShowGroupName(StrUtil.blankToDefault(member.getRemarkGroupName(), group.getName()));
        vo.setQuit(member.getQuit());
        return vo;
    }
```

### 实现过程

群聊存在、加入情况下，拷贝group到vo，设置vo并返回

## 10.7 查询群聊列表

### findGroups

```java
    @Operation(summary = "查询群聊列表", description = "查询群聊列表")
    @GetMapping("/list")
    public Result<List<GroupVO>> findGroups() {
        return ResultUtils.success(groupService.findGroups());
    }

```

### findGroups-GroupServiceImpl

```java
 @Override
    public List<GroupVO> findGroups() {
        UserSession session = SessionContext.getSession();
        // 查询当前用户的群id列表
        List<GroupMember> groupMembers = groupMemberService.findByUserId(session.getUserId());
        // 一个月内退的群可能存在退群前的离线消息,一并返回作为前端缓存
        groupMembers.addAll(groupMemberService.findQuitInMonth(session.getUserId()));
        if (groupMembers.isEmpty()) {
            return new LinkedList<>();
        }
        // 拉取群列表
        List<Long> ids = groupMembers.stream().map((GroupMember::getGroupId)).collect(Collectors.toList());
        LambdaQueryWrapper<Group> groupWrapper = Wrappers.lambdaQuery();
        groupWrapper.in(Group::getId, ids);
        List<Group> groups = this.list(groupWrapper);
        // 转vo
        return groups.stream().map(group -> {
            GroupVO vo = BeanUtils.copyProperties(group, GroupVO.class);
            GroupMember member =
                groupMembers.stream().filter(m -> group.getId().equals(m.getGroupId())).findFirst().get();
            vo.setShowNickName(StrUtil.blankToDefault(member.getRemarkNickName(), session.getNickName()));
            vo.setShowGroupName(StrUtil.blankToDefault(member.getRemarkGroupName(), group.getName()));
            vo.setQuit(member.getQuit());
            return vo;
        }).collect(Collectors.toList());
    }

```

### findByUserId-GroupMemberServiceImpl

```java
    @Override
    public List<GroupMember> findByUserId(Long userId) {
        LambdaQueryWrapper<GroupMember> memberWrapper = Wrappers.lambdaQuery();
        memberWrapper.eq(GroupMember::getUserId, userId).eq(GroupMember::getQuit, false);
        return this.list(memberWrapper);
    }
```

### findQuitInMonth-GroupMemverServiceImpl

```java
    @Override
    public List<GroupMember> findQuitInMonth(Long userId) {
        Date monthTime = DateTimeUtils.addMonths(new Date(), -1);
        LambdaQueryWrapper<GroupMember> memberWrapper = Wrappers.lambdaQuery();
        memberWrapper.eq(GroupMember::getUserId, userId).eq(GroupMember::getQuit, true)
            .ge(GroupMember::getQuitTime, monthTime);
        return this.list(memberWrapper);
    }
```

### 实现过程

查询当前用户的群id以及一个月内的群id，拉群列表， 返回vo

## 10.8 邀请进群

### invite

```java
    @Operation(summary = "邀请进群", description = "邀请好友进群")
    @PostMapping("/invite")
    public Result invite(@Valid @RequestBody GroupInviteVO vo) {
        groupService.invite(vo);
        return ResultUtils.success();
    }
```

### invite-ServiceImpl

```java
    @Override
    public void invite(GroupInviteVO vo) {
        UserSession session = SessionContext.getSession();
        Group group = this.getAndCheckById(vo.getGroupId());
        GroupMember member = groupMemberService.findByGroupAndUserId(vo.getGroupId(), session.getUserId());
        if (Objects.isNull(group) || member.getQuit()) {
            throw new GlobalException("您不在群聊中,邀请失败");
        }
        // 群聊人数校验
        List<GroupMember> members = groupMemberService.findByGroupId(vo.getGroupId());
        long size = members.stream().filter(m -> !m.getQuit()).count();
        if (vo.getFriendIds().size() + size > Constant.MAX_GROUP_MEMBER) {
            throw new GlobalException("群聊人数不能大于" + Constant.MAX_GROUP_MEMBER + "人");
        }
        // 找出好友信息
        List<Friend> friends = friendsService.findFriendByUserId(session.getUserId());
        List<Friend> friendsList = vo.getFriendIds().stream()
            .map(id -> friends.stream().filter(f -> f.getFriendId().equals(id)).findFirst().get())
            .toList();
        if (friendsList.size() != vo.getFriendIds().size()) {
            throw new GlobalException("部分用户不是您的好友，邀请失败");
        }
        // 批量保存成员数据
        List<GroupMember> groupMembers = friendsList.stream().map(f -> {
            Optional<GroupMember> optional =
                members.stream().filter(m -> m.getUserId().equals(f.getFriendId())).findFirst();
            GroupMember groupMember = optional.orElseGet(GroupMember::new);
            groupMember.setGroupId(vo.getGroupId());
            groupMember.setUserId(f.getFriendId());
            groupMember.setUserNickName(f.getFriendNickName());
            groupMember.setHeadImage(f.getFriendHeadImage());
            groupMember.setCreatedTime(new Date());
            groupMember.setQuit(false);
            return groupMember;
        }).collect(Collectors.toList());
        if (!groupMembers.isEmpty()) {
            groupMemberService.saveOrUpdateBatch(group.getId(), groupMembers);
        }
        // 推送进入群聊消息
        List<Long> userIds = groupMemberService.findUserIdsByGroupId(vo.getGroupId());
        String memberNames = groupMembers.stream().map(GroupMember::getShowNickName).collect(Collectors.joining(","));
        String content = String.format("'%s'邀请'%s'加入了群聊", session.getNickName(), memberNames);
        this.sendTipMessage(vo.getGroupId(), userIds, content);
        log.info("邀请进入群聊，群聊id:{},群聊名称:{},被邀请用户id:{}", group.getId(), group.getName(),
            vo.getFriendIds());
    }
```

```java
    @Cacheable(key = "#groupId")
    @Override
    public Group getAndCheckById(Long groupId) {
        Group group = super.getById(groupId);
        if (Objects.isNull(group)) {
            throw new GlobalException("群组不存在");
        }
        if (group.getDeleted()) {
            throw new GlobalException("群组'" + group.getName() + "'已解散");
        }
        if (group.getIsBanned()) {
            throw new GlobalException("群组'" + group.getName() + "'已被封禁,原因:" + group.getReason());
        }
        return group;
    }
```

### 实现过程

群组存在、没有解散和封禁的情况下，看邀请人是否在群中、群聊人数合格，找出邀请人好友，和邀请的好友及逆行对比，保存成员数据，邀请进群，推送进入群聊消息。

## 10.9 查询群聊成员

### findGroupMembers

```java
    @Operation(summary = "查询群聊成员", description = "查询群聊成员")
    @GetMapping("/members/{groupId}")
    public Result<List<GroupMemberVO>> findGroupMembers(
        @NotNull(message = "群聊id不能为空") @PathVariable Long groupId) {
        return ResultUtils.success(groupService.findGroupMembers(groupId));
    }
```

### findGroupMembers-ServiceImpl

```java
    @Override
    public List<GroupMemberVO> findGroupMembers(Long groupId) {
        Group group = getAndCheckById(groupId);
        List<GroupMember> members = groupMemberService.findByGroupId(groupId);
        List<Long> userIds = members.stream().map(GroupMember::getUserId).collect(Collectors.toList());
        List<Long> onlineUserIds = imClient.getOnlineUser(userIds);
        return members.stream().map(m -> {
            GroupMemberVO vo = BeanUtils.copyProperties(m, GroupMemberVO.class);
            vo.setShowNickName(m.getShowNickName());
            vo.setShowGroupName(StrUtil.blankToDefault(m.getRemarkGroupName(), group.getName()));
            vo.setOnline(onlineUserIds.contains(m.getUserId()));
            return vo;
        }).sorted((m1, m2) -> m2.getOnline().compareTo(m1.getOnline())).collect(Collectors.toList());
    }
```

### 实现过程

群组正常，查找群组成员，按在线状态排序，返回。

## 10.10 退出群聊

### quitGroup

```java
    @Operation(summary = "退出群聊", description = "退出群聊")
    @DeleteMapping("/quit/{groupId}")
    public Result quitGroup(@NotNull(message = "群聊id不能为空") @PathVariable Long groupId) {
        groupService.quitGroup(groupId);
        return ResultUtils.success();
    }
```

### quitGroup-ServiceImpl

```java
    @Override
    public void quitGroup(Long groupId) {
        Long userId = SessionContext.getSession().getUserId();
        Group group = this.getById(groupId);
        if (group.getOwnerId().equals(userId)) 
            throw new GlobalException("您是群主，不可退出群聊");
        }
        // 删除群聊成员
        groupMemberService.removeByGroupAndUserId(groupId, userId);
        // 清理已读缓存 
        String key = StrUtil.join(":", RedisKey.IM_GROUP_READED_POSITION, groupId);
        redisTemplate.opsForHash().delete(key, userId.toString());
        // 推送退出群聊提示
        this.sendTipMessage(groupId, List.of(userId), "您已退出群聊");
        log.info("退出群聊，群聊id:{},群聊名称:{},用户id:{}", group.getId(), group.getName(), userId);
    }
```

### removeByGroupAndUserId-GroupMemberServiceImpl

```java
    @CacheEvict(key = "#groupId")
    @Override
    public void removeByGroupId(Long groupId) {
        LambdaUpdateWrapper<GroupMember> wrapper = Wrappers.lambdaUpdate();
        wrapper.eq(GroupMember::getGroupId, groupId).set(GroupMember::getQuit, true)
            .set(GroupMember::getQuitTime, new Date());
        this.update(wrapper);
    }
```

### 实现过程

删除群聊成员、清除缓存、推送提示

## 10.11 踢出群聊

### kickGroup

```java
    @Operation(summary = "踢出群聊", description = "将用户踢出群聊")
    @DeleteMapping("/kick/{groupId}")
    public Result kickGroup(@NotNull(message = "群聊id不能为空") @PathVariable Long groupId,
        @NotNull(message = "用户id不能为空") @RequestParam Long userId) {
        groupService.kickGroup(groupId, userId);
        return ResultUtils.success();
    }
```

### kickGroup-ServiceImpl

```java
    @Override
    public void kickGroup(Long groupId, Long userId) {
        UserSession session = SessionContext.getSession();
        Group group = this.getAndCheckById(groupId);
        if (!group.getOwnerId().equals(session.getUserId())) {
            throw new GlobalException("您不是群主，没有权限踢人");
        }
        if (userId.equals(session.getUserId())) {
            throw new GlobalException("亲，不能移除自己哟");
        }
        // 删除群聊成员
        groupMemberService.removeByGroupAndUserId(groupId, userId);
        // 清理已读缓存
        String key = StrUtil.join(":", RedisKey.IM_GROUP_READED_POSITION, groupId);
        redisTemplate.opsForHash().delete(key, userId.toString());
        // 推送踢出群聊提示
        this.sendTipMessage(groupId, List.of(userId), "您已被移出群聊");
        log.info("踢出群聊，群聊id:{},群聊名称:{},用户id:{}", group.getId(), group.getName(), userId);
    }
```

### 实现过程

删除群聊成员、清除缓存、推送提示

## 10.12 其他代码

### GroupService

```java

public interface GroupService extends IService<Group> {

    /**
     * 创建新群聊
     *
     * @param vo 群聊信息
     * @return 群聊信息
     **/
    GroupVO createGroup(GroupVO vo);

    /**
     * 修改群聊信息
     *
     * @param vo 群聊信息
     * @return 群聊信息
     **/
    GroupVO modifyGroup(GroupVO vo);

    /**
     * 删除群聊
     *
     * @param groupId 群聊id
     **/
    void deleteGroup(Long groupId);

    /**
     * 退出群聊
     *
     * @param groupId 群聊id
     */
    void quitGroup(Long groupId);

    /**
     * 将用户踢出群聊
     *
     * @param groupId 群聊id
     * @param userId  用户id
     */
    void kickGroup(Long groupId, Long userId);

    /**
     * 查询当前用户的所有群聊
     *
     * @return 群聊信息列表
     **/
    List<GroupVO> findGroups();

    /**
     * 邀请好友进群
     *
     * @param vo 群id、好友id列表
     **/
    void invite(GroupInviteVO vo);

    /**
     * 根据id查找群聊，并进行缓存
     *
     * @param groupId 群聊id
     * @return 群聊实体
     */
    Group getAndCheckById(Long groupId);

    /**
     * 根据id查找群聊
     *
     * @param groupId 群聊id
     * @return 群聊vo
     */
    GroupVO findById(Long groupId);

    /**
     * 查询群成员
     *
     * @param groupId 群聊id
     * @return List<GroupMemberVO>
     **/
    List<GroupMemberVO> findGroupMembers(Long groupId);
}

```

### Group

```java

@Data
@TableName("im_group")
public class Group {

    /**
     * id
     */
    @TableId
    private Long id;

    /**
     * 群名字
     */
    private String name;

    /**
     * 群主id
     */
    private Long ownerId;

    /**
     * 群头像
     */
    private String headImage;

    /**
     * 群头像缩略图
     */
    private String headImageThumb;

    /**
     * 群公告
     */
    private String notice;

    /**
     * 是否被封禁
     */
    private Boolean isBanned;

    /**
     * 被封禁原因
     */
    private String reason;

    /**
     * 创建时间
     */
    private Date createdTime;

    /**
     * 是否已删除
     */
    private Boolean deleted;

}

```

### GroupMapper

```java
public interface GroupMapper extends BaseMapper<Group> {}
```

### GroupMemberService

```java

public interface GroupMemberService extends IService<GroupMember> {

    /**
     * 根据群聊id和用户id查询群聊成员
     *
     * @param groupId 群聊id
     * @param userId  用户id
     * @return 群聊成员信息
     */
    GroupMember findByGroupAndUserId(Long groupId, Long userId);

    /**
     * 根据用户id查询群聊成员
     *
     * @param userId 用户id
     * @return 成员列表
     */
    List<GroupMember> findByUserId(Long userId);

    /**
     * 根据用户id查询一个月内退的群
     *
     * @param userId 用户id
     * @return 成员列表
     */
    List<GroupMember> findQuitInMonth(Long userId);

    /**
     * 根据群聊id查询群聊成员（包括已退出）
     *
     * @param groupId 群聊id
     * @return 群聊成员列表
     */
    List<GroupMember> findByGroupId(Long groupId);

    /**
     * 根据群聊id查询没有退出的群聊成员id
     *
     * @param groupId 群聊id
     * @return 群聊成员id列表
     */
    List<Long> findUserIdsByGroupId(Long groupId);

    /**
     * 批量添加成员
     *
     * @param groupId 群聊id
     * @param members 成员列表
     * @return 成功或失败
     */
    boolean saveOrUpdateBatch(Long groupId, List<GroupMember> members);

    /**
     * 根据群聊id删除移除成员
     *
     * @param groupId 群聊id
     */
    void removeByGroupId(Long groupId);

    /**
     * 根据群聊id和用户id移除成员
     *
     * @param groupId 群聊id
     * @param userId  用户id
     */
    void removeByGroupAndUserId(Long groupId, Long userId);

    /**
     * 用户用户是否在群中
     *
     * @param groupId 群聊id
     * @param userIds  用户id
     */
    Boolean isInGroup(Long groupId,List<Long> userIds);
}

```

### GroupMember

```java

@Data
@EqualsAndHashCode(callSuper = false)
@TableName("im_group_member")
public class GroupMember extends Model<GroupMember> {

    /**
     * id
     */
    @TableId
    private Long id;

    /**
     * 群id
     */
    private Long groupId;

    /**
     * 用户id
     */
    private Long userId;

    /**
     * 用户昵称
     */
    private String userNickName;

    /**
     * 显示昵称备注
     */
    private String remarkNickName;

    /**
     * 用户头像
     */
    private String headImage;

    /**
     * 显示群名备注
     */
    private String remarkGroupName;

    /**
     * 是否已退出
     */
    private Boolean quit;

    /**
     * 创建时间
     */
    private Date createdTime;

    /**
     * 退出时间
     */
    private Date quitTime;

    public String getShowNickName() {
        return StrUtil.blankToDefault(remarkNickName, userNickName);
    }

```

#### @EqualsAndHashCode

### GroupMemberMapper

```java
public interface GroupMemberMapper extends BaseMapper<GroupMember> {}
```

### GroupMessage

```java

@Data
@TableName("im_group_message")
public class GroupMessage {

    /**
     * id
     */
    @TableId
    private Long id;

    /**
     * 群id
     */
    private Long groupId;

    /**
     * 发送用户id
     */
    private Long sendId;

    /**
     * 发送用户昵称
     */
    private String sendNickName;

    /**
     * 接受用户id,为空表示全体发送
     */
    private String recvIds;

    /**
     * @用户列表
     */
    private String atUserIds;
    /**
     * 发送内容
     */
    private String content;

    /**
     * 消息类型 MessageType
     */
    private Integer type;

    /**
     *  是否回执消息
     */
    private Boolean receipt;

    /**
     *  回执消息是否完成
     */
    private Boolean receiptOk;

    /**
     * 状态 MessageStatus
     */
    private Integer status;

    /**
     * 发送时间
     */
    private Date sendTime;

}

```

### GroupMessageMapper

```java
public interface GroupMessageMapper extends BaseMapper<GroupMessage> {}
```

### GroupVO

```java

@Data
@Schema(description = "群信息VO")
public class GroupVO {

    @Schema(description = "群id")
    private Long id;

    @Length(max = 20, message = "群名称长度不能大于20")
    @NotEmpty(message = "群名称不可为空")
    @Schema(description = "群名称")
    private String name;

    @Schema(description = "群主id")
    private Long ownerId;

    @Schema(description = "头像")
    private String headImage;

    @Schema(description = "头像缩略图")
    private String headImageThumb;

    @Length(max = 1024, message = "群聊显示长度不能大于1024")
    @Schema(description = "群公告")
    private String notice;

    @Length(max = 20, message = "显示昵称长度不能大于20")
    @Schema(description = "用户在群显示昵称")
    private String remarkNickName;

    @Schema(description = "群内显示名称")
    private String showNickName;

    @Schema(description = "群名显示名称")
    private String showGroupName;

    @Length(max = 20, message = "群备注长度不能大于20")
    @Schema(description = "群名备注")
    private String remarkGroupName;

    @Schema(description = "是否已删除")
    private Boolean deleted;

    @Schema(description = "是否已退出")
    private Boolean quit;


}
```

### MessageType

```java

/**
 * 0-9: 真正的消息，需要存储到数据库
 * 10-19: 状态类消息: 撤回、已读、回执
 * 20-29: 提示类消息: 在会话中间显示的提示
 * 30-39: UI交互类消息: 显示加载状态等
 * 40-49: 操作交互类消息: 语音通话、视频通话消息等
 * 50-60: 后台操作类消息: 用户封禁、群组封禁等
 * 100-199: 单人语音通话rtc信令
 * 200-299: 多人语音通话rtc信令
 *
 */
@AllArgsConstructor
public enum MessageType {

    TEXT(0, "文字消息"),
    IMAGE(1, "图片消息"),
    FILE(2, "文件消息"),
    AUDIO(3, "语音消息"),
    VIDEO(4, "视频消息"),
    RECALL(10, "撤回"),
    READED(11, "已读"),
    RECEIPT(12, "消息已读回执"),
    TIP_TIME(20,"时间提示"),
    TIP_TEXT(21,"文字提示"),
    LOADING(30,"加载中标记"),
    ACT_RT_VOICE(40,"语音通话"),
    ACT_RT_VIDEO(41,"视频通话"),
    USER_BANNED(50,"用户封禁"),
    GROUP_BANNED(51,"群聊封禁"),
    GROUP_UNBAN(52,"群聊解封"),
    RTC_CALL_VOICE(100, "语音呼叫"),
    RTC_CALL_VIDEO(101, "视频呼叫"),
    RTC_ACCEPT(102, "接受"),
    RTC_REJECT(103, "拒绝"),
    RTC_CANCEL(104, "取消呼叫"),
    RTC_FAILED(105, "呼叫失败"),
    RTC_HANDUP(106, "挂断"),
    RTC_CANDIDATE(107, "同步candidate"),
    RTC_GROUP_SETUP(200,"发起群视频通话"),
    RTC_GROUP_ACCEPT(201,"接受通话呼叫"),
    RTC_GROUP_REJECT(202,"拒绝通话呼叫"),
    RTC_GROUP_FAILED(203,"拒绝通话呼叫"),
    RTC_GROUP_CANCEL(204,"取消通话呼叫"),
    RTC_GROUP_QUIT(205,"退出通话"),
    RTC_GROUP_INVITE(206,"邀请进入通话"),
    RTC_GROUP_JOIN(207,"主动进入通话"),
    RTC_GROUP_OFFER(208,"推送offer信息"),
    RTC_GROUP_ANSWER(209,"推送answer信息"),
    RTC_GROUP_CANDIDATE(210,"同步candidate"),
    RTC_GROUP_DEVICE(211,"设备操作"),
    ;

    private final Integer code;

    private final String desc;


    public Integer code() {
        return this.code;
    }
}

```

### MessageStatus

```java
@AllArgsConstructor
public enum MessageStatus {

    /**
     * 文件
     */
    UNSEND(0, "未送达"),
    /**
     * 文件
     */
    SENDED(1, "送达"),
    /**
     * 撤回
     */
    RECALL(2, "撤回"),
    /**
     * 已读
     */
    READED(3, "已读");

    private final Integer code;

    private final String desc;


    public Integer code() {
        return this.code;
    }
}

```

### CommaTextUtils

```java

public class CommaTextUtils {

    /**
     * 文本转列表
     *
     * @param strText 文件
     * @return 列表
     */
    public static List<String> asList(String strText) {
        if (StrUtil.isEmpty(strText)) {
            return new LinkedList<>();
        }
        return new LinkedList<>(Arrays.asList(strText.split(",")));

    }

    /**
     * 列表转字符串，并且自动清空、去重、排序
     *
     * @param texts 列表
     * @return 文本
     */
    public static <T> String asText(Collection<T> texts) {
        if (CollUtil.isEmpty(texts)) {
            return StrUtil.EMPTY;
        }
        return texts.stream().map(text -> StrUtil.toString(text)).filter(StrUtil::isNotEmpty).distinct().sorted().collect(Collectors.joining(","));
    }

    /**
     * 追加一个单词
     *
     * @param strText 文本
     * @param word    单词
     * @return 文本
     */
    public static <T> String appendWord(String strText, T word) {
        List<String> texts = asList(strText);
        texts.add(StrUtil.toString(word));
        return asText(texts);
    }

    /**
     * 删除一个单词
     *
     * @param strText 文本
     * @param word    单词
     * @return 文本
     */
    public static <T> String removeWord(String strText, T word) {
        List<String> texts = asList(strText);
        texts.remove(StrUtil.toString(word));
        return asText(texts);
    }

    /**
     * 合并
     *
     * @param strText1 文本1
     * @param strText2 文本2
     * @return 文本
     */
    public static String merge(String strText1, String strText2) {
        List<String> texts = asList(strText1);
        texts.addAll(asList(strText2));
        return asText(texts);
    }

}
```

### GroupMessageVO

```java

@Data
public class GroupMessageVO {

    @Schema(description = "消息id")
    private Long id;

    @Schema(description = "群聊id")
    private Long groupId;

    @Schema(description = " 发送者id")
    private Long sendId;

    @Schema(description = " 发送者昵称")
    private String sendNickName;

    @Schema(description = "消息内容")
    private String content;

    @Schema(description = "消息内容类型 具体枚举值由应用层定义")
    private Integer type;

    @Schema(description = "是否回执消息")
    private Boolean receipt;

    @Schema(description = "回执消息是否完成")
    private Boolean receiptOk;

    @Schema(description = "已读消息数量")
    private Integer readedCount = 0;

    @Schema(description = "@用户列表")
    private List<Long> atUserIds;

    @Schema(description = " 状态")
    private Integer status;

    @Schema(description = "发送时间")
    @JsonSerialize(using = DateToLongSerializer.class)
    private Date sendTime;
}
```

### IMGroupMessage

```java

@Data
public class IMGroupMessage<T> {

    /**
     * 发送方
     */
    private IMUserInfo sender;

    /**
     * 接收者id列表(群成员列表,为空则不会推送)
     */
    private List<Long> recvIds = new LinkedList<>();


    /**
     * 接收者终端类型,默认全部
     */
    private List<Integer> recvTerminals = IMTerminalType.codes();

    /**
     * 是否发送给自己的其他终端,默认true
     */
    private Boolean sendToSelf = true;

    /**
     * 是否需要回推发送结果,默认true
     */
    private Boolean sendResult = true;

    /**
     * 消息内容
     */
    private T data;

}

```

### IMUserInfo

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class IMUserInfo {

    /**
     * 用户id
     */
    private Long id;

    /**
     * 用户终端类型 IMTerminalType
     */
    private Integer terminal;


}
```

### GroupInviteVO

```java

@Data
@Schema(description = "邀请好友进群请求VO")
public class GroupInviteVO {

    @NotNull(message = "群id不可为空")
    @Schema(description = "群id")
    private Long groupId;

    @NotEmpty(message = "群id不可为空")
    @Schema(description = "好友id列表不可为空")
    private List<Long> friendIds;
}

```

### IMTerminalType

```java
@AllArgsConstructor
public enum IMTerminalType {

    /**
     * web
     */
    WEB(0, "web"),
    /**
     * app
     */
    APP(1, "app"),
    /**
     * pc
     */
    PC(2, "pc");

    private final Integer code;

    private final String desc;


    public static IMTerminalType fromCode(Integer code) {
        for (IMTerminalType typeEnum : values()) {
            if (typeEnum.code.equals(code)) {
                return typeEnum;
            }
        }
        return null;
    }

    public static List<Integer> codes() {
        return Arrays.stream(values()).map(IMTerminalType::code).collect(Collectors.toList());
    }

    public Integer code() {
        return this.code;
    }

}
```

#### GroupMemberVO

```java
@Data
@Schema(description = "群成员信息VO")
public class GroupMemberVO {

    @Schema(description = "用户id")
    private Long userId;

    @Schema(description = "群内显示名称")
    private String showNickName;

    @Schema(description = "群内昵称备注")
    private String remarkNickName;

    @Schema(description = "头像")
    private String headImage;

    @Schema(description = "是否已退出")
    private Boolean quit;

    @Schema(description = "是否在线")
    private Boolean online;

    @Schema(description = "群名显示名称")
    private String showGroupName;

    @Schema(description = "群名备注")
    private String remarkGroupName;

}
```

# 11 群聊功能模块

## 11.1 platform消息推送

### GroupMessageController

```java
 @PostMapping("/send")
    @Operation(summary = "发送群聊消息", description = "发送群聊消息")
    public Result<GroupMessageVO> sendMessage(@Valid @RequestBody GroupMessageDTO vo) {
        return ResultUtils.success(groupMessageService.sendMessage(vo));
    }
```

### sendMessage-Impl

```java
@Override
public GroupMessageVO sendMessage(GroupMessageDTO dto) {
    UserSession session = SessionContext.getSession();
    Group group = groupService.getAndCheckById(dto.getGroupId());
    // 是否在群聊里面
    GroupMember member = groupMemberService.findByGroupAndUserId(dto.getGroupId(), session.getUserId());
    if (Objects.isNull(member) || member.getQuit()) {
        throw new GlobalException("您已不在群聊里面，无法发送消息");
    }
    // 群聊成员列表
    List<Long> userIds = groupMemberService.findUserIdsByGroupId(group.getId());
    // 不用发给自己
    userIds = userIds.stream().filter(id -> !session.getUserId().equals(id)).collect(Collectors.toList());
    // 保存消息
    GroupMessage msg = BeanUtils.copyProperties(dto, GroupMessage.class);
    msg.setSendId(session.getUserId());
    msg.setSendTime(new Date());
    msg.setSendNickName(member.getShowNickName());
    msg.setAtUserIds(CommaTextUtils.asText(dto.getAtUserIds()));
    this.save(msg);
    // 过滤内容中的敏感词
    if(MessageType.TEXT.code().equals(dto.getType())){
        msg.setContent(sensitiveFilterUtil.filter(dto.getContent()));
    }
    // 群发
    GroupMessageVO msgInfo = BeanUtils.copyProperties(msg, GroupMessageVO.class);
    msgInfo.setAtUserIds(dto.getAtUserIds());
    IMGroupMessage<GroupMessageVO> sendMessage = new IMGroupMessage<>();
    sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
    sendMessage.setRecvIds(userIds);
    sendMessage.setSendResult(false);
    sendMessage.setData(msgInfo);
    imClient.sendGroupMessage(sendMessage);
    log.info("发送群聊消息，发送id:{},群聊id:{},内容:{}", session.getUserId(), dto.getGroupId(), dto.getContent());
    return msgInfo;
}
```

## 11.2 client消息推送

### IMSender

```java

    public<T> void sendGroupMessage(IMGroupMessage<T> message) {
        // 根据群聊每个成员所连的IM-server，进行分组
        Map<String, IMUserInfo> sendMap = new HashMap<>();
        for (Integer terminal : message.getRecvTerminals()) {
            message.getRecvIds().forEach(id -> {
                String key = String.join(":", IMRedisKey.IM_USER_SERVER_ID, id.toString(), terminal.toString());
                sendMap.put(key,new IMUserInfo(id, terminal));
            });
        }
        // 批量拉取
        List<Object> serverIds = redisMQTemplate.opsForValue().multiGet(sendMap.keySet());
        // 格式:map<服务器id,list<接收方>>
        Map<Integer, List<IMUserInfo>> serverMap = new HashMap<>();
        List<IMUserInfo> offLineUsers = new LinkedList<>();
        int idx = 0;
        for (Map.Entry<String,IMUserInfo> entry : sendMap.entrySet()) {
            Integer serverId = (Integer)serverIds.get(idx++);
            if (!Objects.isNull(serverId)) {
                List<IMUserInfo> list = serverMap.computeIfAbsent(serverId, o -> new LinkedList<>());
                list.add(entry.getValue());
            } else {
                // 加入离线列表
                offLineUsers.add(entry.getValue());
            }
        }
        // 逐个server发送
        for (Map.Entry<Integer, List<IMUserInfo>> entry : serverMap.entrySet()) {
            IMRecvInfo recvInfo = new IMRecvInfo();
            recvInfo.setCmd(IMCmdType.GROUP_MESSAGE.code());
            recvInfo.setReceivers(new LinkedList<>(entry.getValue()));
            recvInfo.setSender(message.getSender());
            recvInfo.setServiceName(appName);
            recvInfo.setSendResult(message.getSendResult());
            recvInfo.setData(message.getData());
            // 推送至队列
            String key = String.join(":", IMRedisKey.IM_MESSAGE_GROUP_QUEUE, entry.getKey().toString());
            redisMQTemplate.opsForList().rightPush(key, recvInfo);
        }

        // 推送给自己的其他终端
        if (message.getSendToSelf()) {
            for (Integer terminal : IMTerminalType.codes()) {
                if (terminal.equals(message.getSender().getTerminal())) {
                    continue;
                }
                // 获取终端连接的channelId
                String key = String.join(":", IMRedisKey.IM_USER_SERVER_ID, message.getSender().getId().toString(), terminal.toString());
                Integer serverId = (Integer)redisMQTemplate.opsForValue().get(key);
                // 如果终端在线，将数据存储至redis，等待拉取推送
                if (!Objects.isNull(serverId)) {
                    IMRecvInfo recvInfo = new IMRecvInfo();
                    recvInfo.setCmd(IMCmdType.GROUP_MESSAGE.code());
                    recvInfo.setSender(message.getSender());
                    recvInfo.setReceivers(Collections.singletonList(new IMUserInfo(message.getSender().getId(), terminal)));
                    // 自己的消息不需要回推消息结果
                    recvInfo.setSendResult(false);
                    recvInfo.setData(message.getData());
                    String sendKey = String.join(":", IMRedisKey.IM_MESSAGE_GROUP_QUEUE, serverId.toString());
                    redisMQTemplate.opsForList().rightPush(sendKey, recvInfo);
                }
            }
        }
        // 对离线用户回复消息状态
        if(message.getSendResult() && !offLineUsers.isEmpty()){
            List<IMSendResult> results = new LinkedList<>();
            for (IMUserInfo offLineUser : offLineUsers) {
                IMSendResult result = new IMSendResult();
                result.setSender(message.getSender());
                result.setReceiver(offLineUser);
                result.setCode(IMSendCode.NOT_ONLINE.code());
                result.setData(message.getData());
                results.add(result);
            }
            listenerMulticaster.multicast(IMListenerType.GROUP_MESSAGE, results);
        }

    }
```

## 11.3 server消息推送

### PullGroupMessageTask

```java
@Slf4j
@Component
@RequiredArgsConstructor
@RedisMQListener(queue = IMRedisKey.IM_MESSAGE_GROUP_QUEUE, batchSize = 10)
public class PullGroupMessageTask extends AbstractPullMessageTask<IMRecvInfo> {

    @Override
    public void onMessage(IMRecvInfo recvInfo) {
        AbstractMessageProcessor processor = ProcessorFactory.createProcessor(IMCmdType.GROUP_MESSAGE);
        processor.process(recvInfo);
    }

}
```

### GroupMessageProcessor

```java

@Slf4j
@Component
@RequiredArgsConstructor
public class GroupMessageProcessor extends AbstractMessageProcessor<IMRecvInfo> {

    private final RedisTemplate<String, Object> redisTemplate;

    @Override
    public void process(IMRecvInfo recvInfo) {
        IMUserInfo sender = recvInfo.getSender();
        List<IMUserInfo> receivers = recvInfo.getReceivers();
        log.info("接收到群消息，发送者:{},接收用户数量:{}，内容:{}", sender.getId(), receivers.size(), recvInfo.getData());
        for (IMUserInfo receiver : receivers) {
            try {
                ChannelHandlerContext channelCtx = UserChannelCtxMap.getChannelCtx(receiver.getId(), receiver.getTerminal());
                if (!Objects.isNull(channelCtx)) {
                    // 推送消息到用户
                    IMSendInfo sendInfo = new IMSendInfo();
                    sendInfo.setCmd(IMCmdType.GROUP_MESSAGE.code());
                    sendInfo.setData(recvInfo.getData());
                    channelCtx.channel().writeAndFlush(sendInfo);
                    // 消息发送成功确认
                    sendResult(recvInfo, receiver, IMSendCode.SUCCESS);

                } else {
                    // 消息发送成功确认
                    sendResult(recvInfo, receiver, IMSendCode.NOT_FIND_CHANNEL);
                    log.error("未找到channel,发送者:{},接收id:{}，内容:{}", sender.getId(), receiver.getId(), recvInfo.getData());
                }
            } catch (Exception e) {
                // 消息发送失败确认
                sendResult(recvInfo, receiver, IMSendCode.UNKONW_ERROR);
                log.error("发送消息异常,发送者:{},接收id:{}，内容:{}", sender.getId(), receiver.getId(), recvInfo.getData());
            }
        }
    }


    private void sendResult(IMRecvInfo recvInfo, IMUserInfo receiver, IMSendCode sendCode) {
        if (recvInfo.getSendResult()) {
            IMSendResult result = new IMSendResult();
            result.setSender(recvInfo.getSender());
            result.setReceiver(receiver);
            result.setCode(sendCode.code());
            result.setData(recvInfo.getData());
            // 推送到结果队列
            String key = StrUtil.join(":",IMRedisKey.IM_RESULT_GROUP_QUEUE,recvInfo.getServiceName());
            redisTemplate.opsForList().rightPush(key, result);
        }
    }
}

```

## 11.4 server消息接收

同9.4

### 实现过程

- 通过IMChannelHandler-handlerAdded，进行用户连接事件的日志打印
- 通过LoginProcessor-process，进行登陆处理，将用户id和serverid绑定
  - 校验用户token，判断并处理是否有多端同时登陆的情况
  - 绑定用户和channel，设置用户id属性、终端类型、初始化心跳次数、在redis上记录心跳
  - 响应ws，发送sendInfo
- 通过HeartbeatProcessor-process，进行心跳处理，绑定关系续命
  -   响应ws
  -  发送心跳，进行续命
- 通过  IMChannelHandler-userEventTriggered  ，处理心跳超时事件，关闭channel
- 通过 IMChannelHandler-handlerRemoved  ，连接断开事件，移除channel，解除绑定

## 11.5 server推送监听结果

### 实现过程

- 通过 GroupMessageProcessor  sendResult  向redis回推私聊消息推送结果

## 11.6 client监听发送结果

### GroupMessageResultTask

```java

@Component
@RequiredArgsConstructor
@RedisMQListener(queue = IMRedisKey.IM_RESULT_GROUP_QUEUE, batchSize = 100)
public class GroupMessageResultResultTask extends AbstractMessageResultTask<IMSendResult> {

    private final MessageListenerMulticaster listenerMulticaster;

    @Override
    public void onMessage(List<IMSendResult> results) {
        listenerMulticaster.multicast(IMListenerType.GROUP_MESSAGE, results);
    }

}
```

### 实现过程

- 通过PrivateMessageResultTask中的pullMessage方法，从redis中定时拉取群聊消息推送结果
- 通过MessageListenerMulticaster中的multicast方法，向业务层的监听器广播消息推送结果

## 11.7 platform监听发送结果

### GroupMessageListener

```java
@Slf4j
@IMListener(type = IMListenerType.GROUP_MESSAGE)
@AllArgsConstructor
public class GroupMessageListener implements MessageListener<GroupMessageVO> {

    private final RedisTemplate<String, Object> redisTemplate;

    @Override
    public void process(List<IMSendResult<GroupMessageVO>> results) {
        for(IMSendResult<GroupMessageVO> result:results){
            GroupMessageVO messageInfo = result.getData();
            if (result.getCode().equals(IMSendCode.SUCCESS.code())) {
                log.info("消息送达，消息id:{}，发送者:{},接收者:{},终端:{}", messageInfo.getId(), result.getSender().getId(), result.getReceiver().getId(), result.getReceiver().getTerminal());
            }
        }
    }

}

```

## 11.8 GroupMessageController

```java
@Tag(name = "群聊消息")
@RestController
@RequestMapping("/message/group")
@RequiredArgsConstructor
public class GroupMessageController {

    private final GroupMessageService groupMessageService;}
```

## 11.9 GroupMessageControllerServiceImpl

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class GroupMessageServiceImpl extends ServiceImpl<GroupMessageMapper, GroupMessage> implements
    GroupMessageService {
    private final GroupService groupService;
    private final GroupMemberService groupMemberService;
    private final RedisTemplate<String, Object> redisTemplate;
    private final IMClient imClient;
    private final SensitiveFilterUtil sensitiveFilterUtil;
}
```

## 11.10 发送群聊消息

```java
    @PostMapping("/send")
    @Operation(summary = "发送群聊消息", description = "发送群聊消息")
    public Result<GroupMessageVO> sendMessage(@Valid @RequestBody GroupMessageDTO vo) {
        return ResultUtils.success(groupMessageService.sendMessage(vo));
    }
```

```java

    @Override
    public GroupMessageVO sendMessage(GroupMessageDTO dto) {
        UserSession session = SessionContext.getSession();
        Group group = groupService.getAndCheckById(dto.getGroupId());
        // 是否在群聊里面
        GroupMember member = groupMemberService.findByGroupAndUserId(dto.getGroupId(), session.getUserId());
        if (Objects.isNull(member) || member.getQuit()) {
            throw new GlobalException("您已不在群聊里面，无法发送消息");
        }
        // 群聊成员列表
        List<Long> userIds = groupMemberService.findUserIdsByGroupId(group.getId());
        // 不用发给自己
        userIds = userIds.stream().filter(id -> !session.getUserId().equals(id)).collect(Collectors.toList());
        // 保存消息
        GroupMessage msg = BeanUtils.copyProperties(dto, GroupMessage.class);
        msg.setSendId(session.getUserId());
        msg.setSendTime(new Date());
        msg.setSendNickName(member.getShowNickName());
        msg.setAtUserIds(CommaTextUtils.asText(dto.getAtUserIds()));
        this.save(msg);
        // 过滤内容中的敏感词
        if(MessageType.TEXT.code().equals(dto.getType())){
            msg.setContent(sensitiveFilterUtil.filter(dto.getContent()));
        }
        // 群发
        GroupMessageVO msgInfo = BeanUtils.copyProperties(msg, GroupMessageVO.class);
        msgInfo.setAtUserIds(dto.getAtUserIds());
        IMGroupMessage<GroupMessageVO> sendMessage = new IMGroupMessage<>();
        sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
        sendMessage.setRecvIds(userIds);
        sendMessage.setSendResult(false);
        sendMessage.setData(msgInfo);
        imClient.sendGroupMessage(sendMessage);
        log.info("发送群聊消息，发送id:{},群聊id:{},内容:{}", session.getUserId(), dto.getGroupId(), dto.getContent());
        return msgInfo;
    }
```

## 11.11 撤回消息

```java
    @DeleteMapping("/recall/{id}")
    @Operation(summary = "撤回消息", description = "撤回群聊消息")
    public Result<Long> recallMessage(@NotNull(message = "消息id不能为空") @PathVariable Long id) {
        groupMessageService.recallMessage(id);
        return ResultUtils.success();
    }
```

```java
  @Override
    public void recallMessage(Long id) {
        UserSession session = SessionContext.getSession();
        GroupMessage msg = this.getById(id);
        if (Objects.isNull(msg)) {
            throw new GlobalException("消息不存在");
        }
        if (!msg.getSendId().equals(session.getUserId())) {
            throw new GlobalException("这条消息不是由您发送,无法撤回");
        }
        if (System.currentTimeMillis() - msg.getSendTime().getTime() > IMConstant.ALLOW_RECALL_SECOND * 1000) {
            throw new GlobalException("消息已发送超过5分钟，无法撤回");
        }
        // 判断是否在群里
        GroupMember member = groupMemberService.findByGroupAndUserId(msg.getGroupId(), session.getUserId());
        if (Objects.isNull(member) || Boolean.TRUE.equals(member.getQuit())) {
            throw new GlobalException("您已不在群聊里面，无法撤回消息");
        }
        // 修改数据库
        msg.setStatus(MessageStatus.RECALL.code());
        this.updateById(msg);
        // 群发
        List<Long> userIds = groupMemberService.findUserIdsByGroupId(msg.getGroupId());
        // 不用发给自己
        userIds = userIds.stream().filter(uid -> !session.getUserId().equals(uid)).collect(Collectors.toList());
        GroupMessageVO msgInfo = BeanUtils.copyProperties(msg, GroupMessageVO.class);
        msgInfo.setType(MessageType.RECALL.code());
        String content = String.format("'%s'撤回了一条消息", member.getShowNickName());
        msgInfo.setContent(content);
        msgInfo.setSendTime(new Date());

        IMGroupMessage<GroupMessageVO> sendMessage = new IMGroupMessage<>();
        sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
        sendMessage.setRecvIds(userIds);
        sendMessage.setData(msgInfo);
        sendMessage.setSendResult(false);
        sendMessage.setSendToSelf(false);
        imClient.sendGroupMessage(sendMessage);

        // 推给自己其他终端
        msgInfo.setContent("你撤回了一条消息");
        sendMessage.setSendToSelf(true);
        sendMessage.setRecvIds(Collections.emptyList());
        sendMessage.setRecvTerminals(Collections.emptyList());
        imClient.sendGroupMessage(sendMessage);
        log.info("撤回群聊消息，发送id:{},群聊id:{},内容:{}", session.getUserId(), msg.getGroupId(), msg.getContent());
    }
```

## 11.12 拉取离线消息

```java
@GetMapping("/pullOfflineMessage")
@Operation(summary = "拉取离线消息", description = "拉取离线消息,消息将通过webscoket异步推送")
public Result pullOfflineMessage(@RequestParam Long minId) {
    groupMessageService.pullOfflineMessage(minId);
    return ResultUtils.success();
}
```

```java

    @Override
    public void pullOfflineMessage(Long minId) {
        UserSession session = SessionContext.getSession();
        if(!imClient.isOnline(session.getUserId())){
            throw new GlobalException("网络连接失败，无法拉取离线消息");
        }
        // 查询用户加入的群组
        List<GroupMember> members = groupMemberService.findByUserId(session.getUserId());
        Map<Long, GroupMember> groupMemberMap = CollStreamUtil.toIdentityMap(members, GroupMember::getGroupId);
        Set<Long> groupIds = groupMemberMap.keySet();
        if(CollectionUtil.isEmpty(groupIds)){
            // 关闭加载中标志
            this.sendLoadingMessage(false);
            return;
        }
        // 开启加载中标志
        this.sendLoadingMessage(true);
        // 只能拉取最近3个月的,最多拉取3000条
        int months = session.getTerminal().equals(IMTerminalType.APP.code()) ? 1 : 3;
        Date minDate = DateUtils.addMonths(new Date(), -months);
        LambdaQueryWrapper<GroupMessage> wrapper = Wrappers.lambdaQuery();
        wrapper.gt(GroupMessage::getId, minId)
            .gt(GroupMessage::getSendTime, minDate)
            .in(GroupMessage::getGroupId, groupIds)
            .ne(GroupMessage::getStatus, MessageStatus.RECALL.code())
            .orderByAsc(GroupMessage::getId);
        List<GroupMessage> messages = this.list(wrapper);
        // 通过群聊对消息进行分组
        Map<Long, List<GroupMessage>> messageGroupMap = messages.stream().collect(Collectors.groupingBy(GroupMessage::getGroupId));
        // 退群前的消息
        List<GroupMember> quitMembers = groupMemberService.findQuitInMonth(session.getUserId());
        for(GroupMember quitMember: quitMembers){
            wrapper = Wrappers.lambdaQuery();
            wrapper.gt(GroupMessage::getId, minId)
                .between(GroupMessage::getSendTime, minDate,quitMember.getQuitTime())
                .eq(GroupMessage::getGroupId, quitMember.getGroupId())
                .ne(GroupMessage::getStatus, MessageStatus.RECALL.code())
                .orderByAsc(GroupMessage::getId);
            List<GroupMessage> groupMessages = this.list(wrapper);
            messageGroupMap.put(quitMember.getGroupId(),groupMessages);
            groupMemberMap.put(quitMember.getGroupId(),quitMember);
        }
        // 推送消息
        AtomicInteger sendCount = new AtomicInteger();
        messageGroupMap.forEach((groupId, groupMessages) -> {
            // 填充消息状态
            String key = StrUtil.join(":", RedisKey.IM_GROUP_READED_POSITION, groupId);
            Object o = redisTemplate.opsForHash().get(key, session.getUserId().toString());
            long readedMaxId = Objects.isNull(o) ? -1 : Long.parseLong(o.toString());
            Map<Object, Object> maxIdMap = null;
            for(GroupMessage m:groupMessages){
                // 排除加群之前的消息
                GroupMember member = groupMemberMap.get(m.getGroupId());
                if(DateUtil.compare(member.getCreatedTime(), m.getSendTime()) > 0){
                    continue;
                }
                // 排除不需要接收的消息
                List<String> recvIds = CommaTextUtils.asList(m.getRecvIds());
                if(!recvIds.isEmpty() && !recvIds.contains(session.getUserId().toString())){
                    continue;
                }
                // 组装vo
                GroupMessageVO vo = BeanUtils.copyProperties(m, GroupMessageVO.class);
                // 被@用户列表
                List<String> atIds = CommaTextUtils.asList(m.getAtUserIds());
                vo.setAtUserIds(atIds.stream().map(Long::parseLong).collect(Collectors.toList()));
                // 填充状态
                vo.setStatus(readedMaxId >= m.getId() ? MessageStatus.READED.code() : MessageStatus.UNSEND.code());
                // 针对回执消息填充已读人数
                if(m.getReceipt()){
                    if(Objects.isNull(maxIdMap)) {
                        maxIdMap = redisTemplate.opsForHash().entries(key);
                    }
                    int count = getReadedUserIds(maxIdMap, m.getId(),m.getSendId()).size();
                    vo.setReadedCount(count);
                }
                // 推送
                IMGroupMessage<GroupMessageVO> sendMessage = new IMGroupMessage<>();
                sendMessage.setSender(new IMUserInfo(m.getSendId(), IMTerminalType.WEB.code()));
                sendMessage.setRecvIds(Arrays.asList(session.getUserId()));
                sendMessage.setRecvTerminals(Arrays.asList(session.getTerminal()));
                sendMessage.setSendResult(false);
                sendMessage.setSendToSelf(false);
                sendMessage.setData(vo);
                imClient.sendGroupMessage(sendMessage);
                sendCount.getAndIncrement();
            }
        });
        // 关闭加载中标志
        this.sendLoadingMessage(false);
        log.info("拉取离线群聊消息,用户id:{},数量:{}",session.getUserId(),sendCount.get());
    }

```

## 11.13 消息已读

```java
@PutMapping("/readed")
@Operation(summary = "消息已读", description = "将群聊中的消息状态置为已读")
public Result readedMessage(@RequestParam Long groupId) {
    groupMessageService.readedMessage(groupId);
    return ResultUtils.success();
}
```

```java
@Override
public void readedMessage(Long groupId) {
    UserSession session = SessionContext.getSession();
    // 取出最后的消息id
    LambdaQueryWrapper<GroupMessage> wrapper = Wrappers.lambdaQuery();
    wrapper.eq(GroupMessage::getGroupId, groupId)
            .orderByDesc(GroupMessage::getId)
            .last("limit 1")
            .select(GroupMessage::getId);
    GroupMessage message = this.getOne(wrapper);
    if (Objects.isNull(message)) {
        return;
    }
    // 推送消息给自己的其他终端,同步清空会话列表中的未读数量
    GroupMessageVO msgInfo = new GroupMessageVO();
    msgInfo.setType(MessageType.READED.code());
    msgInfo.setSendTime(new Date());
    msgInfo.setSendId(session.getUserId());
    msgInfo.setGroupId(groupId);
    IMGroupMessage<GroupMessageVO> sendMessage = new IMGroupMessage<>();
    sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
    sendMessage.setSendToSelf(true);
    sendMessage.setData(msgInfo);
    sendMessage.setSendResult(true);
    imClient.sendGroupMessage(sendMessage);
    // 已读消息key
    String key = StrUtil.join(":", RedisKey.IM_GROUP_READED_POSITION, groupId);
    // 原来的已读消息位置
    Object maxReadedId = redisTemplate.opsForHash().get(key, session.getUserId().toString());
    // 记录已读消息位置
    redisTemplate.opsForHash().put(key, session.getUserId().toString(), message.getId());
    // 推送消息回执，刷新已读人数显示
    wrapper = Wrappers.lambdaQuery();
    wrapper.eq(GroupMessage::getGroupId, groupId);
    wrapper.gt(!Objects.isNull(maxReadedId), GroupMessage::getId, maxReadedId);
    wrapper.le(!Objects.isNull(maxReadedId), GroupMessage::getId, message.getId());
    wrapper.ne(GroupMessage::getStatus, MessageStatus.RECALL.code());
    wrapper.eq(GroupMessage::getReceipt, true);
    List<GroupMessage> receiptMessages = this.list(wrapper);
    if (CollectionUtil.isNotEmpty(receiptMessages)) {
        List<Long> userIds = groupMemberService.findUserIdsByGroupId(groupId);
        Map<Object, Object> maxIdMap = redisTemplate.opsForHash().entries(key);
        for (GroupMessage receiptMessage : receiptMessages) {
            Integer readedCount = getReadedUserIds(maxIdMap, receiptMessage.getId(),receiptMessage.getSendId()).size();
            // 如果所有人都已读，记录回执消息完成标记
            if(readedCount >= userIds.size() - 1){
                receiptMessage.setReceiptOk(true);
                this.updateById(receiptMessage);
            }
            msgInfo = new GroupMessageVO();
            msgInfo.setId(receiptMessage.getId());
            msgInfo.setGroupId(groupId);
            msgInfo.setReadedCount(readedCount);
            msgInfo.setReceiptOk(receiptMessage.getReceiptOk());
            msgInfo.setType(MessageType.RECEIPT.code());
            sendMessage = new IMGroupMessage<>();
            sendMessage.setSender(new IMUserInfo(session.getUserId(), session.getTerminal()));
            sendMessage.setRecvIds(userIds);
            sendMessage.setData(msgInfo);
            sendMessage.setSendToSelf(false);
            sendMessage.setSendResult(false);
            imClient.sendGroupMessage(sendMessage);
        }
    }
}
```

## 11.14 获取已读用户id

```java
    @GetMapping("/findReadedUsers")
    @Operation(summary = "获取已读用户id", description = "获取消息已读用户列表")
    public Result<List<Long>> findReadedUsers(@RequestParam Long groupId,
        @RequestParam Long messageId) {
        return ResultUtils.success(groupMessageService.findReadedUsers(groupId, messageId));
    }
```

```java
@Override
public List<Long> findReadedUsers(Long groupId, Long messageId) {
    UserSession session = SessionContext.getSession();
    GroupMessage message = this.getById(messageId);
    if (Objects.isNull(message)) {
        throw new GlobalException("消息不存在");
    }
    // 是否在群聊里面
    GroupMember member = groupMemberService.findByGroupAndUserId(groupId, session.getUserId());
    if (Objects.isNull(member) || member.getQuit()) {
        throw new GlobalException("您已不在群聊里面");
    }
        // 已读位置key
    String key = StrUtil.join(":", RedisKey.IM_GROUP_READED_POSITION, groupId);
    // 一次获取所有用户的已读位置
    Map<Object, Object> maxIdMap = redisTemplate.opsForHash().entries(key);
    // 返回已读用户的id集合
    return getReadedUserIds(maxIdMap, message.getId(),message.getSendId());
}
```

## 11.15 查询聊天记录

```java
@GetMapping("/history")
@Operation(summary = "查询聊天记录", description = "查询聊天记录")
public Result<List<GroupMessageVO>> recallMessage(@NotNull(message = "群聊id不能为空") @RequestParam Long groupId,
    @NotNull(message = "页码不能为空") @RequestParam Long page,
    @NotNull(message = "size不能为空") @RequestParam Long size) {
    return ResultUtils.success(groupMessageService.findHistoryMessage(groupId, page, size));
}
```

```java
@Override
public List<GroupMessageVO> findHistoryMessage(Long groupId, Long page, Long size) {
    page = page > 0 ? page : 1;
    size = size > 0 ? size : 10;
    Long userId = SessionContext.getSession().getUserId();
    long stIdx = (page - 1) * size;
    // 群聊成员信息
    GroupMember member = groupMemberService.findByGroupAndUserId(groupId, userId);
    if (Objects.isNull(member) || member.getQuit()) {
        throw new GlobalException("您已不在群聊中");
    }
    // 查询聊天记录，只查询加入群聊时间之后的消息
    QueryWrapper<GroupMessage> wrapper = new QueryWrapper<>();
    wrapper.lambda().eq(GroupMessage::getGroupId, groupId).gt(GroupMessage::getSendTime, member.getCreatedTime())
            .ne(GroupMessage::getStatus, MessageStatus.RECALL.code()).orderByDesc(GroupMessage::getId).last("limit " + stIdx + "," + size);
    List<GroupMessage> messages = this.list(wrapper);
    List<GroupMessageVO> messageInfos =
            messages.stream().map(m -> BeanUtils.copyProperties(m, GroupMessageVO.class)).collect(Collectors.toList());
    log.info("拉取群聊记录，用户id:{},群聊id:{}，数量:{}", userId, groupId, messageInfos.size());
    return messageInfos;
}
```

## 11.16 其他代码

## GroupMessageDTO

```java
@Data
@Schema(description = "群聊消息DTO")
public class GroupMessageDTO {

    @NotNull(message = "群聊id不可为空")
    @Schema(description = "群聊id")
    private Long groupId;

    @Length(max = 1024, message = "发送内容长度不得大于1024")
    @NotEmpty(message = "发送内容不可为空")
    @Schema(description = "发送内容")
    private String content;

    @NotNull(message = "消息类型不可为空")
    @Schema(description = "消息类型 0:文字 1:图片 2:文件 3:语音 4:视频")
    private Integer type;

    @Schema(description = "是否回执消息")
    private Boolean receipt = false;

    @Size(max = 20, message = "一次最多只能@20个小伙伴哦")
    @Schema(description = "被@用户列表")
    private List<Long> atUserIds;
}
```

## GroupBanDTO

```java
@Data
@Schema(description = "群组封禁")
public class GroupBanDTO {

    @Schema(description = "群组id")
    private Long id;

    @Schema(description = "封禁原因")
    private String reason;
}

```

## GroupUnbanDTO

```java
@Data
@Schema(description = "群组解锁")
public class GroupUnbanDTO {

    @Schema(description = "群组id")
    private Long id;

}
```

## GroupMessageService

```java

public interface GroupMessageService extends IService<GroupMessage> {

    /**
     * 发送群聊消息(高并发接口，查询mysql接口都要进行缓存)
     *
     * @param dto 群聊消息
     * @return 群聊id
     */
    GroupMessageVO sendMessage(GroupMessageDTO dto);

    /**
     * 撤回消息
     *
     * @param id 消息id
     */
    void recallMessage(Long id);

    /**
     * 拉取离线消息，只能拉取最近1个月的消息，最多拉取1000条
     *
     * @param minId 消息起始id
     */
    void  pullOfflineMessage(Long minId);

    /**
     * 消息已读,同步其他终端，清空未读数量
     *
     * @param groupId 群聊
     */
    void readedMessage(Long groupId);

    /**
     * 查询群里消息已读用户id列表
     * @param groupId 群里id
     * @param messageId 消息id
     * @return 已读用户id集合
     */
    List<Long> findReadedUsers(Long groupId,Long messageId);
    /**
     * 拉取历史聊天记录
     *
     * @param groupId 群聊id
     * @param page    页码
     * @param size    页码大小
     * @return 聊天记录列表
     */
    List<GroupMessageVO> findHistoryMessage(Long groupId, Long page, Long size);
}

```

# ----cakeim项目简历-----

## 项目简介

- ​	采用websocket协议实现的一个分布式网页版聊天系统。
- ​        基于私有协议支持第三方接入的多端即时通讯系统

## 项目技术

​	SpringBoot + MybatisPlus + Netty + MySQL + Redis + RabbitMQ + MinIO

## 项目总结

1. 采用分布式架构设计。服务器支持集群化部署，每个服务器仅处理自身连接用户的消息。
2. 支持好友、群组、私聊、群聊、离线消息、发送图片、文件等基本功能。
3. 消息转发经过MQ，起到流量削峰和解耦的作用，并通过RabbitMQ的Confirm消息确认模式和ack机制，保证了消息的可靠性。
4. 通过使用 Redis 对部分数据进行缓存，系统性能得到了显著提升。
5. 同时，采用双 Token 机制进行用户登录和鉴权，提升了用户体验。
6. 此外，还使用基于 HashMap 实现的前缀树和字符串匹配，完成了敏感词过滤功能。

## 项目目标

业务目标：满足需求设计篇章中的各类需求场景。
技术目标：支持无限扩容，百万用户同时在线聊天。
架构目标：高并发、高性能、高可用、可监控、可预警、可伸缩，支持无限扩展。

## 技术选型可以有？

开发框架：SpringBoot、SpringCloud、SpringCloud Alibaba、Dubbo。
缓存：Redis分布式缓存+Guava本地缓存。
数据库：MySQL、TiDB、HBase。
流量网关：OpenResty+Lua。
业务网关：SpringCloud Gateway + Sentinel。
持久层框架：MyBatis、Mybatis-Plus。
服务配置、服务注册与发现：Nacos。
消息中间件：RocketMQ。
网络通信：Netty。
文件存储：Minio。
日志可视化治理：ELK。
容器化管理：Swarm、Portainer。
监控：Prometheus、Grafana。
前端：Vue。
单元测试：Junit。
基准测试：JMH。
压力测试：JMeter。

# ----cakeim项目设答-----

## 1. 除了 Redis，你还知道其他分布式缓存方案吗？

如果面试中被问到这个问题的话，面试官主要想看看：

- 你在选择 Redis 作为分布式缓存方案时，是否是经过严谨的调研和思考，还是只是因为 Redis 是当前的“热门”技术。
- 你在分布式缓存方向的技术广度。

如果你了解其他方案，并且能解释为什么最终选择了 Redis（更进一步！），这会对你面试表现加分不少！
下面简单聊聊常见的分布式缓存技术选型。

- 分布式缓存的话，比较老牌同时也是使用的比较多的还是 Memcached 和 Redis。不过，现在基本没有看过还有项目使用 Memcached 来做缓存，都是直接用 Redis。Memcached 是分布式缓存最开始兴起的那会，比较常用的。后来，随着 Redis 的发展，大家慢慢都转而使用更加强大的 Redis 了。
- 有一些大厂也开源了类似于 Redis 的分布式高性能 KV 存储数据库，例如，腾讯开源的 Tendis 。Tendis 基于知名开源项目 RocksDB 作为存储引擎 ，100% 兼容 Redis 协议和 Redis4.0 所有数据模型。关于 Redis 和 Tendis 的对比，腾讯官方曾经发过一篇文章：Redis vs Tendis：冷热混合存储版架构揭秘 ，可以简单参考一下。
  - 不过，从 Tendis 这个项目的 Github 提交记录可以看出，Tendis 开源版几乎已经没有被维护更新了，加上其关注度并不高，使用的公司也比较少。因此，不建议你使用 Tendis 来实现分布式缓存。

目前，比较业界认可的 Redis 替代品还是下面这两个开源分布式缓存（都是通过碰瓷 Redis 火的）：

1. Dragonfly：一种针对现代应用程序负荷需求而构建的内存数据库，完全兼容 Redis 和 Memcached 的 API，迁移时无需修改任何代码，号称全世界最快的内存数据库。
2. KeyDB： Redis 的一个高性能分支，专注于多线程、内存效率和高吞吐量。

不过，个人还是建议分布式缓存首选 Redis ，毕竟经过这么多年的生考验，生态也这么优秀，资料也很全面！
PS：篇幅问题，我这并没有对上面提到的分布式缓存选型做详细介绍和对比，感兴趣的话，可以自行研究一下

### Redis和Memcached区别和共同点

#### 共同点

1. 都是**基于内存**的数据库，一般都用来当做缓存使用。
2. 都有**过期策略。**
3. 两者的性**能都非常高。**

#### 区别

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

1. 你的mq用来做什么的

2. 即时通讯系统，万人群怎么解决写扩散问题？读扩散和写扩散？

3. 在线和没在线的用户有没有考虑

4. 如何保证mq顺序消费

5. 1.消息存储中，内容表和索引表如果需要分库处理，应该按什么字段来哈希？ 索引表可以和内容表合并成一个表吗？
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
   对，限流阈值需要模拟压测，避免由于阈值设置太宽松导致服务仍然可能被拖死或者阈值太敏感导致一点抖动也会整体熔断。

6. 

# RabbitMq

## 可靠性

1. 添加消息到队列中，保证消息成功的添加到了队列
2. 保证消息发送出去，一定会被消费者正常消费
3. 消费者正常消费了，生产者或者队列 知道消费者已经成功的消费了消息

### 问题一解决方案

- 通过AMQP事务机制实现，这也是AMQP协议层面提供的解决方案；（影响性能）
- 通过将channel设置成confirm模式来实现；

#### Confirm消息确认模式

confirm机制： 只要放消息到 queue 是成功的，那么队列就一定会反馈，表示发送成功；并且，只要是成功的发送到队列中，就会反馈成功，就算是在队列中，消费者接收失败，也会反馈成功！

##### 实现原理

​        生产者将channel设置成confirm模式，一旦信道进入confirm模式，所有在该channel上面发布的消息都会被指派一个唯一的ID(从1开始)；

​        一旦消息被投递到所有匹配的队列之后，RabbitMQ就会发送一个确认消息给生产者（包含消息的唯一ID）,这就使得生产者知道消息已经正确到达目的队列了；

​        如果消息和队列是可持久化的，那么确认消息会将消息写入磁盘之后发出，broker回传给生产者的确认消息中deliver-tag域包含了确认消息的序列号，此外broker也可以设置basic.ack的multiple域，表示到这个序列号之前的所有消息都已经得到了处理。

##### 优点

- 支持异步，一旦发布一条消息，生产者应用程序就可以在等channel返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果RabbitMQ因为自身内部错误导致消息丢失，就会发送一条nack消息，生产者应用程序同样可以在回调方法中处理该nack消息。
- 在channel 被设置成 confirm 模式之后，所有被 publish 的后续消息都将被 confirm（即 ack） 或者被nack一次。但是没有对消息被 confirm 的快慢做任何保证，并且同一条消息不会既被 confirm又被nack 。

### 问题二解决方案

使用 RabbitMQ中的 ACK 机制，就是手动确认消息。

消费者在声明队列时，可以指定noAck参数，当noAck=false时，RabbitMQ会等待消费者显式发回ack信号后才从内存(或者磁盘，持久化消息)中移去消息。否则，消息被消费后会被立即删除。

消费者接收每一条消息后都必须进行确认（消息接收和消息确认是两个不同操作）。只有消费者确认了消息，RabbitMQ 才能安全地把消息从队列中删除。

RabbitMQ不会为未ack的消息设置超时时间，它判断此消息是否需要重新投递给消费者的唯一依据是消费该消息的消费者连接是否已经断开。这么设计的原因是RabbitMQ允许消费者消费一条消息的时间可以很长。保证数据的最终一致性；

如果消费者返回ack之前断开了链接，RabbitMQ 会重新分发给下一个订阅的消费者。（可能存在消息重复消费的隐患，需要去重）；

### 问题三解决方案

和问题2的解决方法一致，在手动签收消息之后做手动告诉生产者已经消费成功

## 持久化

可以进行消息持久化, 即使rabbitMQ挂了，重启后也能恢复数据

如果要进行消息持久化，那么需要对以下3种实体均配置持久化

a) Exchange

声明exchange时设置持久化（durable = true）并且不自动删除(autoDelete = false)

b) Queue

声明queue时设置持久化（durable = true）并且不自动删除(autoDelete = false)

c) message

发送消息时通过设置deliveryMode=2持久化消息









































