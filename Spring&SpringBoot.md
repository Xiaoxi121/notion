# Spring&&SpringBoot

## Spring常见面试题

### Spring基础

#### 什么是Spring框架

Spring 支持 IoC（Inversion of Control:控制反转） 和 AOP(Aspect-Oriented Programming:面向切面编程)、可以很方便地对数据库进行访问、可以很方便地集成第三方组件（电子邮件，任务，调度，缓存等等）、对单元测试支持比较好、支持 RESTful Java 应用程序的开发。

Spring框架核心特性包括：

- IoC容器：Spring通过控制反转实现了对象的创建和对象间的依赖关系管理。开发者只需要定义好Bean及其依赖关系，Spring容器负责创建和组装这些对象。
- AOP：面向切面编程，允许开发者定义横切关注点，例如事务管理、安全控制等，独立于业务逻辑的代码。通过AOP，可以将这些关注点模块化，提高代码的可维护性和可重用性。
- 事务管理：Spring提供了一致的事务管理接口，支持声明式和编程式事务。开发者可以轻松地进行事务管理，而无需关心具体的事务API。
- MVC框架：Spring MVC是一个基于Servlet API构建的Web框架，采用了模型-视图-控制器（MVC）架构。它支持灵活的URL到页面控制器的映射，以及多种视图技术。

在 Spring 框架中，IOC 和 AOP 结合使用，可以更好地实现代码的模块化和分层管理。例如：

- **通过 IOC 容器管理对象的依赖关系，然后通过 AOP 将横切关注点统一切入到需要的业务逻辑中。**
- **使用 IOC 容器管理 Service 层和 DAO 层的依赖关系，然后通过 AOP 在 Service 层实现事务管理、日志记录等横切功能，使得业务逻辑更加清晰和可维护**

#### Spring中包含的模块有哪些

- Core Container

  Spring 框架的核心模块，也可以说是基础模块，主要提供 IoC 依赖注入功能的支持

- AOP

- Data Access/Integration

- Spring Web

- Messaging

- Spring Test

#### Spring容器里存的是什么？

**在Spring容器中，存储的主要是Bean对象。**
Bean是Spring框架中的基本组件，用于表示应用程序中的各种对象。当应用程序启动时，Spring容器会根据配置文件或注解的方式创建和管理这些Bean对象。Spring容器会负责创建、初始化、注入依赖以及销毁Bean对象

#### Spring，Spring MVC，SpringBoot之间有什么关系

- **Spring 包含了多个功能模块（上面刚刚提到过），**其中最重要的是 Spring-Core（主要提供 IoC 依赖注入功能的支持） 模块， Spring 中的其他模块（比如 Spring MVC）的功能实现基本都需要依赖于该模块。
- **Spring MVC 是 Spring 中的一个很重要的模块**，主要赋予 Spring 快速构建 MVC 架构的 Web 程序的能力**。MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，**其核心思想是通过将业务逻辑、数据、显示分离来组织代码。
- Spring Boot 只是简化了配置，如果你需要构建 MVC 架构的 Web 程序，你还是需要使用 Spring MVC 作为 MVC 框架，只是说 **Spring Boot 帮你简化了 Spring MVC 的很多配置**，真正做到开箱即用

#### 依赖倒置，依赖注入，控制反转分别是什么？

- 控制反转：“控制”指的是对程序执行流程的控制，而“反转”指的是在没有使用框架之前，程序员自己控制整个程序的执行。在使用框架之后，整个程序的执行流程通过框架来控制。流程的控制权从程序员“反转”给了框架。
- 依赖注入：依赖注入和控制反转恰恰相反，它是一种具体的编码技巧。我们不通过 new 的方式在类内部创建依赖类的对象，而是将依赖的类对象在外部创建好之后，通过构造函数、函数参数等方式传递（或注入）给类来使用。
- 依赖倒置：这条原则跟控制反转有点类似，主要用来指导框架层面的设计。高层模块不依赖低层模块，它们共同依赖同一个抽象。抽象不要依赖具体实现细节，具体实现细节依赖抽象。

#### 在Spring中，在bean加载/销毁前后，如果想实现某些逻辑，可以怎么做

在Spring框架中，如果你希望在Bean加载（即实例化、属性赋值、初始化等过程完成后）或销毁前后执行某些逻辑，你可以使用Spring的生命周期回调接口或注解。这些接口和注解允许你定义在Bean生命周期的关键点执行的代码。

1. 使用init-method和destroy-method

​		在XML配置中，你可以通过init-method和destroy-method属性来指定Bean初始化后和销毁前需要调用的方法。

```java
<bean id="myBean" class="com.example.MyBeanClass"
init-method="init" destroy-method="destroy"/>
```

​		然后，在你的Bean类中实现这些方法：

```java
public class MyBeanClass {
public void init() {
// 初始化逻辑
}
public void destroy() {
// 销毁逻辑
}
}
```

	2.  实现InitializingBean和DisposableBean接口
		你的Bean类可以实现org.springframework.beans.factory.InitializingBean和org.springframework.beans.factory.DisposableBean接口，并分别实现afterPropertiesSet和destroy方法。

```java
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
public class MyBeanClass implements InitializingBean, DisposableBean {
@Override
public void afterPropertiesSet() throws Exception {
// 初始化逻辑
}
@Override
public void destroy() throws Exception {
// 销毁逻辑
}
}
```

3. 使用@PostConstruct和@PreDestroy注解

```java
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
public class MyBeanClass {
@PostConstruct
public void init() {
// 初始化逻辑
}
@PreDestroy
public void destroy() {
// 销毁逻辑
}
}
```

4. 使用@Bean注解的initMethod和destroyMethod属性

   在基于Java的配置中，你还可以在@Bean注解中指定initMethod和destroyMethod属性。

```java
@Configuration
public class AppConfig {
@Bean(initMethod = "init", destroyMethod = "destroy")
public MyBeanClass myBean() {
return new MyBeanClass();
}
}
```

#### Spring给我们提供了很多扩展点，这些有了解吗？

Spring框架提供了许多扩展点，使得开发者可以根据需求定制和扩展Spring的功能。以下是一些常用的扩展点：

- BeanFactoryPostProcessor：**允许在Spring容器实例化bean之前修改bean的定义**。常用于修改bean属性或改变bean的作用域。
- BeanPostProcessor：**可以在bean实例化、配置以及初始化之后对其进行额外处理。**常用于代理bean、修改bean属性等。
- PropertySource：用于定义不同的属性源，如文件、数据库等，以便在Spring应用中使用。
- ImportSelector和ImportBeanDefinitionRegistrar：用于根据条件动态注册bean定义，实现配置类的模块化。
- **Spring MVC中的HandlerInterceptor：用于拦截处理请求，**可以在请求处理前、处理中和处理后执行特定逻辑。
- **Spring MVC中的ControllerAdvice：用于全局处理控制器的异常、**数据绑定和数据校验。
- **Spring Boot的自动配置：**通过创建自定义的自动配置类，可以实现对框架和第三方库的自动配置。
- **自定义注解：**创建自定义注解，用于实现特定功能或约定，如权限控制、日志记录等

### Spring IoC

#### 什么是IoC

**IoC（Inversion of Control:控制反转） 是一种设计思想，将原本在程序中手动创建对象的控制权，交由 Spring 框架来管理**

- 控制：指的是对象创建（实例化、管理）的权力
- 反转：控制权交给外部环境（Spring 框架、IoC 容器）

**将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。**

所谓控制就是对象的创建、初始化、销毁。

- 创建对象：原来是 new 一个，现在是由 Spring 容器创建。
- 初始化对象：原来是对象自己通过构造器或者 setter 方法给依赖的对象赋值，现在是由 Spring 容器自动注入。
- 销毁对象：原来是直接给对象赋值 null 或做一些销毁操作，现在是 Spring 容器管理生命周期负责销毁对象。

#### IOC好处

- 对象之间的**耦合度或者说依赖程度降低；**
- **资源变的容易管理**；比如你用 Spring 容器提供的话很容易就可以实现一个单例。

#### IoC和DI有区别吗？

**IoC 最常见以及最合理的实现方式叫做依赖注入**（Dependency Injection，简称 DI）。

#### IOC实现机制

- 反射：**Spring IOC容器利用Java的反射机制动态地加载类、创建对象实例及调用对象方法**，反射允许在运行时检查类、方法、属性等信息，从而实现灵活的对象实例化和管理。
- 依赖注入：**IOC的核心概念是依赖注入，即容器负责管理应用程序组件之间的依赖关系。Spring通过构造函数注入、属性注入或方法注入，**将组件之间的依赖关系描述在配置文件中或使用注解。
- 设计模式 - 工厂模式：Spring IOC容器通常采用工厂模式来管理对象的创建和生命周期。容**器作为工厂负责实例化Bean并管理它们的生命周期，将Bean的实例化过程交给容器来管理。**
- 容器实现：Spring IOC容器是实现IOC的核心，通常使用BeanFactory或ApplicationContext来管理Bean。BeanFactory是IOC容器的基本形式，提供基本的IOC功能；ApplicationContext是BeanFactory的扩展，并提供更多企业级功能

#### 什么是Spring Bean

**Bean 代指的就是那些被 IoC 容器所管理的对象**。我们需要告诉 IoC 容器帮助我们管理哪些对象，这个是**通过配置元数据来定义的**。配置元数据可以是 **XML 文件、注解或者 Java 配置类。**

#### 将一个类声明为Bean的注解有哪些？

- **@Component：**通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用@Component 注解标注。
- **@Repository :** 对应持久层即 Dao 层，主要用于数据库相关操作。
- **@Service :** 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- **@Controller :** 对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面。

#### @Component和@Bean的区别

- @Component 注解作用于**类**，而@Bean注解作用于**方法。**

- @Component通常**是通过类路径扫描来自动侦测以及自动装配**到 Spring 容器中（我们可以使用 @ComponentScan 注解定义要扫描的路径从中找出标识了需要装配的类自动装配到 Spring 的 bean 容器中）。

  @Bean 注解通常是我们**在标有该注解的方法中定义产生这个 bean,**@Bean告诉了 Spring 这是某个类的实例，当我需要用它的时候还给我。

- @Bean 注解比 @Component 注解的**自定义性更强**，而且很多地方我们只能通过 @Bean 注解来注册 bean。比如当我们引用第三方库中的类需要装配到 Spring容器时，则只能通过 @Bean来实现。

#### 注入Bean的注解有哪些

- Spring内置@Autowired
- JDK内置@Resource@Inject

#### @Autowired和@Resource区别？

- @Autowired
  - @Autowired属于Spring内置注解，默认注入方式为`byType`，即按照接口类型匹配并注入Bean（接口的实现类）。
  - 因此，当一个接口有多个实现类的时候，Spring找到多个满足的选项，无法正确注入。这个时候注入方式变为`byName`，即按照名称进行匹配，这个名称通常就是类名。
    - 建议通过 @Qualifier 注解来显式指定名称而不是依赖变量的名称。
  - @Autowired 支持在构造函数、方法、字段和参数上使用
- @Resource
  - @Resource属于 JDK 提供的注解，默认注入方式为 `byName`。如果无法通过名称匹配到对应的 Bean 的话，注入方式会变为`byType`。
  - 有两个属性：name和type，指定哪个就决定哪个注入方式
  - @Resource 主要用于字段和方法上的注入，不支持在构造函数或参数上使用。

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

#### Bean的作用域有哪些？

- **singleton : IoC 容器中只有唯一的 bean 实例。**Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。
- **prototype : 每次获取都会创建一个新的 bean 实例。**也就是说，连续 getBean() 两次，得到的是不同的 Bean 实例。
- request （仅 Web 应用可用）: **每一次 HTTP 请求都会产生一个新的 bean**（请求 bean），该 bean 仅在当前 HTTP request 内有效。
- session （仅 Web 应用可用） : **每一次来自新 session 的 HTTP 请求都会**产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。
- application/global-session （仅 Web 应用可用）：**每个 Web 应用在启动时创建一个 Bean（**应用 Bean），该 bean 仅在当前应用启动时间内有效。
- websocket （仅 Web 应用可用）：**每一次 WebSocket 会话产生一个新的** bean。

如何配置bean的作用域？

- xml

  ```xml
  <bean id="..." class="..." scope="singleton"></bean>
  ```

- 注解方式

  ```java
  @Bean
  @Scope(value = ConfigurableBeanFactory.SCOPE_PROTOTYPE)
  public Person personPrototype() {
      return new Person();
  }
  ```

#### Bean是线程安全的吗

Spring 框架中的 Bean 是否线程安全，取决于其作用域和状态。

- prototype 作用域下，每次获取都会创建一个新的 bean 实例，**不存在资源竞争问题，所以不存在线程安全问题。**

- singleton 作用域下，IoC 容器中只有唯一的 bean 实例，可能会存在资源竞争问题**（取决于 Bean 是否有状态）。**

  - 如果这个 bean 是有状态的话，那**就存在线程安全问题**（有状态 Bean 是指**包含可变的成员变量的对象**）。

    对于有状态单例Bean的线程安全问题，常见的三种解决方式：

    1. **避免可变成员变量，**尽量将Bean设置为无状态
    2. **使用ThreadLocal，**将可变成员变量保存在ThreadLocal中，确保线程独立
    3. **使用同步机制**，利用synchronized或者ReentrantLock来进行同步控制，确保线程安全。

#### Bean的生命周期?

1. **创建Bean的实例**

   Bean容器首先会找到配置文件中Bean的定义，然后使用反射创建Bean的实例。

2. **Bean属性复制/填充**

   给Bean设置相关属性或者依赖，@Autowired等注解注入对象，@Value注入值，setter方法或者构造函数方法注入依赖和值，@Resource注入各种资源。

3. **Bean初始化**

   初始化这一步涉及到的步骤比较多，包含 Aware 接口的依赖注入、BeanPostProcessor 在初始化前后的处理以及 InitializingBean 和 init-method 的初始化操作。

   1. 检查Aware的相关接口并设置相关依赖
   2. BeanPostProcessor前置处理
   3. 是否实现InitializingBean接口
   4. 是否配置自定义的init-method
   5. BeanPostProcessor后置处理

4. **销毁Bean**

   销毁这一步会注册相关销毁回调接口，最后通过DisposableBean 和 destory-method 进行销毁。

   1. 注册Desturction相关回调接口
   2. 是否实现DisposableBean接口
   3. 是否配置自定义的destroy-moethod

#### Bean注入和xml注入最终得到了相同的效果，它们在底层是怎样做的

##### XML 注入

使用 XML 文件进行 Bean 注入时，Spring 在启动时会读取 XML 配置文件，以下是其底层步骤：

- **Bean 定义解析：**Spring 容器通过 XmlBeanDefinitionReader 类解析 XML 配置文件，读取其中的 <bean> 标签以获取 Bean 的定义信息。
- **注册 Bean 定义：**解析后的 Bean 信息被注册到 BeanDefinitionRegistry（如 DefaultListableBeanFactory）中，包括 Bean 的类、作用域、依赖关系、初始化和销毁方法等。
- **实例化和依赖注入：**当应用程序请求某个 Bean 时，Spring 容器会根据已经注册的 Bean 定义：
  - 首先，**使用反射机制**创建该 Bean 的实例。
  - 然后，**根据 Bean 定义中的配置，通过 setter 方法、构造函数或方法注入所需的依赖 Bean。**

##### 注解注入

使用注解进行 Bean 注入时，Spring 的处理过程如下：

- **类路径扫描：当** Spring 容器启动时，它首先会进行类路径扫描，查找带有特定注解（如 @Component、@Service、@Repository 和 @Controller）的类。
- **注册 Bean 定义**：找到的类会被注册到 BeanDefinitionRegistry 中，Spring 容器将为其生成 Bean 定义信息。这通常通过 AnnotatedBeanDefinitionReader 类来实现。
- **依赖注入**：与 XML 注入类似，Spring 在实例化 Bean 时，也会检查字段上是否有 @Autowired、@Inject 或 @Resource 注解。如果有，Spring 会根据注解的信息进行依赖注入。

尽管使用的方式不同，但 XML 注入和注解注入在底层的实现机制是相似的，主要体现在以下几个方面：

- **BeanDefinition：**无论是 XML 还是注解，最终都会生成 BeanDefinition 对象，并存储在同一个 BeanDefinitionRegistry 中。
- **后处理器：**
  - Spring 提供了多个 Bean 后处理器（如 AutowiredAnnotationBeanPostProcessor），用于处理注解（如 @Autowired）的依赖注入。
  - 对于 XML，Spring 也有相应的后处理器来处理 XML 配置的依赖注入。
- **依赖查找：**在依赖注入时，Spring 容器会通过 ApplicationContext 中的 BeanFactory 方法来查找和注入依赖，无论是通过 XML 还是注解，都会调用类似的查找方法

### Spring AOP

#### 什么是AOP

在面向切面编程中，核心业务功能和周边功能是分别独立进行开发，两者不是耦合的，然后把切面功能和核心业务功能 "编织" 在一起，这就叫AOP。

AOP(Aspect-Oriented Programming:**面向切面编程**)能够**将那些与业务无关**，**却为业务模块**所共同**调用的**逻辑或责任（例如事务处理、日志管理、权限控制等）**封装起来**，便于**减少系统的重复代码**，**降低**模块间的**耦合度**，并有利于未来的可拓展性和可维护性

#### AOP应用场景

1. 日志记录：自定义日志记录注解，利用 AOP，一行代码即可实现日志记录。
2. 性能统计：利用 AOP 在目标方法的执行前后统计方法的执行时间，方便优化和分析。
3. 事务管理：@Transactional 注解可以让 Spring 为我们进行事务管理比如回滚异常操作，免去了重复的事务管理逻辑。@Transactional注解就是基于 AOP 实现的。
4. 权限控制：利用 AOP 在目标方法执行前判断用户是否具备所需要的权限，如果具备，就执行目标方法，否则就不执行。例如，SpringSecurity 利用@PreAuthorize 注解一行代码即可自定义权限校验。
5. 接口限流：利用 AOP 在目标方法执行前通过具体的限流算法和实现对请求进行限流处理。
6. 缓存管理：利用 AOP 在目标方法执行前后进行缓存的读取和更新。
   ……

#### AOP实现机制

Spring AOP的实现依赖于动态代理技术。动态代理是在运行时动态生成代理对象，而不是在编译时。它允许开发者在运行时指定要代理的接口和行为，从而实现在不修改源码的情况下增强方法的功能。

Spring AOP支持两种动态代理：

- 基**于接口的代理（JDK动态代理）：** 这种类型的代理要求目标对象必须实现至少一个接口。**Java动态代理会创建一个实现了相同接口的代理类，然后在运行时动态生成该类的实例。这**种代理的实现核心是java.lang.reflect.Proxy类和java.lang.reflect.InvocationHandler接口。每一个动态代理类都必须实现InvocationHandler接口，并且每个代理类的实例都关联到一个handler。当通过代理对象调用一个方法时，这个方法的调用会被转发为由InvocationHandler接口的invoke()方法来进行调用。
- **基于类的代理（CGLIB动态代理）：** CGLIB（Code Generation Library）是一个强大的高性能的代码生成库，它可以在运行时动态生成一个目标类的子类。**CGLIB代理不需要目标类实现接口，而是通过继承的方式创建代理类。因此，如果目标对象没有实现任何接口，可以使用CGLIB来创建动态代理**

**Spring AOP 是基于动态代理的**

- 如果要代理的对象，**实现了某个接口**，那么 Spring AOP 会使用 **JDK Proxy，去创建代理对象**
- 而对**于没有实现接口**的对象，Spring AOP 会使用 **Cglib 生成一个被代理对象的子类来**作为代理

**专业术语**

|      术语       |                             含义                             |
| :-------------: | :----------------------------------------------------------: |
|   目标target    |                         被通知的对象                         |
|    代理proxy    |             向目标对象应用通知之后创建的代理对象             |
| 连接点joinpoint |         目标对象的所属类中，定义的所有方法均为连接点         |
| 切入点pointcut  | 被切面拦截 / 增强的连接点（切入点一定是连接点，连接点不一定是切入点） |
|   通知advice    | 增强的逻辑 / 代码，也即拦截到目标对象的连接点之后要做的事情  |
|   切面aspect    |                切入点(Pointcut)+通知(Advice)                 |
|   织入weaving   |       将通知应用到目标对象，进而生成代理对象的过程动作       |

#### 动态代理和静态代理的区别

代理是一种常用的设计模式，目的是：**为其他对象提供一个代理以控制对某个对象的访问，将两个类的关系解耦。代理类和委托类都要实现相同的接口，因为代理真正调用的是委托类的方法。**
区别：

- 静态代理：由程序员创建或者是由特定工具创建，在代码编译时就确定了被代理的类是一个静态代理。静态代理通常只代理一个类；
- 动态代理：在代码运行期间，运用反射机制动态创建生成。动态代理代理的是一个接口下的多个实现类。

#### Spring Aop和Aspect Aop的区别

- **Spring AOP 属于运行时增强，而 AspectJ 是编译时增强。** Spring AOP **基于代理(Proxying)**，而 AspectJ **基于字节码操作(Bytecode** Manipulation)。
- Spring AOP 集成了 AspectJ ，AspectJ 应该算的上是 Java 生态系统中最完整的 AOP 框架了。
- 如果我们的切面比较少，那么两者性能差异不大。但是，当切面太多的话，最好选择 AspectJ ，它比 Spring AOP 快很多

#### AOP常见的通知类型有哪些

- Before（前置通知）：目标对象的方法调用之前触发
- After （后置通知）：目标对象的方法调用之后触发
- AfterReturning（返回通知）：目标对象的方法调用完成，在返回结果值之后触发
- AfterThrowing（异常通知）：目标对象的方法运行中抛出 / 触发异常后触发。AfterReturning 和 AfterThrowing 两者互斥。如果方法调用成功无异常，则会有返回值；如果方法抛出了异常，则不会有返回值。
- Around （环绕通知）：编程式控制目标对象的方法调用。环绕通知是所有通知类型中可操作范围最大的一种，因为它可以直接拿到目标对象，以及要执行的方法，所以**环绕通知可以任意的在目标对象的方法调用前后搞事，甚至不调用目标对象的方法**

#### 多个切面的执行顺序如何控制？

1. 通常使用@Order注解直接定义切面顺序
2. 实现Ordered接口重写getOrder方法

#### AOP实现有哪些注解？

常用的注解包括：

1. @Aspect：用于定义切面，标注在切面类上。
2. @Pointcut：定义切点，标注在方法上，用于指定连接点。
3. @Before：在方法执行之前执行通知。
4. @After：在方法执行之后执行通知。
5. @Around：在方法执行前后都执行通知。
6. @AfterReturning：在方法执行后返回结果后执行通知。
7. @AfterThrowing：在方法抛出异常后执行通知。
8. @Advice：通用的通知类型，可以替代@Before、@After等

### Spring MVC

#### 什么是Spring MVC

MVC 是模型(Model)、视图(View)、控制器(Controller)的简写，其核心思想是通过将业务逻辑、数据、显示分离来组织代码。

MVC 是一种设计模式，Spring MVC 是一款很优秀的 MVC 框架。Spring MVC 可以帮助我们进行更简洁的 Web 层的开发，并且它天生与 Spring 框架集成。Spring MVC 下我们一般把**后端项目分为 Service 层（处理业务）、Dao 层（数据库操作）、Entity 层（实体类）、Controller 层(控制层，返回数据给前台页面)**

流程步骤：

1. 用户通过View 页面向服务端提出请求，可以是表单请求、超链接请求、AJAX 请求等；
2. 服务端 Controller 控制器接收到请求后对请求进行解析，找到相应的Model，对用户请求进行处理Model 处理；
3. 将处理结果再交给 Controller（控制器其实只是起到了承上启下的作用）；
4. 根据处理结果找到要作为向客户端发回的响应View 页面，页面经渲染后发送给客户端

#### Sping MVC的核心组件有哪些？

1. DispatcherServlet：核心的中央处理器，负责接收请求、分发，并给予客户端响应。
2. **HandlerMapping：处理器映射器**，根据 URL 去匹配查找能处理的 Handler ，并会将请求涉及到的拦截器和 Handler 一起封装。
3. **HandlerAdapter：处理器适配器，**根据 HandlerMapping 找到的 Handler ，适配执行对应的 Handler；
4. **Handler：请求处理器，处理实际请求的处理器**。
5. **ViewResolver：视图解析器，根据 Handler 返回的逻辑视图 / 视图，解析并渲染真正的视图，并传递给 DispatcherServlet 响应客户端**

#### Handlermapping 和 handleradapter有了解吗？

HandlerMapping：

- 作用：HandlerMapping负责将请求映射到处理器（Controller）。
- 功能：根据请求的URL、请求参数等信息，找到处理请求的 Controller。
- 类型：Spring提供了多种HandlerMapping实现，如BeanNameUrlHandlerMapping、RequestMappingHandlerMapping等。
- 工作流程：根据请求信息确定要请求的处理器(Controller)。HandlerMapping可以根据URL、请求参数等规则确定对应的处理器。

HandlerAdapter：

- 作用：HandlerAdapter负责调用处理器(Controller)来处理请求。
- 功能：处理器(Controller)可能有不同的接口类型（Controller接口、HttpRequestHandler接口等），HandlerAdapter根据处理器的类型来选择合适的方法来调用处理器。
- 类型：Spring提供了多个HandlerAdapter实现，用于适配不同类型的处理器。
- 工作流程：根据处理器的接口类型，选择相应的HandlerAdapter来调用处理器。

工作流程：

1. 当客户端发送请求时，HandlerMapping根据请求信息找到对应的处理器(Controller)。
2. HandlerAdapter根据处理器的类型选择合适的方法来调用处理器。
3. 处理器执行相应的业务逻辑，生成ModelAndView。
4. HandlerAdapter将处理器的执行结果包装成ModelAndView。
5. 视图解析器根据ModelAndView找到对应的视图进行渲染。
6. 将渲染后的视图返回给客户端。

HandlerMapping和HandlerAdapter协同工作，通过将请求映射到处理器，并调用处理器来处理请求，实现了请求处理的流程。它们的灵活性使得在Spring MVC中可以支持多种处理器和处理方式，提高了框架的扩展性和适应性。

#### Spring MVC工作原理了解吗？

![image-20240929092746663](D:\2024\Notes\Typora\八股\Spring&SpringBoot.assets\image-20240929092746663.png)

1. 客户端（浏览器）发送请求， DispatcherServlet拦截请求。

2. DispatcherServlet 根据请求信息调用 HandlerMapping 。

   HandlerMapping 根据 URL 去匹配查找能处理的 Handler（也就是我们平常说的 Controller 控制器） ，并会将请求涉及到的拦截器和 Handler 一起封装。

3. DispatcherServlet 调用 HandlerAdapter适配器执行 Handler 。

4. Handler 完成对用户请求的处理后，会返回一个 ModelAndView 对象给DispatcherServlet，ModelAndView 顾名思义，包含了数据模型以及相应的视图的信息。Model 是返回的数据对象，View 是个逻辑上的 View。

5. ViewResolver 会根据逻辑 View 查找实际的 View。

6. DispaterServlet 把返回的 Model 传给 View（视图渲染）。
   把 View 返回给请求者（浏览器）

#### 统一异常处理怎么做？

@ControllerAdvice+@ExceptionHandler

- 这种异常处理方式下，**会给所有或者指定的 Controller 织入异常处理的逻辑（AOP），当 Controller 中的方法抛出异常的时候，由被@ExceptionHandler 注解修饰的方法进行处理。**
- ExceptionHandlerMethodResolver 中 getMappedMethod 方法决定了异常具体被哪个被 @ExceptionHandler 注解修饰的方法处理异常。
- getMappedMethod()会首先找到可以匹配处理异常的所有方法信息，然后对其进行从小到大的排序，最后取最小的那一个匹配的方法(即匹配度最高的那个)。

### Spring设计模式

1. 工厂设计模式 : Spring 使用工厂模式通过 BeanFactory、ApplicationContext 创建 bean 对象。

2. 代理设计模式 : Spring AOP 功能的实现。

3. **单例设计模式 : Spring 中的 Bean 默认都是单例的。**

   1. 每个Bean的实例只会被创建一次，**并且会被存储在Spring容器的缓存中，以便在后续的请求中重复使用。**这种单例模式可以提高应用程序的性能和内存效率。
   2. 但是，**Spring也支持将Bean设置为多例模式，即每次请求都会创建一个新的Bean实**例。要将Bean设置为多例模式，可以在Bean定义中通过设置scope属性为"prototype"来实现。
   3. 需要注意的是，虽然Spring的默认行为是将Bean设置为单例模式，但在一些情况下，**使用多例模式是更为合适的，例如在创建状态不可变的Bean或有状态Bean时**。此外，需要注意的是，**如果Bean单例是有状态的，那么在使用时需要考虑线程安全性问题。**

   **Bean的单例和非单例，生命周期是否一样?**
   不一样的，Spring Bean 的生命周期完全由 IoC 容器控制。Spring 只帮我们管理单例模式 Bean 的完整生命周期，对于 prototype 的 Bean，Spring 在创建好交给使用者之后，则不会再管理后续的生命周期。

4. 模板方法模式 : Spring 中 jdbcTemplate、hibernateTemplate 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。

5. **包装器设计模式 : 我们的项目需要连接多个数据库**，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。

6. **观察者模式: Spring 事件驱动模型就是观察者模式很经典的一个应用。**

7. **适配器模式 :Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配Controller**

IoC 是一个原则，而不是一个模式，以下模式（但不限于）实现了 IoC 原则。DI(Dependency Inject,依赖注入)是实现控制反转的一种设计模式，依赖注入就是将实例变量传入到一个对象中去。

#### 1. 工厂设计模式

Spring 使用工厂模式可以通过 BeanFactory 或 ApplicationContext 创建 bean 对象

1. BeanFactory

   **延迟至使用到某个bean的时候才会注入，占用更少内存，启动速度更快**。仅仅提供了最基本的依赖注入支持。

2. ApplicationContext

   **容器启动的时候一次性创建所有Bean，ApplicationContext扩展了BeanFactory。**

   三个实现类：

   1. ClassPathXmlApplication：把**上下文文件当成类路径资源**
   2. FileSystemXmlApplication：从**文件系统中的xml文件**载入上下文定义信息
   3. XmlWebApplicationContext：从**web系统中的xml文件**中载入上下文定义信息

```java
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.FileSystemXmlApplicationContext;

public class App {
  public static void main(String[] args) {
    ApplicationContext context = new FileSystemXmlApplicationContext(
        "C:/work/IOC Containers/springframework.applicationcontext/src/main/resources/bean-factory-config.xml");

    HelloApplicationContext obj = (HelloApplicationContext) context.getBean("helloApplicationContext");
    obj.getMsg();
  }
}
```

#### 2. 单例设计模式

一类对象只有一个实例

好处：

1. **对于频繁使用的对象，可以省略创建对象所花费的时间**
2. 由于 **new 操作的次数减少**，因而**对系统内存的使用频率也会降低，**这将减轻 GC 压力，缩短 GC 停顿时间

Spring 中 bean 的默认作用域就是 singleton(单例)的。 除了 singleton 作用域，Spring 中 bean 还有下面几种作用域：

1. **singleton : IoC 容器中只有唯一的 bean 实例。**Spring 中的 bean 默认都是单例的，是对单例设计模式的应用。
2. **prototype : 每次获取都会创建一个新的 bean 实例。**也就是说，连续 getBean() 两次，得到的是不同的 Bean 实例。
3. request （仅 Web 应用可用）: **每一次 HTTP 请求都会产生一个新的 bean**（请求 bean），该 bean 仅在当前 HTTP request 内有效。
4. session （仅 Web 应用可用） : **每一次来自新 session 的 HTTP 请求都会**产生一个新的 bean（会话 bean），该 bean 仅在当前 HTTP session 内有效。
5. application/global-session （仅 Web 应用可用）：**每个 Web 应用在启动时创建一个 Bean（**应用 Bean），该 bean 仅在当前应用启动时间内有效。
6. websocket （仅 Web 应用可用）：**每一次 WebSocket 会话产生一个新的** bean。

Spring **通过 ConcurrentHashMap 实现单例注册表**的特殊方式实现单例模式。

```java
// 通过 ConcurrentHashMap（线程安全） 实现单例注册表
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(64);

public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) {
        Assert.notNull(beanName, "'beanName' must not be null");
        synchronized (this.singletonObjects) {
            // 检查缓存中是否存在实例
            Object singletonObject = this.singletonObjects.get(beanName);
            if (singletonObject == null) {
                //...省略了很多代码
                try {
                    singletonObject = singletonFactory.getObject();
                }
                //...省略了很多代码
                // 如果实例对象在不存在，我们注册到单例注册表中。
                addSingleton(beanName, singletonObject);
            }
            return (singletonObject != NULL_OBJECT ? singletonObject : null);
        }
    }
    //将对象添加到单例注册表
    protected void addSingleton(String beanName, Object singletonObject) {
            synchronized (this.singletonObjects) {
                this.singletonObjects.put(beanName, (singletonObject != null ? singletonObject : NULL_OBJECT));

            }
        }
}
```

单例 Bean 存在线程问题，主要是因为当多个线程操作同一个对象的时候是存在资源竞争的。

- 在 Bean 中尽量避免定义可变的成员变量。
- 在类中定义一个 ThreadLocal 成员变量，将需要的可变成员变量保存在 ThreadLocal 中（推荐的一种方式）。

在Spring配置文件中，可以通过标签的scope属性来指定Bean的作用域。例如：

```java
<bean id="myBean" class="com.example.MyBeanClass" scope="singleton"/>
```

在Spring Boot或基于Java的配置中，可以通过@Scope注解来指定Bean的作用域。例如：

```java
@Bean
@Scope("prototype")
public MyBeanClass myBean() {
return new MyBeanClass();
}
```

#### 3. 代理设计模式

Spring AOP就是基于动态代理的

#### 4. 模板方法

模板方法模式是一种行为设计模式，**它定义一个操作中的算法的骨架，而将一些步骤延迟到子类中**。 模板方法使**得子类可以不改变一个算法的结构即可重定义该算法的某些特定步骤的实现方式**。

```java
public abstract class Template {
    //这是我们的模板方法
    public final void TemplateMethod(){
        PrimitiveOperation1();
        PrimitiveOperation2();
        PrimitiveOperation3();
    }

    protected void  PrimitiveOperation1(){
        //当前类实现
    }

    //被子类实现的方法
    protected abstract void PrimitiveOperation2();
    protected abstract void PrimitiveOperation3();

}
public class TemplateImpl extends Template {

    @Override
    public void PrimitiveOperation2() {
        //当前类实现
    }

    @Override
    public void PrimitiveOperation3() {
        //当前类实现
    }
}
```

Spring 中 JdbcTemplate、HibernateTemplate 等以 Template 结尾的对数据库操作的类，它们就使用到了模板模式。一般情况下，我们都是使用继承的方式来实现模板模式，但是 Spring 并没有使用这种方式，而是使用 Callback 模式与模板方法模式配合，既达到了代码复用的效果，同时增加了灵活性

#### 5. 观察者模式

观察者模式是一种对象行为型模式。它表示的是**一种对象与对象之间具有依赖关系，**当一个对象发生改变的时候，依赖这个对象的所有对象也会做出反应。**Spring 事件驱动模型就是观察者模式很经典的一个应用。**

##### Spring事件驱动模型中的三种角色

###### 事件角色

**ApplicationEvent (org.springframework.context包下)充当事件的角色,**这是一个抽象类，它继承了java.util.EventObject并实现了 java.io.Serializable接口。

Spring 中默认存在以下事件，他们都是对 ApplicationContextEvent 的实现(继承自ApplicationContextEvent)：

- ContextStartedEvent：ApplicationContext 启动后触发的事件;
- ContextStoppedEvent：ApplicationContext 停止后触发的事件;
- ContextRefreshedEvent：ApplicationContext 初始化或刷新完成后触发的事件;
- ContextClosedEvent：ApplicationContext 关闭后触发的事件

###### 事件监听者角色

**ApplicationListener 充当了事件监听者角色，**它是一个接口，里面只定义了一个 onApplicationEvent()方法来处理ApplicationEvent。所以，在 Spring 中我们只要实现 ApplicationListener 接口的 onApplicationEvent() 方法即可完成监听事件

```java
package org.springframework.context;
import java.util.EventListener;
@FunctionalInterface
public interface ApplicationListener<E extends ApplicationEvent> extends EventListener {
    void onApplicationEvent(E var1);
}
```

###### **事件发布者角色**

**ApplicationEventPublisher 充当了事件的发布者，它也是一个接口。**

```java
@FunctionalInterface
public interface ApplicationEventPublisher {
    default void publishEvent(ApplicationEvent event) {
        this.publishEvent((Object)event);
    }

    void publishEvent(Object var1);
}
```

##### Spring事件流程总结

1. 定义一个事件: 实现一个继承自 ApplicationEvent，并且写相应的构造函数；
2. 定义一个事件监听者：实现 ApplicationListener 接口，重写 onApplicationEvent() 方法；
3. 使用事件发布者发布消息: 可以通过 ApplicationEventPublisher 的 publishEvent() 方法发布消息。

```java
// 定义一个事件,继承自ApplicationEvent并且写相应的构造函数
public class DemoEvent extends ApplicationEvent{
    private static final long serialVersionUID = 1L;

    private String message;

    public DemoEvent(Object source,String message){
        super(source);
        this.message = message;
    }

    public String getMessage() {
         return message;
          }


// 定义一个事件监听者,实现ApplicationListener接口，重写 onApplicationEvent() 方法；
@Component
public class DemoListener implements ApplicationListener<DemoEvent>{

    //使用onApplicationEvent接收消息
    @Override
    public void onApplicationEvent(DemoEvent event) {
        String msg = event.getMessage();
        System.out.println("接收到的信息是："+msg);
    }

}
// 发布事件，可以通过ApplicationEventPublisher  的 publishEvent() 方法发布消息。
@Component
public class DemoPublisher {

    @Autowired
    ApplicationContext applicationContext;

    public void publish(String message){
        //发布事件
        applicationContext.publishEvent(new DemoEvent(this, message));
    }
}
```

#### 6. 适配器模式

将一个接口转换成客户希望的另一个接口，适配器模式使接口不兼容的那些类可以一起工作

##### Spring AOP中的适配器模式

**Spring AOP 的实现是基于代理模式，但是 Spring AOP 的增强或通知(Advice)使用到了适配器模式，与之相关的接口是AdvisorAdapter**

- Advice 常用的类型有：

  - BeforeAdvice（目标方法调用前,前置通知）
  - AfterAdvice（目标方法调用后,后置通知）
  - AfterReturningAdvice(目标方法执行结束后，return 之前)等等

- 每个类型 Advice（通知）都有对应的拦截器:

  - MethodBeforeAdviceInterceptor、
  - AfterReturningAdviceInterceptor、
  - ThrowsAdviceInterceptor 等等。

- Spring 预定义的通知要通过对应的适配器，适配成 MethodInterceptor 接口(方法拦截器)类型的对象

  - 如：MethodBeforeAdviceAdapter 通过调用 getInterceptor 方法，
  - 将 MethodBeforeAdvice 适配成 MethodBeforeAdviceInterceptor 

  

##### Spring MVC中的适配器模式

在 Spring MVC 中**，DispatcherServlet 根据请求信息调用 HandlerMapping，解析请求对应的 Handler。**解析到对应的 Handler（也就是我们平常说的 Controller 控制器）后，**开始由HandlerAdapter 适配器处理**。HandlerAdapter 作为期望接口，**具体的适配器实现类用于对目标类进行适配，Controller 作为需要适配的类。**

#### 7. 装饰者模式

InputStream家族，InputStream 类下有 FileInputStream (读取文件)、BufferedInputStream (增加缓存,使读取文件速度大大提升)等子类都在不修改InputStream 代码的情况下扩展了它的功能。

### Spring的循环依赖

#### Spring循环依赖了解吗？怎么解决

循环依赖问题在Spring中主要有三种情况：

- 第一种：通过**构造方法进行依赖注入时产生的循环依赖**问题。
- 第二种：通过**setter方法进行依赖注入且是在多例（原型）模式下产生**的循环依赖问题。
- 第三种：通过s**etter方法进行依赖注入且是在单例模式下产生的循环依赖**问题。

**只有【第三种方式】的循环依赖问题被 Spring 解决了**，其他两种方式在遇到循环依赖问题时，Spring都会产生异常。

- Spring 框架通过**使用三级缓存（其实就是三个map）**来解决这个问题，确保即使在循环依赖的情况下也能正确创建 Bean。

  ```java
  // 一级缓存
  /** Cache of singleton objects: bean name to bean instance. */
  private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
  
  // 二级缓存
  /** Cache of early singleton objects: bean name to bean instance. */
  private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);
  
  // 三级缓存
  /** Cache of singleton factories: bean name to ObjectFactory. */
  private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);
  ```

  - 一级缓存（singletonObjects）：**存放最终形态的 Bean（已经实例化、属性填充、初始化），**单例池，为“Spring 的单例属性”⽽⽣。一般情况我们获取 Bean 都是从这里获取的，但是并不是所有的 Bean 都在单例池里面，例如原型 Bean 就不在里面。
  - 二级缓存（earlySingletonObjects）：**存放过渡 Bean（半成品，尚未属性填充），**也就是三级缓存中ObjectFactory产生的对象，与三级缓存配合使用的，可以防止 AOP 的情况下，每次调用ObjectFactory#getObject()都是会产生新的代理对象的。
  - 三级缓存（singletonFactories）：**存放ObjectFactory，ObjectFactory的getObject()方法**（最终调用的是getEarlyBeanReference()方法）可以生成原始 Bean 对象或者代理对象（如果 Bean 被 AOP 切面代理）。三级缓存只会对单例 Bean 生效。

- Spring创建Bean的流程

  - **实例化 Bean**：Spring 在实例化 Bean 时，会先创建一个空的 Bean 对象，并将其放入一级缓存中。
  - **属性赋值：**Spring 开始对 Bean 进行属性赋值，如果发现循环依赖，会将当前 Bean 对象提前暴露给后续需要依赖的 Bean（通过提前暴露的方式解决循环依赖）。
  - **初始化 Bean：**完成属性赋值后，Spring 将 Bean 进行初始化，并将其放入二级缓存中。
  - **注入依赖：**Spring 继续对 Bean 进行依赖注入，如果发现循环依赖，会从二级缓存中获取已经完成初始化的 Bean 实例

- **如果发生循环依赖的话，就去 三级缓存 singletonFactories 中拿到三级缓存中存储的 ObjectFactory 并调用它的 getObject() 方法来获取这个循环依赖对象的前期暴露对象（虽然还没初始化完成，但是可以拿到该对象在堆中的存储地址了），并且将这个前期暴露对象放到二级缓存中，这样在循环依赖时，就不会重复初始化了！**

  - 当 Spring 创建 A 之后，发现 A 依赖了 B ，又去创建 B，B 依赖了 A ，又去创建 A；
  - 在 B 创建 A 的时候，那么此时 A 就发生了循环依赖，由于 A 此时还没有初始化完成，因此在 一二级缓存 中肯定没有 A；
  - 那么此时就去三级缓存中调用 getObject() 方法去获取 A 的 前期暴露的对象 ，也就是调用上边加入的 getEarlyBeanReference() 方法，生成一个 A 的 前期暴露对象；
  - 然后就将这个 ObjectFactory 从三级缓存中移除，并且将前期暴露对象放入到二级缓存中，那么 B 就将这个前期暴露对象注入到依赖，来支持循环依赖

- 只用两级缓存够吗

  - 在没有 AOP 的情况下，确实可以只使用一级和三级缓存来解决循环依赖问题。但是，**当涉及到 AOP 时，二级缓存就显得非常重要了，因为它确保了即使在 Bean 的创建过程中有多次对早期引用的请求，也始终只返回同一个代理对象，从而避免了同一个 Bean 有多个代理对象的问题。**

- 缺点

  - 增加了内存开销（需要维护三级缓存，也就是三个 Map）
  - 降低了性能（需要进行多次检查和转换）
  - 还有少部分情况是不支持循环依赖的，比如非单例的 bean 和@Async注解的 bean 无法支持循环依赖

#### @Lazy能解决循环依赖吗？

- **@Lazy 用来标识类是否需要懒加载/延迟加载，可作用在类上、方法上、构造器上、方法参数上、成员变量中**
  - **懒加载：不会在Spring IoC容器启动时立即实例化，而是在第一次被请求时才创建**
  - **非懒加载：在Spring IoC容器启动时立即实例化**
- Spring Boot 2.2 新增了全局懒加载属性，开启后全局 bean 被设置为懒加载，需要时再去创建.
  - 全局懒加载会让 Bean 第一次使用的时候加载会变慢，并且它会延迟应用程序问题的发现（当 Bean 被初始化时，问题才会出现）
- @Lazy如何解决循环依赖
  - 没有 @Lazy 的情况下：在 Spring 容器初始化 A 时会立即尝试创建 B，而在创建 B 的过程中又会尝试创建 A，最终导致循环依赖（即无限递归，最终抛出异常）。
  - 使用 @Lazy 的情况下：**Spring 不会立即创建 B，而是会注入一个 B 的代理对象。由于此时 B 仍未被真正初始化，A 的初始化可以顺利完成。等到 A 实例实际调用 B 的方法时，代理对象才会触发 B 的真正初始化。**

### Spring事务

#### Spring的事务会在什么情况下失效？

Spring Boot通过Spring框架的事务管理模块来支持事务操作。事务管理在Spring Boot中通常是通过 @Transactional 注解来实现的。事务可能会失效的一些常见情况包括:

1. **未捕获异常: 如果一个事务方法中发生了未捕获的异常，并且异常未被处理或传播到事务边界之外，那么事务会失效，所有的数据库操作会回滚。**
2. 非受检异常: 默认情况下，**Spring对非受检异常（RuntimeException或其子类）进行回滚处理，这**意味着当事务方法中抛出这些异常时，事务会回滚。
3. **事务传播属性设置不当:** 如果在多个事务之间存在事务嵌套，且事务传播属性配置不正确，可能导致事务失效。**特别是在方法内部调用有 @Transactional 注解的方法时要特别注意。**
4. **多数据源的事务管理: 如果在使用多数据源时，**事务管理没有正确配置或者存在多个 @Transactional 注解时，可能会导致事务失效。
5. 跨方法调用事务问题: **如果一个事务方法内部调用另一个方法，而这个被调用的方法没有 @Transactional 注解，**这种情况下外层事务可能会失效。
6. **事务在非公开方法中失效: 如果 @Transactional 注解标注在私有方法上或者非 public 方法上，事务也会失效**

#### Spring管理事务有几种？

- **编程式事务**：在代码中硬编码(在分布式系统中推荐使用) : 通过 TransactionTemplate或者 TransactionManager 手动管理事务，事务范围过大会出现事务未提交导致超时，因此事务要比锁的粒度更小。
- **声明式事务：**在 XML 配置文件中配置或者直接基于注解（**单体应用或者简单业务系统**推荐使用） : 实际是通过 AOP 实现（基于@Transactional 的全注解方式使用最多）

#### Spring事务中有哪几种事务传播行为？

事务传播行为是为了解决业务层方法之间互相调用的事务问题。当事务方法被另一个事务方法调用时，必须指定事务应该如何传播。

正确的事务传播行为可能的值如下:

1. TransactionDefinition.PROPAGATION_REQUIRED	

   使用的最多的一个事务传播行为，我们平时经常使用的@Transactional注解默认使用就是这个事务传播行为。如果当前存在事务，则加入该事务；如果当前没有事务，则创建一个新的事务。

2. TransactionDefinition.PROPAGATION_REQUIRES_NEW

   创建一个新的事务，如果当前存在事务，则把当前事务挂起。也就是说不管外部方法是否开启事务，Propagation.REQUIRES_NEW修饰的内部方法会新开启自己的事务，且开启的事务相互独立，互不干扰。

3. TransactionDefinition.PROPAGATION_NESTED

   如果当前存在事务，则创建一个事务作为当前事务的嵌套事务来运行；如果当前没有事务，则该取值等价于TransactionDefinition.PROPAGATION_REQUIRED。

4. TransactionDefinition.PROPAGATION_MANDATORY

   如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）

   这个使用的很少。

   

   若是错误的配置以下 3 种事务传播行为，事务将不会发生回滚：

   1. TransactionDefinition.PROPAGATION_SUPPORTS: 如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。
   2. TransactionDefinition.PROPAGATION_NOT_SUPPORTED: 以非事务方式运行，如果当前存在事务，则把当前事务挂起。
   3. TransactionDefinition.PROPAGATION_NEVER: 以非事务方式运行，如果当前存在事务，则抛出异常

#### @Transactional

Exception 分为**运行时异常 RuntimeException 和非运行时异常**。在@Transactional注解中如果不配置rollbackFor属性,那么事务只会在遇到**RuntimeException的时候才会回滚**,加上**rollbackFor=Exception.class,可以让事务在遇到非运行时异常时也回滚。**

- 作用于类：当把@Transactional 注解放在类上时，表示所有该类的 public 方法都配置相同的事务属性信息。
- 作用于方法：当类配置了@Transactional，方法也配置了@Transactional，**方法的事务会覆盖类的事务配置信息。**该注解只能应用到public方法上，否则不生效。
- 如果一个类或者一个类中的 public 方法上被标注@Transactional 注解的话，**Spring 容器就会在启动的时候为其创建一个代理类**，在**调用被@Transactional 注解的 public 方法的时候**，实际**调用的是，TransactionInterceptor 类中的 invoke()方法**。这个方法的作用就是在目标方法之前开启事务，方法执行过程中如果遇到异常的时候回滚事务，方法调用完成之后提交事务。

#### @Transactional事务注解原理

@Transactional 的工作机制是基于 AOP 实现的，AOP 又是使用动态代理实现的。如果目标对象实现了接口，默认情况下会采用 JDK 的动态代理，如果目标对象没有实现了接口,会使用 CGLIB 动态代理。

#### Spring事务管理接口介绍

Spring 框架中，事务管理相关最重要的 3 个接口如下：

1. PlatformTransactionManager：（平台）事务管理器，Spring 事务策略的核心。
2. TransactionDefinition：事务定义信息(**事务隔离级别、传播行为、超时、只读、回滚规则)**。
3. TransactionStatus：事务运行状态。

#### Spring AOP自调用问题

当一个方法被标记了@Transactional 注解的时候，Spring 事务管理器只会在被其他类方法调用的时候生效，而不会在一个类中方法调用生效。

这是因为 Spring AOP 工作原理决定的。因为 Spring AOP 使用动态代理来实现事务的管理，**它会在运行的时候为带有 @Transactional 注解的方法生成代理对象，并在方法调用的前后应用事物逻辑**。如果**该方法被其他类调用我们的代理对象就会拦截方法调用并处理事**务。但是**在一个类中的其他方法内部调用的时候，我们代理对象就无法拦截到这个内部调用，因此事务也就失效了。**

#### 注意事项

1. @Transactional 注**解只有作用到 public 方法上事务才生效，不推荐在接口上使用；**
2. **避免同一个类中调用** @Transactional 注解的方法，这样会导致事务失效；
3. 正确的设置 @Transactional 的 **rollbackFor 和 propagation 属性，否则事务可能会回滚失败;**
4. 被 @Transactional 注解的方法**所在的类必须被 Spring 管理，否则不生效；**
5. **底层使用的数据库必须支持事务机制**，否则不生效

#### Spring的事务，使用this调用是否生效？

不能生效。
**因为Spring事务是通过代理对象来控制的，只有通过代理对象的方法调用才会应用事务管理的相关规则。当使用this直接调用时，是绕过了Spring的代理机制，**因此不会应用事务设置。

### Spring Security

#### 有哪些控制请求访问权限的方法

```java
permitAll()：无条件允许任何形式访问，不管你登录还是没有登录。
anonymous()：允许匿名访问，也就是没有登录才可以访问。
denyAll()：无条件决绝任何形式的访问。
authenticated()：只允许已认证的用户访问。
fullyAuthenticated()：只允许已经登录或者通过 remember-me 登录的用户访问。
hasRole(String) : 只允许指定的角色访问。
hasAnyRole(String) : 指定一个或者多个角色，满足其一的用户即可访问。
hasAuthority(String)：只允许具有指定权限的用户访问
hasAnyAuthority(String)：指定一个或者多个权限，满足其一的用户即可访问。
hasIpAddress(String) : 只允许指定 ip 的用户访问
```

#### 如何对密码进行加密

- 如果我们需要保存密码这类敏感数据到数据库的话，需要先加密再保存。
- Spring Security 提供了多种加密算法的实现，开箱即用，非常方便。这些加密算法实现类的接口是 PasswordEncoder ，如果你想要自己实现一个加密算法的话，也需要实现 PasswordEncoder 接口。
- PasswordEncoder 接口一共也就 3 个必须实现的方法。

```java
public interface PasswordEncoder {
    // 加密也就是对原始密码进行编码
    String encode(CharSequence var1);
    // 比对原始密码和数据库中保存的密码
    boolean matches(CharSequence var1, String var2);
    // 判断加密密码是否需要再次进行加密，默认返回 false
    default boolean upgradeEncoding(String encodedPassword) {
        return false;
    }
}
```

- 突然发现现有的加密算法无法满足我们的需求，需要更换成另外一个加密算法，这个时候应该怎么办呢？
- 推荐的做法是通过 DelegatingPasswordEncoder 兼容多种不同的密码加密方案，以适应不同的业务需求。
- 从名字也能看出来，DelegatingPasswordEncoder 其实就是一个代理类，并非是一种全新的加密算法，它做的事情就是代理上面提到的加密算法实现类。在 Spring Security 5.0 之后，默认就是基于 DelegatingPasswordEncoder 进行密码加密的

------

## SpringBoot常见面试题

### 1. 简单介绍spring及其优缺点

Spring 是一款企业级开发框架，旨在简化 Java 开发，**采用依赖注入和面向切面编程，**提供轻量级组件代码。然而，它的**配置通常较为繁重，**早期版本需要大量 XML 配置，虽然后续引入了注解扫描和基于 Java 的配置，但仍需显式配置某些特性及第三方库。此外，配置工作占据了开发者较多时间和精力，且相关库的依赖管理和版本冲突也是痛点

### 2. 为什么要有SpringBoot

**为了简化Spring应用的开发和部署过程，让开发者更专注于业务逻辑的实现**

### 3. SpringBoot优缺点

使用 Spring Boot 的主要优点：

1. 提高生产力：Spring Boot 的自动配置和开箱即用的功能显著减少了手动配置和样板代码的编写时间，使得开发者能够更快速地构建和交付应用程序。
2. 与 Spring 生态系统的无缝集成：Spring Boot 可以轻松集成 Spring 的各个模块（如 Spring JDBC、Spring ORM、Spring Data、Spring Security 等），简化了与这些工具的整合过程，增强了开发者的工作效率。
3. **减少手动配置：Spring Boot 提供了合理的默认配置，**开发者可以在大多数情况下直接使用这些默认配置来启动项目。当然，这些默认配置也可以根据项目需求进行修改。
4. **嵌入式服务器：Spring Boot 自带内嵌的 HTTP 服务器（**如 Tomcat、Jetty），开发者可以像运行普通 Java 程序一样运行 Spring Boot 应用程序，极大地简化了开发和测试过程。
5. 适合微服务架构：Spring Boot 使得每个微服务都可以独立运行和部署，简化了微服务的开发、测试和运维工作，成为构建微服务架构的理想选择。
6. 多种插件支持：Spring Boot 提供了多种插件，可以使用内置工具（如 Maven 和 Gradle）开发和测试 Spring Boot 应用程序。

### 4. 什么是Spring Boot Starters

Spring Boot Starters 是一组便捷的依赖描述符，它们预先打包了常用的库和配置。当我们开发 Spring 应用时，只需添加一个 Starter 依赖项，即可自动引入所有必要的库和配置，无需手动逐一添加和配置相关依赖。、

以下是一些常见的 Spring Boot Starter 及其用途：

1. `spring-boot-starter`: 基础的 Starter，包含启动 Spring 应用所需的核心依赖，如 Spring 框架本身和日志系统（默认使用 SLF4J 和 Logback）。
2. `spring-boot-starter-web`: 用于构建 Web 应用程序，包括 RESTful 服务。它包含了 Spring MVC、Tomcat（默认嵌入式服务器）、Jackson 等依赖。
3. `spring-boot-starter-data-jpa`: 用于构建 JPA 应用程序，包含 Spring Data JPA 和 Hibernate。简化了数据库访问层的开发，提供了对关系型数据库的便捷操作。
4. `spring-boot-starter-security`: 用于集成 Spring Security，提供身份验证和授权功能，帮助开发者快速实现安全机制。
5. `spring-boot-starter-test`: 提供测试所需的依赖，包含 JUnit、Mockito、Spring Test 等库，帮助开发者编写单元测试、集成测试和 Mock 测试。
6. `spring-boot-starter-actuator`: 可以监控应用程序的运行状态，还可以收集应用程序的各种指标信息。
7. `spring-boot-starter-aop`: 提供面向切面编程（AOP）的支持，包含 Spring AOP 和 AspectJ。
8. `spring-boot-starter-validation`: 集成了 Hibernate Validator，用于实现 Java Bean 的校验机制，通常与 Spring MVC 或 Spring Data 一起使用。

步骤1: 创建Maven项目
首先，需要创建一个新的Maven项目。在pom.xml中添加Spring Boot的starter parent和一些必要的依赖。例如：

```java
<parent>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-parent</artifactId>
<version>2.7.0</version>
<relativePath/> <!-- lookup parent from repository -->
</parent>
<dependencies>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
</dependency>
</dependencies>
```

步骤2: 添加自动配置
在src/main/resources/META-INF/spring.factories中添加自动配置的元数据。例如：

```java
org.springframework.boot.autoconfigure.EnableAutoConfiguration = com.example.starter.MyAutoConfiguration
```

然后，创建MyAutoConfiguration类，该类需要@Configuration和@EnableConfigurationProperties注解。@EnableConfigurationProperties用于启用你定义的配置属性类。

```java
@Configuration
@EnableConfigurationProperties(MyProperties.class)
public class MyAutoConfiguration {
@Autowired
private MyProperties properties;
@Bean
public MyService myService() {
return new MyServiceImpl(properties);
}
}
```

步骤3: 创建配置属性类
创建一个配置属性类，使用@ConfigurationProperties注解来绑定配置文件中的属性。

```java
@ConfigurationProperties(prefix = "my")
public class MyProperties {
private String name;
// getters and setters
}
```

步骤4: 创建服务和控制器
创建一个服务类和服务实现类，以及一个控制器来展示你的starter的功能。

```java
@Service
public interface MyService {
String getName();
}
@Service
public class MyServiceImpl implements MyService {
private final MyProperties properties;
public MyServiceImpl(MyProperties properties) {
this.properties = properties;
}
@Override
public String getName() {
return properties.getName();
}
}
@RestController
public class MyController {
private final MyService myService;
public MyController(MyService myService) {
this.myService = myService;
}
@GetMapping("/name")
public String getName() {
return myService.getName();
}
}
```

步骤5: 发布Starter
将你的starter发布到Maven仓库，无论是私有的还是公共的，如Nexus或Maven Central。
步骤6: 使用Starter
在你的主应用的pom.xml中添加你的starter依赖，然后在application.yml或application.properties中配置你的属性。

```java
my:
	name: Hello World
```

### 5. SpringBoot支持哪些内嵌Servlet容器？如何选择

Spring Boot 提供了三种 Web 容器，分别为 **Tomcat、Jetty 和 Undertow。在** Spring Boot 项目中，我们可以灵活地选择**不同的嵌入式 Servlet 容器来提供 HTTP 服务**。默认情况下，Spring Boot 使用 Tomcat 作为嵌入式服务器

1. **Tomcat：适用于多数常规 Web 应用程序和 RESTful 服务，**易于使用和配置，在**高并发场景下**确实可能不如 Undertow 表现出色。
2. Undertow：Undertow 具有极低的启动时间和资源占用，支持非阻塞 IO（NIO），在高并发场景下表现优秀，性能优于 Tomcat。
3. **Jetty：如果应用程序涉及即时通信、聊天系统或其他需要保持长连接的场景，**Jetty 是一个更好的选择。**它在处理长连接和 WebSocket 时表现优越**。另外，Jetty 在性能和内存使用方面通常优于 Tomcat，虽然在极端高并发场景中可能略逊于 Undertow。

### 6. 如何在SpringBoot应用程序中使用Jetty而不是Tomcat？

Spring Boot（`spring-boot-starter-web`）使用 Tomcat 作为默认的嵌入式 servlet 容器。如果你想使用 Jetty，只需要修改 pom.xml（Maven）或 build.gradle（Gradle）就可以了。

### 7.@SpringBootApplication

把 `@SpringBootApplication` 看作是注解的集合。这三个注解的作用分别是：

1. `@EnableAutoConfiguration`：启用 SpringBoot 的自动配置机制。
2. `@ComponentScan`：扫描被 `@Component` (`@Service`、`@Controller`) 注解的 bean，注解默认会扫描该类所在的包下的所有类。
3. `@Configuration`：允许在上下文中注册额外的 bean 或导入其他配置类。

### 8. SpringBoot的自动配置是如何实现的

1. 引入 Starter 组件
2. SpringBoot 基于约定去寻找配置类
3. SpringBoot 使用 ImportSelector 导入这些配置类，并根据 @Conditional 动态加载配置类里面的 Bean 到容器

4. **Spring Boot 的自动配置机制通过 @EnableAutoConfiguration 启动。**
5. **该注解利用 @Import 注解导入了 AutoConfigurationImportSelector 类，而 AutoConfigurationImportSelector 类则负责加载并管理所有的自动配置类。**
6. **这些自动配置类通常在 META-INF/spring.factories 文件中声明，并根据项目的依赖和配置条件，通过条件注解（如 @ConditionalOnClass、@ConditionalOnBean 等）判断是否应该生效。**

### 怎么理解SpringBoot中的约定大于配置

总结来说，在 Spring Boot 中，“约定大于配置”的理念意味着框架为开发者提供了许多内置的默认设置和行为，减少了需要显式配置的项。以下是几个关键点：

1. **自动化配置**：Spring Boot 根据你添加到项目中的依赖关系，自动配置应用程序所需的组件和服务。例如，如果你添加了一个 JPA 依赖，Spring Boot 就会自动配置数据库连接和其他相关的 JPA 组件。

2. **默认配置**：当没有显式的配置时，Spring Boot 使用合理且常见的默认值来初始化应用。这样可以让你的应用在最少的配置下运行起来。

3. **约定优于配置**：Spring Boot 预设了一些约定，比如项目结构、文件命名等，这样可以减少开发者的工作量，并确保应用之间的一致性。

4. **起步依赖**：这是 Spring Boot 提供的一种特殊的 Maven 或 Gradle 依赖，它包括了一组特定技术的依赖集合。当你添加一个起步依赖后，Spring Boot 会处理好这个技术栈所需的大多数配置。

这种设计让开发者能够专注于实现业务逻辑，而不是繁琐的配置工作，从而加快开发速度并降低错误率。

### 9.开发RESTful Web服务常用的注解有哪些？

Spring Bean 相关：

- @Autowired：自动导入对象到类中，被注入进的类同样要被 Spring 容器管理。
- @RestController：@RestController 注解是 @Controller 和 @ResponseBody 的合集，表示这是个控制器 bean，并且是将函数的返回值直 接填入 HTTP 响应体中，是 REST 风格的控制器。
- @Component：通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用 @Component 注解标注。
- @Repository：对应持久层即 Dao 层，主要用于数据库相关操作。
- @Service：对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- @Controller：对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面。

处理常见的 HTTP 请求类型：

- @GetMapping：GET 请求，
- @PostMapping：POST 请求。
- @PutMapping：PUT 请求。
- @DeleteMapping：DELETE 请求。

前后端传值：

- @RequestParam 以及 @PathVariable：@PathVariable 用于获取路径参数，@RequestParam 用于获取查询参数。
- @RequestBody：用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分并且 Content-Type 为 application/json 格式的数据，收到数据之后会自动将数据绑定到 Java 对象上去。系统会使用 HttpMessageConverter 或者自定义的 HttpMessageConverter 将请求的 body 中的 json 字符串转换为 Java 对象。

### 10.SpringBoot常用两种配置文件

application.properties或者application.yml

### 11. YAML是什么？优缺点？

YAML 是一种人类可读的数据序列化语言。它通常用于配置文件。与属性文件相比，如果我们想要在配置文件中添加复杂的属性**，YAML 文件就更加结构化，而且更清晰。**可以看到 YAML 具有分层配置数据。相比于 Properties 配置的方式，YAML 配置的方式更加直观清晰，简介明了，有层次感。

缺点：**不支持@PropertySource注解导入自定义的YAML配置**

### 12. SpringBoot常用的读取配置文件的方法？

1. 通过@Value读取比较简单的配置信息（不推荐）
   1. 配置项必须手动编写
   2. 缺少类型检查
   3. 需要手动判断空值
   4. 不利于集中管理
2. 通过@ConfigurationProperties读取并和bean绑定
3. 通过@ConfigurationProperties读取并校验
4. @PropertySource读取指定的properties文件

### 14. 常用的Bean映射功能根据

- 我们经常在代码中会对一个数据结构封装成 DO、SDO、DTO、VO 等，而这些 Bean 中的大部分属性都是一样的，**所以使用属性拷贝类工具可以帮助我们节省大量的 set 和 get 操作。**
- 常用的 Bean 映射工具有：Spring BeanUtils、Apache BeanUtils、MapStruct、ModelMapper、Dozer、Orika、JMapper。
- 于 Apache BeanUtils 、 Dozer 、 ModelMapper 性能太差，所以不建议使用**。MapStruct 性能更好而且使用起来比较灵活，是一个不错的选择。**

### 15. SpringBoot如何监控系统实际运行状况

使用SpringBootActuator

### 18. SpringBoot中实现定时任务

@Scheduled

单纯依靠 `@Scheduled` 注解还不行，我们需要在 SpringBoot 中我们在启动类上加上 `@EnableScheduling` 注解，这样才可以启动定时任务。`@EnableScheduling` 注解的作用是发现注解 `@Scheduled` 的任务并在后台执行该任务。

### 19. SpringBoot自动装配

自动装配可以简单理解为：**通过注解或者一些简单的配置就能在 Spring Boot 的帮助下实现某块功能。引入 starter 之后，我们通过少量注解和一些简单的配置就能使用第三方组件提供的功能了。**

#### SpringBoot是如何实现自动装配的

@SpringBootApplication看作是 @Configuration、@EnableAutoConfiguration、@ComponentScan 注解的集合

##### @EnableAutoConfiguration：实现自动装配的核心注解

EnableAutoConfiguration 只是一个简单地注解，自动装配核心功能的实现实际是通过 AutoConfigurationImportSelector类。

##### AutoCongurationImportSelector:加载自动装配类

- AutoConfigurationImportSelector 类实现了 ImportSelector接口，也就实现了这个接口中的 selectImports方法，**该方法主要用于获取所有符合条件的类的全限定类名，这些类需要被加载到 IoC 容器中。**
- Spring Boot 通过@EnableAutoConfiguration**开启自动装配**，通过 SpringFactoriesLoader 最**终加载META-INF/spring.factories中的自动配置类实现自动装配**，自动配置类其实就是**通过@Conditional按需加载的配置类**，想要其生效必须引入spring-boot-starter-xxx包实现起步依赖

- **扫描类路径:** 在应用程序启动时，AutoConfigurationImportSelector 会扫描类路径上的 META-INF/spring.factories 文件，这个文件中包含了各种 Spring 配置和扩展的定义。在这里，它会查找所有实现了 AutoConfiguration 接口的类,具体的实现为getCandidateConfigurations方法。
- **条件判断:** 对于每一个发现的自动配置类，AutoConfigurationImportSelector 会使用条件判断机制（通常是通过 @ConditionalOnXxx注解）来确定是否满足导入条件。这**些条件可以是配置属性、类是否存在、Bean是否存在等等。**
- 根据条件导入自动配置类: 满足条件的自动配置类将被导入到应用程序的上下文中。这意味着它们会被实例化并应用于应用程序的配置

### 20. SpringBoot的项目结构是怎么样的

![image-20241011103408316](D:\2024\Notes\Typora\八股\Spring&SpringBoot.assets\image-20241011103408316.png)

1. 开放接口层：可直接封装 Service 接口暴露成 RPC 接口；通过 Web 封装成 http 接口；网关控制层等。

2. 终端显示层：各个端的模板渲染并执行显示的层。当前主要是 velocity 渲染，JS 渲染，JSP 渲染，移动端展示等。

3. Web 层：主要是对访问控制进行转发，各类基本参数校验，或者不复用的业务简单处理等。

4. Service 层：相对具体的业务逻辑服务层。

5. Manager 层：通用业务处理层，它有如下特征

   1. 1）对第三方平台封装的层，预处理返回结果及转化异常信息，适配上层接口。
   2. 2）对 Service 层通用能力的下沉，如缓存方案、中间件通用处理。
   3. 3）与 DAO 层交互，对多个 DAO 的组合复用。

6. DAO 层：数据访问层，与底层 MySQL、Oracle、Hbase、OceanBase 等进行数据交互。

7. 第三方服务：包括其它部门 RPC 服务接口，基础平台，其它公司的 HTTP 接口，如淘宝开放平台、支付宝付款服务、高德地图服务等。

8. 外部接口：外部（应用）数据存储服务提供的接口，多见于数据迁移场景中。

   ![image-20241011104224940](D:\2024\Notes\Typora\八股\Spring&SpringBoot.assets\image-20241011104224940.png)



------

## Spring&&SpringBoot常用注解总结

### @SpringBootApplication

```java
@SpringBootApplication
public class SpringSecurityJwtGuideApplication {
      public static void main(java.lang.String[] args) {
        SpringApplication.run(SpringSecurityJwtGuideApplication.class, args);
    }
}
```

可以把 @SpringBootApplication看作是 @Configuration、@EnableAutoConfiguration、@ComponentScan 注解的集合

1. @EnableAutoConfiguration：启用 SpringBoot 的自动配置机制
2. @ComponentScan：扫描被@Component (@Repository,@Service,@Controller)注解的 bean，注解默认会扫描该类所在的包下所有的类。
3. @Configuration：允许在 Spring 上下文中注册额外的 bean 或导入其他配置类

### Spring相关

#### @Autowired

自动导入对象到类中，被注入进的类同样要被 Spring 容器管理比如：Service 类注入到 Controller 类中。

#### @Component、@Repository、@Service、@Controller

- @Component：通用的注解，可标注任意类为 Spring 组件。如果一个 Bean 不知道属于哪个层，可以使用@Component 注解标注。
- @Repository : 对应持久层即 Dao 层，主要用于数据库相关操作。
- @Service : 对应服务层，主要涉及一些复杂的逻辑，需要用到 Dao 层。
- @Controller : 对应 Spring MVC 控制层，主要用于接受用户请求并调用 Service 层返回数据给前端页面

#### @RestController

@RestController注解是@Controller和@ResponseBody的合集,表示这是个控制器 bean,并且是将函数的返回值直接填入 HTTP 响应体中,是 REST 风格的控制器。

单独使用 @Controller 不加 @ResponseBody的话一般是用在要返回一个视图的情况，这种情况属于比较传统的 Spring MVC 的应用，对应于前后端不分离的情况。@Controller +@ResponseBody 返回 JSON 或 XML 形式数据

#### @Scope

- 四种常见的 Spring Bean 的作用域：
  - singleton : 唯一 bean 实例，Spring 中的 bean 默认都是单例的。
  - prototype : 每次请求都会创建一个新的 bean 实例。
  - request : 每一次 HTTP 请求都会产生一个新的 bean，该 bean 仅在当前 HTTP request 内有效。
  - session : 每一个 HTTP Session 会产生一个新的 bean，该 bean 仅在当前 HTTP session 内有效。

#### @Configuration

一般用来声明配置类，可以使用 @Component注解替代，不过使用@Configuration注解声明配置类更加语义化。

### 前后端传值

#### @Pathvariable和@RequestParam

@PathVariable用于获取路径参数，@RequestParam用于获取查询参数。

```java
@GetMapping("/klasses/{klassId}/teachers")
public List<Teacher> getKlassRelatedTeachers(
         @PathVariable("klassId") Long klassId,
         @RequestParam(value = "type", required = false) String type ) {
...
}
```

如果我们请求的 url 是：/klasses/123456/teachers?type=web
那么我们服务获取到的数据就是：klassId=123456,type=web。

#### @RequestBody

- **用于读取 Request 请求（可能是 POST,PUT,DELETE,GET 请求）的 body 部分并且Content-Type 为 application/json 格式的数据，接收到数据之后会自动将数据绑定到 Java 对象上去。**
- 系统会使用HttpMessageConverter或者自定义的HttpMessageConverter将请求的 body 中的 json 字符串转换为 java 对象。

一个请求方法只可以有一个@RequestBody，但是可以有多个@RequestParam和@PathVariable。 如果你的方法必须要用两个 @RequestBody来接受数据的话，大概率是你的数据库设计或者系统设计出问题了！

### 读取配置信息

示例application.yml

```yml
wuhan2020: 2020年初武汉爆发了新型冠状病毒，疫情严重，但是，我相信一切都会过去！武汉加油！中国加油！

my-profile:
  name: Guide哥
  email: koushuangbwcx@163.com

library:
  location: 湖北武汉加油中国加油
  books:
    - name: 天才基本法
      description: 二十二岁的林朝夕在父亲确诊阿尔茨海默病这天，得知自己暗恋多年的校园男神裴之即将出国深造的消息——对方考取的学校，恰是父亲当年为她放弃的那所。
    - name: 时间的秩序
      description: 为什么我们记得过去，而非未来？时间“流逝”意味着什么？是我们存在于时间之内，还是时间存在于我们之中？卡洛·罗韦利用诗意的文字，邀请我们思考这一亘古难题——时间的本质。
    - name: 了不起的我
      description: 如何养成一个新习惯？如何让心智变得更成熟？如何拥有高质量的关系？ 如何走出人生的艰难时刻？
```

#### @Value

使用 `@Value("${property}")` 读取比较简单的配置信息：

#### @ConfigurationProperties

```java
@Component
@ConfigurationProperties(prefix = "library")
class LibraryProperties {
    @NotEmpty
    private String location;
    private List<Book> books;

    @Setter
    @Getter
    @ToString
    static class Book {
        String name;
        String description;
    }
  省略getter/setter
  ......
}
```

### 参数校验

所有的注解，推荐使用 JSR 注解，即javax.validation.constraints，而不是org.hibernate.validator.constraint

#### 1. 常用的字段验证注解

```java
@NotEmpty 被注释的字符串的不能为 null 也不能为空
@NotBlank 被注释的字符串非 null，并且必须包含一个非空白字符
@Null 被注释的元素必须为 null
@NotNull 被注释的元素必须不为 null
@AssertTrue 被注释的元素必须为 true
@AssertFalse 被注释的元素必须为 false
@Pattern(regex=,flag=)被注释的元素必须符合指定的正则表达式
@Email 被注释的元素必须是 Email 格式。
@Min(value)被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@Max(value)被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@DecimalMin(value)被注释的元素必须是一个数字，其值必须大于等于指定的最小值
@DecimalMax(value) 被注释的元素必须是一个数字，其值必须小于等于指定的最大值
@Size(max=, min=)被注释的元素的大小必须在指定的范围内
@Digits(integer, fraction)被注释的元素必须是一个数字，其值必须在可接受的范围内
@Past被注释的元素必须是一个过去的日期
@Future 被注释的元素必须是一个将来的日期
……
```

#### 2. 验证请求体（RequestBody）

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Person {

    @NotNull(message = "classId 不能为空")
    private String classId;

    @Size(max = 33)
    @NotNull(message = "name 不能为空")
    private String name;

    @Pattern(regexp = "((^Man$|^Woman$|^UGM$))", message = "sex 值不在可选范围")
    @NotNull(message = "sex 不能为空")
    private String sex;

    @Email(message = "email 格式不正确")
    @NotNull(message = "email 不能为空")
    private String email;

}
```

```java
@RestController
@RequestMapping("/api")
public class PersonController {

    @PostMapping("/person")
    public ResponseEntity<Person> getPerson(@RequestBody @Valid Person person) {
        return ResponseEntity.ok().body(person);
    }
}
```

#### 3.验证请求参数（Path Variables 和Request Parameters）

一定一定不要忘记在类上加上 @Validated 注解了，这个参数可以告诉 Spring 去校验方法参数。

```java
@RestController
@RequestMapping("/api")
@Validated
public class PersonController {

    @GetMapping("/person/{id}")
    public ResponseEntity<Integer> getPersonByID(@Valid @PathVariable("id") @Max(value = 5,message = "超过 id 的范围了") Integer id) {
        return ResponseEntity.ok().body(id);
    }
}d
```

### 全局处理Controller层异常

@ControllerAdvice :注解定义全局异常处理类
@ExceptionHandler :注解声明异常处理方法

SpringBoot借助@RestControllerAdvice和@ExceptionHandler实现全局统一异常处理

- @RestControllerAdvice=@ControllerAdvice@ResponseBody

### JPA相关

1. @Entity声明一个类对应一个数据库实体。
2. @Table 设置表名
3. @Id：声明一个字段为主键。
   1. 通过 @GeneratedValue直接使用 JPA 内置提供的四种主键生成策略来指定主键生成策略。	
      1. TABLE
      2. SEQUENCE
      3. IDENTITY
      4. AUTO
   2. 通过 @GenericGenerator声明一个主键策略，然后 @GeneratedValue使用这个策略
4. @Column 声明字段。
5. @Transient：声明不需要与数据库映射的字段，在保存的时候不需要保存进数据库
6. @Lob:声明某个字段为大字段。
7. 可以使用枚举类型的字段，不过枚举字段要用@Enumerated注解修饰。
8. @Modifying 注解提示 JPA 该操作是修改操作,注意还要配合@Transactional注解使用。
9. @OneToOne 声明一对一关系
   @OneToMany 声明一对多关系
   @ManyToOne 声明多对一关系
   @ManyToMany 声明多对多关系

### 事务@Transactional

Exception 分为**运行时异常 RuntimeException 和非运行时异常**。在@Transactional注解中如果不配置rollbackFor属性,那么事务只会在遇到**RuntimeException的时候才会回滚**,加上**rollbackFor=Exception.class,可以让事务在遇到非运行时异常时也回滚。**

- 作用于类：当把@Transactional 注解放在类上时，表示所有该类的 public 方法都配置相同的事务属性信息。
- 作用于方法：当类配置了@Transactional，方法也配置了@Transactional，**方法的事务会覆盖类的事务配置信息。**

------

## MyBatis常见面试题总结

### 与传统的JDBC相比，MyBatis的优点？

- **基于 SQL 语句编程，相当灵活，**不会对应用程序或者数据库的现有设计造成任 何影响，SQL 写在 XML 里，解除 sql 与程序代码的耦合，便于统一管理；提供 XML 标签，支持编写动态 SQL 语句，并可重用。
- 与 JDBC 相比，**减少了 50%以上的代码量，**消除了 JDBC 大量冗余的代码，不 需要手动开关连接；
- **很好的与各种数据库兼容，**因为 MyBatis 使用 JDBC 来连接数据库，所以只要 JDBC 支持的数据库 MyBatis 都支持。
- **能够与 Spring 很好的集成，**开发效率高
- **提供映射标签，**支持对象与数据库的 ORM 字段关系映射；提供对象关系映射 标签，支持对象关系组件维护

### JDBC连接数据库的步骤

使用Java JDBC连接数据库的一般步骤如下：

1. **加载数据库驱动程序：**在使用JDBC连接数据库之前，需要加载相应的数据库驱动程序。可以通过 Class.forName("com.mysql.jdbc.Driver") 来加载MySQL数据库的驱动程序。不同数据库的驱动类名会有所不同。
2. **建立数据库连接：**使用 DriverManager 类的 getConnection(url, username, password) 方法来连接数据库，其中url是数据库的连接字符串（包括数据库类型、主机、端口等）、username是数据库用户名，password是密码。
3. **创建 Statement 对象：**通过 Connection 对象的 createStatement() 方法创建一个 Statement 对象，用于执行 SQL 查询或更新操作。
4. **执行 SQL 查询或更新操作：**使用 Statement 对象的 executeQuery(sql) 方法来执行 SELECT 查询操作，或者使用 executeUpdate(sql) 方法来执行 INSERT、UPDATE 或 DELETE 操作。
5. **处理查询结果：**如果是 SELECT 查询操作，通过 ResultSet 对象来处理查询结果。可以使用 ResultSet 的 next() 方法遍历查询结果集，然后通过 getXXX() 方法获取各个字段的值。
6. **关闭连接：**在完成数据库操作后，需要逐级关闭数据库连接相关对象，即先关闭 ResultSet，再关闭 Statement，最后关闭 Connection。

### 项目中用到原生的mybatis查询，应该怎么写？

1. **配置MyBatis： 在项目中配置MyBatis的数据源、SQL映射文件等。**
2. **创建实体类：** 创建用于映射数据库表的实体类。
3. **编写SQL映射文件**： 创建XML文件，定义SQL语句和映射关系。
4. **编写DAO接口：** 创建DAO接口，定义数据库操作的方法。
5. **编写具体的SQL查询语句**： 在DAO接口中定义查询方法，并在XML文件中编写对应的SQL语句。
6. **调用查询方法：** 在服务层或控制层调用DAO接口中的方法进行查询

### MybatisPlus和Mybatis的区别？

MybatisPlus是一个基于MyBatis的增强工具库，旨在简化开发并提高效率。以下是MybatisPlus和MyBatis之间的一些主要区别：

1. **CRUD操作**：MybatisPlus通过继承BaseMapper接口，提供了一系列内置的快捷方法，使得CRUD操作更加简单，无需编写重复的SQL语句。
2. **代码生成器**：MybatisPlus提供了代码生成器功能，可以根据数据库表结构自动生成实体类、Mapper接口以及XML映射文件，减少了手动编写的工作量。
3. **通用方法封装**：MybatisPlus封装了许多常用的方法，如条件构造器、排序、分页查询等，简化了开发过程，提高了开发效率。
4. **分页插件：**MybatisPlus内置了分页插件，支持各种数据库的分页查询，开发者可以轻松实现分页功能，而在传统的MyBatis中，需要开发者自己手动实现分页逻辑。
5. **多租户支持**：MybatisPlus提供了多租户的支持，可以轻松实现多租户数据隔离的功能。
6. **注解支持：**MybatisPlus引入了更多的注解支持，使得开发者可以通过注解来配置实体与数据库表之间的映射关系，减少了XML配置文件的编写。

### #{} 和 ${} 的区别是什么？

1. ${}是 **Properties 文件**中的**变量占位符，**它可以用于**标签属性值和 sql 内部，属于原样文本替换**，可以替换任意内容，比如${driver}会被原样替换为com.mysql.jdbc. Driver。
2. #{}是 **sql 的参数占位符**，MyBatis **会将 sql 中的#{}替换为? 号，**在 sql 执行前会使用 PreparedStatement 的参数设置方法，**按序给 sql 的? 号占位符设置参数值**，比如 ps.setInt(0, parameterValue)，#{item.name} 的取值方式为使用反射从参数对象中获取 item 对象的 name 属性值，相当于 param.getItem().getName()

### xml 映射文件中，除了常见的 select、insert、update、delete 标签之外，还有哪些标签？

<resultMap>、 <parameterMap>、 <sql>、 <include>、 <selectKey> ，加上动态 sql 的 9 个标签， trim|where|set|foreach|if|choose|when|otherwise|bind 等，其中 <sql> 为 sql 片段标签，通过 <include> 标签引入 sql 片段， <selectKey> 为不支持自增的主键生成策略标签

### Dao 接口的工作原理是什么？Dao 接口里的方法，参数不同时，方法能重载吗？

- **通常一个 xml 映射文件，都会写一个 Dao 接口与之对应。**Dao 接口就是人们常说的 Mapper 接口，**接口的全限名，就是映射文件中的 namespace 的值**，**接口的方法名，就是映射文件中 MappedStatement 的 id 值**，**接口方法内的参数，就是传递给 sql 的参数**。 
- Mapper 接口是没有实现类的，**当调用接口方法时，接口全限名+方法名拼接字符串作为 key 值，**可唯一定位一个 MappedStatement 
- 举例：com.mybatis3.mappers. StudentDao.findStudentById ，可以唯一找到 namespace 为 com.mybatis3.mappers. StudentDao 下面 id = findStudentById 的 MappedStatement 。
- 在 MyBatis 中，每一个 <select>、 <insert>、 <update>、 <delete> 标签，都会被解析为一个 MappedStatement 对象。

- **Dao 接口里的方法可以重载，但是 Mybatis 的 xml 里面的 ID 不允许重复。**即Mybatis 的 Dao 接口可以有多个重载方法，**但是多个接口对应的映射必须只有一个**，否则启动会报错。

### MyBatis 是如何进行分页的？分页插件的原理是什么？

- (1) MyBatis 使用 **RowBounds 对象进行分页，**它是**针对 ResultSet 结果集执行的内存分页**，而非物理分页；
- (2) 可以在 sql 内**直接书写带有物理分页的参数**来完成物理分页功能，
- (3) 也可以使用**分页插件**来完成物理分页。
  - 分页插件的基本原理**是使用 MyBatis 提供的插件接口，实现自定义插件，**在插件的**拦截方法内拦截待执行的 sql，然后重写 sql，**根据 dialect 方言，添加对应的物理分页语句和物理分页参数。

### 简述 MyBatis 的插件运行原理，以及如何编写一个插件

- MyBatis 仅可以编写针对 ParameterHandler、 ResultSetHandler、 StatementHandler、 Executor 这 4 种接口的插件**，MyBatis 使用 JDK 的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能**，每当执行这 4 种接口对象的方法时，就会进入拦截方法，具体就是 InvocationHandler 的 invoke() 方法，当然，只会拦截那些你指定需要拦截的方法。
- 实现 MyBatis 的 Interceptor 接口并复写 intercept() 方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。

### MyBatis 执行批量插入，能返回数据库主键列表吗？

能

### MyBatis 动态 sql 是做什么的？都有哪些动态 sql？能简述一下动态 sql 的执行原理不？

- MyBatis 动态 sql 可以让我们**在 xml 映射文件内，以标签的形式编写动态 sql，完成逻辑判断和动态拼接 sql 的功能。**其执行原理为，使用 OGNL 从 sql 参数对象中计算表达式的值，根据表达式的值动态拼接 sql，以此来完成动态 sql 的功能。
- yBatis 提供了 9 种动态 sql 标签:
  <if></if>
  <where></where>(trim,set)
  <choose></choose>（when, otherwise）
  <foreach></foreach>
  <bind/>

### MyBatis 是如何将 sql 执行结果封装为目标对象并返回的？都有哪些映射形式？

- 第一种是**使用 <resultMap> 标签，逐一定义列名和对象属性名之间的映射关系**
- 第二种是使**用 sql 列的别名功能，将列别名书写为对象属性名**，比如 T_NAME AS NAME，对象属性名一般是 name，小写，但是列名不区分大小写，MyBatis 会忽略列名大小写，智能找到与之对应对象属性名
- 有了列名与属性名的映射关系后，MyBatis 通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

### MyBatis 能执行一对一、一对多的关联查询吗？都有哪些实现方式，以及它们之间的区别

- 能，MyBatis 不仅可以执行一对一、一对多的关联查询，还可以执行多对一，多对多的关联查询，多对一查询，其实就是一对一查询，只需要把 selectOne() 修改为 selectList() 即可；多对多查询，其实就是一对多查询，只需要把 selectOne() 修改为 selectList() 即可
- 关联对象查询，有两种实现方式，
  - 一种是**单独发送一个 sql** 去查询关联对象，赋给主对象，然后返回主对象。
  - 另一种是使用**嵌套查询，**嵌套查询的含义为使用 join 查询，一部分列是 A 对象的属性值，另外一部分列是关联对象 B 的属性值，好处是只发一个 sql 查询，就可以把主对象和其关联对象查出来
  - join 查询出来 100 条记录，如何确定主对象是 5 个，而不是 100 个？其去重复的原理是 <resultMap> 标签内的 <id> 子标签，指定了唯一确定一条记录的 id 列，MyBatis 根据 <id> 列值来完成 100 条记录的去重复功能， <id> 可以有多个，代表了联合主键的语意。

### MyBatis 是否支持延迟加载？如果支持，它的实现原理是什么？

- **MyBatis 仅支持 association 关联对象和 collection 关联集合对象的延迟加载**，association 指的就是一对一，collection 指的就是一对多查询。在 MyBatis 配置文件中，可以配置是否启用延迟加载 lazyLoadingEnabled=true|false。
- 它的原理是，使用 CGLIB 创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 a.getB().getName() ，**拦截器 invoke() 方法发现 a.getB() 是 null 值，那么就会单独发送事先保存好的查询关联 B 对象的 sql，把 B 查询上来，然后调用 a.setB(b)，于是 a 的对象 b 属性就有值了，接着完成 a.getB().getName() 方法的调用。**这就是延迟加载的基本原理。

### MyBatis 的 xml 映射文件中，不同的 xml 映射文件，id 是否可以重复？

- 不同的 xml 映射文件，如果配置了 namespace，那么 id 可以重复；如果没有配置 namespace，那么 id 不能重复；毕竟 namespace 不是必须的，只是最佳实践而已。
- 原因**就是 namespace+id 是作为 Map<String, MappedStatement> 的 key 使用的**，如果没有 namespace，就剩下 id，那么，id 重复会导致数据互相覆盖。有了 namespace，自然 id 就可以重复，namespace 不同，namespace+id 自然也就不同

### MyBatis 中如何执行批处理？

使用 BatchExecutor 完成批处理

### MyBatis 都有哪些 Executor 执行器？它们之间的区别是什么？

1. **SimpleExecutor：** 每**执行一次 update 或 select，就开启一个 Statement 对象，**用完立刻关闭 Statement 对象。
2. ReuseExecutor： 执行 update 或 select，以 sql 作为 key 查找 Statement 对象，**存在就使用，不存在就创建，**用完后，不关闭 Statement 对象，而是放置于 Map<String, Statement>内，供下一次使用。简言之，就是重复使用 Statement 对象。
3. BatchExecutor：**执行 update（没有 select，JDBC 批处理不支持 select），将所有 sql 都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个 Statement 对象，每个 Statement 对象都是 addBatch()完毕后，等待逐一执行 executeBatch()批处理**。与 JDBC 批处理相同。
4. 作用范围：Executor 的这些特点，都严格限制在 SqlSession 生命周期范围内

### MyBatis 中如何指定使用哪一种 Executor 执行器？

MyBatis 配置文件中，可以指定默认的 ExecutorType 执行器类型，也可以手动给 DefaultSqlSessionFactory 的创建 SqlSession 的方法传递 ExecutorType 类型参数

### MyBatis 是否可以映射 Enum 枚举类？

MyBatis 可以映射枚举类，不单可以映射枚举类，**MyBatis 可以映射任何对象到表的一列上。**映射方式**为自定义一个 TypeHandler ，实现 TypeHandler 的 setParameter() 和 getResult() 接口方法**。 TypeHandler 有两个作用：

- 一是完成从 javaType 至 jdbcType 的转换；
- 二是完成 jdbcType 至 javaType 的转换，体现为 setParameter() 和 getResult() 两个方法，分别代表设置 sql 问号占位符参数和获取列查询结果。

### MyBatis 映射文件中，如果 A 标签通过 include 引用了 B 标签的内容，请问，B 标签能否定义在 A 标签的后面，还是说必须定义在 A 标签的前面？

虽然 MyBatis 解析 xml 映射文件是按照顺序解析的，但是，被引用的 B 标签依然可以定义在任何地方，MyBatis 都可以正确识别。
原理是，MyBatis 解析 A 标签，发现 A 标签引用了 B 标签，但是 B 标签尚未解析到，尚不存在，此时，MyBatis 会将 A 标签标记为未解析状态，然后继续解析余下的标签，包含 B 标签，待所有标签解析完毕，MyBatis 会重新解析那些被标记为未解析的标签，此时再解析 A 标签时，B 标签已经存在，A 标签也就可以正常解析完成了。

### 简述 MyBatis 的 xml 映射文件和 MyBatis 内部数据结构之间的映射关系？

MyBatis 将所有 xml 配置信息都封装到 All-In-One 重量级对象 Configuration 内部。

- 在 xml 映射文件中， <parameterMap> 标签会被解析为 ParameterMap 对象，其每个子元素会被解析为 ParameterMapping 对象。
-  <resultMap> 标签会被解析为 ResultMap 对象，其每个子元素会被解析为 ResultMapping 对象。
- 每一个 <select>、<insert>、<update>、<delete> 标签均会被解析为 MappedStatement 对象，标签内的 sql 会被解析为 BoundSql 对象。

### 为什么说 MyBatis 是半自动 ORM 映射工具？它与全自动的区别在哪里？

Hibernate 属于全自动 ORM 映射工具，**使用 Hibernate 查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的**。而 MyBatis 在查询关联对象或关联集合对象时，**需要手动编写 sql 来完成**，所以，称之为半自动 ORM 映射工具。

## SpringCloud

### 说一下和SpringBoot区别

- **Spring Boot是用于构建单个Spring应用的框架，**而**Spring Cloud则是用于构建分布式系统中的微服务架构的工具**，Spring Cloud提供了服务注册与发现、负载均衡、断路器、网关等功能。
- 两者可以结合使用，通过Spring Boot构建微服务应用，然后用Spring Cloud来实现微服务架构中的各种功能

### 用过哪些微服务组件？

1. **注册中心：注册中心是微服务架构最核心的组件。它起到的作用是对新节点的注册与状态维护**，解决了「如何发现新节点以及检查各节点的运行状态的问题」。微服务节点在启动时会将自己的服务名称、IP、端口等信息在注册中心登记，注册中心会定时检查该节点的运行状态。注册中心通常会采用心跳机制最大程度保证已登记过的服务节点都是可用的。
2. **负载均衡**：负载均衡**解决了「如何发现服务及负载均衡如何实现的问题」**，通常微服务在互相调用时，并不是直接通过IP、端口进行访问调用。而是先通过服务名在注册中心查询该服务拥有哪些节点，注册中心将该服务可用节点列表返回给服务调用者，这个过程叫服务发现，因服务高可用的要求，服务调用者会接收到多个节点，必须要从中进行选择。因此服务调用者一端必须内置负载均衡器，通过负载均衡策略选择合适的节点发起实质性的通信请求。
3. **服务通信**：服务通信组件解决了**「服务间如何进行消息通信的问题」**，服务间通信采用轻量级协议，通常是HTTP RESTful风格。但因为RESTful风格过于灵活，必须加以约束，通常应用时对其封装。例如在SpringCloud中就提供了Feign和RestTemplate两种技术屏蔽底层的实现细节，所有开发者都是基于封装后统一的SDK进行开发，有利于团队间的相互合作。
4. **配置中心**：配置中心主要解决了**「如何集中管理各节点配置文件的问题」**，在微服务架构下，所有的微服务节点都包含自己的各种配置文件，如jdbc配置、自定义配置、环境配置、运行参数配置等。要知道有的微服务可能可能有几十个节点，如果将这些配置文件分散存储在节点上，发生配置更改就需要逐个节点调整，将给运维人员带来巨大的压力。配置中心便由此而生，通过部署配置中心服务器，将各节点配置文件从服务中剥离，集中转存到配置中心。一般配置中心都有UI界面，方便实现大规模集群配置调整。
5. **集中式日志管理：**集中式日志主要是解决了**「如何收集各节点日志并统一管理的问题」**。微服务架构默认将应用日志分别保存在部署节点上，当需要对日志数据和操作数据进行数据分析和数据统计时，必须收集所有节点的日志数据。那么怎么高效收集所有节点的日志数据呢？业内常见的方案有ELK、EFK。通过搭建独立的日志收集系统，定时抓取各节点增量日志形成有效的统计报表，为统计和分析提供数据支撑。
6. **分布式链路追踪**：分布式链路追踪解决了「**如何直观的了解各节点间的调用链路的问题」**。系统中一个复杂的业务流程，可能会出现连续调用多个微服务，我们需要了解完整的业务逻辑涉及的每个微服务的运行状态，通过可视化链路图展现，可以帮助开发人员快速分析系统瓶颈及出错的服务。
7. **服务保护**：服务保护主要是解决了**「如何对系统进行链路保护，避免服务雪崩的问题」**。在业务运行时，微服务间互相调用支撑，如果某个微服务出现高延迟导致线程池满载，或是业务处理失败。这里就需要引入服务保护组件来实现高延迟服务的快速降级，避免系统崩溃。

SpringCloud Alibaba实现的微服务架构：

- SpringCloud Alibaba中使用Alibaba **Nacos组件实现注册中心**，Nacos提供了一组简单易用的特性集，可快速实现动态服务发现、服务配置、服务元数据及流量管理。
- SpringCloud Alibaba 使用**Nacos服务端均衡**实现负载均衡，与Ribbon在调用端负载不同，Nacos是在服务发现的同时利用负载均衡返回服务节点数据。
- SpringCloud Alibaba 使**用Netflix Feign和Alibaba Dubbo组件来实现服务通行，前者与SpringCloud采用了相同的方案，后者则是对自家的RPC 框架Dubbo也给予支持，为服务间通信提供另一种选择。**
- SpringCloud Alibaba 在API服务网关组件中，使用与SpringCloud相同的组件，即：SpringCloud Gateway。
- SpringCloud Alibaba在配置中心组件中**使用Nacos内置配置中心**，Nacos内置的配置中心，可将配置信息**存储保存在指定数据库中**
- SpringCloud Alibaba在原有**的ELK方案外，还可以使用阿里云日志服务（LOG）实现日志集中式管理。**
- SpringCloud Alibaba在**分布式链路组件**中采用与SpringCloud相同的方案，即：Sleuth/Zipkin Server。
- SpringCloud Alibaba使用Alibaba Sentinel实现系统保护，Sentinel不仅功能更强大，实现系统保护比Hystrix更优雅，而且还拥有更好的UI界面。









































