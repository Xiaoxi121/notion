# little point

## 注解

### 注解是什么？

​	java注解是在JDK5的时候引入的一种新特性。
​	注解（也可以称为[元数据](https://so.csdn.net/so/search?q=元数据&spm=1001.2101.3001.7020)）为在代码中添加信息提供了一种形式化的方法，使得在代码中任一时刻可以非常方便的使用这些数据。
​	注解类型定义了一种新的特殊接口类型，在接口关键期interface之前加@符号，即用@interface即可区分注解与普通接口声明。目前大部分框架都是通过使用注解简化代码提高编码效率

### 注解的作用

1. 提供信息给编译器：编译器可直接通过注解探测错误和警告信息，例如：@Override，@Deprecated
2. 编译阶段时的处理：软件工具可以用来利用注解信息生成代码、html文档或者做其他相应处理，例如：@Param，@Return，@See，@Author用于生成javadoc文档
3. 运行时的处理：某些注解可以在程序运行时接收代码的提取，但注解本身不是代码的一部分

### @Test

`@Test` 是一个标记注解，通常用于JUnit测试类中。当一个方法被标注为 `@Test` 时，表示这个方法是一个测试方法，JUnit 在执行测试时会识别并执行带有 `@Test` 注解的方法。

在你提供的代码片段中，该方法被标记为 `@Test`，表明这是一个用于测试的方法。当你运行这段代码时，测试框架（比如JUnit）会执行这个方法，并输出测试结果。在这个方法中，通过容器获取了两个 `HappyMachine` 类型的实例，以及两个 `HappyComponent` 类型的实例，并测试它们之间的对象引用是否相同。

### GAV

**GroupId**和**ArtifactId**被统称为“坐标”是为了保证项目唯一性而提出的，如果你要把你项目弄到maven本地仓库去，你想要找到你的项目就必须根据这两个id去查找。 　**GroupId**一般分为多个段，这里我只说两段，第一段为域，第二段为公司名称。域又分为org、com、cn等等许多，其中org为非营利组织，com为商业组织。举个apache公司的tomcat项目例子：这个项目的**GroupId**是org.apache，它的域是org（因为tomcat是非营利项目），公司名称是apache，**ArtifactId**是tomcat。 　 　　比如我创建一个项目，我一般会将**GroupId**设置为cn.mht，cn表示域为中国，mht是我个人姓名缩写，**ArtifactId**设置为testProj，表示你这个项目的名称是testProj，依照这个设置，在你创建Maven工程后，新建包的时候，包结构最好是**cn.zr.testProj**打头的，如果有个StudentDao[Dao层的]，它的全路径就是**cn.zr.testProj**.dao.StudentDao

## 控制反转和依赖注入

[控制反转和依赖注入的理解(通俗易懂)-CSDN博客](https://blog.csdn.net/sinat_21843047/article/details/80297951?ops_request_misc=%7B%22request%5Fid%22%3A%22171083437616800213049266%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=171083437616800213049266&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-80297951-null-null.142^v99^control&utm_term=依赖注入&spm=1018.2226.3001.4187)****

## 接口和抽象类

### 接口（Interface）示例：

```
javaCopy Code// 定义一个接口Animal
interface Animal {
    void eat(); // 抽象方法 eat
    void sleep(); // 抽象方法 sleep
}

// 实现Animal接口的类Dog
class Dog implements Animal {
    @Override
    public void eat() {
        System.out.println("Dog is eating.");
    }

    @Override
    public void sleep() {
        System.out.println("Dog is sleeping.");
    }
}

// 实现Animal接口的类Cat
class Cat implements Animal {
    @Override
    public void eat() {
        System.out.println("Cat is eating.");
    }

    @Override
    public void sleep() {
        System.out.println("Cat is sleeping.");
    }
}
```

### 抽象类（Abstract Class）示例：

```
javaCopy Code// 定义一个抽象类Shape
abstract class Shape {
    // 抽象方法，子类必须实现
    public abstract double calculateArea();

    // 具体方法，子类可以选择性覆盖
    public void display() {
        System.out.println("This is a shape.");
    }
}

// 继承抽象类Shape的具体类Circle
class Circle extends Shape {
    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}

// 继承抽象类Shape的具体类Rectangle
class Rectangle extends Shape {
    private double length;
    private double width;

    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    public double calculateArea() {
        return length * width;
    }
}
```

在上面的示例中，`Animal`是一个接口，定义了动物的行为；`Dog`和`Cat`实现了`Animal`接口，并提供了eat和sleep方法的具体实现。

而在抽象类的示例中，`Shape`是一个抽象类，定义了形状的计算面积的抽象方法`calculateArea`，以及一个具体方法`display`；`Circle`和`Rectangle`继承了`Shape`抽象类，并实现了`calculateArea`方法。

## 耦合和解耦合

在软件开发中，耦合（Coupling）和解耦（Decoupling）是两个重要的概念，它们描述了模块或组件之间互相依赖的程度。

1. 耦合（Coupling）：耦合指的是模块或组件之间的**依赖关系程度**。高耦合意味着模块之间的依赖关系较强，一个模块的修改可能会导致其他模块受到影响。**耦合度高会增加代码的复杂性，降低代码的可维护性和可重用性**。常见的耦合方式包括数据耦合、控制耦合、内容耦合等。
2. 解耦（Decoupling）：解耦是指降低模块或组件之间的依赖关系，使得它**们能够独立地进行开发、测试和维护。**解耦能够提高代码的灵活性和可扩展性，降低代码的复杂度和风险。通过解耦，可以将系统分解为更小、更独立的部分，减少模块之间的直接联系。

解耦的方法包括：

- 使用接口：通过定义接口和实现类的方式，降低模块之间的直接依赖，提高代码的灵活性。
- 事件驱动：利用事件机制来解耦模块之间的通信，一个模块发送事件，其他模块监听并处理事件。
- 中介者模式：引入一个中介者对象来处理模块之间的交互，降低模块之间的直接耦合。
- 依赖注入：通过依赖注入框架来管理模块之间的依赖关系，降低模块之间的耦合度。

解耦是软件设计中的重要原则之一，能够帮助构建更加灵活、可维护和可扩展的系统。

## 装箱和拆箱

装箱（Boxing）和拆箱（Unboxing）是用来描述**将值类型（如整数、字符等）转换为引用类型（如对象、接口等）以及将引用类型转换为值类型的过程。**

- **装箱（Boxing）：** 是**将一个值类型的数据包装到一个对象中的过**程。在装箱过程中，**编译器会自动创建一个对应的引用类型对象，并将值类型的值复制到这个对象中。装箱通常发生在将值类型赋值给一个引用类型的变量、作为参数传递给一个接受引用类型的方法、或者存储在集合类中等情况下。**
- **拆箱（Unboxing）：** 是将**一个引用类型对象中的值取出来赋给一个值类型的**变量的过程。**在拆箱过程中，编译器会自动从对象中提取值类型的值，并将其赋给目标变量。拆箱通常发生在从一个引用类型变量中获取值、作为参数传递给一个需要值类型参数的方法、或者从集合类中获取值等情况下。**



















