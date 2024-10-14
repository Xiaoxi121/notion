#   ssm

https://www.wolai.com/v5Kuct5ZtPeVBk4NBUGBWF

尚硅谷在线笔记

## Maven

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\maven大纲目录.png)

### Maven简介和快速入门

作用：项目管理、依赖管理

1. **依赖管理：**

    Maven 可以管理项目的依赖，包括自动下载所需依赖库、自动下载依赖需要的依赖并且保证版本没有冲突、依赖版本管理等。通过 Maven，我们可以方便地维护项目所依赖的外部库，而我们仅仅需要编写配置即可。
2. **构建管理：**

    项目构建是指将源代码、配置文件、资源文件等转化为能够运行或部署的应用程序或库的过程！

    Maven 可以管理项目的编译、测试、打包、部署等构建过程。通过实现标准的构建生命周期，Maven 可以确保每一个构建过程都遵循同样的规则和最佳实践。同时，Maven 的插件机制也使得开发者可以对构建过程进行扩展和定制。主动触发构建，只需要简单的命令操作即可。

    ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image.png)

#### 1.1梳理Maven工程GAVP属性

  > Maven工程相对之前的工程，多出一组gavp属性，gav需要我们在创建项目的时指定，p有默认值，后期通过配置文件修改。

  Maven 中的 GAVP 是指 GroupId、ArtifactId、Version、Packaging 等四个属性的缩写，其中前三个是必要的，而 Packaging 属性为可选项。这四个属性主要为每个项目在maven仓库总做一个标识，类似人的《姓-名》。有了具体标识，方便maven软件对项目进行管理和互相引用！

  **GAV遵循规则：**

    1） **GroupID 格式**：com.{公司/BU }.业务线.[子业务线]，最多 4 级。
    
      说明：{公司/BU} 例如：alibaba/taobao/tmall/aliexpress 等 BU 一级；子业务线可选。
    
      正例：com.taobao.tddl 或 com.alibaba.sourcing.multilang  com.atguigu.java
    
    2） **ArtifactID 格式**：产品线名-模块名。语义不重复不遗漏，先到仓库中心去查证一下。
    
      正例：tc-client / uic-api / tair-tool / bookstore
    
    3） **Version版本号格式推荐**：主版本号.次版本号.修订号 1.0.0
    
      1） 主版本号：当做了不兼容的 API 修改，或者增加了能改变产品方向的新功能。
    
      2） 次版本号：当做了向下兼容的功能性新增（新增类、接口等）。
    
      3） 修订号：修复 bug，没有修改方法签名的功能加强，保持 API 兼容性。
    
      例如： 初始→1.0.0  修改bug → 1.0.1  功能调整 → 1.1.1等

  **Packaging定义规则：**

    指示将项目打包为什么类型的文件，idea根据packaging值，识别maven项目类型！
    
    packaging 属性为 jar（默认值），代表普通的Java工程，打包以后是.jar结尾的文件。
    
    packaging 属性为 war，代表Java的web工程，打包以后.war结尾的文件。
    
    packaging 属性为 pom，代表不会打包，用来做继承的父工程。

#### 1.2 Maven工程项目结构说明

  Maven 是一个强大的构建工具，它提供一种标准化的项目结构，可以帮助开发者更容易地管理项目的依赖、构建、测试和发布等任务。以下是 Maven Web 程序的文件结构及每个文件的作用：

```XML
|-- pom.xml                               # Maven 项目管理文件 
|-- src
    |-- main                              # 项目主要代码
    |   |-- java                          # Java 源代码目录
    |   |   `-- com/example/myapp         # 开发者代码主目录
    |   |       |-- controller            # 存放 Controller 层代码的目录
    |   |       |-- service               # 存放 Service 层代码的目录
    |   |       |-- dao                   # 存放 DAO 层代码的目录
    |   |       `-- model                 # 存放数据模型的目录
    |   |-- resources                     # 资源目录，存放配置文件、静态资源等
    |   |   |-- log4j.properties          # 日志配置文件
    |   |   |-- spring-mybatis.xml        # Spring Mybatis 配置文件
    |   |   `-- static                    # 存放静态资源的目录
    |   |       |-- css                   # 存放 CSS 文件的目录
    |   |       |-- js                    # 存放 JavaScript 文件的目录
    |   |       `-- images                # 存放图片资源的目录
    |   `-- webapp                        # 存放 WEB 相关配置和资源
    |       |-- WEB-INF                   # 存放 WEB 应用配置文件
    |       |   |-- web.xml               # Web 应用的部署描述文件
    |       |   `-- classes               # 存放编译后的 class 文件
    |       `-- index.html                # Web 应用入口页面
    `-- test                              # 项目测试代码
        |-- java                          # 单元测试目录
        `-- resources                     # 测试资源目录
```

  - pom.xml：Maven 项目管理文件，用于描述项目的依赖和构建配置等信息。
  - src/main/java：存放项目的 Java 源代码。
  - src/main/resources：存放项目的资源文件，如配置文件、静态资源等。
  - src/main/webapp/WEB-INF：存放 Web 应用的配置文件。
  - src/main/webapp/index.html：Web 应用的入口页面。
  - src/test/java：存放项目的测试代码。
  - src/test/resources：存放测试相关的资源文件，如测试配置文件等。

### maven核心功能依赖和构建管理

#### 2.1 依赖管理和配置

  Maven 依赖管理是 Maven 软件中最重要的功能之一。Maven 的依赖管理能够帮助开发人员自动解决软件包依赖问题，使得开发人员能够轻松地将其他开发人员开发的模块或第三方框架集成到自己的应用程序或模块中，避免出现版本冲突和依赖缺失等问题。

  我们通过定义 POM 文件，Maven 能够自动解析项目的依赖关系，并通过 Maven **仓库自动**下载和管理依赖，从而避免了手动下载和管理依赖的繁琐工作和可能引发的版本冲突问题。

  重点: 编写pom.xml文件!

#####   maven项目信息属性配置和读取：

```XML
<!-- 模型版本 -->
<modelVersion>4.0.0</modelVersion>
<!-- 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成， 如com.companyname.project-group，maven会将该项目打成的jar包放本地路径：/com/companyname/project-group -->
<groupId>com.companyname.project-group</groupId>
<!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->
<artifactId>project</artifactId>
<!-- 版本号 -->
<version>1.0.0</version>

<!--打包方式
    默认：jar
    jar指的是普通的java项目打包方式！ 项目打成jar包！
    war指的是web项目打包方式！项目打成war包！
    pom不会讲项目打包！这个项目作为父工程，被其他工程聚合或者继承！后面会讲解两个概念
-->
<packaging>jar/pom/war</packaging>
```

#####   依赖管理和添加：

```XML
<!-- 
   通过编写依赖jar包的gav必要属性，引入第三方依赖！
   scope属性是可选的，可以指定依赖生效范围！
   依赖信息查询方式：
      1. maven仓库信息官网 https://mvnrepository.com/
      2. mavensearch插件搜索
 -->
<dependencies>
    <!-- 引入具体的依赖包 -->
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
        <!--
            生效范围
            - compile ：main目录 test目录  打包打包 [默认]
            - provided：main目录 test目录  Servlet
            - runtime： 打包运行           MySQL
            - test:    test目录           junit
         -->
        <scope>runtime</scope>
    </dependency>

</dependencies>
```

#####   依赖版本提取和维护:

```XML
<!--声明版本-->
<properties>
  <!--命名随便,内部制定版本号即可！-->
  <junit.version>4.11</junit.version>
  <!-- 也可以通过 maven规定的固定的key，配置maven的参数！如下配置编码格式！-->
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
</properties>

<dependencies>
  <dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <!--引用properties声明版本 -->
    <version>${junit.version}</version>
  </dependency>
</dependencies>
```

#### 2.2 依赖传递和依赖冲突

##### **依赖传递**

​	指的是当一个模块或库 A 依赖于另一个模块或库 B，而 B 又依赖于模块或库 C，那么 A 会间接依赖于 C。这种依赖传递结构可以形成一个依赖树。当我们引入一个库或框架时，构建工具（如 Maven、Gradle）会自动解析和加载其所有的直接和间接依赖，确保这些依赖都可用。

依赖传递的作用是：

1. 减少重复依赖：当多个项目依赖同一个库时，Maven 可以自动下载并且只下载一次该库。这样可以减少项目的构建时间和磁盘空间。
2. 自动管理依赖: Maven 可以自动管理依赖项，使用依赖传递，简化了依赖项的管理，使项目构建更加可靠和一致。
3. 确保依赖版本正确性：通过依赖传递的依赖，之间都不会存在版本兼容性问题，确实依赖的版本正确性！

##### **依赖冲突**

​	当直接引用或者间接引用出现了相同的jar包! 这时呢，一个项目就会出现相同的重复jar包，这就算作冲突！依赖冲突避免出现重复依赖，并且终止依赖传递！

解决依赖冲突（如何选择重复依赖）方式：

自动选择原则
- 短路优先原则（第一原则）

    A—>B—>C—>D—>E—>X(version 0.0.1)

    A—>F—>X(version 0.0.2)

    则A依赖于X(version 0.0.2)。
- 依赖路径长度相同情况下，则“先声明优先”（第二原则）

    A—>E—>X(version 0.0.1)

    A—>F—>X(version 0.0.2)

    在<depencies></depencies>中，先声明的，路径相同，会优先选择！

<img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240317171042659.png" alt="image-20240317171042659" style="zoom: 80%;" />

#### 2.3 依赖导入失败和解决方案

#### 2.4 扩展构建管理和插件配置

##### **构建概念:**

项目构建是指将**源代码、依赖库和资源文件**等转换成可执行或可部署的应用程序的过程，在这个过程中包括**编译源代码、链接依赖库、打包和部署**等多个步骤。

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710667257953-2.png)

##### **主动触发场景：**

- 重新编译 : 编译不充分, 部分文件没有被编译!
- 打包 : 独立部署到外部服务器软件,打包部署
- 部署本地或者私服仓库 : maven工程加入到本地或者私服仓库,供其他工程使用

##### **命令方式构建:**

- 命令执行需要我们进入项目的根路径，pom.cm平级
- 部署 必须是jar包形式

语法: mvn 构建命令  构建命令....

| 命令        | 描述                                        |
| ----------- | ------------------------------------------- |
| mvn clean   | 清理编译或打包后的项目结构,删除target文件夹 |
| mvn compile | 编译项目，生成target文件                    |
| mvn test    | 执行测试源码 (测试)                         |
| mvn site    | 生成一个项目依赖信息的展示页面              |
| mvn package | 打包项目，生成war / jar 文件                |
| mvn install | 打包后上传到maven本地仓库(本地部署)         |
| mvn deploy  | 只打包，上传到maven私服仓库(私服部署)       |

##### **可视化方式构建:**

![](https://secure2.wostatic.cn/static/85Cv2rhHoFN4C9JgJzVdho/image.png?auth_key=1710667170-cMbLiWXBjpJjxcsjGtn4iC-0-0ad4e2346701d2a0060b6c0b42ec4f5f)

##### **构建命令周期:**

​	构建生命周期可以理解成是一组固定构建命令的有序集合，触发周期后的命令，会自动触发周期前的命令！也是一种简化构建的思路!

- 清理周期：主要是对项目编译生成文件进行清理

    包含命令：clean
- 默认周期：定义了真正构件时所需要执行的所有步骤，它是生命周期中最核心的部分

    包含命令：compile - test - package - install / deploy
- 报告周期

    包含命令：site

    打包: mvn clean package 
    
    ​	本地仓库: mvn clean install

##### **最佳使用方案:**

```text
打包: mvn clean package
重新编译: mvn clean compile
本地部署: mvn clean install 
```

##### **周期，命令和插件:**

周期→包含若干命令→包含若干插件!	使用周期命令构建，简化构建过程！	最终进行构建的是插件！

war 插件配置:

```XML
<build>
   <!-- jdk17 和 war包版本插件不匹配 -->
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.2.2</version>
        </plugin>
    </plugins>
</build>
```

### maven继承和聚合特性

#### maven工程继承关系

Maven 继承是指在 Maven 的项目中，让一个项目从另一个项目中**继承配置信息的机制**。继承可以让我们在多个项目中共享同一配置信息，简化项目的管理和维护工作。

##### 继承作用

​	作用：在父工程中统一管理项目中的依赖信息,进行统一版本管理!

​	它的背景是：

- 对一个比较大型的项目进行了模块拆分。
- 一个 project 下面，创建了很多个 module。
- 每一个 module 都需要配置自己的依赖信息。

​	它背后的需求是：

- 多个模块要使用同一个框架，它们应该是同一个版本，所以整个项目中使用的**框架版本需要统一管理。**
- 使用框架时所需要的 jar 包组合（或者说依赖信息组合）需要经过长期摸索和反复调试，最终确定一个可用组合。这个耗费很大精力总结出来的方案不应该在新的项目中重新摸索。

​	通过在父工程中为整个项目维护依赖信息的组合既保证了整个项目使用规范、准确的 jar 包；又能够将以往的经验沉淀下来，节约时间和精力。

##### 继承语法

- 父工程

```XML
<groupId>com.atguigu.maven</groupId>
<artifactId>pro03-maven-parent</artifactId>
<version>1.0-SNAPSHOT</version>
<!-- 当前工程作为父工程，它要去管理子工程，所以打包方式必须是 pom -->
<packaging>pom</packaging>
<!-- 父工程不打包 也不写代码 -->
```
- 子工程

```XML
<!-- 使用parent标签指定当前工程的父工程gav属性-->
<parent>
  <!-- 父工程的坐标 -->
  <groupId>com.atguigu.maven</groupId>
  <artifactId>pro03-maven-parent</artifactId>
  <version>1.0-SNAPSHOT</version>
</parent>

<!-- 子工程的坐标 -->
<!-- 如果子工程坐标中的groupId和version与父工程一致，那么可以省略 -->
<!-- <groupId>com.atguigu.maven</groupId> -->
<artifactId>pro04-maven-module</artifactId>
<!-- <version>1.0-SNAPSHOT</version> -->
```
2. 父工程依赖统一管理
    - 父工程声明版本

```XML
<!-- 使用dependencyManagement标签配置对依赖的管理 -->
<!-- 被管理的依赖并没有真正被引入到工程 -->
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-expression</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>4.0.0.RELEASE</version>
    </dependency>
  </dependencies>
</dependencyManagement>
```
- 子工程引用版本

```XML
<!-- 子工程引用父工程中的依赖信息时，可以把版本号去掉。  -->
<!-- 把版本号去掉就表示子工程中这个依赖的版本由父工程决定。 -->
<!-- 具体来说是由父工程的dependencyManagement来决定。 -->
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-beans</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-expression</artifactId>
  </dependency>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
  </dependency>
</dependencies>
```

#### maven工程聚合关系

1. 聚合概念

    Maven 聚合是指将多个项目组织到一个父级项目中，**通过触发父工程的构建,统一按顺序触发子工程构建的过程!!**
2. 聚合作用
    1. 统一管理子项目构建：通过聚合，可以将多个子项目组织在一起，方便管理和维护。
    2. 优化构建顺序：通过聚合，可以对多个项目进行顺序控制，避免出现构建依赖混乱导致构建失败的情况。
3. 聚合语法

    父项目中包含的子项目列表。

```XML
<project>
  <groupId>com.example</groupId>
  <artifactId>parent-project</artifactId>
  <packaging>pom</packaging>
  <version>1.0.0</version>
  <modules>
    <module>child-project1</module>
    <module>child-project2</module>
  </modules>
</project>
```
4. 聚合演示

    通过触发父工程构建命令、引发所有子模块构建！产生反应堆！

    ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710765257085-1.png)

### Maven核心掌握总结

|            |                                                    |
| ---------- | -------------------------------------------------- |
| 核心点     | 掌握目标                                           |
| 安装       | maven安装、环境变量、maven配置文件修改             |
| 工程创建   | gavp属性理解、JavaSE/EE工程创建、项目结构          |
| 依赖管理   | 依赖添加、依赖传递、版本提取、导入依赖错误解决     |
| 构建管理   | 构建过程、构建场景、构建周期等                     |
| 继承和聚合 | 理解继承和聚合作用、继承语法和实践、聚合语法和实践 |

## SpringFrameWork

### 总体技术体系
  - 单一架构

      一个项目，一个工程，导出为一个war包，在一个Tomcat上运行。也叫all in one。

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710765686508-4.png)

      单一架构，项目主要应用技术框架为：Spring , SpringMVC , Mybatis
  - 分布式架构

      一个项目（对应 IDEA 中的一个 project），拆分成很多个模块，每个模块是一个 IDEA 中的一个 module。每一个工程都是运行在自己的 Tomcat 上。模块之间可以互相调用。每一个模块内部可以看成是一个单一架构的应用。

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710765686508-5.png)

      分布式架构，项目主要应用技术框架：SpringBoot (SSM), SpringCloud , 中间件等

站在文件结构的角度理解框架，可以将框架总结：**框架 = jar包+配置文件**

<img src="https://secure2.wostatic.cn/static/mVVMygbUkcQx9Loxs21XdC/image.png?auth_key=1710765667-41JVqQM3B4L55BcgxAjVr3-0-937b720fd556ad43ede9e7684b77a8da" style="zoom: 50%;" />

#### Spring和SpringFramework概念

#### SpringFramework主要功能

SpringFramework框架结构图：

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710766345623-10.png)

| 功能模块       | 功能介绍                                                    |
| -------------- | ----------------------------------------------------------- |
| Core Container | 核心容器，在 Spring 环境下使用任何功能都必须基于 IOC 容器。 |
| AOP&Aspects    | 面向切面编程                                                |
| TX             | 声明式事务管理。                                            |
| Spring MVC     | 提供了面向Web应用程序的集成功能。                           |

#### SpringFramework主要优势

### SpringIoC容器和核心概念

#### 组件和组件管理

- **什么是组件?**

  回顾常规的三层架构处理请求流程：

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710766702272-13.png)

  整个项目就是由各种组件搭建而成的：

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710766702273-14.png)

Spring具体的组件管理动作包含：

- 组件对象实例化
- 组件属性属性赋值g'f'j'd'k两个接口的分隔符打开。
- 组件对象之间引用
- 组件对象存活周期管理

我们只需要编写元数据（配置文件）告知Spring 管理哪些类组件和他们的关系即可！注意：**组件是映射到应用程序中所有可重用组件的Java对象，应该是可复用的功能对象！**

- 组件一定是对象
- 对象不一定是组件

综上所述，Spring 充当一个组件容器，创建、管理、存储组件，减少了我们的编码压力，让我们更加专注进行业务编写！

**组件交给Spring管理优势**：

1. 降低了组件之间的耦合性：Spring IoC容器通过依赖注入机制，将组件之间的依赖关系削弱，减少了程序组件之间的耦合性，使得组件更加松散地耦合。
2. 提高了代码的可重用性和可维护性：将组件的实例化过程、依赖关系的管理等功能交给Spring IoC容器处理，使得组件代码更加模块化、可重用、更易于维护。
3. 方便了配置和管理：Spring IoC容器通过XML文件或者注解，轻松的对组件进行配置和管理，使得组件的切换、替换等操作更加的方便和快捷。
4. **交给Spring管理的对象（组件），方可享受Spring框架的其他功能（AOP,声明事务管理）等**

#### SpringIoc（Inversion of Control控制反转）

### SpringIoc容器和容器实现

​	Spring管理组件的容器，就是一个复杂容器，不仅存储组件，也可以管理组件之间依赖关系，并且创建和销毁组件等！

#### **SpringIoc容器接口**： 

​	`BeanFactory` 接口提供了一种高级配置机制，能够管理任何类型的对象，它是SpringIoC容器标准化超接口！`	ApplicationContext` 是 `BeanFactory` 的子接口。它扩展了以下功能：

- 更容易与 Spring 的 AOP 功能集成
- 消息资源处理（用于国际化）
- 特定于应用程序给予此接口实现，例如Web 应用程序的 `WebApplicationContext`

​	简而言之， `BeanFactory` 提供了配置框架和基本功能，而 `ApplicationContext` 添加了更多特定于企业的功能。 `ApplicationContext` 是 `BeanFactory` 的完整超集！

#### **ApplicationContext容器实现类**：

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img004.f6680aef.png)

| 类型名                             | 简介                                                         |
| ---------------------------------- | :----------------------------------------------------------- |
| ClassPathXmlApplicationContext     | 通过读取类路径下的 XML 格式的配置文件创建 IOC 容器对象       |
| FileSystemXmlApplicationContext    | 通过文件系统路径读取 XML 格式的配置文件创建 IOC 容器对象     |
| AnnotationConfigApplicationContext | 通过读取Java配置类创建 IOC 容器对象                          |
| WebApplicationContext              | 专门为 Web 应用准备，基于 Web 环境创建 IOC 容器对象，并将对象引入存入 ServletContext 域中。 |

![image-20240318221228215](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240318221228215.png)

![image-20240319161526682](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240319161526682.png)

#### **SpringIoC容器管理配置方式**

Spring IoC 容器使用多种形式的配置元数据。此配置元数据表示您作为应用程序开发人员如何告诉 Spring 容器实例化、配置和组装应用程序中的对象。

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710771201078-21.png)

Spring框架提供了多种配置方式：XML配置方式、注解方式和Java配置类方式

1. XML配置方式：是Spring框架最早的配置方式之一，通过在XML文件中定义Bean及其依赖关系、Bean的作用域等信息，让Spring IoC容器来管理Bean之间的依赖关系。该方式从Spring框架的第一版开始提供支持。
2. 注解方式：从Spring 2.5版本开始提供支持，可以通过在Bean类上使用注解来代替XML配置文件中的配置信息。通过在Bean类上加上相应的注解（如@Component, @Service, @Autowired等），将Bean注册到Spring IoC容器中，这样Spring IoC容器就可以管理这些Bean之间的依赖关系。
3. **Java配置类**方式：从Spring 3.0版本开始提供支持，通过Java类来定义Bean、Bean之间的依赖关系和配置信息，从而代替XML配置文件的方式。Java配置类是一种使用Java编写配置信息的方式，通过@Configuration、@Bean等注解来实现Bean和依赖关系的配置。

为了迎合当下开发环境，我们将以**配置类+注解方式**为主进行讲解！

#### Spring IoC / DI概念总结

  - **IoC容器**

    Spring IoC 容器，负责实例化、配置和组装 bean（组件）核心容器。容器通过读取配置元数据来获取有关要实例化、配置和组装组件的指令。

  - **IoC（Inversion of Control）控制反转**

    IoC 主要是针对对象的创建和调用控制而言的，也就是说，当应用程序需要使用一个对象时，**不再是应用程序直接创建该对象，而是由 IoC 容器来创建和管理，即控制权由应用程序转移到 IoC 容器中**，也就是“反转”了控制权。这种方式基本上是通过依赖查找的方式来实现的，即 IoC 容器维护着构成应用程序的对象，并负责创建这些对象。

  - **DI (Dependency Injection) 依赖注入**

    **DI 是指在组件之间传递依赖关系的过程中，将依赖关系在容器内部进行处理，这样就不必在应用程序代码中硬编码对象之间的依赖关系**，实现了对象之间的解耦合。在 Spring 中，DI 是通过 XML 配置文件或注解的方式实现的。它提供了三种形式的依赖注入：**构造函数注入、Setter 方法注入和接口注入。**

### Spring IoC实践和应用

#### 基于xml配置方式组件管理

##### Spring IoC/DI实现步骤

组件的信息+ioc的配置-》applicationContext读取-》实例化对象

1. **配置元数据（配置）** （**写配置文件信息  xml 注解 配置 包含组件类信息、组件之间的引用关系）**配置元数据，既是编写交给SpringIoC容器管理组件的信息，配置方式有三种。 

    ​	基于 XML 的配置元数据的基本结构：

    ​		<bean id="..." [1] class="..." [2]>  
    ​		<!-- collaborators and configuration for this bean go here -->
    ​		  </bean>

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!-- 此处要添加一些约束，配置文件的标签并不是随意命名 -->
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.springframework.org/schema/beans
    https://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="..." [1] class="..." [2]>  
    <!-- collaborators and configuration for this bean go here -->
  </bean>

  <bean id="..." class="...">
    <!-- collaborators and configuration for this bean go here -->
  </bean>
  <!-- more bean definitions go here -->
</beans>
```

    Spring IoC 容器管理一个或多个组件。这些 组件是使用你提供给容器的配置元数据（例如，以 XML `<bean/>` 定义的形式）创建的。
    
    <bean /> 标签 == 组件信息声明
    
    - `id` 属性是标识单个 Bean 定义的字符串。
    - `class` 属性定义 Bean 的类型并使用完全限定的类名。
2. **实例化IoC容器**

    提供给 `ApplicationContext` 构造函数的位置路径是资源字符串地址，允许容器从各种外部资源（如本地文件系统、Java `CLASSPATH` 等）加载配置元数据。

    我们应该选择一个合适的容器实现类，进行IoC容器的实例化工作：

```Java
//实例化ioc容器,读取外部配置文件,最终会在容器内进行ioc和di动作
ApplicationContext context = 
           new ClassPathXmlApplicationContext("services.xml", "daos.xml");
```
3. **获取Bean（组件）**

    `ApplicationContext` 是一个高级工厂的接口，能够维护不同 bean 及其依赖项的注册表。通过使用方法 `T getBean(String name, Class<T> requiredType)` ，您可以检索 bean 的实例。

    允许读取 Bean 定义并访问它们，如以下示例所示：

```Java
//创建ioc容器对象，指定配置文件，ioc也开始实例组件对象
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
//获取ioc容器的组件对象
PetStoreService service = context.getBean("petStore", PetStoreService.class);
//使用组件对象
List<String> userList = service.getUsernameList();
```

##### 1.组件（Bean）信息声明配置（IoC）
  1. 目标

      Spring IoC 容器管理一个或多个 bean。这些 Bean 是使用您提供给容器的配置元数据创建的（例如，以 XML `<bean/>` 定义的形式）。我们学习，**如何通过定义XML配置文件，声明组件类信息，交给 Spring 的 IoC 容器进行组件管理！**

  2. 思路

      <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240319152341425.png" alt="image-20240319152341425"  />

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img006.c8bae859.png)

  3. 准备项目
      1. 创建maven工程（spring-ioc-xml-01）
      2. 导入SpringIoC相关依赖

          pom.xml

```XML
<dependencies>
    <!--spring context依赖-->
    <!--当你引入Spring Context依赖之后，表示将Spring的基础依赖引入了-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>6.0.6</version>
    </dependency>
    <!--junit5测试-->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.3.1</version>
    </dependency>
</dependencies>
```
<img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240318223731380.png" alt="image-20240318223731380" style="zoom:67%;" />

  4. 基于无参数构造函数

      > 当通过构造函数方法创建一个 bean（组件对象） 时，所有普通类都可以由 Spring 使用并与之兼容。也就是说，正在开发的类不需要实现任何特定的接口或以特定的方式进行编码。只需指定 Bean 类信息就足够了。但是，默认情况下，我们需要一个默认（空）构造函数。

      1. 准备组件类

```Java
package com.atguigu.ioc;


public class HappyComponent {

    //默认包含无参数构造函数

    public void doWork() {
        System.out.println("HappyComponent.doWork");
    }
}
```
      2. xml配置文件编写
    
          创建携带spring约束的xml配置文件
    
          ![](https://secure2.wostatic.cn/static/7eC1WeTyXz1oLaGkPfJBfh/image.png?auth_key=1710772407-benNZfW1S1xY2RfnLA4S3R-0-d6bfc8d9a8085458f1d4daf2988ff0fd)
    
          编写配置文件：
    
          文件：resources/spring-bean-01.xml

```Java
<!-- 实验一 [重要]创建bean -->
<bean id="happyComponent" class="com.atguigu.ioc.HappyComponent"/>
```

          - bean标签：通过配置bean标签告诉IOC容器需要创建对象的组件信息
          - id属性：bean的唯一标识,方便后期获取Bean！
          - class属性：组件类的全限定符！
          - 注意：要求当前组件类必须包含无参数构造函数！
![image-20240319150752816](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240319150752816.png)

  5. 基于静态工厂方法实例化

      > 除了使用构造函数实例化对象，还有一类是通过工厂模式实例化对象。接下来我们讲解如何定义使用静态工厂方法创建Bean的配置 ！

      1. 准备组件类

```Java
public class ClientService {
  private static ClientService clientService = new ClientService();
  private ClientService() {}

  public static ClientService createInstance() {
  
    return clientService;
  }
}
```
      2. xml配置文件编写
    
          文件：resources/spring-bean-01.xml

```XML
<bean id="clientService"
  class="examples.ClientService"
  factory-method="createInstance"/>
```


          - class属性：指定工厂类的全限定符！
          - factory-method: 指定静态工厂方法，注意，该方法必须是static方法。
  6. 基于实例工厂（非静态工厂）方法实例化

      > 接下来我们讲解下如何定义使用实例工厂方法创建Bean的配置 ！

      1. 准备组建类

```Java
public class DefaultServiceLocator {

  private static ClientServiceImplclientService = new ClientServiceImpl();

  public ClientService createClientServiceInstance() {
    return clientService;
  }
}
```
      2. xml配置文件编写
    
          文件：resources/spring-bean-01.xml

```XML
<!-- 将工厂类进行ioc配置 -->
<bean id="serviceLocator" class="examples.DefaultServiceLocator">
</bean>

<!-- 根据工厂对象的实例工厂方法进行实例化组件对象 -->
<bean id="clientService"
  factory-bean="serviceLocator"
  factory-method="createClientServiceInstance"/>
```

```
      - factory-bean属性：指定当前容器中工厂Bean 的名称。
      - factory-method:  指定实例工厂方法名。注意，实例方法必须是非static的！
```
  7. 图解IoC配置流程

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710772438009-24.png)

##### 2.组件（Bean）依赖注入配置（DI）

springioc容器是一个高级容器，内部会有缓存动作

- 先创建ioc对象
- 再进行属性赋值di
- 所以写代码的时候这两个哪个在前哪个在后无所谓

  1. 目标

      通过配置文件,实现IoC容器中Bean之间的引用（依赖注入DI配置）。

      主要涉及注入场景：基于构造函数的依赖注入和基于 Setter 的依赖注入。
  2. 思路

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710833206607-1.png)
  3. 基于构造函数的依赖注入（单个构造参数）
      1. 介绍

          基于构造函数的 DI  是通过 容器 调用具有 **多个参数 的 构造函数**来完成的，**每个参数表示一个依赖项**。下面的示例演示一个**只能通过构造函数注入进行依赖项注入的类**！

      2. 准备组件类

```Java
public class UserDao {
}


public class UserService {
    
    private UserDao userDao;

    public UserService(UserDao userDao) {
        this.userDao = userDao;
    }
}

```
      c. 编写配置文件
    
          文件：resources/spring-02.xml

```XML
<beans>
  <!-- 引用类bean声明 -->
  <bean id="userService" class="x.y.UserService">
   <!-- 构造函数引用 -->
    <constructor-arg ref="userDao"/>
  </bean>
  <!-- 被引用类bean声明 将其放在ioc容器中-->
  <bean id="userDao" class="x.y.UserDao"/>
</beans>
```

          - constructor-arg标签：可以引用构造参数 ref引用其他bean的标识。
         value = 直接属性值 String name ="1" int age = 18
         ref = 引用其他的bean beanId值
  4. 基于构造函数的依赖注入（多构造参数解析）
      1. 介绍

          基于构造函数的 DI 是通过容器调用具有多个参数的构造函数来完成的，每个参数表示一个依赖项。下面的示例演示通过构造函数注入多个参数，参数包含其他bean和基本数据类型！

      2. 准备组件类

```Java
public class UserDao {
}


public class UserService {
    
    private UserDao userDao;
    
    private int age;
    
    private String name;

    public UserService(int age , String name ,UserDao userDao) {
        this.userDao = userDao;
        this.age = age;
        this.name = name;
    }
}
```
      c. 编写配置文件

```XML
<!-- 场景1: 按照顺序注入数据 -->
<beans>
  <bean id="userService" class="x.y.UserService">
    <!-- value直接注入基本类型值 -->
    <constructor-arg  value="18"/>
    <constructor-arg  value="赵伟风"/>
    
    <constructor-arg  ref="userDao"/>
  </bean>
  <!-- 被引用类bean声明 将其放在ioc容器中-->
  <bean id="userDao" class="x.y.UserDao"/>
</beans>


<!-- 场景2: 按照名称填写注入数据 -->
<beans>
  <bean id="userService" class="x.y.UserService">
    <!-- value直接注入基本类型值 -->
    <constructor-arg name="name" value="赵伟风"/>
    <constructor-arg name="userDao" ref="userDao"/>
    <constructor-arg name="age"  value="18"/>
  </bean>
  <!-- 被引用类bean声明 -->
  <bean id="userDao" class="x.y.UserDao"/>
</beans>

<!-- 场景3: 按照角标注入数据 index从0开始 构造函数(0,1,2....)
-->
<beans>
    <bean id="userService" class="x.y.UserService">
    <!-- value直接注入基本类型值 -->
    <constructor-arg index="1" value="赵伟风"/>
    <constructor-arg index="2" ref="userDao"/>
    <constructor-arg index="0"  value="18"/>
  </bean>
  <!-- 被引用类bean声明 -->
  <bean id="userDao" class="x.y.UserDao"/>
</beans>

```
          - constructor-arg标签：指定构造参数和对应的值
          - constructor-arg标签：name属性指定参数名、index属性指定参数角标、value属性指定普通属性值
  5. **基于Setter方法依赖注入**
     
      1. 介绍
      
          **除了构造函数注入（DI）****更多的使用的Setter方法进行注入！**下面的示例演示一个**只能使用纯 setter 注入进行依赖项注入**的类。
      2. 准备组件类

```Java
public Class MovieFinder{
	//这个类也要放到容器中，因为下面用到了
}

public class SimpleMovieLister {

  private MovieFinder movieFinder;
  
  private String movieName;

  public void setMovieFinder(MovieFinder movieFinder) {
    this.movieFinder = movieFinder;
  }
  
  public void setMovieName(String movieName){
    this.movieName = movieName;
  }

  // business logic that actually uses the injected MovieFinder is omitted...
}
```
      c. 编写配置文件

```XML
<bean id="simpleMovieLister" class="examples.SimpleMovieLister">
 
    
  <!-- setter方法，注入movieFinder对象的标识id
       name = 属性名  ref = 引用bean的id值
   -->
  <property name="movieFinder" ref="movieFinder" />

  <!-- setter方法，注入基本数据类型movieName
       name = 属性名 value= 基本类型值
   -->
  <property name="movieName" value="消失的她"/>
</bean>

<bean id="movieFinder" class="examples.MovieFinder"/>
```
          - property标签： 可以给setter方法对应的属性赋值
          - property 标签： 
          		name属性代表set方法标识（setter方法的 去set和首字母小写的值 即调用set方法的名）
          			eg
          				方法名 setMovieFinder name：movieFinder
          		ref代表引用bean的标识id
          		value属性代表基本属性值

  **总结：**

    依赖注入（DI）包含引用类型和基本数据类型，同时注入的方式也有多种！主流的注入方式为setter方法注入和构造函数注入，两种注入语法都需要掌握！
    需要特别注意：引用其他bean，使用ref属性。直接注入基本类型值，使用value属性。

##### 3.IoC容器创建和使用
  1. 介绍

      上面的实验只是讲解了如何在XML格式的配置文件编写IoC和DI配置！

      如图：

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710835351619-4.png)

      想要配置文件中声明组件类信息真正的进行实例化成Bean对象和形成Bean之间的引用关系，我们需要声明IoC容器对象，读取配置文件，实例化组件和关系维护的过程都是在IoC容器中实现的！
  2. 容器实例化

```Java
//方式1:实例化并且指定配置文件
	//参数：String...locations 传入一个或者多个配置文件
	ApplicationContext context = 
           new ClassPathXmlApplicationContext("services.xml", "daos.xml");
           
//方式2:先实例化，再指定配置文件，最后刷新容器触发Bean实例化动作 
	//	[springmvc源码和contextLoadListener源码方式]  
	ApplicationContext context = 
           new ClassPathXmlApplicationContext();   
	//设置配置配置文件,方法参数为可变参数,可以设置一个或者多个配置
	iocContainer1.setConfigLocations("services.xml", "daos.xml");
	//后配置的文件,需要调用refresh方法,触发刷新配置
	iocContainer1.refresh();           
```
  3. Bean对象读取

```Java
//方式1: 根据id获取(不推荐)		没有指定类型,返回为Object,需要类型转化!
HappyComponent happyComponent = 
        (HappyComponent) iocContainer.getBean("bean的id标识");
        
//使用组件对象        
happyComponent.doWork();

//方式2: 根据类型获取
//根据类型获取,但是要求,同类型(当前类,或者之类,或者接口的实现类)只能有一个对象交给IoC容器管理
//配置两个或者以上出现: org.springframework.beans.factory.NoUniqueBeanDefinitionException 问题

HappyComponent happyComponent = iocContainer.getBean(HappyComponent.class);
happyComponent.doWork();

//方式3: 根据id和类型获取
HappyComponent happyComponent = iocContainer.getBean("bean的id标识", HappyComponent.class);
happyComponent.doWork();


！ioc的配置一定是实现类 但是可以根据接口类型获取值 
根据类型来获取bean时，在满足bean唯一性的前提下，其实只是看：『对象 instanceof 指定的类型』的返回结果，
只要返回的是true就可以认定为和类型匹配，能够获取到。
```

![image-20240319163045384](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240319163045384.png)

##### 4.高级特性：组件（Bean）作用域和周期方法配置

![image-20240319163445434](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240319163445434.png)

  1. 组件周期方法配置
      1. 周期方法概念

          我们可以在组件类中定义方法，然后当IoC容器实例化和销毁组件对象的时候进行调用！这两个方法我们称为生命周期方法！

          类似于Servlet的init/destroy方法,我们可以在周期方法完成初始化和释放资源等工作。
      2. 周期方法声明（是要求方法必须是 public void 无形参列表）

```Java
public class BeanOne {

  //周期方法要求： 方法命名随意，但是要求方法必须是 public void 无形参列表
  public void init() {
    // 初始化逻辑
  }
}

public class BeanTwo {

  public void cleanup() {
    // 释放资源逻辑
  }
}



正常结束ioc容器
    applicationContext.close()
```
      c. 周期方法配置

```XML
<beans>
  <bean id="beanOne" class="examples.BeanOne" init-method="init" />
  <bean id="beanTwo" class="examples.BeanTwo" destroy-method="cleanup" />
</beans>
```
  2. 组件作用域配置
      1. Bean作用域概念

          `<bean` 标签声明Bean，只是将Bean的信息配置给SpringIoC容器！在IoC容器中，这些`<bean`标签对应的信息**转成Spring内部 `BeanDefinition` 对象，`BeanDefinition` 对象内，包含定义的信息（id,class,属性等等）！**这意味着，`BeanDefinition`与`类`概念一样，SpringIoC容器可以可以根据`BeanDefinition`对象反射创建多个Bean对象实例。具体创建多少个Bean的实例对象，由Bean的作用域Scope属性指定！
      2. 作用域可选值

| 取值          | 含义                                            | 创建对象的时机       | 默认值 |
| ------------- | ----------------------------------------------- | -------------------- | ------ |
| **singleton** | **在 IOC 容器中，这个 bean 的对象始终为单实例** | **IOC 容器初始化时** | **是** |
| prototype     | 这个 bean 在 IOC 容器中有多个实例               | 获取 bean 时         | 否     |


          如果是在WebApplicationContext环境下还会有另外两个作用域（但不常用）：

| 取值    | 含义                 | 创建对象的时机 | 默认值 |
| ------- | -------------------- | -------------- | ------ |
| request | 请求范围内有效的实例 | 每次请求       | 否     |
| session | 会话范围内有效的实例 | 每次会话       | 否     |

      c. 作用域配置
    
          配置scope范围

```XML
<!--bean的作用域 
    准备两个引用关系的组件类即可！！
-->
<!-- scope属性：取值singleton（默认值），bean在IOC容器中只有一个实例，IOC容器初始化时创建对象 -->
<!-- scope属性：取值prototype，bean在IOC容器中可以有多个实例，getBean()时创建对象 -->
<bean id="happyMachine8" scope="prototype" class="com.atguigu.ioc.HappyMachine">
    <property name="machineName" value="happyMachine"/>
</bean>

<bean id="happyComponent8" scope="singleton" class="com.atguigu.ioc.HappyComponent">
    <property name="componentName" value="happyComponent"/>
</bean>
```
     d. 作用域测试

```Java
@Test
public void testExperiment08()  {
    ApplicationContext iocContainer = new ClassPathXmlApplicationContext("配置文件名");

    HappyMachine bean = iocContainer.getBean(HappyMachine.class);
    HappyMachine bean1 = iocContainer.getBean(HappyMachine.class);
    //多例对比 false
    System.out.println(bean == bean1);

    HappyComponent bean2 = iocContainer.getBean(HappyComponent.class);
    HappyComponent bean3 = iocContainer.getBean(HappyComponent.class);
    //单例对比 true
    System.out.println(bean2 == bean3);
}
```

##### 5.高级特性：FactoryBean特性和使用
  1. FactoryBean简介

      `FactoryBean` 接口是Spring IoC容器实例化逻辑的可插拔性点。用于配置复杂的Bean对象，可以将创建过程存储在`FactoryBean` 的getObject方法！

      `FactoryBean<T>` 接口提供三种方法：

      - `T getObject()`: 

          返回此工厂创建的对象的实例。该返回值会被存储到IoC容器！
      - `boolean i sSingleton()`: 
      
          如果此 `FactoryBean` 返回单例，则返回 `true` ，否则返回 `false` 。此方法的默认实现返回 `true` （注意，lombok插件使用，可能影响效果）。
      - `Class<?> getObjectType()`: 返回 `getObject()` 方法返回的对象类型，如果事先不知道类型，则返回 `null` 。
      
      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710847344573-7.png)
  2. FactoryBean使用场景
      1. 代理类的创建
      2. 第三方框架整合
      3. 复杂对象实例化等
  3. Factorybean应用
      1. 准备FactoryBean实现类

```Java
// 实现FactoryBean接口时需要指定泛型
// 泛型类型就是当前工厂要生产的对象的类型
public class HappyFactoryBean implements FactoryBean<HappyMachine> {
    
    private String machineName;
    
    public String getMachineName() {
        return machineName;
    }
    
    public void setMachineName(String machineName) {
        this.machineName = machineName;
    }
    
    @Override
    public HappyMachine getObject() throws Exception {
    
        // 方法内部模拟创建、设置一个对象的复杂过程
        HappyMachine happyMachine = new HappyMachine();
    
        happyMachine.setMachineName(this.machineName);
    
        return happyMachine;
    }
    
    @Override
    public Class<?> getObjectType() {
    
        // 返回要生产的对象的类型
        return HappyMachine.class;
    }
}
```
      2. 配置FactoryBean实现类

```XML
<!-- FactoryBean机制 -->
<!-- 这个bean标签中class属性指定的是HappyFactoryBean，但是将来从这里获取的bean是HappyMachine对象 -->
<bean id="happyMachine7" class="com.atguigu.ioc.HappyFactoryBean">
    <!-- property标签仍然可以用来通过setXxx()方法给属性赋值 -->
    <property name="machineName" value="iceCreamMachine"/>
</bean>
```
      3. 测试读取FactoryBean和FactoryBean.getObject对象

```Java
@Test
public void testExperiment07()  {

    ApplicationContext iocContainer = new ClassPathXmlApplicationContext("spring-bean-07.xml");

    //注意: 直接根据声明FactoryBean的id,获取的是getObject方法返回的对象
    HappyMachine happyMachine = iocContainer.getBean("happyMachine7",HappyMachine.class);
    System.out.println("happyMachine = " + happyMachine);

    //如果想要获取FactoryBean对象, 直接在id前添加&符号即可!  &happyMachine7 这是一种固定的约束
    Object bean = iocContainer.getBean("&happyMachine7");
    System.out.println("bean = " + bean);
}
```
  4. FactoryBean和BeanFactory区别

      **FactoryBean **是 Spring 中一种特殊的 bean，可以在 getObject() 工厂方法自定义的逻辑创建Bean！是一种能够生产其他 Bean 的 Bean。FactoryBean 在容器启动时被创建，而在实际使用时则是通过调用 getObject() 方法来得到其所生产的 Bean。因此，FactoryBean 可以自定义任何所需的初始化逻辑，生产出一些定制化的 bean。

      一般情况下，整合第三方框架，都是通过定义FactoryBean实现！！！

      **BeanFactory** 是 Spring 框架的基础，其作为一个顶级接口定义了容器的基本行为，例如管理 bean 的生命周期、配置文件的加载和解析、bean 的装配和依赖注入等。BeanFactory 接口提供了访问 bean 的方式，例如 getBean() 方法获取指定的 bean 实例。它可以从不同的来源（例如 Mysql 数据库、XML 文件、Java 配置类等）获取 bean 定义，并将其转换为 bean 实例。同时，BeanFactory 还包含很多子类（例如，ApplicationContext 接口）提供了额外的强大功能。

      总的来说，FactoryBean 和 BeanFactory 的区别主要在于前者是用于创建 bean 的接口，它提供了更加灵活的初始化定制功能，而后者是用于管理 bean 的框架基础接口，提供了基本的容器功能和 bean 生命周期管理。

#### 基于注解配置方式组件管理

##### 1. Bean注解标记和扫描（IoC）

1. **准备Spring项目和组件**
    1. 准备项目pom.xml

```XML
<dependencies>
    <!--spring context依赖-->
    <!--当你引入Spring Context依赖之后，表示将Spring的基础依赖引入了-->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>6.0.6</version>
    </dependency>

    <!--junit5测试-->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.3.1</version>
    </dependency>
</dependencies>
```
    b. 准备组件类
    
        普通组件

```Java
/**
 * projectName: com.atguigu.components
 *
 * description: 普通的组件
 */
public class CommonComponent {
}

```

        Controller组件

```Java
/**
 * projectName: com.atguigu.components
 *
 * description: controller类型组件
 */
public class XxxController {
}

```

        Service组件

```Java
/**
 * projectName: com.atguigu.components
 *
 * description: service类型组件
 */
public class XxxService {
}

```

        Dao组件

```Java
/**
 * projectName: com.atguigu.components
 *
 * description: dao类型组件
 */
public class XxxDao {
}

```

2. **组件添加标记注解**

​						a. 组件标记注解和区别

​			Spring 提供了以下多个注解，这些注解可以直接标注在 Java 类上，将它们定义成 Spring Bean。

| 注解        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| @Component  | 该注解用于描述 Spring 中的 Bean，它是一个泛化的概念，**仅仅表示容器中的一个组件（Bean）**，并且可以作用在应用的任何层次，例如 Service 层、Dao 层等。 使用时只需将该注解标注在相应类上即可。 |
| @Repository | 该注解用于将**数据访问层（Dao 层**）的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |
| @Service    | 该注解通常作用在**业务层（Service 层）**，用于将业务层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |
| @Controller | 该注解通常作用在**控制层（如SpringMVC 的 Controller）**，用于将控制层的类标识为 Spring 中的 Bean，其功能与 @Component 相同。 |

![img](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\imagesrc=http%3A%2F%2Fheavy_code_industry.gitee.io%2Fcode_heavy_industry%2Fassets%2Fimg%2Fimg017.93fb56c5.png)


​    
​        通过查看源码我们得知，@Controller、@Service、@Repository这三个注解只是在@Component注解的基础上起了三个新的名字。对于Spring使用IOC容器管理这些组件来说没有区别，也就是语法层面没有区别。所以@Controller、@Service、@Repository这三个注解只是给开发人员看的，让我们能够便于分辨组件的作用。
​        注意：虽然它们本质上一样，但是为了代码的可读性、程序结构严谨！我们肯定不能随便胡乱标记。
​        
​    b. 使用注解标记
​        普通组件

```Java
/**
 * projectName: com.atguigu.components
 *
 * description: 普通的组件
 */
@Component
public class CommonComponent {
}
```

        Controller组件

```Java
/**
 * projectName: com.atguigu.components
 *
 * description: controller类型组件
 */
@Controller
public class XxxController {
}

```

        Service组件

```Java
/**
 * projectName: com.atguigu.components
 *
 * description: service类型组件
 */
@Service
public class XxxService {
}

```

        Dao组件

```Java
/**
 * projectName: com.atguigu.components
 *
 * description: dao类型组件
 */
@Repository
public class XxxDao {
}

```

​			c.**配置文件确定扫描范围**

​				情况1：基本扫描配置

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <!-- 配置自动扫描的包 -->
    <!-- 1.包要精准,提高性能!
         2.！！会扫描指定的包和子包内容
         3.！！多个包可以使用,分割 例如: com.atguigu.controller,com.atguigu.service等
    -->
    <context:component-scan base-package="com.atguigu.components"/>
  
</beans>
```

​				情况2：指定排除组件

```XML
<!-- 情况三：指定不扫描的组件 -->
<context:component-scan base-package="com.atguigu.components">
    
    <!-- context:exclude-filter标签：指定排除规则 -->
    <!-- type属性：指定根据什么来进行排除，annotation取值表示根据注解来排除 -->
    <!-- expression属性：指定排除规则的表达式，对于注解来说指定全类名即可 -->
    <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

​				情况3：指定扫描组件	

```XML
<!-- 情况四：仅扫描指定的组件 -->
<!-- 仅扫描 = 关闭默认规则 + 追加规则 -->
<!-- use-default-filters属性：取值false表示关闭默认扫描规则 -->
<context:component-scan base-package="com.atguigu.ioc.components" use-default-filters="false">
    
    <!-- context:include-filter标签：指定在原有扫描规则的基础上追加的规则 -->
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```
2. **组件BeanName问题**

    在我们使用 XML 方式管理 bean 的时候，每个 bean 都有一个唯一标识——id 属性的值，便于在其他地方引用。现在使用注解后，每个组件仍然应该有一个唯一标识。

    默认情况：

    **类名首字母小写就是 bean 的 id**。例如：SoldierController 类对应的 bean 的 id 就是 soldierController。

    使用value属性指定：

```Java
@Controller(value = "tianDog")
public class SoldierController {
}
```

    当注解中只设置一个属性时，value属性的属性名可以省略：

```Java
@Service("smallDog")
public class SoldierService {
}
```
3. **总结**
    1. 注解方式IoC只是标记哪些类要被Spring管理
    2. 最终，我们还需要XML方式或者后面讲解Java配置类方式指定注解生效的包
    3. **现阶段配置方式为 注解 （标记）+ XML（扫描）**

##### 2. 组件（Bean）作用域和周期方法注解 
  1. 组件周期方法配置
      1. 周期方法概念

          我们可以在组件类中定义方法，然后当IoC容器实例化和销毁组件对象的时候进行调用！这两个方法我们成为生命周期方法！类似于Servlet的init/destroy方法,我们可以在周期方法完成初始化和释放资源等工作。

      2. 周期方法声明

```Java
public class BeanOne {

  //周期方法要求： 方法命名随意，但是要求方法必须是 public void 无形参列表
  @PostConstruct  //注解制指定初始化方法
  public void init() {
    // 初始化逻辑
  }
}

public class BeanTwo {
  
  @PreDestroy //注解指定销毁方法
  public void cleanup() {
    // 释放资源逻辑
  }
}
```
			2. 组件作用域配置
			2. 作用域配置

```Java
@Scope(scopeName = ConfigurableBeanFactory.SCOPE_SINGLETON) //单例,默认值
@Scope(scopeName = ConfigurableBeanFactory.SCOPE_PROTOTYPE) //多例  二选一
public class BeanOne {
  //周期方法要求： 方法命名随意，但是要求方法必须是 public void 无形参列表
  @PostConstruct  //注解制指定初始化方法
  public void init() {
    // 初始化逻辑
  }
}

//多例默认不会调用周期方法
```

 ##### 3. Bean属性赋值：引用类型自动装配 (DI)

- 在软件开发中，"装配"（Assembly）通常指的是将各个独立的组件、模块或对象组合在一起，以构建一个完整的系统或应用程序的过程。这个过程涉及将各个部分组装到一起，使它们协同工作以实现特定的功能。
- 在面向对象编程中，装配通常涉及将不同的类实例化并将它们连接在一起，通过调用各个对象的方法或访问其属性来实现特定的业务逻辑。这种装配过程可以通过依赖注入（Dependency Injection）、配置文件、反射等方式来实现。

自动装配注解（DI）：ioc容器中查找复合类型的组件对象+设置当前属性（di）

  1. **设定场景**
     
      - SoldierController 需要 SoldierService
      - SoldierService 需要 SoldierDao
      
        同时在各个组件中声明要调用的方法。
      
      - SoldierController中声明方法

```Java
import org.springframework.stereotype.Controller;

@Controller(value = "tianDog")
public class SoldierController {

    private SoldierService soldierService;

    public void getMessage() {
        soldierService.getMessage();
    }

}
```
      - SoldierService中声明方法

```Java
@Service("smallDog")
public class SoldierService {

    private SoldierDao soldierDao;

    public void getMessage() {
        soldierDao.getMessage();
    }
}
```
      - SoldierDao中声明方法

```Java
@Repository
public class SoldierDao {

    public void getMessage() {
        System.out.print("I am a soldier");
    }

}
```
  2. **自动装配实现**
      1. 前提

          参与自动装配的组件（需要装配、被装配）**全部都必须在IoC容器中。**

          注意：不区分IoC的方式！**XML和注解都可以！**
      2. @Autowired注解

          在**成员变量上直接标记@Autowired注解**即可，**不需要提供setXxx()方法**。以后我们在项目中的正式用法就是这样。
      3. **给Controller装配Service**

```Java
@Controller(value = "tianDog")
public class SoldierController {
    
    @Autowired
    private SoldierService soldierService;
    
    public void getMessage() {
        soldierService.getMessage();
    }
    
}
```
  							d. 给Service装配Dao

```Java
@Service("smallDog")
public class SoldierService {
    
    @Autowired
    private SoldierDao soldierDao;
    
    public void getMessage() {
        soldierDao.getMessage();
    }
}
```
  3. **@Autowired注解细节**
      1. 标记位置
          1. 成员变量:这是最主要的使用方式。与xml进行bean ref引用不同，他不需要有set方法！


```Java
@Service("smallDog")
public class SoldierService {
    
    @Autowired
    private SoldierDao soldierDao;
    
    public void getMessage() {
        soldierDao.getMessage();
    }
}
```
​      										ii.构造器

```Java
@Controller(value = "tianDog")
public class SoldierController {
    
    private SoldierService soldierService;
    
    @Autowired
    public SoldierController(SoldierService soldierService) {
        this.soldierService = soldierService;
    }
    ……
```
  										  iii. setXxx()方法

```Java
@Controller(value = "tianDog")
public class SoldierController {

    private SoldierService soldierService;

    @Autowired
    public void setSoldierService(SoldierService soldierService) {
        this.soldierService = soldierService;
    }
    ……
```
  b. 工作流程
      - 首先根据所需要的组件类型到 IOC 容器中查找
          - 能够找到唯一的 bean：直接执行装配
          - 如果完全找不到匹配这个类型的 bean：装配失败
          - 和所需类型匹配的 bean 不止一个
              - 没有 @Qualifier 注解：根据 @Autowired 标记位置成员变量的变量名作为 bean 的 id 进行匹配
                  - 能够找到：执行装配
                  - 找不到：装配失败。
              - 使用 @Qualifier 注解：根据 @Qualifier 注解中指定的名称作为 bean 的id进行匹配
                  - 能够找到：执行装配
                  - 找不到：装配失败

![img](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\imagesrc=http%3A%2F%2Fheavy_code_industry.gitee.io%2Fcode_heavy_industry%2Fassets%2Fimg%2Fimg018.2ff0ae09.png)

```Java
@Controller(value = "tianDog")
public class SoldierController {
    
    @Autowired
    @Qualifier(value = "maomiService222")
    // 根据面向接口编程思想，使用接口类型引入Service组件
    private ISoldierService soldierService;
```
  4. **佛系装配**

      给 @Autowired 注解设置 required = false 属性表示：能装就装，装不上就不装。但是实际开发时，基本上所有需要装配组件的地方都是必须装配的，用不上这个属性

```Java
@Controller(value = "tianDog")
public class SoldierController {

    // 给@Autowired注解设置required = false属性表示：能装就装，装不上就不装
    @Autowired(required = false)
    private ISoldierService soldierService;
```
  5. **扩展JSR-250注解@Resource**
     
      ![image-20240320201015760](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240320201015760.png)
      
      - 理解JSR系列注解
      
          JSR（Java Specification Requests）是Java平台标准化进程中的一种技术规范，而JSR注解是其中一部分重要的内容。按照JSR的分类以及注解语义的不同，可以将JSR注解分为不同的系列，主要有以下几个系列：
      
          1. JSR-175: 这个JSR是Java SE 5引入的，是Java注解最早的规范化版本，Java SE 5后的版本中都包含该JSR中定义的注解。主要包括以下几种标准注解：
          - `@Deprecated`: 标识一个程序元素（如类、方法或字段）已过时，并且在将来的版本中可能会被删除。
          - `@Override`: 标识一个方法重写了父类中的方法。
          - `@SuppressWarnings`: 抑制编译时产生的警告消息。
          - `@SafeVarargs`: 标识一个有安全性警告的可变参数方法。
          - `@FunctionalInterface`: 标识一个接口只有一个抽象方法，可以作为lambda表达式的目标。
          1. JSR-250: 这个JSR主要用于在Java EE 5中定义一些支持注解。该JSR主要定义了一些用于进行对象管理的注解，包括：
          - `@Resource`: 标识一个需要注入的资源，是实现Java EE组件之间依赖关系的一种方式。
          - `@PostConstruct`: 标识一个方法作为初始化方法。
          - `@PreDestroy`: 标识一个方法作为销毁方法。
          - `@Resource.AuthenticationType`: 标识注入的资源的身份验证类型。
          - `@Resource.AuthenticationType`: 标识注入的资源的默认名称。
          1. JSR-269: 这个JSR主要是Java SE 6中引入的一种支持编译时元数据处理的框架，即使用注解来处理Java源文件。该JSR定义了一些可以用注解标记的注解处理器，用于生成一些元数据，常用的注解有：
          - `@SupportedAnnotationTypes`: 标识注解处理器所处理的注解类型。
          - `@SupportedSourceVersion`: 标识注解处理器支持的Java源码版本。
          1. JSR-330: 该JSR主要为Java应用程序定义了一个依赖注入的标准，即Java依赖注入标准（javax.inject）。在此规范中定义了多种注解，包括：
          - `@Named`: 标识一个被依赖注入的组件的名称。
          - `@Inject`: 标识一个需要被注入的依赖组件。
          - `@Singleton`: 标识一个组件的生命周期只有一个唯一的实例。
          1. JSR-250: 这个JSR主要是Java EE 5中定义一些支持注解。该JSR包含了一些支持注解，可以用于对Java EE组件进行管理，包括：
          - `@RolesAllowed`: 标识授权角色
          - `@PermitAll`: 标识一个活动无需进行身份验证。
          - `@DenyAll`: 标识不提供针对该方法的访问控制。
          - `@DeclareRoles`: 声明安全角色。

          但是你要理解JSR是Java提供的**技术规范**，也就是说，他只是规定了注解和注解的含义，**JSR并不是直接提供特定的实现**，而是提供标准和指导方针，由第三方框架（Spring）和库来实现和提供对应的功能。
      - JSR-250 @Resource注解
      
          @Resource注解也可以完成属性注入。那它和@Autowired注解有什么区别？
      
          - @Resource注解是JDK扩展包中的，也就是说属于JDK的一部分。所以该注解是标准注解，更加具有通用性。(JSR-250标准中制定的注解类型。JSR是Java规范提案。)
          - @Autowired注解是Spring框架自己的。
          - **@Resource注解默认根据Bean名称装配，未指定name时，使用属性名作为name。通过name找不到的话会自动启动通过类型装配。**
          - **@Autowired注解默认根据类型装配，如果想根据名称装配，需要配合@Qualifier注解一起用。**
          - @Resource注解用在属性上、setter方法上。
          - @Autowired注解用在属性上、setter方法上、构造方法上、构造方法参数上。
      
          @Resource注解属于JDK扩展包，所以不在JDK当中，需要额外引入以下依赖：【**高于JDK11或低于JDK8需要引入以下依赖**】

```XML
<dependency>
    <groupId>jakarta.annotation</groupId>
    <artifactId>jakarta.annotation-api</artifactId>
    <version>2.1.1</version>
</dependency>
```
      - @Resource使用

```Java
@Controller
public class XxxController {
    /**
     * 1. 如果没有指定name,先根据属性名查找IoC中组件xxxService
     * 2. 如果没有指定name,并且属性名没有对应的组件,会根据属性类型查找
     * 3. 可以指定name名称查找!  @Resource(name='test') == @Autowired + @Qualifier(value='test')
     */
    @Resource
    private XxxService xxxService;

    //@Resource(name = "指定beanName")
    //private XxxService xxxService;

    public void show(){
        System.out.println("XxxController.show");
        xxxService.show();
    }
}
```

##### 4.  Bean属性赋值：基本类型属性赋值 (DI)

  **`@Value` 通常用于注入外部化属性**

  **声明外部配置**

  application.properties

```Java
catalog.name=MovieCatalog
```

  **xml引入外部配置**

```Java
<!-- 引入外部配置文件-->
<context:property-placeholder location="application.properties" />
```

  **@Value注解读取配置**

```Java
package com.atguigu.components;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

/**
 * projectName: com.atguigu.components
 *
 * description: 普通的组件
 */
@Component
public class CommonComponent {

    /**
     * 情况1: ${key} 取外部配置key对应的值!
     * 情况2: ${key:defaultValue} 没有key,可以给与默认值
     */
    @Value("${catalog:hahaha}")
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```

  catalog

#### 基于配置类方式组件管理

##### 完全注解开发理解

  Spring 完全注解配置（Fully Annotation-based Configuration）是指通过 Java配置类 代码来配置 Spring 应用程序，使用注解来替代原本在 XML 配置文件中的配置。相对于 XML 配置，完全注解配置具有更强的类型安全性和更好的可读性。

  **两种方式思维转化**

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710937462358-5.png)

##### 1. 配置类和扫描注解

  a. **xml+注解方式**

  配置文件application.xml

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">


    <!-- 配置自动扫描的包 -->
    <!-- 1.包要精准,提高性能!
         2.会扫描指定的包和子包内容
         3.多个包可以使用,分割 例如: com.atguigu.controller,com.atguigu.service等
    -->
    <context:component-scan base-package="com.atguigu.components"/>

    <!-- 引入外部配置文件-->
    <context:property-placeholder location="application.properties" />
</beans>
```

  测试创建IoC容器

```Java
 // xml方式配置文件使用ClassPathXmlApplicationContext容器读取
 ApplicationContext applicationContext =
                new ClassPathXmlApplicationContext("application.xml");
```

b. 配置类+注解方式（完全注解方式）

  配置类

  使用 @Configuration 注解将一个普通的类标记为 Spring 的配置类。

```Java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

//标注当前类是配置类，替代application.xml    
@Configuration
//使用注解读取外部配置，替代 <context:property-placeholder标签
@PropertySource("classpath:application.properties")
//使用@ComponentScan注解,可以配置扫描包,替代<context:component-scan标签
@ComponentScan(basePackages = {"com.atguigu.components"})
public class MyConfiguration {
    
}
```

  测试创建IoC容器

```Java
// AnnotationConfigApplicationContext 根据配置类创建 IOC 容器对象
ApplicationContext iocContainerAnnotation = 
new AnnotationConfigApplicationContext(MyConfiguration.class);
```

  可以使用 no-arg 构造函数实例化 `AnnotationConfigApplicationContext` ，然后使用 `register()` 方法对其进行配置。此方法在以编程方式生成 `AnnotationConfigApplicationContext` 时特别有用。以下示例演示如何执行此操作：

```Java
// AnnotationConfigApplicationContext-IOC容器对象
ApplicationContext iocContainerAnnotation = 
new AnnotationConfigApplicationContext();
//外部设置配置类
iocContainerAnnotation.register(MyConfiguration.class);
//刷新后方可生效！！
iocContainerAnnotation.refresh();
```

  **总结：**

    @Configuration指定一个类为配置类，可以添加配置注解，替代配置xml文件
    
    @ComponentScan(basePackages = {"包","包"}) 替代<context:component-scan标签实现注解扫描
    
    @PropertySource("classpath:配置文件地址") 替代 <context:property-placeholder标签
    
    配合IoC/DI注解，可以进行完整注解开发！

##### 2. @Bean定义组件

  **场景需求**：将Druid连接池对象存储到IoC容器

  **需求分析**：第三方jar包的类，添加到ioc容器，无法使用@Component等相关注解！因为源码jar包内容为只读模式！

  **xml方式实现**：

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 引入外部属性文件 -->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!-- 实验六 [重要]给bean的属性赋值：引入外部属性文件 -->
    <bean id="druidDataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="url" value="${jdbc.url}"/>
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="username" value="${jdbc.user}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

</beans>
```

  **配置类方式实现**：

    `@Bean` 注释用于指示方法实例化、配置和初始化要由 Spring IoC 容器管理的新对象。对于那些熟悉 Spring 的 `<beans/>` XML 配置的人来说， `@Bean` 注释与 `<bean/>` 元素起着相同的作用。

```Java
//标注当前类是配置类，替代application.xml    
@Configuration
//引入jdbc.properties文件
@PropertySource({"classpath:application.properties","classpath:jdbc.properties"})
@ComponentScan(basePackages = {"com.atguigu.components"})
public class MyConfiguration {
    
    //如果第三方类进行IoC管理,无法直接使用@Component相关注解
    //解决方案: xml方式可以使用<bean标签
    //解决方案: 配置类方式,可以使用方法返回值+@Bean注解
    @Bean
    public DataSource createDataSource(@Value("${jdbc.user}") String username,
                                       @Value("${jdbc.password}")String password,
                                       @Value("${jdbc.url}")String url,
                                       @Value("${jdbc.driver}")String driverClassName){
        //使用Java代码实例化
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        dataSource.setUrl(url);
        dataSource.setDriverClassName(driverClassName);
        //返回结果即可
        return dataSource;
    }
}
```

##### 3. 高级特性：@Bean注解细节
  1. **@Bean生成BeanName问题**

      @Bean注解源码：

```Java
public @interface Bean {
    //前两个注解可以指定Bean的标识
    @AliasFor("name")
    String[] value() default {};
    @AliasFor("value")
    String[] name() default {};
  
    //autowireCandidate 属性来指示该 Bean 是否候选用于自动装配。
    //autowireCandidate 属性默认值为 true，表示该 Bean 是一个默认的装配目标，
    //可被候选用于自动装配。如果将 autowireCandidate 属性设置为 false，则说明该 Bean 不是默认的装配目标，不会被候选用于自动装配。
    boolean autowireCandidate() default true;

    //指定初始化方法
    String initMethod() default "";
    //指定销毁方法
    String destroyMethod() default "(inferred)";
}

```

​		指定@Bean的名称：

```Java
@Configuration
public class AppConfig {

  @Bean("myThing") //指定名称
  public Thing thing() {
    return new Thing();
  }
}
```

​			  `@Bean` 注释注释方法。使用此方法在指定为方法返回值的类型的 `ApplicationContext` 中注册 Bean 定义。缺省情况下，Bean 名称与方法名称相同。下面的示例演示 `@Bean` 方法声明：

```Java
@Configuration
public class AppConfig {

  @Bean
  public TransferServiceImpl transferService() {
    return new TransferServiceImpl();
  }  
}
```

​		 前面的配置完全等同于下面的Spring XML：

```Java
<beans>
  <bean id="transferService" class="com.acme.TransferServiceImpl"/>
</beans>
```
  2. **@Bean 初始化和销毁方法指定**

      `@Bean` 注解支持指定任意初始化和销毁回调方法，非常类似于 Spring XML 在 `bean` 元素上的 `init-method` 和 `destroy-method` 属性，如以下示例所示：

```Java
public class BeanOne {

  public void init() {
    // initialization logic
  }
}

public class BeanTwo {

  public void cleanup() {
    // destruction logic
  }
}

@Configuration
public class AppConfig {

  @Bean(initMethod = "init")
  public BeanOne beanOne() {
    return new BeanOne();
  }

  @Bean(destroyMethod = "cleanup")
  public BeanTwo beanTwo() {
    return new BeanTwo();
  }
}
```
  3. **@Bean Scope作用域**

      可以指定使用 `@Bean` 注释定义的 bean 应具有特定范围。您可以使用在 Bean 作用域部分中指定的任何标准作用域。

      默认作用域为 `singleton` ，但您可以使用 `@Scope` 注释覆盖此范围，如以下示例所示：

```Java
@Configuration
public class MyConfiguration {

  @Bean
  @Scope("prototype")
  public Encryptor encryptor() {
    // ...
  }
}
```
  4. **@Bean方法之间依赖**

      **准备组件**

```Java
public class HappyMachine {
    
    private String machineName;
    
    public String getMachineName() {
        return machineName;
    }
    
    public void setMachineName(String machineName) {
        this.machineName = machineName;
    }
}
```

```Java
public class HappyComponent {
    //引用新组件
    private HappyMachine happyMachine;

    public HappyMachine getHappyMachine() {
        return happyMachine;
    }

    public void setHappyMachine(HappyMachine happyMachine) {
        this.happyMachine = happyMachine;
    }

    public void doWork() {
        System.out.println("HappyComponent.doWork");
    }

}
```

  **Java配置类实现：**

  方案1：

  直接调用方法返回 Bean 实例：在一个 `@Bean` 方法中直接调用其他 `@Bean` 方法来获取 Bean 实例，虽然是方法调用，也是通过IoC容器获取对应的Bean，例如：

```Java
@Configuration
public class JavaConfig {

    @Bean
    public HappyMachine happyMachine(){
        return new HappyMachine();
    }

    @Bean
    public HappyComponent happyComponent(){
        HappyComponent happyComponent = new HappyComponent();
        //直接调用方法即可! 
        happyComponent.setHappyMachine(happyMachine());
        return happyComponent;
    }

}
```

  方案2：

  参数引用法：通过方法参数传递 Bean 实例的引用来解决 Bean 实例之间的依赖关系，例如：

```Java
package com.atguigu.config;

import com.atguigu.ioc.HappyComponent;
import com.atguigu.ioc.HappyMachine;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * projectName: com.atguigu.config
 * description: 配置HappyComponent和HappyMachine关系
 */

@Configuration
public class JavaConfig {

    @Bean
    public HappyMachine happyMachine(){
        return new HappyMachine();
    }

    /**
     * 可以直接在形参列表接收IoC容器中的Bean!
     *    情况1: 直接指定类型即可
     *    情况2: 如果有多个bean,(HappyMachine 名称 ) 形参名称等于要指定的bean名称!
     *           例如:
     *               @Bean
     *               public Foo foo1(){
     *                   return new Foo();
     *               }
     *               @Bean
     *               public Foo foo2(){
     *                   return new Foo()
     *               }
     *               @Bean
     *               public Component component(Foo foo1 / foo2 通过此处指定引入的bean)
     */
    @Bean
    public HappyComponent happyComponent(HappyMachine happyMachine){
        HappyComponent happyComponent = new HappyComponent();
        //赋值
        happyComponent.setHappyMachine(happyMachine);
        return happyComponent;
    }

}
```

##### 4. 高级特性：@Import扩展

  `@Import` 注释允许从另一个配置类加载 `@Bean` 定义，如以下示例所示：

```Java
@Configuration
public class ConfigA {

  @Bean
  public A a() {
    return new A();
  }
}

@Configuration
@Import(ConfigA.class)
public class ConfigB {

  @Bean
  public B b() {
    return new B();
  }
}
```

  现在，在实例化上下文时不需要同时指定 `ConfigA.class` 和 `ConfigB.class` ，只需显式提供 `ConfigB` ，如以下示例所示：

```Java
public static void main(String[] args) {
  ApplicationContext ctx = new AnnotationConfigApplicationContext(ConfigB.class);

  // now both beans A and B will be available...
  A a = ctx.getBean(A.class);
  B b = ctx.getBean(B.class);
}
```

  此方法简化了容器实例化，因为只需要处理一个类，而不是要求您在构造期间记住可能大量的 `@Configuration` 类。

#### 三种配置方式总结

  ##### XML方式配置总结
    1. 所有内容写到xml格式配置文件中
    2. 声明bean通过<bean标签
    3. <bean标签包含基本信息（id,class）和属性信息 <property name value / ref
    4. 引入外部的properties文件可以通过<context:property-placeholder
    5. IoC具体容器实现选择ClassPathXmlApplicationContext对象

  ##### XML+注解方式配置总结
    1. 注解负责标记IoC的类和进行属性装配
    2. xml文件依然需要，需要通过<context:component-scan标签指定注解范围
    3. 标记IoC注解：@Component,@Service,@Controller,@Repository 
    4. 标记DI注解：@Autowired @Qualifier @Resource @Value
    5. IoC具体容器实现选择ClassPathXmlApplicationContext对象

  ##### 完全注解方式配置总结
    1. 完全注解方式指的是去掉xml文件，使用配置类 + 注解实现
    2. xml文件替换成使用@Configuration注解标记的类
    3. 标记IoC注解：@Component,@Service,@Controller,@Repository 
    4. 标记DI注解：@Autowired @Qualifier @Resource @Value
    5. <context:component-scan标签指定注解范围使用@ComponentScan(basePackages = {"com.atguigu.components"})替代
    6. <context:property-placeholder引入外部配置文件使用@PropertySource({"classpath:application.properties","classpath:jdbc.properties"})替代
    7. <bean 标签使用@Bean注解和方法实现
    8. IoC具体容器实现选择AnnotationConfigApplicationContext对象

#### 整合Spring5-Test5搭建测试环境
  1. 整合测试环境作用

      好处1：不需要自己创建IOC容器对象了

      好处2：任何需要的bean都可以在测试类中直接享受自动装配
  2. 导入相关依赖

```XML
<!--junit5测试-->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.3.1</version>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>6.0.6</version>
    <scope>test</scope>
</dependency>
```
  3. 整合测试注解使用

```Java
//方法1： @SpringJUnitConfig(locations = {"classpath:spring-context.xml"})  //指定配置文件xml
方法2：@SpringJUnitConfig(value = {BeanConfig.class})  //指定配置类
public class Junit5IntegrationTest {
    
    @Autowired
    private User user;
    
    @Test
    public void testJunit5() {
        System.out.println(user);
    }
}
```

### Spring AOP 面向切面编程

#### 场景设定和问题复现

**带日志输出快捷键：soutp soutv**

1. 代码缺陷
    - 对核心业务功能有干扰，导致程序员在开发核心业务功能时分散了精力
    - 附加功能代码重复，分散在各个业务功能方法中！冗余，且不方便统一维护！
2. 解决思路

      核心就是：解耦。我们需要把附加功能从业务功能代码中抽取出来。将重复的代码统一提取，并且[[动态插入]]到每个业务方法！

3. 技术困难

    解决问题的困难：提取重复附加功能代码到一个类中。


#### 解决技术代理模式

1. **代理模式**

    二十三种设计模式中的一种，属于结构型模式。它的作用就是通过提供一个代理类，让我们在调用目标方法的时候，**不再是直接对目标方法进行调用，而是通过代理类间接调用**。让不属于目标方法核心逻辑的代码从目标方法中剥离出来——解耦。**调用目标方法时先调用代理对象的方法，减少对目标方法的调用和打扰，同时让附加功能能够集中在一起也有利于统一维护。**

    无代理场景：

    ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img004.e76b3080.png)

    有代理场景：

    ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img005.74dd7746.png)

    

    相关术语：
    
    - 代理：**将非核心逻辑剥离出来以后，封装这些非核心逻辑的类、对象、方法。(中介)**
        - 动词：指做代理这个动作，或这项工作
        - 名词：扮演代理这个角色的类、对象、方法
    - 目标：**被代理**“套用”了核心逻辑代码的类、对象、方法。(房东)

    代理在开发中实现的方式具体有两种：**静态代理，[动态代理技术]**
2. **静态代理**

    主动创建代理类：

```Java
public class CalculatorStaticProxy implements Calculator {
    
    // 将被代理的目标对象声明为成员变量
    private Calculator target;
    
    public CalculatorStaticProxy(Calculator target) {
        this.target = target;
    }
    
    @Override
    public int add(int i, int j) {
    
        // 附加功能由代理类中的代理方法来实现
        System.out.println("参数是：" + i + "," + j);
    
        // 通过目标对象来实现核心业务逻辑
        int addResult = target.add(i, j);
    
        System.out.println("方法内部 result = " + result);
    
        return addResult;
    }
    ……
```

静态代理确实实现了解耦，但是由于代码都写死了，完全不具备任何的灵活性。就拿日志功能来说，将来其他地方也需要附加日志，那还得再声明更多个静态代理类，那就产生了大量重复的代码，日志功能还是分散的，没有统一管理。提出进一步的需求：将日志功能集中到一个代理类中，将来有任何日志需求，都通过这一个代理类来实现。这就需要使用动态代理技术了。

3. **动态代理**

    动态代理技术分类

    - **JDK动态代理：**JDK原生的实现方式，需要被代理的目标类必须**实现接口**！他会根据目标类的接口动态生成一个代理对象！代理对象和目标对象有相同的接口！（拜把子）
      - **目标类必须有接口**
      - **接口生成一个代理类**
    
    - **cglib**：通过继承被代理的目标类实现代理，所以不需要目标类实现接口！（认干爹）
      - **不要求目标类有接口**
      - **直接根据目标类生成一个子类对象**
    
    
    JDK动态代理技术实现（了解）
    
      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img003.2fe524a2.png)
    
      代理工程：基于jdk代理技术，生成代理对象

```Java
public class ProxyFactory {

    private Object target;

    public ProxyFactory(Object target) {
        this.target = target;
    }

    public Object getProxy(){

        /**
         * newProxyInstance()：创建一个代理实例
         * 其中有三个参数：
         * 1、classLoader：加载动态生成的代理类的类加载器
         * 2、interfaces：目标对象实现的所有接口的class对象所组成的数组
         * 3、invocationHandler：设置代理对象实现目标对象方法的过程，即代理类中如何重写接口中的抽象方法
         */
        ClassLoader classLoader = target.getClass().getClassLoader();
        Class<?>[] interfaces = target.getClass().getInterfaces();
        InvocationHandler invocationHandler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                /**
                 * proxy：代理对象
                 * method：代理对象需要实现的方法，即其中需要重写的方法
                 * args：method所对应方法的参数
                 */
                Object result = null;
                try {
                    System.out.println("[动态代理][日志] "+method.getName()+"，参数："+ Arrays.toString(args));
                    result = method.invoke(target, args);
                    System.out.println("[动态代理][日志] "+method.getName()+"，结果："+ result);
                } catch (Exception e) {
                    e.printStackTrace();
                    System.out.println("[动态代理][日志] "+method.getName()+"，异常："+e.getMessage());
                } finally {
                    System.out.println("[动态代理][日志] "+method.getName()+"，方法执行完毕");
                }
                return result;
            }
        };

        return Proxy.newProxyInstance(classLoader, interfaces, invocationHandler);
    }
}
```

  测试代码：

```Java
@Test
public void testDynamicProxy(){
    ProxyFactory factory = new ProxyFactory(new CalculatorLogImpl());
    Calculator proxy = (Calculator) factory.getProxy();
    proxy.div(1,0);
    //proxy.div(1,1);
}
```
4. **代理总结**

    **代理方式可以解决附加功能代码干扰核心代码和不方便统一维护的问题！**他主要是将附加功能代码提取到代理中执行，不干扰目标核心代码！但是我们也发现，无论使用静态代理和动态代理(jdk,cglib)，程序员的工作都比较繁琐！需要自己编写代理工厂等！但是，提前剧透，我们在实际开发中，不需要编写代理代码，我们可以使用[Spring AOP]框架，他会简化动态代理的实现！！！

#### 面向切面编程思维（AOP）

##### 面向切面编程思想AOP

AOP：Aspect Oriented Programming面向切面编程

AOP可以说是OOP（Object Oriented Programming，面向对象编程）的补充和完善。**OOP引入封装、继承、多态等概念来建立一种对象层次结构，用于模拟公共行为的一个集合。不过OOP允许开发者定义纵向的关系，但并不适合定义横向的关系，**例如日志功能。日志代码**往往横向地散布在所有对象层次中，而与它对应的对象的核心功能毫无关系对于其他类型的代码，如安全性、异常处理和透明的持续性也都是如此，这种散布在各处的无关的代码被称为横切（cross cutting）**，在OOP设计中，它导致了大量代码的重复，而不利于各个模块的重用。

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710984787093-8.png)

AOP技术恰恰相反，它利用一种称为"横切"的技术，**剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其命名为"Aspect"，即切面**。**所谓"切面"，简单说就是那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块之间的耦合度**，并有利于未来的可操作性和可维护性。使用AOP，可以在不修改原来代码的基础上添加新功能。

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1710984787093-7.png)

##### AOP思想主要的应用场景

AOP（面向切面编程）是一种编程范式，它通过将通用的横切关注点（如日志、事务、权限控制等）与业务逻辑分离，使得代码更加清晰、简洁、易于维护。AOP可以应用于各种场景，以下是一些常见的AOP应用场景：

1. 日志记录：在系统中记录日志是非常重要的，可以使用AOP来实现日志记录的功能，可以在方法执行前、执行后或异常抛出时记录日志。
2. 事务处理：在数据库操作中使用事务可以保证数据的一致性，可以使用AOP来实现事务处理的功能，可以在方法开始前开启事务，在方法执行完毕后提交或回滚事务。
3. 安全控制：在系统中包含某些需要安全控制的操作，如登录、修改密码、授权等，可以使用AOP来实现安全控制的功能。可以在方法执行前进行权限判断，如果用户没有权限，则抛出异常或转向到错误页面，以防止未经授权的访问。
4. 性能监控：在系统运行过程中，有时需要对某些方法的性能进行监控，以找到系统的瓶颈并进行优化。可以使用AOP来实现性能监控的功能，可以在方法执行前记录时间戳，在方法执行完毕后计算方法执行时间并输出到日志中。
5. 异常处理：系统中可能出现各种异常情况，如空指针异常、数据库连接异常等，可以使用AOP来实现异常处理的功能，在方法执行过程中，如果出现异常，则进行异常处理（如记录日志、发送邮件等）。
6. 缓存控制：在系统中有些数据可以缓存起来以提高访问速度，可以使用AOP来实现缓存控制的功能，可以在方法执行前查询缓存中是否有数据，如果有则返回，否则执行方法并将方法返回值存入缓存中。
7. 动态代理：AOP的实现方式之一是通过动态代理，可以代理某个类的所有方法，用于实现各种功能。

综上所述，AOP可以应用于各种场景，**它的作用是将通用的横切关注点与业务逻辑分离**，使得代码更加清晰、简洁、易于维护。

##### AOP术语名词介绍

###### 1-横切关注点

从每个方法中抽取出来的同一类非核心业务。在同一个项目中，我们可以使用多个横切关注点对相关方法进行多个不同方面的增强。这个概念不是语法层面天然存在的，而是根据附加功能的逻辑上的需要：有十个附加功能，就有十个横切关注点。

<img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img007.9ad7afe5.png" style="zoom:50%;" />

AOP把软件系统分为两个部分：**核心关注点和横切关注点。业务处理的主要流程是核心关注点，与之关系不大的部分是横切关注点。**横切关注点的一个特点是，他们经常发生在核心关注点的多处，而各处基本相似，比如权限认证、日志、事务、异常等。**AOP的作用在于分离系统中的各种关注点，将核心关注点和横切关注点分离开来。**

###### 2-通知(增强)

每一个横切关注点上要做的事情都需要写一个方法来实现，这样的方法就叫通知方法。

- 前置通知：在被代理的**目标方法前执行**
  - @Before

- 返回通知：在被代理的目标方法**成功结束后执行**（**寿终正寝**）
  - @After

- 异常通知：在被代理的目标方法**异常结束后执行**（**死于非命**）
  - @AfterThrowing

- 后置通知：在被代理的目标方法**最终结束后执行**（**盖棺定论**）
  - @AfterReturning

- 环绕通知：**使用try...catch...finally结构围绕整个被代理的目标方法，包括上面四种通知对应的所有位置**
  - @Around


<img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img008.ea600562.png" style="zoom:50%;" />

###### 3-连接点 joinpoint

这也是一个纯逻辑概念，不是语法定义的。**指那些被拦截到的点。在 Spring 中，可以被动态代理拦截目标类的方法**

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img010.5af189f7.png)

###### 4-切入点 pointcut

定位连接点的方式，**或者可以理解成被选中的连接点**！是一个表达式，比如execution(* com.spring.service.impl.*.*(..))。符合条件的每个方法都是一个具体的连接点。

###### 5-切面 aspect=切点+增强

切入点和通知的结合。是一个类。

<img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img009.a0b70cb3.png" style="zoom:50%;" />

###### 6-目标 target

被代理的目标对象。

###### 7-代理 proxy

向目标对象应用通知之后创建的代理对象。

###### 8-织入 weave

指把通知应用到目标上，生成代理对象的过程。可以在编译期织入，也可以在运行期织入，Spring采用后者。

#### Spring AOP框架介绍和关系梳理
  1. AOP一种区别于OOP的编程思维，用来完善和解决OOP的非核心代码冗余和不方便统一维护问题！
  2. 代理技术（动态代理|静态代理）是实现AOP思维编程的具体技术，但是自己使用动态代理实现代码比较繁琐！
  3. **Spring AOP框架，基于AOP编程思维，封装动态代理技术，简化动态代理技术实现的框架！**SpringAOP内部帮助我们实现动态代理，我们只需写少量的配置，指定生效范围即可,即可完成面向切面思维编程的实现！

#### Spring  AOP基于注解方式实现和细节

##### Spring AOP底层技术组成

**只需要导sprig-aspectj包，如果没导入spring-context包需要导入spring-aop包**

  <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img006.84eb95b7.png" style="zoom:50%;" />

  - 动态代理（InvocationHandler）：JDK原生的实现方式，需要被代理的目标类必须实现接口。因为这个技术要求代理对象和目标对象实现同样的接口（兄弟两个拜把子模式）。
  - cglib：通过继承被代理的目标类（认干爹模式）实现代理，所以不需要目标类实现接口。
  - AspectJ：早期的AOP实现的框架，SpringAOP借用了AspectJ中的AOP注解。

##### 初步实现

![image-20240325195022841](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240325195022841.png)

  1. 加入依赖

```XML
<!-- spring-aspects会帮我们传递过来aspectjweaver -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>6.0.6</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>6.0.6</version>
</dependency>
```
  2. 准备接口

```Java
public interface Calculator {
    
    int add(int i, int j);
    
    int sub(int i, int j);
    
    int mul(int i, int j);
    
    int div(int i, int j);
    
}
```
  3. 纯净实现类

```Java
package com.atguigu.proxy;


/**
 * 实现计算接口,单纯添加 + - * / 实现! 掺杂其他功能!
 */
@Component
public class CalculatorPureImpl implements Calculator {
    
    @Override
    public int add(int i, int j) {
    
        int result = i + j;
    
        return result;
    }
    
    @Override
    public int sub(int i, int j) {
    
        int result = i - j;
    
        return result;
    }
    
    @Override
    public int mul(int i, int j) {
    
        int result = i * j;
    
        return result;
    }
    
    @Override
    public int div(int i, int j) {
    
        int result = i / j;
    
        return result;
    }
}
```
  4. 声明切面类

     1. 定义方法存储增强代码

        具体定义几个方法要根据插入的位置决定

     2. 使用注解配置 指定插入目标方法的位置

        ![image-20240325200219287](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240325200219287.png)

     3. 配置切点表达式【选中插入的方法 切点】

     4. 补全注解（切面和ioc的配置）

          1. 加入ioc容器 @Component
          2. 配置切面 @Aspect = 切点+增强

     5. 开启aspect注解支持


```Java
package com.atguigu.advice;

import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

// @Aspect表示这个类是一个切面类
@Aspect
// @Component注解保证这个切面类能够放入IOC容器
@Component
public class LogAspect {
        
    // @Before注解：声明当前方法是前置通知方法
    // value属性：指定切入点表达式，由切入点表达式控制当前通知方法要作用在哪一个目标方法上
    @Before(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
    public void printLogBeforeCore() {
        System.out.println("[AOP前置通知] 方法开始了");
    }
    
    @AfterReturning(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
    public void printLogAfterSuccess() {
        System.out.println("[AOP返回通知] 方法成功返回了");
    }
    
    @AfterThrowing(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
    public void printLogAfterException() {
        System.out.println("[AOP异常通知] 方法抛异常了");
    }
    
    @After(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
    public void printLogFinallyEnd() {
        System.out.println("[AOP后置通知] 方法最终结束了");
    }
    
}
```
  5. 开启aspectj注解支持
      1. xml方式

```XML
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 进行包扫描-->
    <context:component-scan base-package="com.atguigu" />
    <!-- 开启aspectj框架注解支持-->
    <aop:aspectj-autoproxy />
</beans>
```
​							  b.配置类方式

```Java
@Configuration
@ComponentScan(basePackages = "com.atguigu")
//作用等于 <aop:aspectj-autoproxy /> 配置类上开启 Aspectj注解支持!
@EnableAspectJAutoProxy
public class MyConfig {
}

```
  6. 测试效果

```Java
//@SpringJUnitConfig(locations = "classpath:spring-aop.xml")
@SpringJUnitConfig(value = {MyConfig.class})
public class AopTest {

    @Autowired
    private Calculator calculator;

    @Test
    public void testCalculator(){
        calculator.add(1,1);
    }
}

```

  输出结果：

```Java
"C:\Program Files\Java\jdk-17\bin\java.exe" -ea -Didea.test.cyclic.buffer.size=1048576 "-...otation-api\1.3.2\javax.annotation-api-1.3.2.jar;D:\repository\org\springframework\spring-aop\6.0.6\spring-aop-6.0.6.jar;D:\repository\org\springframework\spring-aspects\6.0.6\spring-aspects-6.0.6.jar;D:\repository\org\aspectj\aspectjweaver\1.9.9.1\aspectjweaver-1.9.9.1.jar" com.intellij.rt.junit.JUnitStarter -ideVersion5 -junit5 com.atguigu.test.AopTest,testCalculator
[AOP前置通知] 方法开始了
[AOP返回通知] 方法成功返回了
[AOP后置通知] 方法最终结束了
```

##### 获取通知细节信息
  1. **JointPoint接口**

      **import org.aspectj.lang.JoinPoint;**

      需要获取方法签名、传入的实参等信息时，可以在通知方法声明JoinPoint类型的形参。
      
      - 要点1：JoinPoint 接口通过 getSignature() 方法获取目标方法的签名（方法声明时的完整信息）
      - 要点2：通过目标方法签名对象**获取方法名**
      - 要点3：通过 JoinPoint 对象获取外界调用目标方法时传入的实参列表组成的数组

```Java
// @Before注解标记前置通知方法
// value属性：切入点表达式，告诉Spring当前通知方法要套用到哪个目标方法上
// 在前置通知方法形参位置声明一个JoinPoint类型的参数，Spring就会将这个对象传入
// 根据JoinPoint对象就可以获取目标方法名称、实际参数列表
@Before(value = "execution(public int com.atguigu.aop.api.Calculator.add(int,int))")
public void printLogBeforeCore(JoinPoint joinPoint) {
    
    // 1.通过JoinPoint对象获取目标方法签名对象
    // 方法的签名：一个方法的全部声明信息
    Signature signature = joinPoint.getSignature();
    
    // 2.通过方法的签名对象获取目标方法的详细信息
    String methodName = signature.getName();
    System.out.println("methodName = " + methodName);
    
    int modifiers = signature.getModifiers();
    System.out.println("modifiers = " + modifiers);
    
    String declaringTypeName = signature.getDeclaringTypeName();
    System.out.println("declaringTypeName = " + declaringTypeName);
    
    // 3.通过JoinPoint对象获取外界调用目标方法时传入的实参列表
    Object[] args = joinPoint.getArgs();
    
    // 4.由于数组直接打印看不到具体数据，所以转换为List集合
在Java中，直接使用System.out.println()打印一个数组对象时，实际上打印的是该数组对象的内存地址，而不是数组中具体的数据内容。这是因为数组并没有实现toString方法来提供数组内容的字符串表示。

如果想要查看数组中的具体数据内容，可以将数组转换为列表或使用循环遍历数组来输出元素。在你提供的代码中，通过Arrays.asList(args)将数组转换为列表，这样就能够更方便地查看数组中的数据内容。

另外，需要注意的是，通过Arrays.asList()方法转换后的列表是一个固定大小的列表，不能进行添加或删除元素的操作，只能进行读取和修改元素值
    List<Object> argList = Arrays.asList(args);
    
    System.out.println("[AOP前置通知] " + methodName + "方法开始了，参数列表：" + argList);
}
```
  2. **方法返回值**

      在返回通知中，通过**@AfterReturning**注解的returning属性获取目标方法的返回值！

```Java
// @AfterReturning注解标记返回通知方法
// 在返回通知中获取目标方法返回值分两步：
// 第一步：在@AfterReturning注解中通过returning属性设置一个名称
// 第二步：使用returning属性设置的名称在通知方法中声明一个对应的形参
@AfterReturning(
        value = "execution(public int com.atguigu.aop.api.Calculator.add(int,int))",
        returning = "targetMethodReturnValue"
)
public void printLogAfterCoreSuccess(JoinPoint joinPoint, Object targetMethodReturnValue) {
    
    String methodName = joinPoint.getSignature().getName();
    
    System.out.println("[AOP返回通知] "+methodName+"方法成功结束了，返回值是：" + targetMethodReturnValue);
}
```
  3. **异常对象捕捉**

      在异常通知中，通过@AfterThrowing注解的throwing属性获取目标方法抛出的异常对象

```Java
// @AfterThrowing注解标记异常通知方法
// 在异常通知中获取目标方法抛出的异常分两步：
// 第一步：在@AfterThrowing注解中声明一个throwing属性设定形参名称
// 第二步：使用throwing属性指定的名称在通知方法声明形参，Spring会将目标方法抛出的异常对象从这里传给我们
@AfterThrowing(
        value = "execution(public int com.atguigu.aop.api.Calculator.add(int,int))",
        throwing = "targetMethodException"
)
public void printLogAfterCoreException(JoinPoint joinPoint, Throwable targetMethodException) {
    
    String methodName = joinPoint.getSignature().getName();
    
    System.out.println("[AOP异常通知] "+methodName+"方法抛异常了，异常类型是：" + targetMethodException.getClass().getName());
}
```

##### 切点表达式语法
  1. **切点表达式作用**

      AOP切点表达式（Pointcut Expression）是一种用于指定切点的语言，它可以通过定义匹配规则，来选择需要被切入的目标对象。

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img028.cb7f2153.png)

  2. **切点表达式语法**

      切点表达式总结

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img011.dde1a79a.png)

      语法细节

      - 第一位：execution( ) 固定开头
      
      - 第二位：方法访问修饰符
      
        public private 直接描述对应修饰符即可
      
      -   第三位：方法返回值
      
        ```java
        int String void 直接描述返回值类型
        ```
      
        注意：
      
        特殊情况 不考虑 访问修饰符和返回值  execution(*) 相当于全部不考虑了
      
        execution(* * ) 这是错误语法
      
        
      
        
      
        - 第四位：指定包的地址

```Java
 固定的包: com.atguigu.api | service | dao
 单层的任意命名: com.atguigu.*  = com.atguigu.api  com.atguigu.dao  * = 任意一层的任意命名
 任意层任意命名: com.. = com.atguigu.api.erdaye com.a.a.a.a.a.a.a  ..任意层,任意命名 用在包上!
 注意: ..不能用作包开头   public int .. 错误语法  com..
 找到任何包下: *..
```
  - 第五位：指定类名称

```Java
固定名称: UserService
任意类名: *
部分任意: com..service.impl.*Impl
任意包任意类: *..*

```
-   - 第六位：指定方法名称

```Java
语法和类名一致
任意访问修饰符,任意类的任意方法: * *..*.*
```
-   - 第七位：方法参数

```Java
第七位: 方法的参数描述
       具体值: (String,int) != (int,String) 没有参数 ()
       模糊值: 任意参数 有 或者 没有 (..)  ..任意参数的意识
       部分具体和模糊:
         第一个参数是字符串的方法 (String..)
         最后一个参数是字符串 (..String)
         字符串开头,int结尾 (String..int)
         包含int类型(..int..)
```
  3. **切点表达式案例**

```Java
1.查询某包某类下，访问修饰符是公有，返回值是int的全部方法
    	public int xx.xx.jj.*(..)
2.查询某包下类中第一个参数是String的方法
    	* xx.xx.jj.*(String..)
3.查询全部包下，无参数的方法！
    	* *..*.*(..)
4.查询com包下，以int参数类型结尾的方法
    	* com..*.*(..int)
5.查询指定包下，Service开头类的私有返回值int的无参数方法
    	private int xx.xx.Service*.*()
```

##### 重用（提取）切点表达式
  1. 重用切点表达式优点

```Java
 // @Before注解：声明当前方法是前置通知方法
// value属性：指定切入点表达式，由切入点表达式控制当前通知方法要作用在哪一个目标方法上
@Before(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
public void printLogBeforeCore() {
    System.out.println("[AOP前置通知] 方法开始了");
}

@AfterReturning(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
public void printLogAfterSuccess() {
    System.out.println("[AOP返回通知] 方法成功返回了");
}

@AfterThrowing(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
public void printLogAfterException() {
    System.out.println("[AOP异常通知] 方法抛异常了");
}

@After(value = "execution(public int com.atguigu.proxy.CalculatorPureImpl.add(int,int))")
public void printLogFinallyEnd() {
    System.out.println("[AOP后置通知] 方法最终结束了");
}
```

  上面案例，是我们之前编写切点表达式的方式，发现， 所有增强方法的切点表达式相同！出现了冗余，如果需要切换也不方便统一维护！我们可以将切点提取，在增强上进行引用即可！

  2. 同一类内部引用

      提取

```Java
// 切入点表达式重用
@Pointcut("execution(public int com.atguigu.aop.api.Calculator.add(int,int)))")
public void declarPointCut() {}
```

  		注意：提取切点注解使用@Pointcut(切点表达式) ， 需要添加到一个无参数无返回值方法上即可！
  	
  			引用

```Java
@Before(value = "declarPointCut()")
public void printLogBeforeCoreOperation(JoinPoint joinPoint) {
```
  3. 不同类中引用

      不同类在引用切点，只需要添加类的全限定符+方法名即可！

```Java
@Before(value = "com.atguigu.spring.aop.aspect.LogAspect.declarPointCut()")
public Object roundAdvice(ProceedingJoinPoint joinPoint) {
```
  4. 切点统一管理

      建议：将切点表达式统一存储到一个类中进行集中管理和维护！

```Java
@Component
public class AtguiguPointCut {
    
    @Pointcut(value = "execution(public int *..Calculator.sub(int,int))")
    public void atguiguGlobalPointCut(){}
    
    @Pointcut(value = "execution(public int *..Calculator.add(int,int))")
    public void atguiguSecondPointCut(){}
    
    @Pointcut(value = "execution(* *..*Service.*(..))")
    public void transactionPointCut(){}
}
```

##### 环绕通知

  环绕通知对应整个 try...catch...finally 结构，包括前面四种通知的所有功能。

```Java
// 使用@Around注解标明环绕通知方法
@Around(value = "com.atguigu.aop.aspect.AtguiguPointCut.transactionPointCut()")
public Object manageTransaction(
        // 通过在通知方法形参位置声明ProceedingJoinPoint类型的形参，
        // Spring会将这个类型的对象传给我们
        ProceedingJoinPoint joinPoint) {
    
    // 通过ProceedingJoinPoint对象获取外界调用目标方法时传入的实参数组
    Object[] args = joinPoint.getArgs();
    
    // 通过ProceedingJoinPoint对象获取目标方法的签名对象
    Signature signature = joinPoint.getSignature();
    
    // 通过签名对象获取目标方法的方法名
    String methodName = signature.getName();
    
    // 声明变量用来存储目标方法的返回值
    Object targetMethodReturnValue = null;
    
    try {
    
        // 在目标方法执行前：开启事务（模拟）
        log.debug("[AOP 环绕通知] 开启事务，方法名：" + methodName + "，参数列表：" + Arrays.asList(args));
    
        // 过ProceedingJoinPoint对象调用目标方法
        // 目标方法的返回值一定要返回给外界调用者
        targetMethodReturnValue = joinPoint.proceed(args);
    
        // 在目标方法成功返回后：提交事务（模拟）
        log.debug("[AOP 环绕通知] 提交事务，方法名：" + methodName + "，方法返回值：" + targetMethodReturnValue);
    
    }catch (Throwable e){
    
        // 在目标方法抛异常后：回滚事务（模拟）
        log.debug("[AOP 环绕通知] 回滚事务，方法名：" + methodName + "，异常：" + e.getClass().getName());
    
    }finally {
    
        // 在目标方法最终结束后：释放数据库连接
        log.debug("[AOP 环绕通知] 释放数据库连接，方法名：" + methodName);
    
    }
    
    return targetMethodReturnValue;
}
```

##### 切面优先级设置

  相同目标方法上同时存在多个切面时，切面的优先级控制切面的内外嵌套顺序。

  - 优先级高的切面：外面
  - 优先级低的切面：里面

  使用 @Order 注解可以控制切面的优先级：

  - @Order(较小的数)：优先级高
  - @Order(较大的数)：优先级低

  <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img012.b353bc56.png" style="zoom:50%;" />

  实际意义

  实际开发时，如果有多个切面嵌套的情况，要慎重考虑。例如：如果事务切面优先级高，那么在缓存中命中数据的情况下，事务切面的操作都浪费了。

  <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img013.53c41dc7.png" style="zoom: 50%;" />

  

  此时应该将缓存切面的优先级提高，在事务操作之前先检查缓存中是否存在目标数据。

  <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img014.ee4ed40a.png" style="zoom:50%;" />

##### CGLib动态代理生效

  在目标类没有实现任何接口的情况下，Spring会自动使用cglib技术实现代理。为了证明这一点，我们做下面的测试：

```Java
@Service
public class EmployeeService {
    
    public void getEmpList() {
       System.out.print("方法内部 com.atguigu.aop.imp.EmployeeService.getEmpList");
    }
}
```

  测试：

```Java
  @Autowired
  private EmployeeService employeeService;
  
  @Test
  public void testNoInterfaceProxy() {
      employeeService.getEmpList();
  }
```

  没有接口：

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img029.d45d40f4.png)

  有接口：

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img030.e2f27997.png)

  使用总结：

    a.  如果目标类有接口,选择使用jdk动态代理
    b.  如果目标类没有接口,选择cglib动态代理
    c.  如果有接口,接口接值
    d.  如果没有接口,类进行接值

##### 注解实现小结

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img015.9c921baf.png)

#### Spring AOP对获取Bean的影响理解

##### 根据类型装配 bean
  1. 情景一
      - bean 对应的类没有实现任何接口
      - 根据 bean 本身的类型获取 bean
          - 测试：IOC容器中同类型的 bean 只有一个

              正常获取到 IOC 容器中的那个 bean 对象
          - 测试：IOC 容器中同类型的 bean 有多个

              会抛出 NoUniqueBeanDefinitionException 异常，表示 IOC 容器中这个类型的 bean 有多个
  2. 情景二
      - bean 对应的类实现了接口，这个接口也只有这一个实现类
          - 测试：根据接口类型获取 bean
          - 测试：根据类获取 bean
          - 结论：上面两种情况其实都能够正常获取到 bean，而且是同一个对象
  3. 情景三
      - 声明一个接口
      - 接口有多个实现类
      - 接口所有实现类都放入 IOC 容器
          - 测试：根据接口类型获取 bean

              会抛出 NoUniqueBeanDefinitionException 异常，表示 IOC 容器中这个类型的 bean 有多个
          - 测试：根据类获取bean

              正常
  4. 情景四
      - 声明一个接口
      - 接口有一个实现类
      - 创建一个切面类，对上面接口的实现类应用通知
          - 测试：根据接口类型获取bean

              正常
          - 测试：根据类获取bean

              无法获取

      原因分析：

      - 应用了切面后，真正放在IOC容器中的是代理类的对象
      - 目标类并没有被放到IOC容器中，所以根据目标类的类型从IOC容器中是找不到的

          <img src="http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img021.3e0da1cc.png" style="zoom: 67%;" />
  5. 情景五
      - 声明一个类
      - 创建一个切面类，对上面的类应用通知
          - 测试：根据类获取 bean，能获取到

          <img src="http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img023.b5696f3e.png" style="zoom:67%;" />

          debug查看实际类型：

          ![](http://heavy_code_industry.gitee.io/code_heavy_industry/assets/img/img024.558f6062.png)

#### 5.7.2 使用总结

  对实现了接口的类应用切面

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711372285583-18.png)

  对没实现接口的类应用切面new

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711372285583-17.png)

  **如果使用AOP技术，目标类有接口，必须使用接口类型接收IoC容器中代理组件！**

### Spring声明式事务

#### 声明式事务概念

##### 编程式事务

  编程式事务是指**手动编写程序来管理事务，即通过编写代码的方式直接控制事务的提交和回滚**。在 Java 中，通常使用事务管理器(如 Spring 中的 `PlatformTransactionManager`)来实现编程式事务。

  编程式事务的主要优点是灵活性高，可以按照自己的需求来控制事务的粒度、模式等等。但是，编写大量的事务控制代码容易出现问题，对代码的可读性和可维护性有一定影响。

```Java
Connection conn = ...;
  
try {
    // 开启事务：关闭事务的自动提交
    conn.setAutoCommit(false);
    // 核心操作
    // 业务代码
    // 提交事务
    conn.commit();
  
}catch(Exception e){
  
    // 回滚事务
    conn.rollBack();
  
}finally{
  
    // 释放数据库连接
    conn.close();
  
}
```

  编程式的实现方式存在缺陷：

  - 细节没有被屏蔽：具体操作过程中，所有细节都需要程序员自己来完成，比较繁琐。
  - 代码复用性不高：如果没有有效抽取出来，每次实现功能都需要自己编写代码，代码就没有得到复用。

##### 声明式事务

  声明式事务是指使用注解或 XML 配置的方式来控制事务的提交和回滚。开发者只需要添加配置即可， 具体事务的实现由第三方框架实现，避免我们直接进行事务操作！使用声明式事务可以将事务的控制和业务逻辑分离开来，提高代码的可读性和可维护性。

  区别：

  - 编程式事务需要手动编写代码来管理事务
  - 而声明式事务可以通过配置文件或注解来控制事务。 

##### Spring事务管理器
  1. Spring声明式事务对应依赖
      - spring-tx: 包含声明式事务实现的基本规范（事务管理器规范接口和事务增强等等）
      - spring-jdbc: 包含DataSource方式事务管理器实现类DataSourceTransactionManager
      - spring-orm: 包含其他持久层框架的事务管理器实现类例如：Hibernate/Jpa等
  2. Spring声明式事务对应事务管理器接口

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711374274334-23.png)

      我们现在要使用的事务管理器是org.springframework.jdbc.datasource.DataSourceTransactionManager，将来整合 JDBC方式、JdbcTemplate方式、Mybatis方式的事务实现！

      DataSourceTransactionManager类中的主要方法：

      - doBegin()：开启事务
      - doSuspend()：挂起事务
      - doResume()：恢复挂起的事务
      - doCommit()：提交事务
      - doRollback()：回滚事务

#### 基于注解的声明式事务

##### 准备工作
  1. 准备项目

```XML
<dependencies>
  <!--spring context依赖-->
  <!--当你引入Spring Context依赖之后，表示将Spring的基础依赖引入了-->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>6.0.6</version>
  </dependency>

  <!--junit5测试-->
  <dependency>
      <groupId>org.junit.jupiter</groupId>
      <artifactId>junit-jupiter-api</artifactId>
      <version>5.3.1</version>
  </dependency>

  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>6.0.6</version>
      <scope>test</scope>
  </dependency>

  <dependency>
      <groupId>jakarta.annotation</groupId>
      <artifactId>jakarta.annotation-api</artifactId>
      <version>2.1.1</version>
  </dependency>

  <!-- 数据库驱动 和 连接池-->
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.25</version>
  </dependency>

  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.2.8</version>
  </dependency>

  <!-- spring-jdbc -->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>6.0.6</version>
  </dependency>

  <!-- 声明式事务依赖-->
  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>6.0.6</version>
  </dependency>

  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aop</artifactId>
      <version>6.0.6</version>
  </dependency>

  <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>6.0.6</version>
  </dependency>
</dependencies>
```
  2. 外部配置文件

      jdbc.properties

```.properties
atguigu.url=jdbc:mysql://localhost:3306/studb
atguigu.driver=com.mysql.cj.jdbc.Driver
atguigu.username=root
atguigu.password=root
```
  3. spring配置文件

```Java
@Configuration
@ComponentScan("com.atguigu")
@PropertySource("classpath:jdbc.properties")
public class JavaConfig {

    @Value("${atguigu.driver}")
    private String driver;
    @Value("${atguigu.url}")
    private String url;
    @Value("${atguigu.username}")
    private String username;
    @Value("${atguigu.password}")
    private String password;

    //druid连接池
    @Bean
    public DataSource dataSource(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    @Bean
    //jdbcTemplate
    public JdbcTemplate jdbcTemplate(DataSource dataSource){
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }

}
```
  4. 准备dao/service层

      dao

```Java
@Repository
public class StudentDao {
    
    @Autowired
    private JdbcTemplate jdbcTemplate;
    
    public void updateNameById(String name,Integer id){
        String sql = "update students set name = ? where id = ? ;";
        int rows = jdbcTemplate.update(sql, name, id);
    }

    public void updateAgeById(Integer age,Integer id){
        String sql = "update students set age = ? where id = ? ;";
        jdbcTemplate.update(sql,age,id);
    }
}

```

 			 service

```Java
@Service
public class StudentService {
    
    @Autowired
    private StudentDao studentDao;
    
    public void changeInfo(){
        studentDao.updateAgeById(100,1);
        System.out.println("-----------");
        studentDao.updateNameById("test1",1);
    }
}

```
  5. 测试环境搭建

```Java
/**
 * projectName: com.atguigu.test
 *
 * description:
 */
@SpringJUnitConfig(JavaConfig.class)
public class TxTest {

    @Autowired
    private StudentService studentService;

    @Test
    public void  testTx(){
        studentService.changeInfo();
    }
}

```

##### 基本事务控制

<img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240325220416297.png" alt="image-20240325220416297" style="zoom: 67%;" />

  1. 配置事务管理器

      数据库相关的配置

```Java
/**
 * projectName: com.atguigu.config
 *
 * description: 数据库和连接池配置类
 */

@Configuration
@ComponenScan("com.atguigu")
@PropertySource(value = "classpath:jdbc.properties")
@EnableTransactionManagement//开启事务注解的支持
public class DataSourceConfig {
    /**
     * 实例化dataSource加入到ioc容器
     * @param url
     * @param driver
     * @param username
     * @param password
     * @return
     */
    @Bean
    public DataSource dataSource(@Value("${atguigu.url}")String url,
                                 @Value("${atguigu.driver}")String driver,
                                 @Value("${atguigu.username}")String username,
                                 @Value("${atguigu.password}")String password){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(driver);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);

        return dataSource;
    }

    /**
     * 实例化JdbcTemplate对象,需要使用ioc中的DataSource
     * @param dataSource
     * @return
     */
    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource){
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource);
        return jdbcTemplate;
    }
    
    /**
     * 装配事务管理实现对象
     * @param dataSource
     * @return
     */
    @Bean
    public TransactionManager transactionManager(DataSource dataSource){
        return new DataSourceTransactionManager(dataSource);
    }
}
```
  2. 使用声明事务注解@Transactional

```Java
/**
 * projectName: com.atguigu.service
 *
 */
@Service
public class StudentService {

    @Autowired
    private StudentDao studentDao;

    @Transactional
    //位置：方法/类上
    // 方法：当前方法有事务
    // 类上：类下的所有方法都有事务
    public void changeInfo(){
        studentDao.updateAgeById(100,1);
        System.out.println("-----------");
        int i = 1/0;
        studentDao.updateNameById("test1",1);
    }
}

```
  3. 测试事务效果

```Java
/**
 * projectName: com.atguigu.test
 *
 * description:
 */
//@SpringJUnitConfig(locations = "classpath:application.xml")
@SpringJUnitConfig(classes = DataSourceConfig.class)
public class TxTest {

    @Autowired
    private StudentService studentService;

    @Test
    public void  testTx(){
        studentService.changeInfo();
    }
}

```

##### 事务属性：只读
  1. 只读介绍

      对一个查询操作来说，如果我们把它设置成只读，就能够明确告诉数据库，这个操作不涉及写操作。这样数据库就能够针对查询操作来进行优化。
  2. 设置方式

```Java
// readOnly = true把当前事务设置为只读 默认是false!
@Transactional(readOnly = true)
```
  3. 针对DML动作设置只读模式

      会抛出下面异常：

      Caused by: java.sql.SQLException: Connection is read-only. Queries leading to data modification are not allowed
  4. @Transactional注解放在类上
      1. 生效原则

          如果一个类中每一个方法上都使用了 @Transactional 注解，那么就可以将 @Transactional 注解提取到类上。反过来说：**@Transactional 注解在类级别标记，会影响到类中的每一个方法。**同时，类级别标记的 @Transactional 注解中设置的事务属性也会延续影响到方法执行时的事务属性。除非在方法上又设置了 @Transactional 注解。对一个方法来说，**离它最近的 @Transactional 注解中的事务属性设置生效。**

      2. 用法举例
      
          在类级别@Transactional注解中设置只读，这样类中所有的查询方法都不需要设置@Transactional注解了。因为对查询操作来说，其他属性通常不需要设置，所以使用公共设置即可。然后在这个基础上，对增删改方法设置@Transactional注解 readOnly 属性为 false。
      

```Java
@Service
@Transactional(readOnly = true)
public class EmpService {
    
    // 为了便于核对数据库操作结果，不要修改同一条记录
    @Transactional(readOnly = false)
    public void updateTwice(……) {
    ……
    }
    
    // readOnly = true把当前事务设置为只读
    // @Transactional(readOnly = true)
    public String getEmpName(Integer empId) {
    ……
    }
    
}
```

##### 事务属性：超时时间

**也可以设置在类上**

  1. 需求

      事务在执行过程中，有可能因为遇到某些问题，导致程序卡住，从而长时间占用数据库资源。而长时间占用资源，大概率是因为程序运行出现了问题（可能是Java程序或MySQL数据库或网络连接等等）。此时这个很可能出问题的程序应该被回滚，撤销它已做的操作，事务结束，把资源让出来，让其他正常程序可以执行。

      概括来说就是一句话：**超时回滚，释放资源。**

      
      
      2.设置超时时间

```Java
@Service
public class StudentService {

    @Autowired
    private StudentDao studentDao;

    /**
     * timeout设置事务超时时间,单位秒! 默认: -1 永不超时,不限制事务时间!
     */
    @Transactional(readOnly = false,timeout = 3)
    public void changeInfo(){
        studentDao.updateAgeById(100,1);
        //休眠4秒,等待方法超时!
        try {
            Thread.sleep(4000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        studentDao.updateNameById("test1",1);
    }
}

```
  3. 测试超时效果

      执行抛出事务超时异常

```Java
org.springframework.transaction.TransactionTimedOutException: Transaction timed out: deadline was Wed May 24 09:10:43 IRKT 2023

  at org.springframework.transaction.support.ResourceHolderSupport.checkTransactionTimeout(ResourceHolderSupport.java:155)
  at org.springframework.transaction.support.ResourceHolderSupport.getTimeToLiveInMillis(ResourceHolderSupport.java:144)
  at org.springframework.transaction.support.ResourceHolderSupport.getTimeToLiveInSeconds(ResourceHolderSupport.java:128)
  at org.springframework.jdbc.datasource.DataSourceUtils.applyTimeout(DataSourceUtils.java:341)
  at org.springframework.jdbc.core.JdbcTemplate.applyStatementSettings(JdbcTemplate.java:1467)

```

##### 事务属性：事务异常
  1. 默认情况

      **默认只针对运行时异常回滚，编译时异常不回滚**。情景模拟代码如下：

```Java
@Service
public class StudentService {

    @Autowired
    private StudentDao studentDao;

    /**
     * timeout设置事务超时时间,单位秒! 默认: -1 永不超时,不限制事务时间!
     * rollbackFor = 指定哪些异常才会回滚,默认是 RuntimeException and Error 异常方可回滚!
     * noRollbackFor = 指定哪些异常不会回滚, 默认没有指定,如果指定,应该在rollbackFor的范围内!
     */
    @Transactional(readOnly = false,timeout = 3)
    public void changeInfo() throws FileNotFoundException {
        studentDao.updateAgeById(100,1);
        //主动抛出一个检查异常,测试! 发现不会回滚,因为不在rollbackFor的默认范围内! 
        new FileInputStream("xxxx");
        studentDao.updateNameById("test1",1);
    }
}
```
  2. 设置回滚异常

      rollbackFor属性：指定哪些异常类才会回滚,默认是 RuntimeException and Error 异常方可回滚!

```Java
/**
 * timeout设置事务超时时间,单位秒! 默认: -1 永不超时,不限制事务时间!
 * rollbackFor = 指定哪些异常才会回滚,默认是 RuntimeException and Error 异常方可回滚!
 * noRollbackFor = 指定哪些异常不会回滚, 默认没有指定,如果指定,应该在rollbackFor的范围内!
 */
@Transactional(readOnly = false,timeout = 3,rollbackFor = Exception.class)
public void changeInfo() throws FileNotFoundException {
    studentDao.updateAgeById(100,1);
    //主动抛出一个检查异常,测试! 发现不会回滚,因为不在rollbackFor的默认范围内! 
    new FileInputStream("xxxx");
    studentDao.updateNameById("test1",1);
}
```
  3. 设置不回滚的异常

      在默认设置和已有设置的基础上，再指定一个异常类型，碰到它不回滚。

      noRollbackFor属性：指定哪些异常不会回滚, 默认没有指定,如果指定,应该在rollbackFor的范围内!

```Java
@Service
public class StudentService {

    @Autowired
    private StudentDao studentDao;

    /**
     * timeout设置事务超时时间,单位秒! 默认: -1 永不超时,不限制事务时间!
     * rollbackFor = 指定哪些异常才会回滚,默认是 RuntimeException and Error 异常方可回滚!
     * noRollbackFor = 指定哪些异常不会回滚, 默认没有指定,如果指定,应该在rollbackFor的范围内!
     */
    @Transactional(readOnly = false,timeout = 3,rollbackFor = Exception.class,noRollbackFor = FileNotFoundException.class)
    public void changeInfo() throws FileNotFoundException {
        studentDao.updateAgeById(100,1);
        //主动抛出一个检查异常,测试! 发现不会回滚,因为不在rollbackFor的默认范围内!
        new FileInputStream("xxxx");
        studentDao.updateNameById("test1",1);
    }
}

```

##### 事务属性：事务隔离级别
  1. 事务隔离级别

      数据库事务的隔离级别是指在多个事务并发执行时，数据库系统为了保证数据一致性所遵循的规定。常见的隔离级别包括：**（默认为可重复读，也建议修改为读已提交）**

      1. 读未提交（Read Uncommitted）：**事务可以读取未被提交的数据，容易产生脏读、不可重复读和幻读**等问题。实现简单但不太安全，一般不用。
      2. 读已提交（Read Committed）：事务只能读取已经提交的数据，**可以避免脏读问题，但可能引发不可重复读和幻读。**（Oracle）
      3. 可重复读（Repeatable Read）：在一个事务中，相同的查询将返回相同的结果集，不管其他事务对数据做了什么修改。可以避免脏读和不可重复读，但仍有幻读的问题。（Mysql）
      4. 串行化（Serializable）：最高的隔离级别，完全禁止了并发，只允许一个事务执行完毕之后才能执行另一个事务。可以避免以上所有问题，但效率较低，**不适用于高并发场景。**

      
      
      不同的隔离级别适用于不同的场景，需要根据实际业务需求进行选择和调整。
      
      1. 脏读（Dirty Read）：**一个事务读取了另一个事务未提交的数据**，然后基于这些未提交的数据进行操作。如果另一个事务在之后回滚，那么读取到的数据就是无效的，这就是脏读。脏读会导致不一致的数据被传播，破坏了事务的隔离性。
      2. 不可重复读（Non-Repeatable Read）：**一个事务在多次读取同一条记录时，由于其他事务的更新操作，在这几次读取中得到了不同的结果**。这是因为在两次读取之间，有另一个事务修改了数据，导致第一个事务看到了不一致的数据。这种情况称为不可重复读。
      3. 幻读（Phantom Read）：**一个事务在同样的查询条件下多次查询，但是在每次查询中返回的数据集不同，这是由于其他事务插入新数据或者删除现有数据导致的**。换句话说，幻读指的是同样的查询条件下，事务在不同时间点得到了不同数量的记录，造成了"幻觉"一样的现象。
      4. 不可重复读关注的是同一条记录的多次读取结果不一致的情况，而幻读关注的是同一个范围内多次查询结果集不一致的情况。
      
  2. 事务隔离级别设置

```Java
package com.atguigu.service;

import com.atguigu.dao.StudentDao;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Isolation;
import org.springframework.transaction.annotation.Transactional;

import java.io.FileInputStream;
import java.io.FileNotFoundException;

/**
 * projectName: com.atguigu.service
 */
@Service
public class StudentService {

    @Autowired
    private StudentDao studentDao;

    /**
     * timeout设置事务超时时间,单位秒! 默认: -1 永不超时,不限制事务时间!
     * rollbackFor = 指定哪些异常才会回滚,默认是 RuntimeException and Error 异常方可回滚!
     * noRollbackFor = 指定哪些异常不会回滚, 默认没有指定,如果指定,应该在rollbackFor的范围内!
     * isolation = 设置事务的隔离级别,mysql默认是repeatable read!
     */
    @Transactional(readOnly = false,
                   timeout = 3,
                   rollbackFor = Exception.class,
                   noRollbackFor = FileNotFoundException.class,
                   isolation = Isolation.REPEATABLE_READ)
    public void changeInfo() throws FileNotFoundException {
        studentDao.updateAgeById(100,1);
        //主动抛出一个检查异常,测试! 发现不会回滚,因为不在rollbackFor的默认范围内!
        new FileInputStream("xxxx");
        studentDao.updateNameById("test1",1);
    }
}

```

##### 事务属性：事务传播行为
  1. 事务传播行为要研究的问题

      ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img012.faac2cb7.png)

      举例代码：

```Java
@Transactional
public void MethodA(){
    // ...
    MethodB();
    // ...
}

//在被调用的子方法中设置传播行为，代表如何处理调用的事务！ 是加入，还是新事务等！
@Transactional(propagation = Propagation.REQUIRES_NEW)
public void MethodB(){
    // ...
}

```
  2. propagation(传播)属性

      @Transactional 注解通过 propagation 属性设置事务的传播行为。它的默认值是：

```Java
Propagation propagation() default Propagation.REQUIRED;
```

  propagation 属性的可选值由 org.springframework.transaction.annotation.Propagation 枚举类提供：

| 名称             | 含义                                               |
| ---------------- | -------------------------------------------------- |
| REQUIRED  默认值 | 如果父方法有事务，就加入，如果没有就新建自己独立！ |
| REQUIRES_NEW     | 不管父方法是否有事务，我都新建事务，都是独立的！   |

  3. 测试
      1. 声明两个业务方法

```Java
@Service
public class StudentService {

    @Autowired
    private StudentDao studentDao;

    /**
     * timeout设置事务超时时间,单位秒! 默认: -1 永不超时,不限制事务时间!
     * rollbackFor = 指定哪些异常才会回滚,默认是 RuntimeException and Error 异常方可回滚!
     * noRollbackFor = 指定哪些异常不会回滚, 默认没有指定,如果指定,应该在rollbackFor的范围内!
     * isolation = 设置事务的隔离级别,mysql默认是repeatable read!
     */
    @Transactional(readOnly = false,
                   timeout = 3,
                   rollbackFor = Exception.class,
                   noRollbackFor = FileNotFoundException.class,
                   isolation = Isolation.REPEATABLE_READ)
    public void changeInfo() throws FileNotFoundException {
        studentDao.updateAgeById(100,1);
        //主动抛出一个检查异常,测试! 发现不会回滚,因为不在rollbackFor的默认范围内!
        new FileInputStream("xxxx");
        studentDao.updateNameById("test1",1);
    }
    

    /**
     * 声明两个独立修改数据库的事务业务方法
     */
    @Transactional(propagation = Propagation.REQUIRED)
    public void changeAge(){
        studentDao.updateAgeById(99,1);
    }

    @Transactional(propagation = Propagation.REQUIRED)
    public void changeName(){
        studentDao.updateNameById("test2",1);
        int i = 1/0;
    }

}
```
b.	声明一个整合业务方法

```Java
@Service
public class TopService {

    @Autowired
    private StudentService studentService;

    @Transactional
    public void  topService(){
        studentService.changeAge();
        studentService.changeName();
    }
}

```
  c. 添加传播行为测试

```Java
@SpringJUnitConfig(classes = AppConfig.class)
public class TxTest {

    @Autowired
    private StudentService studentService;

    @Autowired
    private TopService topService;

    @Test
    public void  testTx() throws FileNotFoundException {
        topService.topService();
    }
}

```

  **注意：**

​    在同一个类中，对于@Transactional注解的方法调用，事务传播行为不会生效。这是因为Spring框架中使用代理模式实现了事务机制，在同一个类中的方法调用并不经过代理，而是通过对象的方法调用，因此@Transactional注解的设置不会被代理捕获，也就不会产生任何事务传播行为的效果。

  4. 其他传播行为值（了解）
      1. Propagation.REQUIRED：如果当前存在事务，则加入当前事务，否则创建一个新事务。
      2. Propagation.REQUIRES_NEW：创建一个新事务，并在新事务中执行。如果当前存在事务，则挂起当前事务，即使新事务抛出异常，也不会影响当前事务。
      3. Propagation.NESTED（嵌套的）：如果当前存在事务，则在该事务中嵌套一个新事务，如果没有事务，则与Propagation.REQUIRED一样。
      4. Propagation.SUPPORTS：如果当前存在事务，则加入该事务，否则以非事务方式执行。
      5. Propagation.NOT_SUPPORTED：以非事务方式执行，如果当前存在事务，挂起该事务。
      6. Propagation.MANDATORY：必须在一个已有的事务中执行，否则抛出异常。
      7. Propagation.NEVER：必须在没有事务的情况下执行，否则抛出异常。

### Spring核心掌握总结

| 核心点          | 掌握目标                                           |
| --------------- | -------------------------------------------------- |
| spring框架理解  | spring家族和spring framework框架                   |
| spring核心功能  | ioc/di , aop , tx                                  |
| spring ioc / di | 组件管理、ioc容器、ioc/di , 三种配置方式           |
| spring aop      | aop和aop框架和代理技术、基于注解的aop配置          |
| spring tx       | 声明式和编程式事务、动态事务管理器、事务注解、属性 |

## Spring MVC：构建高效表述层框架

### Spring MVC 简介和体验

Spring Web MVC是基于Servlet API构建的原始Web框架，从一开始就包含在Spring Framework中。正式名称“Spring Web MVC”来自其源模块的名称（ **spring-webmvc** ），但它通常被称为“Spring MVC”。

#### 作用

    1. 简化前端参数接收( 形参列表 )
    2. 简化后端数据响应(返回值)
    3. 以及其他......

#### 核心组件和调用流程理解

  Spring MVC与许多其他Web框架一样，是围绕前端控制器模式设计的，其中中央 `Servlet`  `DispatcherServlet` 做整体请求处理调度！  除了`DispatcherServlet`SpringMVC还会提供其他特殊的组件协作完成请求处理和响应呈现。

#####   **SpringMVC处理请求流程：**

![image-20240326162859153](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240326162859153.png)

  <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711638235769-31.png" style="zoom:150%;" />

#####   **SpringMVC涉及组件理解：**

       1. DispatcherServlet :  SpringMVC提供，**我们需要使用web.xml配置使其生效**，它是整个流程处理的核心，所有请求都经过它的处理和分发！[ CEO ]
       2. HandlerMapping :  SpringMVC提供，我们**需要进行IoC配置使其加入IoC容器**方可生效，它内部缓存handler(controller方法)和handler访问路径数据，被DispatcherServlet调用，**用于查找路径对应的handler！**[秘书]
       3. HandlerAdapter : SpringMVC提供，我们需要**进行IoC配置使其加入IoC容器**方可生效，它可以处理请求参数和处理响应数据数据，每次DispatcherServlet都是通过handlerAdapter**间接调用handler**，他是handler和DispatcherServlet之间的适配器！[经理]
       4. Handler : **handler又称处理器，他是Controller类内部的方法简称**，是由我们自己定义，用来接收参数，向后调用业务，最终返回响应结果！[打工人]
       5. ViewResovler : SpringMVC提供，我们需要**进行IoC配置使其加入IoC容器方可生效**！视图解析器主要作用简化模版视图页面查找的，但是需要注意，前后端分离项目，后端只返回JSON数据，不返回页面，那就不需要视图解析器！所以，视图解析器，相对其他的组件不是必须的！[财务]

#### 快速体验

![image-20240326163629059](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240326163629059.png)

### Spring MVC接收数据

![image-20240326195247897](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240326195247897.png)

#### 访问路径设置

  **@RequestMapping注解的作用就是将请求的 URL 地址和处理请求的方式（handler方法）关联起来**，建立映射关系。  SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的方法来处理这个请求。

  1. **精准路径匹配**

     在@RequestMapping注解指定 URL 地址时，不使用任何通配符，按照请求地址进行精确匹配。

```Java
@Controller
public class UserController {

    /**
     * 精准设置访问地址 /user/login
     */
    @RequestMapping(value = {"/user/login"})
    @ResponseBody
    public String login(){
        System.out.println("UserController.login");
        return "login success!!";
    }

    /**
     * 精准设置访问地址 /user/register
     */
    @RequestMapping(value = {"/user/register"})
    @ResponseBody
    public String register(){
        System.out.println("UserController.register");
        return "register success!!";
    }
    
}

```

  2. **模糊路径匹配**

     在@RequestMapping注解指定 URL 地址时，通过使用通配符，匹配多个类似的地址。

```Java
@Controller
public class ProductController {

    /**
     *  路径设置为 /product/*  
     *    /* 为单层任意字符串  /product/a  /product/aaa 可以访问此handler  
     *    /product/a/a 不可以
     *  路径设置为 /product/** 
     *   /** 为任意层任意字符串  /product/a  /product/aaa 可以访问此handler  
     *   /product/a/a 也可以访问
     */
    @RequestMapping("/product/*")
    @ResponseBody
    public String show(){
        System.out.println("ProductController.show");
        return "product show!";
    }
}

```

单层匹配和多层匹配：
  /*：只能匹配URL地址中的一层，如果想准确匹配两层，那么就写“/*/*”以此类推。
  /**：可以匹配URL地址中的多层。
其中所谓的一层或多层是指一个URL地址字符串被“/”划分出来的各个层次
这个知识点虽然对于@RequestMapping注解来说实用性不大，但是将来配置拦截器的时候也遵循这个规则。

  3. **类和方法级别区别**

     `@RequestMapping` 注解可以用于类级别和方法级别，它们之间的区别如下：

     1. **设置到类级别**：`@RequestMapping` 注解可以设置在控制器类上，用于映射整个控制器的通用请求路径。这样，如果控制器中的多个方法都需要映射同一请求路径，就不需要在每个方法上都添加映射路径。
     2. **设置到方法级别**：`@RequestMapping` 注解也可以单独设置在控制器方法上，用于更细粒度地映射请求路径和处理方法。当多个方法处理同一个路径的不同操作时，可以使用方法级别的 `@RequestMapping` 注解进行更精细的映射。

```Java
//1.标记到handler方法
@RequestMapping("/user/login")
@RequestMapping("/user/register")
@RequestMapping("/user/logout")

//2.优化标记类+handler方法
//类上
@RequestMapping("/user")
//handler方法上
@RequestMapping("/login")
@RequestMapping("/register")
@RequestMapping("/logout")
```

  4. **附带请求方式限制**

     HTTP 协议定义了八种请求方式，在 SpringMVC 中封装到了下面这个枚举类：

```Java
public enum RequestMethod {
  GET, HEAD, POST, PUT, PATCH, DELETE, OPTIONS, TRACE
}
```

  **默认**情况下：@RequestMapping("/logout") **任何请求方式都可以访问！**

  如果需要特定指定：

```Java
@Controller
public class UserController {

    /**
     * 精准设置访问地址 /user/login
     * method = RequestMethod.POST 可以指定单个或者多个请求方式!
     */
    @RequestMapping(value = {"/user/login"} , method = RequestMethod.POST)
    @ResponseBody
    public String login(){
        System.out.println("UserController.login");
        return "login success!!";
    }

    /**
     * 精准设置访问地址 /user/register
     */
    @RequestMapping(value = {"/user/register"},method = {RequestMethod.POST,RequestMethod.GET})
    @ResponseBody
    public String register(){
        System.out.println("UserController.register");
        return "register success!!";
    }

}
```

  注意：违背请求方式，会出现405异常！！！

  5. **进阶注解**

     还有 `@RequestMapping` 的 HTTP 方法特定快捷方式变体：

     - `@GetMapping`
     - `@PostMapping`
     - `@PutMapping`
     - `@DeleteMapping`
     - `@PatchMapping`

```Java
@RequestMapping(value="/login",method=RequestMethod.GET)
||
@GetMapping(value="/login")
```

  注意：**进阶注解只能添加到handler方法上，无法添加到类上！**

  6. **常见配置问题**

     出现原因：多个 handler 方法映射了同一个地址，导致 SpringMVC 在接收到这个地址的请求时该找哪个 handler 方法处理。

     > There is already 'demo03MappingMethodHandler' bean method com.atguigu.mvc.handler.Demo03MappingMethodHandler#empGet() **mapped**.

#### 接收参数

##### 1. param 和 json参数比较

  在 HTTP 请求中，我们可以选择不同的参数类型，如 param 类型和 JSON 类型。

  1. 参数编码：  

     param 类型的参数会被编码为 **ASCII 码**。例如，假设 `name=john doe`，则会被编码为 `name=john%20doe`。而 **JSON 类型的参数会被编码为 UTF-8。**

  2. 参数顺序：  

     param 类型的参数没有顺序限制。但是，J**SON 类型的参数是有序的**。JSON 采用键值对的形式进行传递，其中键值对是有序排列的。

  3. 数据类型：  

     param 类型的参数仅支持字符串类型、数值类型和布尔类型等简单数据类型。而 **JSON 类型的参数则支持更复杂的数据类型，如数组、对象等。**

  4. 嵌套性：  

     param 类型的参数不支持嵌套。但是，JSON 类型的参数**支持嵌套，可**以传递更为复杂的数据结构。

  5. 可读性：  

     param 类型的参数格式比 JSON 类型的参数更加简单、易读。但是，JSON 格式在传递嵌套数据结构时更加清晰易懂。

  总的来说，param 类型的参数适用于单一的数据传递，而 JSON 类型的参数则更适用于更复杂的数据结构传递。根据具体的业务需求，需要选择合适的参数类型。在实际开发中，常见的做法是：在 GET 请求中采用 param 类型的参数，而在 POST 请求中采用 JSON 类型的参数传递。

##### 2. param参数接收

  1. **直接接值**

     客户端请求

     ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711446641945-3.png)

     handler接收参数

     只要形参数名和类型与传递参数相同，即可自动接收!

```Java
@Controller
@RequestMapping("param")
public class ParamController {

    /**
     * 前端请求: http://localhost:8080/param/value?name=xx&age=18
     *
     * 可以利用形参列表,直接接收前端传递的param参数!
     *    要求: 参数名 = 形参名
     *          类型相同
     * 出现乱码正常，json接收具体解决！！
     * @return 返回前端数据
     */
    @GetMapping(value="/value")
    @ResponseBody
    public String setupForm(String name,int age){
        System.out.println("name = " + name + ", age = " + age);
        return name + age;
    } 
}
```

  2. **@RequestParam注解**

     可以使用 `@RequestParam` 注释将 Servlet 请求参数（即查询参数或表单数据）绑定到控制器中的方法参数。

     `@RequestParam`使用场景：

       - **指定绑定的请求参数名**   
       - **要求请求参数必须传递**
       - **为请求参数提供默认值**

```Java
 /**
 * 前端请求: http://localhost:8080/param/data?name=xx&stuAge=18
 * 
 *  使用@RequestParam注解标记handler方法的形参
 *  指定形参对应的请求参数@RequestParam(请求参数名称)
 */
@GetMapping(value="/data")
@ResponseBody
public Object paramForm(@RequestParam("name") String name, 
                        @RequestParam("stuAge") int age){
    System.out.println("name = " + name + ", age = " + age);
    return name+age;
}
```

  默认情况下，使用此批注的方法参数是必需的，但您可以通过将 `@RequestParam` 批注的 `required` 标志设置为 `false`！**如果必须传但是没有传递参数会出现：**

![](https://secure2.wostatic.cn/static/rdbdJyYUSsMtSsANx5icFq/image.png?auth_key=1711446623-hb3yPg8u4v7RU41REV1Zgx-0-373bbf899d9b9bbbd132ebfcde0139af)

  将参数设置非必须，并且设置默认值：

```Java
@GetMapping(value="/data")
@ResponseBody
public Object paramForm(@RequestParam("name") String name, 
                        @RequestParam(value = "stuAge",required = false,defaultValue = "18") int age){
    System.out.println("name = " + name + ", age = " + age);
    return name+age;
}
```

  3. ##### 特殊场景接值

     1. 一名多值

        多选框，提交的数据的时候一个key对应多个值，我们可以使用集合进行接收！必须使用requestparam注解！！！

```Java
  /**
   * 前端请求: http://localhost:8080/param/mul?hbs=吃&hbs=喝
   *  一名多值,可以使用集合接收即可!但是需要使用@RequestParam注解指定 必须使用该注解！！！
   */
  @GetMapping(value="/mul")
  @ResponseBody
  public Object mulForm(@RequestParam List<String> hbs){
      System.out.println("hbs = " + hbs);
      return hbs;
  }
```

**b. 实体接收**

Spring MVC 是 Spring 框架提供的 Web 框架，它允许开发者使用实体对象来接收 HTTP 请求中的参数。通过这种方式，可以在方法内部**直接使用对象的属性来访问请求参数，而不需要每个参数都写一遍。下面是一个使用实体对象接收参数的示例：定义一个用于接收参数的实体类：**

```Java
public class User {
  private String name;
  private int age = 18;
  // getter 和 setter 略
}
```

​     		 在控制器中，使用实体对象接收，示例代码如下：

```Java
@Controller
@RequestMapping("param")
public class ParamController {

    @RequestMapping(value = "/user", method = RequestMethod.POST)
    @ResponseBody
    public String addUser(User user) {
        // 在这里可以使用 user 对象的属性来接收请求参数
        System.out.println("user = " + user);
        return "success";
    }
}
```

​      在上述代码中，**将请求参数name和age映射到实体类属性上！要求属性名必须等于参数名！否则无法映射！**使用postman传递参数测试：

​      <img src="https://secure2.wostatic.cn/static/p9AtC4uV8sMvSkgcUKbGTa/image.png?auth_key=1711446623-BPiLhG2xoWj4a6qBseuxH-0-501c1c2b55534027867568ed75e95e0a" style="zoom: 33%;" />

##### 3. 路径参数接收

  路径传递参数是一种在 URL 路径中传递参数的方式。在 RESTful 的 Web 应用程序中，经常使用路径传递参数来表示资源的唯一标识符或更复杂的表示方式。而 Spring MVC 框架提供了 `@PathVariable` 注解来处理路径传递参数。

  `@PathVariable` 注解允许将 URL 中的占位符映射到控制器方法中的参数。例如，如果我们想将 `/user/{id}` 路径下的 `{id}` 映射到控制器方法的一个参数中，则可以使用 `@PathVariable` 注解来实现。下面是一个使用 `@PathVariable` 注解处理路径传递参数的示例：

```Java
 /**
 * 动态路径设计: /user/{动态部分}/{动态部分}   动态部分使用{}包含即可! {}内部动态标识!
 * 形参列表取值: @PathVariable Long id  如果形参名 = {动态标识} 自动赋值!
 *              @PathVariable("动态标识") Long id  如果形参名 != {动态标识} 可以通过指定动态标识赋值!
 *
 * 访问测试:  /param/user/1/root  -> id = 1  uname = root
 */
@GetMapping("/user/{id}/{name}")
@ResponseBody
public String getUser(@PathVariable Long id, 
                      @PathVariable("name") String uname) {
    System.out.println("id = " + id + ", uname = " + uname);
    return "user_detail";
}
```

##### 4. json参数接收

  前端传递 JSON 数据时，Spring MVC 框架可以**使用 `@RequestBody` 注解来将 JSON 数据转换为 Java 对象。**`@RequestBody` 注解表示当前方法参数的值应该从请求体中获取，并且需要指定 value 属性来指示请求体应该映射到哪个参数上。其使用方式和示例代码如下：

    1. 前端发送 JSON 数据的示例：（使用postman测试）

```JSON
{
  "name": "张三",
  "age": 18,
  "gender": "男"
}
```

    2. 定义一个用于接收 JSON 数据的 Java 类，例如：

```Java
public class Person {
  private String name;
  private int age;
  private String gender;
  // getter 和 setter 略
}
```

    3. 在控制器中，使用 `@RequestBody` 注解来接收 JSON 数据，并将其转换为 Java 对象，例如：

```Java
@PostMapping("/person")
@ResponseBody
public String addPerson(@RequestBody Person person) {
  // 在这里可以使用 person 对象来操作 JSON 数据中包含的属性
  return "success";
}
```

  在上述代码中，`@RequestBody` 注解将请求体中的 JSON 数据映射到 `Person` 类型的 `person` 参数上，并将其作为一个对象来传递给 `addPerson()` 方法进行处理。

  4. 完善配置

     测试：

       ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711451020795-6.png)

     问题：

       org.springframework.web.HttpMediaTypeNotSupportedException: Content-Type 'application/json;charset=UTF-8' is not supported]

       ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711451020796-7.png)

     原因：

     - 不支持json数据类型处理
     - 没有json类型处理的工具（jackson）

     解决：

     springmvc handlerAdpater配置json转化器,配置类需要明确：

```Java
//TODO: SpringMVC对应组件的配置类 [声明SpringMVC需要的组件信息]

//TODO: 导入handlerMapping和handlerAdapter的三种方式
 //1.自动导入handlerMapping和handlerAdapter [推荐]
 //2.可以不添加,springmvc会检查是否配置handlerMapping和handlerAdapter,没有配置默认加载
 //3.使用@Bean方式配置handlerMapper和handlerAdapter



@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器
@Configuration
@ComponentScan(basePackages = "com.atguigu.controller") //TODO: 进行controller扫描

//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现
public class SpringMvcConfig implements WebMvcConfigurer {


}
```

  pom.xml 加入jackson依赖

```XML
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.0</version>
</dependency>
```

  5. @EnableWebMvc注解说明

     @EnableWebMvc注解效果等同于在 XML 配置中，可以使用 `<mvc:annotation-driven>` 元素！我们来解析`<mvc:annotation-driven>`对应的解析工作！

     让我们来查看下`<mvc:annotation-driven>`具体的动作！

     - 先查看`<mvc:annotation-driven>`标签最终对应解析的Java类

       ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711451020796-8.png)

     - 查看解析类中具体的动作即可

       打开源码：org.springframework.web.servlet.config.MvcNamespaceHandler

       ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711451020796-9.png)

       打开源码：org.springframework.web.servlet.config.AnnotationDrivenBeanDefinitionParser

```Java
class AnnotationDrivenBeanDefinitionParser implements BeanDefinitionParser {

  public static final String HANDLER_MAPPING_BEAN_NAME = RequestMappingHandlerMapping.class.getName();

  public static final String HANDLER_ADAPTER_BEAN_NAME = RequestMappingHandlerAdapter.class.getName();

  static {
    ClassLoader classLoader = AnnotationDrivenBeanDefinitionParser.class.getClassLoader();
    javaxValidationPresent = ClassUtils.isPresent("jakarta.validation.Validator", classLoader);
    romePresent = ClassUtils.isPresent("com.rometools.rome.feed.WireFeed", classLoader);
    jaxb2Present = ClassUtils.isPresent("jakarta.xml.bind.Binder", classLoader);
    jackson2Present = ClassUtils.isPresent("com.fasterxml.jackson.databind.ObjectMapper", classLoader) &&
            ClassUtils.isPresent("com.fasterxml.jackson.core.JsonGenerator", classLoader);
    jackson2XmlPresent = ClassUtils.isPresent("com.fasterxml.jackson.dataformat.xml.XmlMapper", classLoader);
    jackson2SmilePresent = ClassUtils.isPresent("com.fasterxml.jackson.dataformat.smile.SmileFactory", classLoader);
    jackson2CborPresent = ClassUtils.isPresent("com.fasterxml.jackson.dataformat.cbor.CBORFactory", classLoader);
    gsonPresent = ClassUtils.isPresent("com.google.gson.Gson", classLoader);
  }


  @Override
  @Nullable
  public BeanDefinition parse(Element element, ParserContext context) {
    //handlerMapping加入到ioc容器
    readerContext.getRegistry().registerBeanDefinition(HANDLER_MAPPING_BEAN_NAME, handlerMappingDef);

    //添加jackson转化器
    addRequestBodyAdvice(handlerAdapterDef);
    addResponseBodyAdvice(handlerAdapterDef);

    //handlerAdapter加入到ioc容器
    readerContext.getRegistry().registerBeanDefinition(HANDLER_ADAPTER_BEAN_NAME, handlerAdapterDef);
    return null;
  }

  //具体添加jackson转化对象方法
  protected void addRequestBodyAdvice(RootBeanDefinition beanDef) {
    if (jackson2Present) {
      beanDef.getPropertyValues().add("requestBodyAdvice",
          new RootBeanDefinition(JsonViewRequestBodyAdvice.class));
    }
  }

  protected void addResponseBodyAdvice(RootBeanDefinition beanDef) {
    if (jackson2Present) {
      beanDef.getPropertyValues().add("responseBodyAdvice",
          new RootBeanDefinition(JsonViewResponseBodyAdvice.class));
    }
  }

```

#### 接收Cookie数据

  可以使用 `@CookieValue` 注释将 HTTP Cookie 的值绑定到控制器中的方法参数。

  考虑使用以下 cookie 的请求：

```Java
JSESSIONID=415A4AC178C59DACE0B2C9CA727CDD84
```

  下面的示例演示如何获取 cookie 值：

```Java
@GetMapping("/demo")
public void handle(@CookieValue("JSESSIONID") String cookie) { 
  //...
}
```

#### 接收请求头数据

  可以使用 `@RequestHeader` 批注将请求标头绑定到控制器中的方法参数。

  请考虑以下带有标头的请求：

```Java
Host                    localhost:8080
Accept                  text/html,application/xhtml+xml,application/xml;q=0.9
Accept-Language         fr,en-gb;q=0.7,en;q=0.3
Accept-Encoding         gzip,deflate
Accept-Charset          ISO-8859-1,utf-8;q=0.7,*;q=0.7
Keep-Alive              300
```

  下面的示例获取 `Accept-Encoding` 和 `Keep-Alive` 标头的值：

```Java
@GetMapping("/demo")
public void handle(
    @RequestHeader("Accept-Encoding") String encoding, 
    @RequestHeader("Keep-Alive") long keepAlive) { 
  //...
}
```

#### 原生Api对象操作

  https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/arguments.html

  下表描述了支持的控制器方法参数

| Controller method argument 控制器方法参数                    | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `jakarta.servlet.ServletRequest`, `jakarta.servlet.ServletResponse` | 请求/响应对象                                                |
| `jakarta.servlet.http.HttpSession`                           | 强制存在会话。因此，这样的参数永远不会为 `null` 。           |
| `java.io.InputStream`, `java.io.Reader`                      | 用于访问由 Servlet API 公开的原始请求正文。                  |
| `java.io.OutputStream`, `java.io.Writer`                     | 用于访问由 Servlet API 公开的原始响应正文。                  |
| `@PathVariable`                                              | 接收路径参数注解                                             |
| `@RequestParam`                                              | 用于访问 Servlet 请求参数，包括多部分文件。参数值将转换为声明的方法参数类型。 |
| `@RequestHeader`                                             | 用于访问请求标头。标头值将转换为声明的方法参数类型。         |
| `@CookieValue`                                               | 用于访问Cookie。Cookie 值将转换为声明的方法参数类型。        |
| `@RequestBody`                                               | 用于访问 HTTP 请求正文。正文内容通过使用 `HttpMessageConverter` 实现转换为声明的方法参数类型。 |
| `java.util.Map`, `org.springframework.ui.Model`, `org.springframework.ui.ModelMap` | 共享域对象，并在视图呈现过程中向模板公开。                   |
| `Errors`, `BindingResult`                                    | 验证和数据绑定中的错误信息获取对象！                         |


  获取原生对象示例：

```Java
/**
 * 如果想要获取请求或者响应对象,或者会话等,可以直接在形参列表传入,并且不分先后顺序!
 * 注意: 接收原生对象,并不影响参数接收!
 */
@GetMapping("api")
@ResponseBody
public String api(HttpSession session , HttpServletRequest request,
                  HttpServletResponse response){
    String method = request.getMethod();
    System.out.println("method = " + method);
    return "api";
}
```

#### 共享域对象操作（了解）

##### 1. 属性（共享）域作用回顾

  在 JavaWeb 中，共享域指的是在 Servlet 中存储数据，以便在同一 Web 应用程序的多个组件中进行共享和访问。常见的共享域有四种：`ServletContext`、`HttpSession`、`HttpServletRequest`、`PageContext`。

    1. `ServletContext` 共享域：`ServletContext` 对象可以在整个 Web 应用程序中共享数据，是最大的共享域。一般可以用于保存整个 Web 应用程序的全局配置信息，以及所有用户都共享的数据。在 `ServletContext` 中保存的数据是线程安全的。
    2. `HttpSession` 共享域：`HttpSession` 对象可以在同一用户发出的多个请求之间共享数据，但只能在同一个会话中使用。比如，可以将用户登录状态保存在 `HttpSession` 中，让用户在多个页面间保持登录状态。
    3. `HttpServletRequest` 共享域：`HttpServletRequest` 对象可以在同一个请求的多个处理器方法之间共享数据。比如，可以将请求的参数和属性存储在 `HttpServletRequest` 中，让处理器方法之间可以访问这些数据。
    4. `PageContext` 共享域：`PageContext` 对象是在 JSP 页面Servlet 创建时自动创建的。它可以在 JSP 的各个作用域中共享数据，包括`pageScope`、`requestScope`、`sessionScope`、`applicationScope` 等作用域。

  共享域的作用是提供了方便实用的方式在同一 Web 应用程序的多个组件之间传递数据，并且可以将数据保存在不同的共享域中，根据需要进行选择和使用。

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\img010.png)

##### 2. Request级别属性（共享）域

    1. 使用 Model 类型的形参

```Java
@RequestMapping("/attr/request/model")
@ResponseBody
public String testAttrRequestModel(
    
        // 在形参位置声明Model类型变量，用于存储模型数据
        Model model) {
    
    // 我们将数据存入模型，SpringMVC 会帮我们把模型数据存入请求域
    // 存入请求域这个动作也被称为暴露到请求域
    model.addAttribute("requestScopeMessageModel","i am very happy[model]");
    
    return "target";
}
```

    2. 使用 ModelMap 类型的形参

```Java
@RequestMapping("/attr/request/model/map")
@ResponseBody
public String testAttrRequestModelMap(
    
        // 在形参位置声明ModelMap类型变量，用于存储模型数据
        ModelMap modelMap) {
    
    // 我们将数据存入模型，SpringMVC 会帮我们把模型数据存入请求域
    // 存入请求域这个动作也被称为暴露到请求域
    modelMap.addAttribute("requestScopeMessageModelMap","i am very happy[model map]");
    
    return "target";
}
```

    3. 使用 Map 类型的形参

```Java
@RequestMapping("/attr/request/map")
@ResponseBody
public String testAttrRequestMap(
    
        // 在形参位置声明Map类型变量，用于存储模型数据
        Map<String, Object> map) {
    
    // 我们将数据存入模型，SpringMVC 会帮我们把模型数据存入请求域
    // 存入请求域这个动作也被称为暴露到请求域
    map.put("requestScopeMessageMap", "i am very happy[map]");
    
    return "target";
}
```

    4. 使用原生 request 对象

```Java
@RequestMapping("/attr/request/original")
@ResponseBody
public String testAttrOriginalRequest(
    
        // 拿到原生对象，就可以调用原生方法执行各种操作
        HttpServletRequest request) {
    
    request.setAttribute("requestScopeMessageOriginal", "i am very happy[original]");
    
    return "target";
}
```

    5. 使用 ModelAndView 对象

```Java
@RequestMapping("/attr/request/mav")
public ModelAndView testAttrByModelAndView() {
    
    // 1.创建ModelAndView对象
    ModelAndView modelAndView = new ModelAndView();
    // 2.存入模型数据
    modelAndView.addObject("requestScopeMessageMAV", "i am very happy[mav]");
    // 3.设置视图名称
    modelAndView.setViewName("target");
    
    return modelAndView;
}
```

##### 3. Session级别属性（共享）域

```Java
@RequestMapping("/attr/session")
@ResponseBody
public String testAttrSession(HttpSession session) {
    //直接对session对象操作,即对会话范围操作!
    return "target";
}
```

##### 4. Application级别属性（共享）域

  解释：springmvc会在初始化容器的时候，讲servletContext对象存储到ioc容器中！

```Java
@Autowired
private ServletContext servletContext;

@RequestMapping("/attr/application")
@ResponseBody
public String attrApplication() {
    
    servletContext.setAttribute("appScopeMsg", "i am hungry...");
    
    return "target";
}
```

### Spring MVC 响应数据

#### handler方法分析

  理解handler方法的作用和组成：

```Java
/**
 * TODO: 一个controller的方法是控制层的一个处理器,我们称为handler
 * TODO: handler需要使用@RequestMapping/@GetMapping系列,声明路径,在HandlerMapping中注册,供DS查找!
 * TODO: handler作用总结:
 *       1.接收请求参数(param,json,pathVariable,共享域等) 
 *       2.调用业务逻辑 
 *       3.响应前端数据(页面（不讲解模版页面跳转）,json,转发和重定向等)
 * TODO: handler如何处理呢
 *       1.接收参数: handler(形参列表: 主要的作用就是用来接收参数)
 *       2.调用业务: { 方法体  可以向后调用业务方法 service.xx() }
 *       3.响应数据: return 返回结果,可以快速响应前端数据
 */
@GetMapping
public Object handler(简化请求参数接收){
    调用业务方法
    返回的结果 （页面跳转，返回数据（json））
    return 简化响应前端数据;
}
```

  总结： 请求数据接收，我们都是通过handler的形参列表。前端数据响应，我们都是通过handler的return关键字快速处理！springmvc简化了参数接收和响应！

#### 页面跳转控制

  ##### 快速返回模板视图

![image-20240326202943260](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240326202943260.png)

1. 开发模式回顾

   在 Web 开发中，有两种主要的开发模式：**前后端分离和混合开发。**

   **前后端分离模式：[重点]**

     指将前端的界面和后端的业务逻辑通过接口分离开发的一种方式。开发人员使用不同的技术栈和框架，前端开发人员主要负责页面的呈现和用户交互，后端开发人员主要负责业务逻辑和数据存储。前后端通信通过 API 接口完成，数据格式一般使用 JSON 或 XML。前后端分离模式可以提高开发效率，同时也有助于代码重用和维护。

   **混合开发模式：**

     指将前端和后端的代码集成在同一个项目中，共享相同的技术栈和框架。这种模式在小型项目中比较常见，可以减少学习成本和部署难度。但是，在大型项目中，这种模式会导致代码耦合性很高，维护和升级难度较大。对于混合开发，我们就需要使用动态页面技术，动态展示Java的共享域数据！！

2. jsp技术了解

   JSP（JavaServer Pages）是一种动态网页开发技术，它是由 Sun 公司提出的一种基于 Java 技术的 Web 页面制作技术，**可以在 HTML 文件中嵌入 Java 代码，使得生成动态内容的编写更加简单。**

   JSP 最主要的作用是生成动态页面。它允许将 Java 代码嵌入到 HTML 页面中，以便使用 Java 进行数据库查询、处理表单数据和生成 HTML 等动态内容。另外，JSP 还可以与 Servlet 结合使用，实现更加复杂的 Web 应用程序开发。

   JSP 的主要特点包括：

   1. 简单：JSP 通过将 Java 代码嵌入到 HTML 页面中，使得生成动态内容的编写更加简单。
   2. 高效：JSP 首次运行时会被转换为 Servlet，然后编译为字节码，从而可以启用 Just-in-Time（JIT）编译器，实现更高效的运行。
   3. 多样化：JSP 支持多种标准标签库，包括 JSTL（JavaServer Pages 标准标签库）、EL（表达式语言）等，可以帮助开发人员更加方便的处理常见的 Web 开发需求。

   总之，JSP 是一种简单高效、多样化的动态网页开发技术，它可以方便地生成动态页面和与 Servlet 结合使用，是 Java Web 开发中常用的技术之一。

3. 准备jsp页面和依赖

   pom.xml依赖

```XML
<!-- jsp需要依赖! jstl-->
<dependency> 
    <groupId>jakarta.servlet.jsp.jstl</groupId>
    <artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
    <version>3.0.0</version>
</dependency>
```

  			  jsp页面创建

- ​	建议位置：/WEB-INF/下，避免外部直接访问！

- ​    位置：/WEB-INF/views/home.jsp

```Java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>Title</title>
  </head>
  <body>
        <!-- 可以获取共享域的数据,动态展示! jsp== 后台vue -->
        ${msg}
  </body>
</html>

```

4. 快速响应模版页面
   1. 配置jsp视图解析器

```Java
@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器
@Configuration
@ComponentScan(basePackages = "com.atguigu.controller") //TODO: 进行controller扫描

//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现
public class SpringMvcConfig implements WebMvcConfigurer {

    //配置jsp对应的视图解析器
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        //快速配置jsp模板语言对应的
        registry.jsp("/WEB-INF/views/",".jsp");
    }
}
```

​    							b. handler返回视图

```Java
/**
 *  跳转到提交文件页面  /save/jump
 *  
 *  如果要返回jsp页面!
 *     1.方法返回值改成字符串类型
  		不能添加@ResponseBody，直接返回给字符串给浏览器，不找试图，不走视图解析器
 *     2.返回逻辑视图名即可    
 *         <property name="prefix" value="/WEB-INF/views/"/>
 *            + 逻辑视图名 +
 *         <property name="suffix" value=".jsp"/>
 */
@GetMapping("jump")
public String jumpJsp(Model model){
    System.out.println("FileController.jumpJsp");
    model.addAttribute("msg","request data!!");
    return "home";
}
```

  ##### 转发和重定向

在 Spring MVC 中，Handler 方法返回值来实现快速转发，可以使用 `redirect` 或者 `forward` 关键字来实现重定向。

```Java
@RequestMapping("/redirect-demo")
public String redirectDemo() {
    // 重定向到 /demo 路径 
    return "redirect:/demo";
}

@RequestMapping("/forward-demo")
public String forwardDemo() {
    // 转发到 /demo 路径
    return "forward:/demo";
}

//注意： 转发和重定向到项目下资源路径都是相同，都不需要添加项目根路径！填写项目下路径即可！
```

总结：

- 将方法的返回值，设置String类型
- 转发使用forward关键字，重定向使用redirect关键字
- 关键字: /路径
- 注意：如果是项目下的资源，转发和重定向都一样都是项目下路径！都不需要添加项目根路径！

#### 返回JSON数据（重点）

  ##### 前置准备

导入jackson依赖

```XML
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.0</version>
</dependency>
```

添加json数据转化器   @EnableWebMvc 

```Java
//TODO: SpringMVC对应组件的配置类 [声明SpringMVC需要的组件信息]

//TODO: 导入handlerMapping和handlerAdapter的三种方式
 //1.自动导入handlerMapping和handlerAdapter [推荐]
 //2.可以不添加,springmvc会检查是否配置handlerMapping和handlerAdapter,没有配置默认加载
 //3.使用@Bean方式配置handlerMapper和handlerAdapter
@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器
@Configuration
@ComponentScan(basePackages = "com.atguigu.controller") //TODO: 进行controller扫描

//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现
public class SpringMvcConfig implements WebMvcConfigur 
                                                

}
```

  ##### @ResponseBody

1. 方法上使用@ResponseBody

   可以在方法上使用 `@ResponseBody`注解，用于将方法返回的对象序列化为 JSON 或 XML 格式的数据，并发送给客户端。在前后端分离的项目中使用！

   测试方法：

```Java
@GetMapping("/accounts/{id}")
@ResponseBody
public Object handle() {
  // ...
  return obj;
}
```


具体来说，`@ResponseBody` 注解可以用来标识方法或者方法返回值，表示方法的返回值是要直接返回给客户端的数据，而不是由视图解析器来解析并渲染生成响应体（viewResolver没用）。

​    测试方法：

```Java
@RequestMapping(value = "/user/detail", method = RequestMethod.POST)
@ResponseBody
public User getUser(@RequestBody User userParam) {
    System.out.println("userParam = " + userParam);
    User user = new User();
    user.setAge(18);
    user.setName("John");
    //返回的对象,会使用jackson的序列化工具,转成json返回给前端!
    return user;
}
```

​    返回结果：

   ![img](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711457414269-20.png)

2. 类上使用@ResponseBody

   如果类中每个方法上都标记了 @ResponseBody 注解，那么这些注解就可以提取到类上。

```Java
@ResponseBody  //responseBody可以添加到类上,代表默认类中的所有方法都生效!
@Controller
@RequestMapping("param")
public class ParamController {
```

  ##### @RestController

类上的 @ResponseBody 注解可以和 @Controller 注解合并为 @RestController 注解。所以使用了 @RestController 注解就相当于给类中的每个方法都加了 @ResponseBody 注解。

RestController源码:

```Java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {
 
  /**
   * The value may indicate a suggestion for a logical component name,
   * to be turned into a Spring bean in case of an autodetected component.
   * @return the suggested component name, if any (or empty String otherwise)
   * @since 4.0.1
   */
  @AliasFor(annotation = Controller.class)
  String value() default "";
}
```

#### 返回静态资源处理

1. **静态资源概念**

   资源本身已经是可以直接拿到浏览器上使用的程度了，**不需要在服务器端做任何运算、处理**。典型的静态资源包括：

   - 纯HTML文件
   - 图片
   - CSS文件
   - JavaScript文件
   - ……

2. **静态资源访问和问题解决**

   - web应用加入静态资源

     <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711636808973-19.png" style="zoom:50%;" />

   - 手动构建确保编译

     <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711636808973-20.png" style="zoom:50%;" />

     <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711638235761-3.png" style="zoom:50%;" />

     <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711636808974-22.png" style="zoom: 33%;" />

   - 访问静态资源

     <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711636808974-23.png" style="zoom:50%;" />

   - 问题分析

     - DispatcherServlet 的 url-pattern 配置的是“/”
     - url-pattern 配置“/”表示整个 Web 应用范围内所有请求都由 SpringMVC 来处理
     - 对 SpringMVC 来说，必须有对应的 @RequestMapping 才能找到处理请求的方法
     - 现在 images/mi.jpg 请求没有对应的 @RequestMapping 所以返回 404

   - 问题解决

     在 SpringMVC 配置配置类：

```Java
@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器
@Configuration
@ComponentScan(basePackages = "com.atguigu.controller") //TODO: 进行controller扫描
//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现
public class SpringMvcConfig implements WebMvcConfigurer {

    //配置jsp对应的视图解析器
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        //快速配置jsp模板语言对应的
        registry.jsp("/WEB-INF/views/",".jsp");
    }
    
    //开启静态资源处理 <mvc:default-servlet-handler/>
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
}
```

​    再次测试访问图片：

​    <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711638235761-4.png" style="zoom: 33%;" />

- 新的问题：其他原本正常的handler请求访问不了了

  handler无法访问

  解决方案：

```XML
@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器
```

### RESTFul风格设计和实战

  #### RESTFul风格简介

<img src="https://secure2.wostatic.cn/static/2pYk4YrDx33dEFrpSiyCJw/image.png?auth_key=1711459755-kfzvLpRWYfDBe6THNVy8gD-0-d1f00542bc4aab211ba290cb752d8a21" style="zoom:50%;" />

**RESTful（Representational State Transfer）**是一种软件架构风格，用于设计网络应用程序和服务之间的通信。它是一种基于标准 HTTP 方法的简单和轻量级的通信协议，广泛应用于现代的Web服务开发。通过遵循 RESTful 架构的设计原则，可以构建出易于理解、可扩展、松耦合和可重用的 Web 服务。RESTful API 的特点是简单、清晰，并且易于使用和理解，它们使用标准的 HTTP 方法和状态码进行通信，不需要额外的协议和中间件。总而言之，RESTful 是一种基于 HTTP 和标准化的设计原则的软件架构风格，用于设计和实现可靠、可扩展和易于集成的 Web 服务和应用程序！

<img src="https://secure2.wostatic.cn/static/rT5byNjNzYh141n4cBFYmZ/image.png?auth_key=1711459755-exRkdJP8fsqYUMFroEjLsw-0-db454ad85b0e797c63cb04021057d172" style="zoom:67%;" />

学习RESTful设计原则可以帮助我们更好去设计HTTP协议的API接口！！

  #### RESTFul风格特点

1. 每一个**URI代表1种资源**（URI 是名词）；
2. 客户端使用GET、POST、PUT、DELETE 4个表示操作方式的动词对服务端资源进行操作：GET用来获取资源，POST用来新建资源（也可以用于更新资源），PUT用来更新资源，DELETE用来删除资源；
3. 资源的表现形式是XML或者**JSON**；
4. **客户端与服务端之间的交互在请求之间是无状态的，从客户端到服务端的每个请求都必须包含理解请求所必需的信息。**

  #### RESTFul风格设计规范

1. **HTTP协议请求方式要求**

   REST 风格主张在项目设计、开发过程中，具体的操作符合**HTTP协议定义的请求方式的语义**。

| 操作         | 请求方式 |
| ------------ | -------- |
| 查询操作 查  | GET      |
| 保存操作  增 | POST     |
| 删除操作 删  | DELETE   |
| 更新操作  改 | PUT      |

2. **URL路径风格要求**

   REST风格下每个资源都应该有一个唯一的标识符，**例如一个 URI（统一资源标识符）或者一个 URL（统一资源定位符）。**资源的标识符应该能明确地说明该资源的信息，同时也应该是可被理解和解释的！

   **使用URL+请求方式确定具体的动作**，他也是一种标准的HTTP协议请求！

| 操作 | 传统风格                      | REST 风格                              |
| ---- | ----------------------------- | -------------------------------------- |
| 保存 | /CRUD/saveEmp                 | URL 地址：/CRUD/emp 请求方式：POST     |
| 删除 | /CRUD/removeEmp?empId=2       | URL 地址：/CRUD/emp/2 请求方式：DELETE |
| 更新 | /CRUD/updateEmp               | URL 地址：/CRUD/emp 请求方式：PUT      |
| 查询 | /CRUD/editEmp?****empId=2**** | URL 地址：/CRUD/emp/2 请求方式：GET    |

- 总结

  根据接口的具体动作，选择具体的HTTP协议请求方式

  路径设计从**原来携带动标识，改成名词**，对应资源的唯一标识即可！

  #### RESTFul风格好处

1. 含蓄，安全

   使用问号键值对的方式给服务器传递数据太明显，容易被人利用来对系统进行破坏。使用 REST 风格携带数据不再需要明显的暴露数据的名称。

2. 风格统一

   URL 地址整体格式统一，从前到后始终都使用斜杠划分各个单词，用简单一致的格式表达语义。

3. 无状态

   在调用一个接口（访问、操作资源）的时候，可以不用考虑上下文，不用考虑当前状态，极大的降低了系统设计的复杂度。

4. 严谨，规范

   严格按照 HTTP1.1 协议中定义的请求方式本身的语义进行操作。

5. 简洁，优雅

   过去做增删改查操作需要设计4个不同的URL，现在一个就够了。

6. 丰富的语义

   通过 URL 地址就可以知道资源之间的关系。它能够把一句话中的很多单词用斜杠连起来，反过来说就是可以在 URL 地址中用一句话来充分表达语义。

   > [http://localhost:8080/shop](http://localhost:8080/shop) [http://localhost:8080/shop/product](http://localhost:8080/shop/product) [http://localhost:8080/shop/product/cellPhone](http://localhost:8080/shop/product/cellPhone) [http://localhost:8080/shop/product/cellPhone/iPhone](http://localhost:8080/shop/product/cellPhone/iPhone)

### Spring MVC其他拓展

#### 全局异常处理机制

  ##### 异常处理两种方式

开发过程中是不可避免地会出现各种异常情况的，例如网络连接异常、数据格式异常、空指针异常等等。异常的出现可能导致程序的运行出现问题，甚至直接导致程序崩溃。因此，在开发过程中，合理处理异常、避免异常产生、以及对异常进行有效的调试是非常重要的。

对于异常的处理，一般分为两种方式：

- **编程式异常处理：是指在代码中显式地编写处理异常的逻辑。**它通常涉及到对异常类型的检测及其处理，例如**使用 try-catch 块来捕获异常**，然后在 catch 块中编写特定的处理代码，或者在 finally 块中执行一些清理操作。在编程式异常处理中，开发人员需要显式地进行异常处理，异常处理代码混杂在业务代码中，导致代码可读性较差。
- **声明式异常处理**：则是将异常处理的逻辑**从具体的业务逻辑中分离出来**，通过配置等方式进行统一的管理和处理。在声明式异常处理中，开发人员**只需要为方法或类标注相应的注解**（如 `@Throws` 或 `@ExceptionHandler`），就可以处理特定类型的异常。相较于编程式异常处理，声明式异常处理可以使代码更加简洁、易于维护和扩展。

站在宏观角度来看待声明式事务处理：整个项目从架构这个层面设计的异常处理的统一机制和规范。 一个项目中会包含很多个模块，各个模块需要分工完成。如果张三负责的模块按照 A 方案处理异常，李四负责的模块按照 B 方案处理异常……各个模块处理异常的思路、代码、命名细节都不一样，那么就会让整个项目非常混乱。使用声明式异常处理，可以统一项目处理异常思路，项目更加清晰明了！

  ##### 基于注解异常声明异常处理

1. 声明异常处理控制器类

   异常处理控制类，统一定义异常处理handler方法！

```Java
/**
 * projectName: com.atguigu.execptionhandler
 * 
 * description: 全局异常处理器,内部可以定义异常处理Handler!
 */

/**
 * @RestControllerAdvice = @ControllerAdvice + @ResponseBody
 * @ControllerAdvice 代表当前类的异常处理controller! 
 */
@RestControllerAdvice
public class GlobalExceptionHandler {

  
}
```

2. 声明异常处理hander方法

   异常处理handler方法和普通的handler方法参数接收和响应都一致！只不过异常处理handler方法要映射异常，发生对应的异常会调用！普通的handler方法要使用@RequestMapping注解映射路径，发生对应的路径调用！


```Java
/**
 * 异常处理handler 
 * @ExceptionHandler(HttpMessageNotReadableException.class) 
 * 该注解标记异常处理Handler,并且指定发生异常调用该方法!
 * 
 * 
 * @param e 获取异常对象!
 * @return 返回handler处理结果!
 */
@ExceptionHandler(HttpMessageNotReadableException.class)
public Object handlerJsonDateException(HttpMessageNotReadableException e){
    
    return null;
}

/**
 * 当发生空指针异常会触发此方法!
 * @param e
 * @return
 */
@ExceptionHandler(NullPointerException.class)
public Object handlerNullException(NullPointerException e){

    return null;
}

/**
 * 所有异常都会触发此方法!但是如果有具体的异常处理Handler! 
 * 具体异常处理Handler优先级更高!
 * 例如: 发生NullPointerException异常!
 *       会触发handlerNullException方法,不会触发handlerException方法!
 * @param e
 * @return
 */
@ExceptionHandler(Exception.class)
public Object handlerException(Exception e){

    return null;
}
```

3. 配置文件扫描控制器类配置

   确保异常处理控制类被扫描

```Java
 <!-- 扫描controller对应的包,将handler加入到ioc-->
 @ComponentScan(basePackages = {"com.atguigu.controller",
 "com.atguigu.exceptionhandler"})
```

#### 拦截器使用

  ##### 拦截器概念

拦截器和过滤器解决问题

- 生活中

  为了提高乘车效率，在乘客进入站台前统一检票

  ![](https://secure2.wostatic.cn/static/qnwL468SbgHHucUQqmYxPV/img008.png?auth_key=1711462035-4DETPS5hZjqNKdF1aPhX8G-0-ac7bd68adc405b80467967ed125b7280)

- 程序中

  在程序中，使用拦截器在请求到达具体 handler 方法前，统一执行检测

  ![](https://secure2.wostatic.cn/static/eBwRN4iKLw9e9DHpVGP4WX/img009.png?auth_key=1711462035-vMensTrcJGH63TN1c2K7x2-0-00d1d7394906b8760790e80fab5170f8)

拦截器 Springmvc VS 过滤器 javaWeb：

- 相似点
  - 拦截：必须先把请求拦住，才能执行后续操作
  - 过滤：拦截器或过滤器存在的意义就是对请求进行统一处理
  - 放行：对请求执行了必要操作后，放请求过去，让它访问原本想要访问的资源
- 不同点
  - 工作平台不同
    - 过滤器工作在 Servlet 容器中
    - 拦截器工作在 SpringMVC 的基础上
  - 拦截的范围
    - 过滤器：能够拦截到的最大范围是整个 Web 应用
    - 拦截器：能够拦截到的最大范围是整个 SpringMVC 负责的请求
  - IOC 容器支持
    - 过滤器：想得到 IOC 容器需要调用专门的工具方法，是间接的
    - 拦截器：它自己就在 IOC 容器中，所以可以直接从 IOC 容器中装配组件，也就是可以直接得到 IOC 容器的支持

选择：

  **功能需要如果用 SpringMVC 的拦截器能够实现，就不使用过滤器。**

  ![](https://secure-bigfile.wostatic.cn/static/prqG4dtu3rDWj7VwX4WgsW/image.png?auth_key=1711462035-8dxwWxCq9Up8hUMetXgRcS-0-e3f2010ded7d28746f460373398e3ef2)

  ##### 拦截器使用

1. 创建拦截器类

```Java
public class Process01Interceptor implements HandlerInterceptor {

    // if( ! preHandler()){return;}
    // 在处理请求的目标 handler 方法前执行
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("request = " + request + ", response = " + response + ", handler = " + handler);
        System.out.println("Process01Interceptor.preHandle");
         
        // 返回true：放行
        // 返回false：不放行
        return true;
    }
 
    // 在目标 handler 方法之后，handler报错不执行!
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("request = " + request + ", response = " + response + ", handler = " + handler + ", modelAndView = " + modelAndView);
        System.out.println("Process01Interceptor.postHandle");
    }
 
    // 渲染视图之后执行(最后),一定执行!
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("request = " + request + ", response = " + response + ", handler = " + handler + ", ex = " + ex);
        System.out.println("Process01Interceptor.afterCompletion");
    }
}
```

​    拦截器方法拦截位置：

![img](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711462564890-23.png)

2. 修改配置类添加拦截器

```Java
@EnableWebMvc  //json数据处理,必须使用此注解,因为他会加入json处理器
@Configuration
@ComponentScan(basePackages = {"com.atguigu.controller","com.atguigu.exceptionhandler"}) //TODO: 进行controller扫描
//WebMvcConfigurer springMvc进行组件配置的规范,配置组件,提供各种方法! 前期可以实现
public class SpringMvcConfig implements WebMvcConfigurer {

    //配置jsp对应的视图解析器
    @Override
    public void configureViewResolvers(ViewResolverRegistry registry) {
        //快速配置jsp模板语言对应的
        registry.jsp("/WEB-INF/views/",".jsp");
    }

    //开启静态资源处理 <mvc:default-servlet-handler/>
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    //添加拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) { 
        //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
        registry.addInterceptor(new Process01Interceptor());
    }
}
```

3. 配置详解
   1. 默认拦截全部

```Java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
    registry.addInterceptor(new Process01Interceptor());
}
```

​    b.精准配置

```Java
@Override
public void addInterceptors(InterceptorRegistry registry) {
    
    //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
    registry.addInterceptor(new Process01Interceptor());
    
    //精准匹配,设置拦截器处理指定请求 路径可以设置一个或者多个,为项目下路径即可
    //addPathPatterns("/common/request/one") 添加拦截路径
    //也支持 /* 和 /** 模糊路径。 * 任意一层字符串 ** 任意层 任意字符串
    registry.addInterceptor(new Process01Interceptor()).addPathPatterns("/common/request/one","/common/request/tow");
}

```

​    c.排除配置

```Java
//添加拦截器
@Override
public void addInterceptors(InterceptorRegistry registry) {
    
    //将拦截器添加到Springmvc环境,默认拦截所有Springmvc分发的请求
    registry.addInterceptor(new Process01Interceptor());
    
    //精准匹配,设置拦截器处理指定请求 路径可以设置一个或者多个,为项目下路径即可
    //addPathPatterns("/common/request/one") 添加拦截路径
    registry.addInterceptor(new Process01Interceptor()).addPathPatterns("/common/request/one","/common/request/tow");
    
    
    //排除匹配,排除应该在匹配的范围内排除
    //addPathPatterns("/common/request/one") 添加拦截路径
    //excludePathPatterns("/common/request/tow"); 排除路径,排除应该在拦截的范围内
    registry.addInterceptor(new Process01Interceptor())
            .addPathPatterns("/common/request/one","/common/request/tow")
            .excludePathPatterns("/common/request/tow");
}
```

4. 多个拦截器执行顺序
   1. preHandle() 方法：SpringMVC 会把所有拦截器收集到一起，然后按照配置顺序调用各个 preHandle() 方法。
   2. postHandle() 方法：SpringMVC 会把所有拦截器收集到一起，然后按照配置相反的顺序调用各个 postHandle() 方法。
   3. afterCompletion() 方法：SpringMVC 会把所有拦截器收集到一起，然后按照配置相反的顺序调用各个 afterCompletion() 方法。

#### 参数校验

  > 在 Web 应用三层架构体系中，表述层负责接收浏览器提交的数据，业务逻辑层负责数据的处理。为了能够让业务逻辑层基于正确的数据进行处理，我们需要在表述层对数据进行检查，将错误的数据隔绝在业务逻辑层之外。

  1. **校验概述**

     JSR 303 是 Java 为 Bean 数据合法性校验提供的标准框架，它已经包含在 JavaEE 6.0 标准中。**JSR 303 通过在 Bean 属性上标注类似于 @NotNull、@Max 等标准的注解指定校验规则，并通过标准的验证接口对Bean进行验证。**

| 注解                       | 规则                                           |
| -------------------------- | ---------------------------------------------- |
| @Null                      | 标注值必须为 null                              |
| @NotNull                   | 标注值不可为 null                              |
| @AssertTrue                | 标注值必须为 true                              |
| @AssertFalse               | 标注值必须为 false                             |
| @Min(value)                | 标注值必须大于或等于 value                     |
| @Max(value)                | 标注值必须小于或等于 value                     |
| @DecimalMin(value)         | 标注值必须大于或等于 value                     |
| @DecimalMax(value)         | 标注值必须小于或等于 value                     |
| @Size(max,min)             | 标注值大小必须在 max 和 min 限定的范围内       |
| @Digits(integer,fratction) | 标注值值必须是一个数字，且必须在可接受的范围内 |
| @Past                      | 标注值只能用于日期型，且必须是过去的日期       |
| @Future                    | 标注值只能用于日期型，且必须是将来的日期       |
| @Pattern(value)            | 标注值必须符合指定的正则表达式                 |

  JSR 303 只是一套标准，需要提供其实现才可以使用。Hibernate Validator 是 JSR 303 的一个参考实现，除支持所有标准的校验注解外，它还支持以下的扩展注解：

| 注解      | 规则                               |
| --------- | ---------------------------------- |
| @Email    | 标注值必须是格式正确的 Email 地址  |
| @Length   | 标注值字符串大小必须在指定的范围内 |
| @NotEmpty | 标注值字符串不能是空字符串         |
| @Range    | 标注值必须在指定的范围内           |

  Spring 4.0 版本已经拥有自己独立的数据校验框架，同时支持 JSR 303 标准的校验框架。Spring 在进行数据绑定时，可同时调用校验框架完成数据校验工作。在SpringMVC 中，可直接通过注解驱动 @EnableWebMvc 的方式进行数据校验。Spring 的 LocalValidatorFactoryBean 既实现了 Spring 的 Validator 接口，也实现了 JSR 303 的 Validator 接口。只要在Spring容器中定义了一个LocalValidatorFactoryBean，即可将其注入到需要数据校验的 Bean中。Spring本身并没有提供JSR 303的实现，所以必须将JSR 303的实现者的jar包放到类路径下。

  配置 @EnableWebMvc后，SpringMVC 会默认装配好一个 LocalValidatorFactoryBean，通过在处理方法的入参上标注 @Validated 注解即可让 SpringMVC 在完成数据绑定后执行数据校验的工作。

    2. **操作演示**
       - 导入依赖

```XML
<!-- 校验注解 -->
<dependency>
    <groupId>jakarta.platform</groupId>
    <artifactId>jakarta.jakartaee-web-api</artifactId>
    <version>9.1.0</version>
    <scope>provided</scope>
</dependency>
        
<!-- 校验注解实现-->        
<!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator -->
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>8.0.0.Final</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.hibernate.validator/hibernate-validator-annotation-processor -->
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator-annotation-processor</artifactId>
    <version>8.0.0.Final</version>
</dependency>
```

  - 应用校验注解

```Java
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.Min;
import org.hibernate.validator.constraints.Length;

/**
 * projectName: com.atguigu.pojo
 */
public class User {
    //age   1 <=  age < = 150
    @Min(10)
    private int age;

    //name 3 <= name.length <= 6
    @Length(min = 3,max = 10)
    private String name;

    //email 邮箱格式
    @Email
    private String email;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}

```

  - handler标记和绑定错误收集

```Java
@RestController
@RequestMapping("user")
public class UserController {

    /**
     * @Validated 代表应用校验注解! 必须添加!
     */
    @PostMapping("save")
    public Object save(@Validated @RequestBody User user,
                       //在实体类参数和 BindingResult 之间不能有任何其他参数, BindingResult可以接受错误信息,避免信息抛出!
                       BindingResult result){
       //判断是否有信息绑定错误! 有可以自行处理!
        if (result.hasErrors()){
            System.out.println("错误");
            String errorMsg = result.getFieldError().toString();
            return errorMsg;
        }
        //没有,正常处理业务即可
        System.out.println("正常");
        return user;
    }
}
```

  - 测试效果

    ![](https://secure2.wostatic.cn/static/oXLwvcaMaLc4TggmPFNToV/image.png?auth_key=1711497315-hb9rtgHgwGswaKZPxbKZJV-0-8ea3a3df03f81dd42225922e98ddee6c)

  3. **易混总结**

     @NotNull、@NotEmpty、@NotBlank 都是用于在数据校验中检查字段值是否为空的注解，但是它们的用法和校验规则有所不同。

     1. @NotNull  (包装类型不为null)

        @NotNull 注解是 JSR 303 规范中定义的注解，当被标注的字段值为 null 时，会认为校验失败而抛出异常。该注解不能用于字符串类型的校验，若要对字符串进行校验，应该使用 @NotBlank 或 @NotEmpty 注解。

     2. @NotEmpty (集合类型长度大于0)

        @NotEmpty 注解同样是 JSR 303 规范中定义的注解，对于 CharSequence、Collection、Map 或者数组对象类型的属性进行校验，校验时会检查该属性是否为 Null 或者 size()==0，如果是的话就会校验失败。但是对于其他类型的属性，该注解无效。需要注意的是只校验空格前后的字符串，如果该字符串中间只有空格，不会被认为是空字符串，校验不会失败。

     3. @NotBlank （字符串，不为null，切不为"  "字符串）

        @NotBlank 注解是 Hibernate Validator 附加的注解，对于字符串类型的属性进行校验，校验时会检查该属性是否为 Null 或 “” 或者只包含空格，如果是的话就会校验失败。需要注意的是，@NotBlank 注解只能用于字符串类型的校验。

     总之，这三种注解都是用于校验字段值是否为空的注解，但是其校验规则和用法有所不同。在进行数据校验时，需要根据具体情况选择合适的注解进行校验。

### Spring MVC总结

|                 |                                            |
| --------------- | ------------------------------------------ |
| 核心点          | 掌握目标                                   |
| springmvc框架   | 主要作用、核心组件、调用流程               |
| 简化参数接收    | 路径设计、参数接收、请求头接收、cookie接收 |
| 简化数据响应    | 模板页面、转发和重定向、JSON数据、静态资源 |
| restful风格设计 | 主要作用、具体规范、请求方式和请求参数选择 |
| 功能扩展        | 全局异常处理、拦截器、参数校验注解         |

## ssm整合

  ### 什么是SSM整合？

**微观**：将学习的Spring SpringMVC Mybatis框架应用到项目中

- SpringMVC框架**负责控制层**
- Spring 框架负责整体和**业务层**的声明式事务管理
- MyBatis框架负责**数据库访问层**

**宏观**：Spring接管一切（将框架核心组件交给Spring进行IoC管理），代码更加简洁。

- SpringMVC管理表述层、SpringMVC相关组件
- Spring管理业务层、持久层、以及数据库相关（DataSource,MyBatis）的组件
- 使用IoC的方式管理一切所需组件

**实施**：通过编写配置文件，实现SpringIoC容器接管一切组件。

  ### SSM整合核心问题明确

#### 1. SSM整合需要几个IoC容器？

  **两个容器**本质上说，整合就是将三层架构和框架核心API组件交给SpringIoC容器管理！

  一个容器可能就够了，但是我们常见的操作是创建两个IoC容器**（web容器和root容器）**，组件分类管理！

  这种做法有以下好处和目的：

    1. 分离关注点：通过初始化两个容器，可以将各个层次的关注点进行分离。这种分离使得各个层次的组件能够更好地聚焦于各自的责任和功能。
    2. 解耦合：各个层次组件分离装配不同的IoC容器，这样可以进行解耦。这种解耦合使得各个模块可以独立操作和测试，提高了代码的可维护性和可测试性。
    3. 灵活配置：通过使用两个容器，可以为每个容器提供各自的配置，以满足不同层次和组件的特定需求。每个配置文件也更加清晰和灵活。

  总的来说，初始化两个容器在SSM整合中可以实现关注点分离、解耦合、灵活配置等好处。它们各自负责不同的层次和功能，并通过合适的集成方式协同工作，提供一个高效、可维护和可扩展的应用程序架构！

#### 2. 每个IoC容器对应哪些类型组件？

  图解：

![img](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711499008129-26.png)

  总结：

|          |                                                              |
| -------- | ------------------------------------------------------------ |
| 容器名   | 盛放组件                                                     |
| web容器  | web相关组件（controller,springmvc核心组件）                  |
| root容器 | 业务和持久层相关组件（service,aop,tx,dataSource,mybatis,mapper等） |

#### 3. IoC容器之间关系和调用方向？

  情况1：两个无关联IoC容器之间的组件无法注入！

  ![img](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711499199251-29.png)

  情况2：子IoC容器可以单向的注入父IoC容器的组件！

![img](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711499210676-32.png)

  结论：**web容器是root容器的子容器，父子容器关系。**

  - 父容器：root容器，盛放service、mapper、mybatis等相关组件
  - 子容器：web容器，盛放controller、web相关组件

  源码体现：

  FrameworkServlet  655行！

```Java
protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
    Class<?> contextClass = getContextClass();
    if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
      throw new ApplicationContextException(
          "Fatal initialization error in servlet with name '" + getServletName() +
          "': custom WebApplicationContext class [" + contextClass.getName() +
          "] is not of type ConfigurableWebApplicationContext");
    }
    ConfigurableWebApplicationContext wac =
        (ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);

    wac.setEnvironment(getEnvironment());
    //wac 就是web ioc容器
    //parent 就是root ioc容器
    //web容器设置root容器为父容器，所以web容器可以引用root容器
    wac.setParent(parent);
    String configLocation = getContextConfigLocation();
    if (configLocation != null) {
      wac.setConfigLocation(configLocation);
    }
    configureAndRefreshWebApplicationContext(wac);

    return wac;
  }
```

  调用流程图解：

<img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711499305889-35.png" alt="img" style="zoom:67%;" />

#### 4. 具体多少配置类以及对应容器关系？

  配置类的数量不是固定的，但是至少要两个，为了方便编写，我们可以三层架构每层对应一个配置类，分别指定两个容器加载即可！

![img](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711499359382-38.png)

  建议配置文件：

|                   |                               |          |
| ----------------- | ----------------------------- | -------- |
| 配置名            | 对应内容                      | 对应容器 |
| WebJavaConfig     | controller,springmvc相关      | web容器  |
| ServiceJavaConfig | service,aop,tx相关            | root容器 |
| MapperJavaConfig  | mapper,datasource,mybatis相关 | root容器 |

#### 5. IoC初始化方式和配置位置？

  在web项目下，我们可以选择web.xml和配置类方式进行ioc配置，推荐配置类。

  对于使用基于 web 的 Spring 配置的应用程序，建议这样做，如以下示例所示：

```Java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

  //指定root容器对应的配置类
  //root容器的配置类
  @Override
  protected Class<?>[] getRootConfigClasses() {
    return new Class<?>[] { ServiceJavaConfig.class,MapperJavaConfig.class };
  }
  
  //指定web容器对应的配置类 webioc容器的配置类
  @Override
  protected Class<?>[] getServletConfigClasses() {
    return new Class<?>[] { WebJavaConfig.class };
  }
  
  //指定dispatcherServlet处理路径，通常为 / 
  @Override
  protected String[] getServletMappings() {
    return new String[] { "/" };
  }
}
```

  图解配置类和容器配置：

![img](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711499744306-41.png)

## SpringBoot3实战

### 入门总结

  1. 为什么依赖不需要写版本？

     - 每个boot项目都有一个父项目`spring-boot-starter-parent`
     - parent的父项目是`spring-boot-dependencies`
     - 父项目 **版本仲裁中心**，把所有常见的jar的依赖版本都声明好了。
     - 比如：`mysql-connector-j`

     ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\1679294529375-4ee1cd26-8ebc-4abf-bff9-f8775e10c927.png)

  2. 启动器(Starter)是何方神圣？

     Spring Boot提供了一种**叫做Starter的概念，它是一组预定义的依赖项集合**，旨在简化Spring应用程序的配置和构建过程。Starter包含了一组相关的依赖项，以便在启动应用程序时自动引入所需的库、配置和功能。

     主要作用如下：

     1. **简化依赖管理：Spring Boot Starter通过捆绑和管理一组相关的依赖项**，减少了手动解析和配置依赖项的工作。只需引入一个相关的Starter依赖，即可获取应用程序所需的全部依赖。
     2. **自动配置：Spring Boot Starter在应用程序启动时自动配置所需的组件和功能**。通过根据类路径和其他设置的自动检测，Starter可以自动配置Spring Bean、数据源、消息传递等常见组件，从而使应用程序的配置变得简单和维护成本降低。
     3. 提供约定优于配置：Spring Boot Starter遵循“约定优于配置”的原则，通过提供一组默认设置和约定，减少了手动配置的需要。它定义了标准的配置文件命名约定、默认属性值、日志配置等，使得开发者可以更专注于业务逻辑而不是繁琐的配置细节。
     4. 快速启动和开发应用程序：Spring Boot Starter使得从零开始构建一个完整的Spring Boot应用程序变得容易。它提供了主要领域（如Web开发、数据访问、安全性、消息传递等）的Starter，帮助开发者快速搭建一个具备特定功能的应用程序原型。
     5. 模块化和可扩展性：Spring Boot Starter的组织结构使得应用程序的不同模块可以进行分离和解耦。每个模块可以有自己的Starter和依赖项，使得应用程序的不同部分可以按需进行开发和扩展。

     ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711501774315-44.png)

     Spring Boot提供了许多预定义的Starter，例**如spring-boot-starter-web用于构建Web应用程序**，spring-boot-starter-data-jpa用于使用JPA进行数据库访问，spring-boot-starter-security用于安全认证和授权等等。

     使用Starter非常简单，只需要在项目的构建文件（例如Maven的pom.xml）中添加所需的Starter依赖，Spring Boot会自动处理依赖管理和配置。

     通过使用Starter，开发人员可以方便地引入和配置应用程序所需的功能，避免了手动添加大量的依赖项和编写冗长的配置文件的繁琐过程。同时，Starter也提供了一致的依赖项版本管理，确保依赖项之间的兼容性和稳定性。

  spring boot提供的全部启动器地址：

  [https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters](https://docs.spring.io/spring-boot/docs/current/reference/html/using.html#using.build-systems.starters)

  命名规范：

  - 官方提供的场景：命名为：`spring-boot-starter-*`
  - 第三方提供场景：命名为：`*-spring-boot-starter`

  3. @SpringBootApplication注解的功效？

     @SpringBootApplication添加到启动类上，是一个组合注解，他的功效有具体的子注解实现！

```Java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
public @interface SpringBootApplication {}
```

  @SpringBootApplication注解是Spring Boot框架中的核心注解，它的主要作用是简化和加速Spring Boot应用程序的配置和启动过程。

  具体而言，@SpringBootApplication注解起到以下几个主要作用：

    1. 自动配置：@SpringBootApplication注解包含了**@EnableAutoConfiguration注解，用于启用Spring Boot的自动配置机制**。自动配置会根据应用程序的依赖项和类路径，自动配置各种常见的Spring配置和功能，减少开发者的手动配置工作。它通过智能地分析类路径、加载配置和条件判断，为应用程序提供适当的默认配置。
    2. 组件扫描：@SpringBootApplication注解包含了**@ComponentScan注解，用于自动扫描并加载应用程序中的组件**，例如控制器（Controllers）、服务（Services）、存储库（Repositories）等。它默认会扫描@SpringBootApplication注解所在类的包及其子包中的组件，并将它们纳入Spring Boot应用程序的上下文中，使它们可被自动注入和使用。
    3. 声明配置类：@SpringBootApplication注解本身就是一个组合注解，**它包含了@Configuration注解，将被标注的类声明为配置类**。配置类可以包含Spring框架相关的配置、Bean定义，以及其他的自定义配置。通过@SpringBootApplication注解，开发者可以将配置类与启动类合并在一起，使得配置和启动可以同时发生。

  总的来说，@SpringBootApplication注解的主要作用是简化Spring Boot应用程序的配置和启动过程。它自动配置应用程序、扫描并加载组件，并将配置和启动类合二为一，简化了开发者的工作量，提高了开发效率。

### 配置文件

#### 统一配置管理概述

  SpringBoot工程下，进行统一的配置管理，你想设置的任何参数（端口号、项目根路径、数据库连接信息等等)都集中到一个固定位置和命名的配置文件（`application.properties`或`application.yml`）中！

  配置文件应该放置在Spring Boot工程的`src/main/resources`目录下。这是因为`src/main/resources`目录是Spring Boot默认的类路径（classpath），配置文件会被自动加载并可供应用程序访问。

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711503048346-49.png)

  功能配置参数说明：

  [https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties)

  细节总结：

  - 集中式管理配置。统一在一个文件完成程序功能参数设置和自定义参数声明 。

  - 位置：resources文件夹下，必须命名application  后缀 .properties / .yaml /  .yml 。

  - 如果同时存在application.properties | application.yml(.yaml) , properties的优先级更高。

  - 配置基本都有默认值。

    ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711503048347-50.png)

#### 属性配置文件使用

  1. 配置文件

     在 resource 文件夹下面新建 application.properties 配置文件

```.properties
# application.properties 为统一配置文件
# 内部包含: 固定功能的key,自定义的key
# 此处的配置信息,我们都可以在程序中@Value等注解读取

# 固定的key
# 启动端口号
server.port=80 

# 自定义
spring.jdbc.datasource.driverClassName=com.mysql.cj.jdbc.driver
spring.jdbc.datasource.url=jdbc:mysql:///springboot_01
spring.jdbc.datasource.username=root
spring.jdbc.datasource.password=root
```

    2. 读取配置文件

```Java
package com.atguigu.properties;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class DataSourceProperties {

    @Value("${spring.jdbc.datasource.driverClassName}")
    private String driverClassName;

    @Value("${spring.jdbc.datasource.url}")
    private String url;

    @Value("${spring.jdbc.datasource.username}")
    private String username;

    @Value("${spring.jdbc.datasource.password}")
    private String password;

    // 生成get set 和 toString方法
    public String getDriverClassName() {
        return driverClassName;
    }

    public void setDriverClassName(String driverClassName) {
        this.driverClassName = driverClassName;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "DataSourceProperties{" +
                "driverClassName='" + driverClassName + '\'' +
                ", url='" + url + '\'' +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

  3. 测试效果

     在controller注入，输出进行测试

```Java
@Autowired
private DataSourceProperties dataSourceProperties ;

@RequestMapping(path = "/hello")
public String sayHello() {
  System.out.println(dataSourceProperties);
  return "Hello Spring Boot ! " ;
}
```

  浏览器访问路径，控制台查看效果

  ![](https://secure2.wostatic.cn/static/vCb6myPBNpMgVjF6nAXATV/image.png?auth_key=1711502389-mbJdFrSCvwqgLFcPGcmrTZ-0-c30d4103162a6c6aa9a13dc07674aa9d)

#### YAML配置文件使用

  1. yaml格式介绍

     YAML（YAML Ain’t Markup Language）是一种基于层次结构的数据序列化格式，旨在提供一种易读、人类友好的数据表示方式。

     与`.properties`文件相比，YAML格式有以下优势：

     1. 层次结构：YAML文件使用缩进和冒号来表示层次结构，使得数据之间的关系更加清晰和直观。这样可以更容易理解和维护复杂的配置，特别适用于深层次嵌套的配置情况。
     2. 自我描述性：YAML文件具有自我描述性，字段和值之间使用冒号分隔，并使用缩进表示层级关系。这使得配置文件更易于阅读和理解，并且可以减少冗余的标点符号和引号。
     3. 注释支持：YAML格式支持注释，可以在配置文件中添加说明性的注释，使配置更具可读性和可维护性。相比之下，`.properties`文件不支持注释，无法提供类似的解释和说明。
     4. 多行文本：YAML格式支持多行文本的表示，可以更方便地表示长文本或数据块。相比之下，`.properties`文件需要使用转义符或将长文本拆分为多行。
     5. 类型支持：YAML格式天然支持复杂的数据类型，如列表、映射等。这使得在配置文件中表示嵌套结构或数据集合更加容易，而不需要进行额外的解析或转换。
     6. 更好的可读性：由于YAML格式的特点，它更容易被人类读懂和解释。它减少了配置文件中需要的特殊字符和语法，让配置更加清晰明了，从而减少了错误和歧义。

     综上所述，YAML格式相对于`.properties`文件具有更好的层次结构表示、自我描述性、注释支持、多行文本表示、复杂数据类型支持和更好的可读性。这些特点使YAML成为一种有力的配置文件格式，尤其适用于复杂的配置需求和人类可读的场景。然而，选择使用YAML还是`.properties`取决于实际需求和团队的偏好，简单的配置可以使用`.properties`，而复杂的配置可以选择YAML以获得更多的灵活性和可读性

  2. yaml语法说明

     1. **数据结构用树形结构呈现，通过缩进来表示层级，**
     2. **连续的项目（集合）通过减号 ” - ” 来表示**
     3. **键值结构里面的key/value对用冒号 ” : ” 来分隔。**
     4. **YAML配置文件的扩展名是yaml 或 yml**
     5. **例如：**

```YAML
# YAML配置文件示例
app_name: 我的应用程序
version: 1.0.0
author: 张三
 
database:
  host: localhost
  port: 5432
  username: admin
  password: password123

features:
  - 登录
  - 注册
  - 仪表盘

settings:
  analytics: true
  theme: dark
```

    3. 配置文件

```YAML
spring:
  jdbc:
    datasource:
      driverClassName: com.mysql.jdbc.Driver
      url: jdbc:mysql:///springboot_02
      username: root
      password: root
      
server:
  port: 80
```

  4. 读取配置文件

     > 读取方式和properties一致

```Java
package com.atguigu.properties;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class DataSourceProperties {

    @Value("${spring.jdbc.datasource.driverClassName}")
    private String driverClassName;
    
    
    /*@Value 注解是 Spring 框架中用来从外部配置文件中读取数值并注入到 Spring 管理的 Bean 中的注解。在你提供的代码片段中，@Value("${spring.jdbc.datasource.driverClassName}") 的作用是将外部配置文件中 spring.jdbc.datasource.driverClassName 对应的值注入到 driverClassName 字段中。

具体来说，这行代码的作用是从外部配置文件（比如 properties 文件、yml 文件等）中读取名为 spring.jdbc.datasource.driverClassName 的属性值，并将其注入到 driverClassName 字段中。这样可以方便地通过外部配置文件动态配置应用程序中的参数，而不需要硬编码在代码中。

使用 @Value 注解能够使得代码更具灵活性和可维护性，因为可以通过修改外部配置文件来改变应用程序的行为，而不需要修改源代码。

总之，@Value 注解在 Spring 中是一个很有用的注解，可以帮助实现外部配置和依赖注入，提高代码的可配置性和可维护性。*/

    @Value("${spring.jdbc.datasource.url}")
    private String url;

    @Value("${spring.jdbc.datasource.username}")
    private String username;

    @Value("${spring.jdbc.datasource.password}")
    private String password;

    // 生成get set 和 toString方法
    public String getDriverClassName() {
        return driverClassName;
    }

    public void setDriverClassName(String driverClassName) {
        this.driverClassName = driverClassName;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "DataSourceProperties{" +
                "driverClassName='" + driverClassName + '\'' +
                ", url='" + url + '\'' +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

  5. 测试效果

     在controller注入，输出进行测试

```Java
@Autowired
private DataSourceProperties dataSourceProperties ;

@RequestMapping(path = "/hello")
public String sayHello() {
  System.out.println(dataSourceProperties);
  return "Hello Spring Boot ! " ;
}
```

  浏览器访问路径，控制台查看效果

  ![](https://secure2.wostatic.cn/static/vCb6myPBNpMgVjF6nAXATV/image.png?auth_key=1711535717-pMMvj8kp5fxALnGHzXGmsZ-0-fdbc6853ec52be64846e2f55ddf76e49)

#### 批量配置文件注入

![image-20240327185328374](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240327185328374.png)

  >  **@ConfigurationProperties**是SpringBoot提供的重要注解, 他可以将一些配置属性批量注入到bean对象。

  1. 创建类，添加属性和注解

     在类上通过**@ConfigurationProperties注解声明该类要读取属性配置**

     prefix="spring.jdbc.datasource" 读取属性文件中前缀为spring.jdbc.datasource的值。前缀和属性名称和配置文件中的key必须要保持一致才可以注入成功

```Java
package com.atguigu.properties;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
 
@Component
@ConfigurationProperties(prefix = "spring.jdbc.datasource")
public class DataSourceConfigurationProperties {

    private String driverClassName;
    private String url;
    private String username;
    private String password;

    public String getDriverClassName() {
        return driverClassName;
    }

    public void setDriverClassName(String driverClassName) {
        this.driverClassName = driverClassName;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "DataSourceConfigurationProperties{" +
                "driverClassName='" + driverClassName + '\'' +
                ", url='" + url + '\'' +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

    2. 测试效果

```Java
@RestController
public class HelloController {

    @Autowired
    private DataSourceProperties dataSourceProperties;

    @Autowired
    private DataSourceConfigurationProperties dataSourceConfigurationProperties;

    @GetMapping("/hello")
    public String hello(){
        System.out.println("dataSourceProperties = " + dataSourceProperties);
        System.out.println("dataSourceConfigurationProperties = " + dataSourceConfigurationProperties);
        return "Hello,Spring Boot 3!";
    }
}
```

  浏览器访问路径，控制台查看效果

  ![](https://secure2.wostatic.cn/static/vCb6myPBNpMgVjF6nAXATV/image.png?auth_key=1711536557-2MJdqJLu7mRjn75PbkHPRg-0-0aeaae7bbe649d68c7399118567fb60a)

#### 多环境配置和使用

  1. 需求

     在Spring Boot中，可以使用多环境配置来根据不同的运行环境（如开发、测试、生产）加载不同的配置。**SpringBoot支持多环境配置让应用程序在不同的环境中使用不同的配置参数，例如数据库连接信息、日志级别、缓存配置等。**

     以下是实现Spring Boot多环境配置的常见方法：

     1. 属性文件分离：将应用程序的配置参数分离到不同的属性文件中，每个环境对应一个属性文件。例如，可以创建`application-dev.properties`、`application-prod.properties`和`application-test.properties`等文件。在这些文件中，可以定义各自环境的配置参数，如数据库连接信息、端口号等。然后，在`application.properties`中通过`spring.profiles.active`属性指定当前使用的环境。Spring Boot会根据该属性来加载对应环境的属性文件，覆盖默认的配置。
     2. YAML配置文件：与属性文件类似，可以将配置参数分离到不同的YAML文件中，每个环境对应一个文件。例如，可以创建`application-dev.yml`、`application-prod.yml`和`application-test.yml`等文件。在这些文件中，可以使用YAML语法定义各自环境的配置参数。同样，通过`spring.profiles.active`属性指定当前的环境，Spring Boot会加载相应的YAML文件。
     3. 命令行参数(动态)：可以通过命令行参数来指定当前的环境。例如，可以使用`--spring.profiles.active=dev`来指定使用开发环境的配置。

     通过上述方法，Spring Boot会根据当前指定的环境来加载相应的配置文件或参数，从而实现多环境配置。这样可以简化在不同环境之间的配置切换，并且确保应用程序在不同环境中具有正确的配置。

  2. 多环境配置（基于方式b实践）

     > 创建开发、测试、生产三个环境的配置文件

     application-dev.yml（开发）

```YAML
spring:
  jdbc:
    datasource:
      driverClassName: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql:///dev
      username: root
      password: root
```

  application-test.yml（测试）

```YAML
spring:
  jdbc:
    datasource:
      driverClassName: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql:///test
      username: root
      password: root
```

  application-prod.yml（生产）

```YAML
spring:
  jdbc:
    datasource:
      driverClassName: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql:///prod
      username: root
      password: root
```

    3. 环境激活

```YAML
spring:
  profiles:
    active: dev
```

  4. 测试效果

     ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711536984083-1.png)

     **注意 :**

     1.**如果设置了spring.profiles.active，并且和application有重叠属性，以active设置优先。**

     2.如果设置了spring.profiles.active，和application无重叠属性，application设置依然生效！

     3.

![image-20240327190614606](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240327190614606.png)



### Springboot3 整合SpirngMVC

#### 实现过程

    1. 创建程序
    2. 引入依赖

```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.0.5</version>
    </parent>

    <groupId>com.atguigu</groupId>
    <artifactId>springboot-starter-springmvc-03</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!--        web开发的场景启动器 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
```

    3. 创建启动类

```Java
@SpringBootApplication
public class MainApplication {

    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }
}
```

    4. 创建实体类

```Java
package com.atguigu.pojo;

public class User {
    private String username ;
    private String password ;
    private Integer age ;
    private String sex ;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }
}
```

    5. 编写Controller

```Java
package com.atguigu.controller;

import com.atguigu.pojo.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("/user")
public class UserController {

    @GetMapping("/getUser")
    @ResponseBody
    public User getUser(){
        
        User user = new User();
        user.setUsername("杨过");
        user.setPassword("123456");
        user.setAge(18);
        user.setSex("男");
        return user;
    }
}
```

  6. 访问测试

     ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711537725927-4.png)

 #### web相关配置

​    位置：application.yml

```YAML
# web相关的配置
# https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.server
server:
  # 端口号设置
  port: 80
  # 项目根路径
  servlet:
    context-path: /boot
```

  当涉及Spring Boot的Web应用程序配置时，以下是五个重要的配置参数：

    1. `server.port`: 指定应用程序的HTTP服务器端口号。默认情况下，Spring Boot使用8080作为默认端口。您可以通过在配置文件中设置`server.port`来更改端口号。
    2. `server.servlet.context-path`: 设置应用程序的上下文路径。这是应用程序在URL中的基本路径。默认情况下，上下文路径为空。您可以通过在配置文件中设置`server.servlet.context-path`属性来指定自定义的上下文路径。
    3. `spring.mvc.view.prefix`和`spring.mvc.view.suffix`: 这两个属性用于配置视图解析器的前缀和后缀。视图解析器用于解析控制器返回的视图名称，并将其映射到实际的视图页面。`spring.mvc.view.prefix`定义视图的前缀，`spring.mvc.view.suffix`定义视图的后缀。
    4. `spring.resources.static-locations`: 配置静态资源的位置。静态资源可以是CSS、JavaScript、图像等。默认情况下，Spring Boot会将静态资源放在`classpath:/static`目录下。您可以通过在配置文件中设置`spring.resources.static-locations`属性来自定义静态资源的位置。
    5. `spring.http.encoding.charset`和`spring.http.encoding.enabled`: 这两个属性用于配置HTTP请求和响应的字符编码。`spring.http.encoding.charset`定义字符编码的名称（例如UTF-8），`spring.http.encoding.enabled`用于启用或禁用字符编码的自动配置。

  这些是在Spring Boot的配置文件中与Web应用程序相关的一些重要配置参数。根据您的需求，您可以在配置文件中设置这些参数来定制和配置您的Web应用程序

#### 静态资源处理

  > 在WEB开发中我们需要引入一些静态资源 , 例如 : HTML , CSS , JS , 图片等 , 如果是普通的项目静态资源可以放在项目的webapp目录下。现在使用Spring Boot做开发 , 项目中没有webapp目录 , 我们的项目是一个jar工程，那么就没有webapp，我们的静态资源该放哪里呢？

  1. 默认路径

     在springboot中就定义了静态资源的默认查找路径：

```Java
package org.springframework.boot.autoconfigure.web;
//..................
public static class Resources {
        private static final String[] CLASSPATH_RESOURCE_LOCATIONS = new String[]{"classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"};
        private String[] staticLocations;
        private boolean addMappings;
        private boolean customized;
        private final Chain chain;
        private final Cache cache;

        public Resources() {
            this.staticLocations = CLASSPATH_RESOURCE_LOCATIONS;
            this.addMappings = true;
            this.customized = false;
            this.chain = new Chain();
            this.cache = new Cache();
        }
//...........        
```

  **默认的静态资源路径为：**

  **· classpath:/META-INF/resources/**

  **· classpath:/resources/**

  **· classpath:/static/**

  **· classpath:/public/**

  我们只要静态资源放在这些目录中任何一个，SpringMVC都会帮我们处理。 我们习惯会把静态资源放在classpath:/static/ 目录下。在resources目录下创建index.html文件

  <img src="https://secure2.wostatic.cn/static/whoDp3vndysPK89ybUfmJf/image.png?auth_key=1711541065-8JBQC71wsrAbn7QmZAcZH8-0-d6fd25589ab0f7c71ecb693ad7f9100c" style="zoom:50%;" />

  打开浏览器输入 : [http://localhost:8080/index.html](http://localhost:8080/index.html)

    2. 覆盖路径

```YAML
# web相关的配置
# https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties.server
server:
  # 端口号设置
  port: 80
  # 项目根路径
  servlet:
    context-path: /boot
spring:
  web:
    resources:
      # 配置静态资源地址,如果设置,会覆盖默认值
      static-locations: classpath:/webapp
```

  <img src="https://secure2.wostatic.cn/static/iAmdM5vRon6g3VUanf5uEW/image.png?auth_key=1711541065-8DGKFZGr6myzcEb8b21HNM-0-44e5559f7c7812d568f8705c3d79c333" style="zoom:50%;" />

  访问地址：[http://localhost/boot/login.html](http://localhost/boot/login.html)

#### 自定义拦截器(SpringMVC配置)

    1. 拦截器声明

```Java
package com.atguigu.interceptor;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

@Component
public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("MyInterceptor拦截器的preHandle方法执行....");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("MyInterceptor拦截器的postHandle方法执行....");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("MyInterceptor拦截器的afterCompletion方法执行....");
    }
}
```

  2. 拦截器配置

     正常使用配置类，只要保证，**配置类要在启动类的同包或者子包方可生效！**

```Java
package com.atguigu.config;

import com.atguigu.interceptor.MyInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MvcConfig implements WebMvcConfigurer {

    @Autowired
    private MyInterceptor myInterceptor ;

    /**
     * /**  拦截当前目录及子目录下的所有路径 /user/**   /user/findAll  /user/order/findAll
     * /*   拦截当前目录下的以及子路径   /user/*     /user/findAll
     * @param registry
     */
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(myInterceptor).addPathPatterns("/**");
    }
}
```

  3. 拦截器效果测试

     ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711541290848-7.png)

### Springboot3 整合druid数据源

1. 创建程序
2. 引入依赖

```XML
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.0.5</version>
    </parent>
    <groupId>com.atguigu</groupId>
    <artifactId>springboot-starter-druid-04</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>


    <dependencies>
        <!--  web开发的场景启动器 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- 数据库相关配置启动器 jdbctemplate 事务相关-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>

        <!-- druid启动器的依赖  -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-3-starter</artifactId>
            <version>1.2.18</version>
        </dependency>

        <!-- 驱动类-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.28</version>
        </dependency>

    </dependencies>

    <!--    SpringBoot应用打包插件-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

3. 启动类

```Java
@SpringBootApplication
public class MainApplication {

    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class,args);
    }
}

```

4. 配置文件编写

   > 添加druid连接池的基本配置

```YAML
spring:
  datasource:
    # 连接池类型 
    type: com.alibaba.druid.pool.DruidDataSource

    # Druid的其他属性配置 springboot3整合情况下,数据库连接信息必须在Druid属性下!
    druid:
      url: jdbc:mysql://localhost:3306/day01
      username: root
      password: root
      driver-class-name: com.mysql.cj.jdbc.Driver
      # 初始化时建立物理连接的个数
      initial-size: 5
      # 连接池的最小空闲数量
      min-idle: 5
      # 连接池最大连接数量
      max-active: 20
      # 获取连接时最大等待时间，单位毫秒
      max-wait: 60000
      # 申请连接的时候检测，如果空闲时间大于timeBetweenEvictionRunsMillis，执行validationQuery检测连接是否有效。
      test-while-idle: true
      # 既作为检测的间隔时间又作为testWhileIdel执行的依据
      time-between-eviction-runs-millis: 60000
      # 销毁线程时检测当前连接的最后活动时间和当前时间差大于该值时，关闭当前连接(配置连接在池中的最小生存时间)
      min-evictable-idle-time-millis: 30000
      # 用来检测数据库连接是否有效的sql 必须是一个查询语句(oracle中为 select 1 from dual)
      validation-query: select 1
      # 申请连接时会执行validationQuery检测连接是否有效,开启会降低性能,默认为true
      test-on-borrow: false
      # 归还连接时会执行validationQuery检测连接是否有效,开启会降低性能,默认为true
      test-on-return: false
      # 是否缓存preparedStatement, 也就是PSCache,PSCache对支持游标的数据库性能提升巨大，比如说oracle,在mysql下建议关闭。
      pool-prepared-statements: false
      # 要启用PSCache，必须配置大于0，当大于0时，poolPreparedStatements自动触发修改为true。在Druid中，不会存在Oracle下PSCache占用内存过多的问题，可以把这个数值配置大一些，比如说100
      max-pool-prepared-statement-per-connection-size: -1
      # 合并多个DruidDataSource的监控数据
      use-global-data-source-stat: true

logging:
  level:
    root: debug
```

5. 编写Controller

```Java
@Slf4j
@Controller
@RequestMapping("/user")
public class UserController {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @GetMapping("/getUser")
    @ResponseBody
    public User getUser(){
        String sql = "select * from users where id = ? ; ";
        User user = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<>(User.class), 1);
        log.info("查询的user数据为:{}",user.toString());
        return user;
    }
    
}
```

6. 启动测试

7. 问题解决（druid版本1.2.20已经解决 不需要额外操作）

   通过源码分析，druid-spring-boot-3-starter目前最新版本是1.2.18，虽然适配了SpringBoot3，但缺少自动装配的配置文件，需要手动在resources目录下创建META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports，文件内容如下!

```Java
com.alibaba.druid.spring.boot3.autoconfigure.DruidDataSourceAutoConfigure
```

​    ![](https://secure2.wostatic.cn/static/emBrGSESbCgeAmKY6cjYtU/image.png?auth_key=1711541409-iPoHQKu5DcBqZFaBhFFyNR-0-44f3768123634a12697db537747bde10)



### SpringBoot3 项目打包运行

#### 添加打包插件

  > 在Spring Boot项目中添加`spring-boot-maven-plugin`插件是为了支持将项目打包成可执行的可运行jar包。如果不添加`spring-boot-maven-plugin`插件配置，使用常规的`java -jar`命令来运行打包后的Spring Boot项目是无法找到应用程序的入口点，因此导致无法运行。

```XML
<!--    SpringBoot应用打包插件-->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

#### 执行打包

  在idea点击package进行打包

  可以在编译的target文件中查看jar包

  <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711543720684-10.png" style="zoom:50%;" />

#### 命令启动和参数说明

  `java -jar`命令用于在Java环境中执行可执行的JAR文件。下面是关于`java -jar`命令的说明：

```XML
命令格式：java -jar  [选项] [参数] <jar文件名>
```

    1. `-D<name>=<value>`：设置系统属性，可以通过`System.getProperty()`方法在应用程序中获取该属性值。例如：`java -jar -Dserver.port=8080 myapp.jar`。
    2. `-X`：设置JVM参数，例如内存大小、垃圾回收策略等。常用的选项包括：
       - `-Xmx<size>`：设置JVM的最大堆内存大小，例如 `-Xmx512m` 表示设置最大堆内存为512MB。
       - `-Xms<size>`：设置JVM的初始堆内存大小，例如 `-Xms256m` 表示设置初始堆内存为256MB。
    3. `-Dspring.profiles.active=<profile>`：指定Spring Boot的激活配置文件，可以通过`application-<profile>.properties`或`application-<profile>.yml`文件来加载相应的配置。例如：`java -jar -Dspring.profiles.active=dev myapp.jar`。

  启动和测试：

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711543720684-11.png)

  注意： -D 参数必须要在jar之前！否者不生效！

## MyBatis-Plus

- 自动生成单表的CRUD功能

- 提供丰富的条件拼接方式

- 全自动ORM类型持久层框架

### MyBatis核心功能

#### 基于Mapper接口CRUD

  > 通用 CRUD 封装**[BaseMapper (opens new window)](https://gitee.com/baomidou/mybatis-plus/blob/3.0/mybatis-plus-core/src/main/java/com/baomidou/mybatisplus/core/mapper/BaseMapper.java)接口，** `Mybatis-Plus` 启动时自动解析实体表关系映射转换为 `Mybatis` 内部对象注入容器! 内部包含常见的单表操作！

  ##### Insert方法

![image-20240327215738742](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240327215738742.png)

```Java
// 插入一条记录
// T 就是要插入的实体对象
// 默认主键生成策略为雪花算法（后面讲解）
int insert(T entity);  
```

| 类型 | 参数名 | 描述     |
| ---- | ------ | -------- |
| T    | entity | 实体对象 |


  ##### Delete方法

```Java
// 根据 entity 条件，删除记录
int delete(@Param(Constants.WRAPPER) Wrapper<T> wrapper);

// 删除（根据ID 批量删除）
int deleteBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);

// 根据 ID 删除
int deleteById(Serializable id);

// 根据 columnMap 条件，删除记录
int deleteByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);
```

| 类型                               | 参数名    | 描述                                 |
| ---------------------------------- | --------- | ------------------------------------ |
| Wrapper<T>                         | wrapper   | 实体对象封装操作类（可以为 null）    |
| Collection<? extends Serializable> | idList    | 主键 ID 列表(不能为 null 以及 empty) |
| Serializable                       | id        | 主键 ID                              |
| Map<String, Object>                | columnMap | 表字段 map 对象                      |


  ##### Update方法

```Java
// 根据 whereWrapper 条件，更新记录
int update(@Param(Constants.ENTITY) T updateEntity, 
            @Param(Constants.WRAPPER) Wrapper<T> whereWrapper);

// 根据 ID 修改  主键属性必须值
int updateById(@Param(Constants.ENTITY) T entity);
```

| 类型       | 参数名        | 描述                                                         |
| ---------- | ------------- | ------------------------------------------------------------ |
| T          | entity        | 实体对象 (set 条件值,可为 null)                              |
| Wrapper<T> | updateWrapper | 实体对象封装操作类（可以为 null,里面的 entity 用于生成 where 语句） |


  ##### Select方法

```Java
// 根据 ID 查询
T selectById(Serializable id);

// 根据 entity 条件，查询一条记录
T selectOne(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 查询（根据ID 批量查询）
List<T> selectBatchIds(@Param(Constants.COLLECTION) Collection<? extends Serializable> idList);

// 根据 entity 条件，查询全部记录
List<T> selectList(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 查询（根据 columnMap 条件）
List<T> selectByMap(@Param(Constants.COLUMN_MAP) Map<String, Object> columnMap);

// 根据 Wrapper 条件，查询全部记录
List<Map<String, Object>> selectMaps(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 根据 Wrapper 条件，查询全部记录。注意： 只返回第一个字段的值
List<Object> selectObjs(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 根据 entity 条件，查询全部记录（并翻页）
IPage<T> selectPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 根据 Wrapper 条件，查询全部记录（并翻页）
IPage<Map<String, Object>> selectMapsPage(IPage<T> page, @Param(Constants.WRAPPER) Wrapper<T> queryWrapper);

// 根据 Wrapper 条件，查询总记录数
Integer selectCount(@Param(Constants.WRAPPER) Wrapper<T> queryWrapper);
```

  参数说明

| 类型                               | 参数名       | 描述                                     |
| ---------------------------------- | ------------ | ---------------------------------------- |
| Serializable                       | id           | 主键 ID                                  |
| Wrapper<T>                         | queryWrapper | 实体对象封装操作类（可以为 null）        |
| Collection<? extends Serializable> | idList       | 主键 ID 列表(不能为 null 以及 empty)     |
| Map<String, Object>                | columnMap    | 表字段 map 对象                          |
| IPage<T>                           | page         | 分页查询条件（可以为 RowBounds.DEFAULT） |


  ##### 自定义和多表映射

  mybatis-plus的默认mapperxml位置

```YAML
mybatis-plus: # mybatis-plus的配置
  # 默认位置 private String[] mapperLocations = new String[]{"classpath*:/mapper/**/*.xml"};    
  mapper-locations: classpath:/mapper/*.xml
```

  自定义mapper方法：

```Java
public interface UserMapper extends BaseMapper<User> {

    //正常自定义方法！
    //可以使用注解@Select或者mapper.xml实现
    List<User> queryAll();
}

```

  基于mapper.xml实现：

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace = 接口的全限定符 -->
<mapper namespace="com.atguigu.mapper.UserMapper">

   <select id="queryAll" resultType="user" >
       select * from user
   </select>
</mapper>
```

#### 基于Service接口CRUD

  通用 Service CRUD 封装[IService (opens new window)](https://gitee.com/baomidou/mybatis-plus/blob/3.0/mybatis-plus-extension/src/main/java/com/baomidou/mybatisplus/extension/service/IService.java)接口，进一步封装 CRUD 采用 `get 查询单行` `remove 删除` `list 查询集合` `page 分页` 前缀命名方式区分 `Mapper` 层避免混淆，

  ##### 对比Mapper接口CRUD区别：

  - **service添加了批量方法**
  - **service层的方法自动添加事务**

  ##### 使用Iservice接口方式

  接口继承IService接口

```Java
public interface UserService extends IService<User> {
}
```

  类继承ServiceImpl实现类

```Java
@Service
public class UserServiceImpl extends ServiceImpl<UserMapper,User> implements UserService{

} 
```

  ##### CRUD方法介绍

```Java
保存：
// 插入一条记录（选择字段，策略插入）
boolean save(T entity);
// 插入（批量）
boolean saveBatch(Collection<T> entityList);
// 插入（批量）
boolean saveBatch(Collection<T> entityList, int batchSize);

修改或者保存：
// TableId 注解存在更新记录，否插入一条记录
boolean saveOrUpdate(T entity);
// 根据updateWrapper尝试更新，否继续执行saveOrUpdate(T)方法
boolean saveOrUpdate(T entity, Wrapper<T> updateWrapper);
// 批量修改插入
boolean saveOrUpdateBatch(Collection<T> entityList);
// 批量修改插入
boolean saveOrUpdateBatch(Collection<T> entityList, int batchSize);

移除：
// 根据 queryWrapper 设置的条件，删除记录
boolean remove(Wrapper<T> queryWrapper);
// 根据 ID 删除
boolean removeById(Serializable id);
// 根据 columnMap 条件，删除记录
boolean removeByMap(Map<String, Object> columnMap);
// 删除（根据ID 批量删除）
boolean removeByIds(Collection<? extends Serializable> idList);

更新：
// 根据 UpdateWrapper 条件，更新记录 需要设置sqlset
boolean update(Wrapper<T> updateWrapper);
// 根据 whereWrapper 条件，更新记录
boolean update(T updateEntity, Wrapper<T> whereWrapper);
// 根据 ID 选择修改
boolean updateById(T entity);
// 根据ID 批量更新
boolean updateBatchById(Collection<T> entityList);
// 根据ID 批量更新
boolean updateBatchById(Collection<T> entityList, int batchSize);

数量： 
// 查询总记录数
int count();
// 根据 Wrapper 条件，查询总记录数
int count(Wrapper<T> queryWrapper);

查询：
// 根据 ID 查询
T getById(Serializable id);
// 根据 Wrapper，查询一条记录。结果集，如果是多个会抛出异常，随机取一条加上限制条件 wrapper.last("LIMIT 1")
T getOne(Wrapper<T> queryWrapper);
// 根据 Wrapper，查询一条记录
T getOne(Wrapper<T> queryWrapper, boolean throwEx);
// 根据 Wrapper，查询一条记录
Map<String, Object> getMap(Wrapper<T> queryWrapper);
// 根据 Wrapper，查询一条记录
<V> V getObj(Wrapper<T> queryWrapper, Function<? super Object, V> mapper);

集合：
// 查询所有
List<T> list();
// 查询列表
List<T> list(Wrapper<T> queryWrapper);
// 查询（根据ID 批量查询）
Collection<T> listByIds(Collection<? extends Serializable> idList);
// 查询（根据 columnMap 条件）
Collection<T> listByMap(Map<String, Object> columnMap);
// 查询所有列表
List<Map<String, Object>> listMaps();
// 查询列表
List<Map<String, Object>> listMaps(Wrapper<T> queryWrapper);
// 查询全部记录
List<Object> listObjs();
// 查询全部记录
<V> List<V> listObjs(Function<? super Object, V> mapper);
// 根据 Wrapper 条件，查询全部记录
List<Object> listObjs(Wrapper<T> queryWrapper);
// 根据 Wrapper 条件，查询全部记录
<V> List<V> listObjs(Wrapper<T> queryWrapper, Function<? super Object, V> mapper);

```

#### 分页查询实现

    1. 导入分页插件

```Java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
    return interceptor;
}
```

    2. 使用分页查询

```Java
@Test
public void testPageQuery(){
    //设置分页参数
    Page<User> page = new Page<>(1, 5);
    userMapper.selectPage(page, null);
    //获取分页数据
    List<User> list = page.getRecords();
    list.forEach(System.out::println);
    System.out.println("当前页："+page.getCurrent());
    System.out.println("每页显示的条数："+page.getSize());
    System.out.println("总记录数："+page.getTotal());
    System.out.println("总页数："+page.getPages());
    System.out.println("是否有上一页："+page.hasPrevious());
    System.out.println("是否有下一页："+page.hasNext());
}
```

  3. 自定义的mapper方法使用分页

     方法

```Java
//传入参数携带Ipage接口
//返回结果为IPage
IPage<User> selectPageVo(IPage<?> page, Integer id);

//在这个方法中，IPage 是一个泛型接口，通常用于实现分页查询功能。在 MyBatis-Plus 等一些Java框架中经常会看到类似的接口定义。

具体来说：

IPage<T> 表示一个分页查询结果对象，其中的 <T> 是指定查询结果的类型。在这个方法中，IPage<User> 表示查询结果为 User 类型的分页数据。
selectPageVo 方法是一个用于查询并返回分页数据的方法，它接受一个 IPage<?> page 参数用来指定查询的分页信息，以及一个 Integer id 参数用于查询条件。
```

  接口实现

```Java
<select id="selectPageVo" resultType="xxx.xxx.xxx.User">
    SELECT * FROM user WHERE id > #{id}
</select>
```

  测试

```Java
@Test
public void testQuick(){

    IPage page = new Page(1,2);   

    userMapper.selectPageVo(page,2);

    long current = page.getCurrent();
    System.out.println("current = " + current);
    long pages = page.getPages();
    System.out.println("pages = " + pages);
    long total = page.getTotal();
    System.out.println("total = " + total);
    List records = page.getRecords();
    System.out.println("records = " + records);

}
```

#### 条件构造器

##### 条件构造器作用

  实例代码：

```Java
QueryWrapper<User> queryWrapper = new QueryWrapper<>();
queryWrapper.eq("name", "John"); // 添加等于条件
queryWrapper.ne("age", 30); // 添加不等于条件
queryWrapper.like("email", "@gmail.com"); // 添加模糊匹配条件
等同于： 
delete from user where name = "John" and age != 30
                                  and email like "%@gmail.com%"
// 根据 entity 条件，删除记录
int delete(@Param(Constants.WRAPPER) Wrapper<T> wrapper);
```

  使用MyBatis-Plus的条件构造器，你可以构建灵活、高效的查询条件，而不需要手动编写复杂的 SQL 语句。它提供了许多方法来支持各种条件操作符，并且可以通过链式调用来组合多个条件。这样可以简化查询的编写过程，并提高开发效率。

##### 条件构造器继承结构

  条件构造器类结构：

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711628196066-1.png)

  Wrapper ： 条件构造抽象类，最顶端父类

  - AbstractWrapper ： 用于查询条件封装，生成 sql 的 where 条件
    - QueryWrapper ： 查询/删除条件封装
    - UpdateWrapper ： 修改条件封装
    - AbstractLambdaWrapper ： 使用Lambda 语法
      - LambdaQueryWrapper ：用于Lambda语法使用的查询Wrapper
      - LambdaUpdateWrapper ： Lambda 更新封装Wrapper

##### 基于QueryWrapper 组装条件

![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711628279395-4.png)

  组装查询条件：

```Java
@Test
public void test01(){
    //查询用户名包含a，年龄在20到30之间，并且邮箱不为null的用户信息
    //SELECT id,username AS name,age,email,is_deleted FROM t_user WHERE is_deleted=0 AND (username LIKE ? AND age BETWEEN ? AND ? AND email IS NOT NULL)
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.like("username", "a")
            .between("age", 20, 30)
            .isNotNull("email");
    List<User> list = userMapper.selectList(queryWrapper);
    list.forEach(System.out::println);

```

  组装排序条件:

```Java
@Test
public void test02(){
    //按年龄降序查询用户，如果年龄相同则按id升序排列
    //SELECT id,username AS name,age,email,is_deleted FROM t_user WHERE is_deleted=0 ORDER BY age DESC,id ASC
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper
            .orderByDesc("age")
            .orderByAsc("id");
    List<User> users = userMapper.selectList(queryWrapper);
    users.forEach(System.out::println);
}
```

  组装删除条件:

```Java
@Test
public void test03(){
    //删除email为空的用户
    //DELETE FROM t_user WHERE (email IS NULL)
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.isNull("email");
    //条件构造器也可以构建删除语句的条件
    int result = userMapper.delete(queryWrapper);
    System.out.println("受影响的行数：" + result);
}
```

  and和or关键字使用(修改)：

```Java
@Test
public void test04() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    //将年龄大于20并且用户名中包含有a或邮箱为null的用户信息修改
    //UPDATE t_user SET age=?, email=? WHERE username LIKE ? AND age > ? OR email IS NULL)
    queryWrapper
            .like("username", "a")
            .gt("age", 20)
            .or()
            .isNull("email");
    User user = new User();
    user.setAge(18);
    user.setEmail("user@atguigu.com");
    int result = userMapper.update(user, queryWrapper);
    System.out.println("受影响的行数：" + result);
}
```

  指定列映射查询：

```Java
@Test
public void test05() {
    //查询用户信息的username和age字段
    //SELECT username,age FROM t_user
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    queryWrapper.select("username", "age");
    //selectMaps()返回Map集合列表，通常配合select()使用，避免User对象中没有被查询到的列值为null
    List<Map<String, Object>> maps = userMapper.selectMaps(queryWrapper);
    maps.forEach(System.out::println);
}
```

  condition判断组织条件:

```Java
 @Test
public void testQuick3(){
    
    String name = "root";
    int    age = 18;

    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    //判断条件拼接
    //当name不为null拼接等于, age > 1 拼接等于判断
    //方案1: 手动判断
    if (!StringUtils.isEmpty(name)){
        queryWrapper.eq("name",name);
    }
    if (age > 1){
        queryWrapper.eq("age",age);
    }
    
    userMapper.selectList(queryMapper);   
    
    
    
    //方案2: 拼接condition判断  
    //每个条件拼接方法都condition参数,这是一个比较运算,为true追加当前条件!
    //eq(condition,列名,值)
    queryWrapper.eq(!StringUtils.isEmpty(name),"name",name)
            .eq(age>1,"age",age);   
}
```

##### 基于 UpdateWrapper组装条件

  使用queryWrapper:

```Java
@Test
public void test04() {
    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    //将年龄大于20并且用户名中包含有a或邮箱为null的用户信息修改
    //UPDATE t_user SET age=?, email=? WHERE username LIKE ? AND age > ? OR email IS NULL)
    queryWrapper
            .like("username", "a")
            .gt("age", 20)
            .or()
            .isNull("email");
    User user = new User();
    user.setAge(18);
    user.setEmail("user@atguigu.com");
    int result = userMapper.update(user, queryWrapper);
    System.out.println("受影响的行数：" + result);
}
```

  注意：使用queryWrapper + 实体类形式可以实现修改，但是无法将列值修改为null值！

  使用updateWrapper:

```Java
@Test
public void testQuick2(){

    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
    //将id = 3 的email设置为null, age = 18
    updateWrapper.eq("id",3)
            .set("email",null)  // set 指定列和结果
            .set("age",18);
    //如果使用updateWrapper 实体对象写null即可!
    int result = userMapper.update(null, updateWrapper);
    System.out.println("result = " + result);

}
```

  使用updateWrapper可以随意设置列的值！！



  ##### 基于LambdaQueryWrapper组装条件

    1. **LambdaQueryWrapper对比QueryWrapper优势**

  QueryWrapper 示例代码：

```Java
QueryWrapper<User> queryWrapper = new QueryWrapper<>();
queryWrapper.eq("name", "John")
  .ge("age", 18)
  .orderByDesc("create_time")
  .last("limit 10");
List<User> userList = userMapper.selectList(queryWrapper);
```

  LambdaQueryWrapper 示例代码：

```Java
LambdaQueryWrapper<User> lambdaQueryWrapper = new LambdaQueryWrapper<>();

lambdaQueryWrapper.eq(User::getName, "John")
  .ge(User::getAge, 18)
  .orderByDesc(User::getCreateTime)
  .last("limit 10");
List<User> userList = userMapper.selectList(lambdaQueryWrapper);
```

  从上面的代码对比可以看出，相比于 QueryWrapper，LambdaQueryWrapper 使用了实体类的属性引用（例如 `User::getName`、`User::getAge`），而不是字符串来表示字段名，这提高了代码的可读性和可维护性。

    2. **lambda表达式回顾**

  Lambda 表达式是 Java 8 引入的一种函数式编程特性，它提供了一种更简洁、更直观的方式来表示匿名函数或函数式接口的实现。Lambda 表达式可以用于简化代码，提高代码的可读性和可维护性。

  Lambda 表达式的语法可以分为以下几个部分：

  1. **参数列表：** 参数列表用小括号 `()` 括起来，可以指定零个或多个参数。如果没有参数，可以省略小括号；如果只有一个参数，可以省略小括号。

     示例：`(a, b)`, `x ->`, `() ->`

  2. **箭头符号：** 箭头符号 `->` 分割参数列表和 Lambda 表达式的主体部分。

     示例：`->`

  3. **Lambda 表达式的主体：** Lambda 表达式的主体部分可以是一个表达式或一个代码块。如果是一个表达式，可以省略 return 关键字；如果是多条语句的代码块，需要使用大括号 `{}` 括起来，并且需要明确指定 return 关键字。

     示例：

     - 单个表达式：`x -> x * x`
     - 代码块：`(x, y) -> { int sum = x + y; return sum; }`

  Lambda 表达式的语法可以更具体地描述如下：

```Java
// 使用 Lambda 表达式实现一个接口的方法
interface Greeting {
    void sayHello();
}

public class LambdaExample {
    public static void main(String[] args) {
    
        //原始匿名内部类方式
        Greeting greeting = new Greeting() {
            @Override
            public void sayHello(int a) {
                System.out.println("Hello, world!");
            }
        };
        
        a->System.out.println("Hello, world!")
        
        // 使用 Lambda 表达式实现接口的方法
        greeting = () -> System.out.println("Hello, world!");

          System.out::println;
           () ->  类.XXX(); -> 类：：方法名
        // 调用接口的方法
        greeting.sayHello();
    }
}
```

  **3. 方法引用回顾:**

  方法引用是 Java 8 中引入的一种语法特性，它提供了一种简洁的方式来直接引用已有的方法或构造函数。方法引用可以替代 Lambda 表达式，使代码更简洁、更易读。

  Java 8 支持以下几种方法引用的形式：

    1. **静态方法引用：** 引用静态方法，语法为 `类名::静态方法名`。
    2. **实例方法引用：** 引用实例方法，语法为 `实例对象::实例方法名`。
    3. **对象方法引用：** 引用特定对象的实例方法，语法为 `类名::实例方法名`。
    4. **构造函数引用：** 引用构造函数，语法为 `类名::new`。

  演示代码:

```Java
import java.util.Arrays;
import java.util.List;
import java.util.function.Consumer;

public class MethodReferenceExample {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("John", "Tom", "Alice");
        // 使用 Lambda 表达式
        names.forEach(name -> System.out.println(name));
        // 使用方法引用
        names.forEach(System.out::println);
    }
}
```

  **4. lambdaQueryWrapper使用案例:**

```Java
@Test
public void testQuick4(){

    String name = "root";
    int    age = 18;

    QueryWrapper<User> queryWrapper = new QueryWrapper<>();
    //每个条件拼接方法都condition参数,这是一个比较运算,为true追加当前条件!
    //eq(condition,列名,值)
    queryWrapper.eq(!StringUtils.isEmpty(name),"name",name)
            .eq(age>1,"age",age);

    //TODO: 使用lambdaQueryWrapper
    LambdaQueryWrapper<User> lambdaQueryWrapper = new LambdaQueryWrapper<>();
    //注意: 需要使用方法引用
    //技巧: 类名::方法名
    lambdaQueryWrapper.eq(!StringUtils.isEmpty(name), User::getName,name);
    List<User> users= userMapper.selectList(lambdaQueryWrapper);
    System.out.println(users);
}
```

##### 基于LambdaUpdateWrapper组装条件

  使用案例:

```Java
@Test
public void testQuick2(){

    UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
    //将id = 3 的email设置为null, age = 18
    updateWrapper.eq("id",3)
            .set("email",null)  // set 指定列和结果
            .set("age",18);

    //使用lambdaUpdateWrapper
    LambdaUpdateWrapper<User> updateWrapper1 = new LambdaUpdateWrapper<>();
    updateWrapper1.eq(User::getId,3)
            .set(User::getEmail,null)
            .set(User::getAge,18);
    
    //如果使用updateWrapper 实体对象写null即可!
    int result = userMapper.update(null, updateWrapper);
    System.out.println("result = " + result);
}
```

#### 核心注解使用

  1. 理解和介绍

     MyBatis-Plus是一个基于MyBatis框架的增强工具，提供了一系列简化和增强的功能，用于加快开发人员在使用MyBatis进行数据库访问时的效率。MyBatis-Plus提供了一种基于注解的方式来定义和映射数据库操作，其中的注解起到了重要作用。

     理解：

```Java
public interface UserMapper extends BaseMapper<User> {

}
```

  此接口对应的方法为什么会自动触发 user表的crud呢？默认情况下， 根据指定的<实体类>的名称对应数据库表名，属性名对应数据库的列名！但是不是所有数据库的信息和实体类都完全映射！例如： 表名 t_user  → 实体类 User 这时候就不对应了！自定义映射关系就可以使用mybatis-plus提供的注解即可！

    2. @TableName注解
       - 描述：表名注解，标识实体类对应的表
       - 使用位置：实体类

```Java
@TableName("sys_user") //对应数据库表名
public class User {
    private Long id;
    private String name;
    private Integer age;
    private String email;
} 
```

  特殊情况：如果表名和实体类名相同**（忽略大小写）**可以省略该注解！其他解决方案：全局设置前缀 ([https://www.baomidou.com/pages/56bac0/#基本配置](https://www.baomidou.com/pages/56bac0/#基本配置))

```YAML
mybatis-plus: # mybatis-plus的配置
  global-config:
    db-config:
      table-prefix: sys_ # 表名前缀字符串
```

    3. @TableId 注解
       - 描述：主键注解
       - 使用位置：实体类主键字段

```Java
@TableName("sys_user")
public class User {
    @TableId(value="主键列名",type=主键策略)
    private Long id; 
    private String name;
    private Integer age;
    private String email;
}
```

| 属性  | 类型   | 必须指定 | 默认值      | 描述         |
| ----- | ------ | -------- | ----------- | ------------ |
| value | String | 否       | ""          | 主键字段名   |
| type  | Enum   | 否       | IdType.NONE | 指定主键类型 |

- `type` 属性:用于指定主键生成类型。可**以通过设置 `type` 属性来指定主键的生成方式，比如自增长、UUID、全局唯一ID（雪花算法）等。**`type` 属性的取值是 `IdType` 枚举类型的常量之一。
- `value` 属性：用于**指定数据库表中的字段名**。****如果实体类的主键字段名和数据库表中的主键字段名不一致****，则可以通过 `value` 属性来指定数据库表中对应的主键字段名。

 [IdType](https://github.com/baomidou/mybatis-plus/blob/3.0/mybatis-plus-annotation/src/main/java/com/baomidou/mybatisplus/annotation/IdType.java)属性可选值：  

|        值         |                             描述                             |
| :---------------: | :----------------------------------------------------------: |
|       AUTO        |             数据库 ID 自增 (mysql配置主键自增长)             |
| ASSIGN_ID（默认） | 分配 ID(主键类型为 Number(Long )或 String)(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextId`(默认实现类为`DefaultIdentifierGenerator`雪花算法) |

  全局配置修改主键策略:

```Java
mybatis-plus:
  configuration:
    # 配置MyBatis日志
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    db-config:
      # 配置MyBatis-Plus操作表的默认前缀
      table-prefix: t_
      # 配置MyBatis-Plus的主键策略
      id-type: auto
```

  在以下场景下，添加`@TableId`注解是必要的：

  1. 实体类的字段与数据库表的主键字段不同名：如果实体类中的字段与数据库表的主键字段不一致，需要使用`@TableId`注解来指定实体类中表示主键的字段。

  2. 主键生成策略不是默认策略：如果需要使用除了默认主键生成策略以外的策略，也需要添加`@TableId`注解，并通过`value`属性指定生成策略。

  3. 雪花算法使用场景

     **你需要记住的: 雪花算法生成的数字,需要使用Long 或者 String类型主键!!**

     雪花算法（Snowflake Algorithm）是一种用于生成唯一ID的算法。它由Twitter公司提出，用于解决分布式系统中生成全局唯一ID的需求。在传统的自增ID生成方式中，使用单点数据库生成ID会成为系统的瓶颈，而雪花算法通过在分布式系统中生成唯一ID，避免了单点故障和性能瓶颈的问题。

     雪花算法生成的ID是一个64位的整数，由以下几个部分组成：

     1. 时间戳：41位，精确到毫秒级，可以使用69年。
     2. 节点ID：10位，用于标识分布式系统中的不同节点。
     3. 序列号：12位，表示在同一毫秒内生成的不同ID的序号。

     通过将这三个部分组合在一起，雪花算法可以在分布式系统中生成全局唯一的ID，并保证ID的生成顺序性。

     雪花算法的工作方式如下：

     1. 当前时间戳从某一固定的起始时间开始计算，可以用于计算ID的时间部分。
     2. 节点ID是分布式系统中每个节点的唯一标识，可以通过配置或自动分配的方式获得。
     3. 序列号用于记录在同一毫秒内生成的不同ID的序号，从0开始自增，最多支持4096个ID生成。

     需要注意的是，雪花算法依赖于系统的时钟，需要确保系统时钟的准确性和单调性，否则可能会导致生成的ID不唯一或不符合预期的顺序。雪花算法是一种简单但有效的生成唯一ID的算法，广泛应用于分布式系统中，如微服务架构、分布式数据库、分布式锁等场景，以满足全局唯一标识的需求。

     

  4. @TableField

     描述：字段注解（非主键）

```Java
@TableName("sys_user")
public class User {
    @TableId
    private Long id;
    @TableField("nickname")
    private String name;
    //@TableField("nickname") 中的 "nickname" 参数表示实体类中的 name 字段在数据库表中对应的字段名是 nickname。也就是说，通过这个注解，可以指定实体类中的字段与数据库表中字段的对应关系。
    private Integer age;
    private String email;
}
```

  属性	        类型	    必须指定	默认值	描述

value	String	否	          ""	        数据库字段名
exist	        boolean	否	         true	        是否为数据库表字段

  **MyBatis-Plus会自动开启驼峰命名风格映射!!!**

### MyBatis高级拓展

#### 逻辑删除实现

  **概念:**

  逻辑删除，可以方便地实现对数据库记录的逻辑删除而不是物理删除。逻辑删除是指通过更改记录的状态或添加标记字段来模拟删除操作，从而保留了删除前的数据，便于后续的数据分析和恢复。

  - 物理删除：真实删除，将对应数据从数据库中删除，之后查询不到此条被删除的数据
  - 逻辑删除：假删除，将**对应数据中代表是否被删除字段的状态修改为“被删除状态”，之后在数据库中仍旧能看到此条数据记录**

  **逻辑删除实现:**

  1. 数据库和实体类添加逻辑删除字段

     1. 表添加逻辑删除字段

        可以是一个布尔类型、整数类型或枚举类型。

```SQL
ALTER TABLE USER ADD deleted INT DEFAULT 0 ;  # int 类型 1 逻辑删除 0 未逻辑删除
```

  	·	b. 实体类添加逻辑删除属性

```SQL
@Data
public class User {

   // @TableId
    private Integer id;
    private String name;
    private Integer age;
    private String email;
    
    @TableLogic
    //逻辑删除字段 int mybatis-plus下,默认 逻辑删除值为1 未逻辑删除 1 
    private Integer deleted;
}
```

    2. 指定逻辑删除字段和属性值
       1. 单一指定

```SQL
@Data
public class User {

   // @TableId
    private Integer id;
    private String name;
    private Integer age;
    private String email;
     @TableLogic
    //逻辑删除字段 int mybatis-plus下,默认 逻辑删除值为1 未逻辑删除 1 
    private Integer deleted;
}
```

  				b. 全局指定

```YAML
mybatis-plus:
  global-config:
    db-config:
      logic-delete-field: deleted # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
      logic-delete-value: 1 # 逻辑已删除值(默认为 1)
      logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
```

  3. 演示逻辑删除操作

     > 逻辑删除以后,没有真正的删除语句,删除改为修改语句!

     删除代码:

```Java
//逻辑删除
@Test
public void testQuick5(){
    //逻辑删除
    userMapper.deleteById(5);
}
```

      执行效果:
    
      JDBC Connection [com.alibaba.druid.proxy.jdbc.ConnectionProxyImpl@5871a482] will not be managed by Spring

==> Preparing: UPDATE user SET deleted=1 WHERE id=? AND deleted=0
==> Parameters: 5(Integer)
<==    Updates: 1

    4. 测试查询数据

```Java
@Test
public void testQuick6(){
    //正常查询.默认查询非逻辑删除数据
    userMapper.selectList(null);
}

//SELECT id,name,age,email,deleted FROM user WHERE deleted=0
```

#### 乐观锁实现

##### 悲观锁和乐观锁场景和介绍

  **并发问题场景演示:**

​                                       <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711633888047-7.png" style="zoom: 33%;" />

 **解决思路: **

  **乐观锁和悲观锁**是在并发编程中用于处理并发访问和资源竞争的两种不同的锁机制!!

  悲观锁：  
悲观锁的基本思想是，在整个数据访问过程中，**将共享资源锁定**，以确保其他线程或进程不能同时访问和修改该资源。悲观锁的核心思想是"先保护，再修改"。在悲观锁的应用中，线程在访问共享资源之前会获取到锁，并在整个操作过程中保持锁的状态，阻塞其他线程的访问。只有当前线程完成操作后，才会释放锁，让其他线程继续操作资源。这种锁机制可以确保资源独占性和数据的一致性，但是在高并发环境下，悲观锁的效率相对较低。

  乐观锁：  
乐观锁的基本思想是，认为并发冲突的概率较低，**因此不需要提前加锁，而是在数据更新阶段进行冲突检测和处理**。乐观锁的核心思想是"先修改，后校验"。在乐观锁的应用中，线程在读取共享资源时不会加锁，而是记录特定的版本信息。当线程准备更新资源时，会先检查该资源的版本信息是否与之前读取的版本信息一致，如果一致则执行更新操作，否则说明有其他线程修改了该资源，需要进行相应的冲突处理。乐观锁通过避免加锁操作，提高了系统的并发性能和吞吐量，但是在并发冲突较为频繁的情况下，乐观锁会导致较多的冲突处理和重试操作。

  理解点: 悲观锁和乐观锁是两种解决并发数据问题的思路,不是具体技术!!!、

 **具体技术和方案:**

    1. 乐观锁实现方案和技术：
       - 版本号/时间戳：为数据添加一个版本号或时间戳字段，每次更新数据时，比较当前版本号或时间戳与期望值是否一致，若一致则更新成功，否则表示数据已被修改，需要进行冲突处理。
       - CAS（Compare-and-Swap）：使用原子操作比较当前值与旧值是否一致，若一致则进行更新操作，否则重新尝试。
       - 无锁数据结构：采用无锁数据结构，如无锁队列、无锁哈希表等，通过使用原子操作实现并发安全。
    2. 悲观锁实现方案和技术：
       - 锁机制：使用传统的锁机制，如互斥锁（Mutex Lock）或读写锁（Read-Write Lock）来保证对共享资源的独占访问。
       - 数据库锁：在数据库层面使用行级锁或表级锁来控制并发访问。
       - 信号量（Semaphore）：使用信号量来限制对资源的并发访问。

 **介绍版本号乐观锁技术的实现流程:**

  - **每条数据添加一个版本号字段version**
  - 取出记录时，获取当前 version
  - 更新时，**检查获取版本号是不是数据库当前最新版本号**
  - 如果是[证明没有人修改数据], 执行更新, set 数据更新 , version = version+ 1 
  - 如果 version 不对[证明有人已经修改了]，我们现在的其他记录就是失效数据!就更新失败

  ##### 使用mybatis-plus数据使用乐观锁

   1. 添加版本号更新插件

      ![image-20240328220508029](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-20240328220508029.png)

```Java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
    interceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    return interceptor;
}
```

​			2.乐观锁字段添加@Version注解

​			注意: 数据库也需要添加version字段

```Java
ALTER TABLE USER ADD VERSION INT DEFAULT 1 ;  # int 类型 乐观锁字段
```

  - 支持的数据类型只有:int,Integer,long,Long,Date,Timestamp,LocalDateTime
  - 仅支持 `updateById(id)` 与 `update(entity, wrapper)` 方法

```Java
@Version
private Integer version;
```

​			3. 正常更新使用即可

```Java
//演示乐观锁生效场景
@Test
public void testQuick7(){
    //步骤1: 先查询,在更新 获取version数据
    //同时查询两条,但是version唯一,最后更新的失败
    User user  = userMapper.selectById(5);
    User user1  = userMapper.selectById(5);

    user.setAge(20);
    user1.setAge(30);

    userMapper.updateById(user);
    //乐观锁生效,失败!
    userMapper.updateById(user1);
}
```

#### 防全表更新和删除实现

  针对 update 和 delete 语句 作用: 阻止恶意的全表更新删除

  添加防止全表更新和删除拦截器

```Java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
  MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
  interceptor.addInnerInterceptor(new BlockAttackInnerInterceptor());
  return interceptor;
}
}
```

  测试全部更新或者删除

```Java
@Test
public void testQuick8(){
    User user = new User();
    user.setName("custom_name");
    user.setEmail("xxx@mail.com");
    //Caused by: com.baomidou.mybatisplus.core.exceptions.MybatisPlusException: Prohibition of table update operation
    //全局更新,报错
    userService.saveOrUpdate(user,null);
}
```

### Mybatisx插件逆向工程

  MyBatis-Plus为我们提供了强大的mapper和service模板，能够大大的提高开发效率

  但是在真正开发过程中，MyBatis-Plus并不能为我们解决所有问题，例如一些复杂的SQL，多表联查，我们就需要自己去编写代码和SQL语句，我们该如何快速的解决这个问题呢，这个时候可以使用MyBatisX插件

  MyBatisX一款基于 IDEA 的快速开发插件，为效率而生。

  <img src="D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711635250859-10.png" style="zoom: 33%;" />

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711635250859-11.png)

  ![](D:\2024\Notes\Typora\Study\Java_ssm\ssm.assets\image-1711635250859-12.png)



































































































