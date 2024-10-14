# Java基础教程

## java基础

### JDK JRE JVM

- JDK：Java开发工具包
  - JVM虚拟机：Java运行程序的地方
  - 核心类库：Java已经写好的东西，我们可以直接使用
  - 开发工具：javac、java、jdb、jhat
- JRE：Java运行环境
  - JVM、核心类库、运行工具
- JDK、JVM、JRE包含关系
  - JDK包含JRE
  - JRE包含JVM

### 注释

- 单行注释//
- 多行注释/* */
- 文档注释/** **/

### 字面量的分类

- 整数类型
  - byte	short	int 	long
- 小数类型
  - float double
- 字符串类型  用双引号括起来的内容
- 字符类型 用单引号括起来的内容
  - char
- 布尔类型
  - boolean
- 空类型

### 不同进制在代码中的表示

- 二进制 以0b开头
- 八进制 以0开头
- 十六进制 以0x开头

```java
int i=20;
system.out.println(i);

//如果要定义long类型的变量，在数据值后面加L
long x=999999999L;
system.out.println(x);

//定义float类型变量的时候，在数值后面加F
float y=2.2F;
system.out.prinln(y);
```

### 驼峰命名法

- 小驼峰命名法：方法、变量
  - 一个单词全部小写
  - 多个单词，第一个小写，其他单词首字母大写
- 大驼峰命名法：类名
  - 每个单词首字母都大写

### 键盘录入

​	Java帮我们写好一个类叫Scanner，这个类可以接受键盘输入的数字

- 导包：出现在类定义的上面

  ```java
  import java.util.Scanner;
  ```

- 创建对象

  ```java
  Scanner sc =  new Scanner(system.in);
  ```

- 接收数据

  ```java
  int i = sc.nextInt();
  ```
  
  <img src="D:\2024\Notes\Typora\Pictures\image-20240115210322079.png" alt="image-20240115210322079" style="zoom:50%;" />
  
  <img src="D:\2024\Notes\Typora\Pictures\image-20240115210529563.png" alt="image-20240115210529563" style="zoom:33%;" />

### 隐式转换和强制类型转换

在Java中，byte是一个8位的有符号整数数据类型，而int是一个32位的有符号整数数据类型。当两个byte类型的变量i和j相加时，由于byte类型的取值范围比int类型要小，可能导致结果超出byte类型的表示范围。

为了避免数据溢出或丢失精度的问题，**Java会对byte类型的操作进行自动类型提升。在表达式中，byte类型的操作数会先被自动提升为int类型，然后进行相加运算。**这样可以确保不会发生数据溢出的情况。

例如，如果将两个byte类型的变量i和j相加：

```java
javaCopy Codebyte i = 10;
byte j = 20; 
byte result = i + j; // 这里会报错
```

### 字符串和字符的加操作

##### 字符串的+操作

- ##### 当+操作出现字符串时，是字符串连接符不是算术运算符，会将前后的数据进行拼接，产生一个新的字符串

  “123”+123	“123123”

- 连续进行+操作时，从左至右逐个执行

  1+99+“黑马”	“100年黑马”

- ```Java
  System.out.println(1+2+"abc"+1+2);
  
  //"3abc12"
  ```

- A 65 a97

- <img src="D:\2024\Notes\Typora\Pictures\image-20240111114245438.png" alt="image-20240111114245438" style="zoom:50%;" />\

- ```java
  1+'a'+"abc" 98abc
  ```

### 赋值运算符和关系运算符

​	关系运算符结果都是boolean类型

### 数组

<img src="D:\2024\Notes\Typora\Pictures\image-20240111120655912.png" alt="image-20240111120655912" style="zoom: 67%;" />

关于数组有长度属性length

`int []arr = new int[]{1,2,3};`

### java内存分配

<img src="D:\2024\Notes\Typora\Pictures\image-20240111124020616.png" alt="image-20240111124020616" style="zoom: 33%;" />

### 引用

<img src="D:\2024\Notes\Typora\Pictures\image-20240112183625379.png" alt="image-20240112183625379" style="zoom: 33%;" />

## 面向对象

### 类和对象

#### 如何定义一个类 

public修饰的类名要和文件名保持一致

```java
public class 类名{
	成员变量（代表属性）
	成员方法（代表行为）
	构造器
	代码块
	内部类
}

public class Phone{
	String brand;
    double price;
    
    public void call(){
        
    }
    
    public void playGame(){
	
    }
}
```

#### 如何得到类的对象

```java
类名 对象名 = new 类名 ();
Phone P = new Phone();
```

#### 如何使用对象

```java
访问属性：对象名.成员变量
访问行为：对象名.方法名
```

#### 定义类的补充注意事项

- 用来描述一类事物的类，专业叫做Javabean类

  在Javabean类中，不写main方法

- 在以前，编写main方法的类，叫做测试类

  我们可以在测试类中创建javabean类的对象并进行赋值调用

- 对象的成员变量的默认规则

  <img src="D:\2024\Notes\Typora\Pictures\image-20240112190232956.png" alt="image-20240112190232956" style="zoom:50%;" />

  

### 面向对象的三大特征：封装 继承 多态

#### 封装

对象代表什么， 就得封装对应的数据，并提供数据对应的行为

#### private关键字

- 是一个权限修饰符
- 可以修饰成员（成员变量和成员方法）;
- 被private修饰的成员只能在本类中才能访问

#### this代表当前对象引用的变量

<img src="D:\2024\Notes\Typora\Pictures\image-20240112201502300.png" alt="image-20240112201502300" style="zoom:50%;" />

#### get和set方法的public后面为什么不能加static

**get set方法是对某个实例的属性值进行修改**

在Java中，getter和setter方法是用于访问和修改类的属性（字段）的公共方法。它们通常被设计为实例方法，而不是静态方法。

如果将getter或setter方法声明为**静态方法，则无法访问实例变量，因为静态方法只能访问静态变量，无法访问非静态变量。**因此，将getter或setter方法设置为静态方法通常是不合适的。

此外，getter和setter方法的主要目的是提供对类的属性的访问和修改，这些属性通常是与特定对象实例相关联的。因此，将它们声明为静态方法将使其失去这种关联，从而破坏了封装和数据隐藏的原则。

因此，一般情况下，getter和setter方法都应该声明为实例方法，而不是静态方法，并且不应该使用static关键字来修饰public访问修饰符。

当我们创建一个类的实例时，通常会使用该实例来访问和修改该类的属性。下面是一个示例，展示了为什么getter和setter方法应该声明为实例方法而不是静态方法。

```Java
public class Person {
    private String name; // 实例变量

    public String getName() { // 实例方法
        return name;
    }

    public void setName(String name) { // 实例方法
        this.name = name;
    }

    public static void main(String[] args) {
        Person person1 = new Person();
        person1.setName("Alice"); // 通过实例对象调用实例方法

        Person person2 = new Person();
        person2.setName("Bob"); // 通过另一个实例对象调用实例方法

        System.out.println(person1.getName()); // 通过实例对象获取属性值
        System. out.println(person2.getName()); // 通过另一个实例对象获取属性值
    }
}
```

在上述示例中，我们创建了一个Person类，并定义了一个私有实例变量name。然后，我们提供了一个公共的实例方法getName()来获取name属性的值，以及另一个实例方法setName()来设置name属性的值。

在main方法中，我们创建了两个Person对象person1和person2，并通过它们分别调用setName()方法来设置name属性的值。然后，我们通过这两个实例对象分别调用getName()方法来获取name属性的值，并将其输出到控制台。

这样，我们可以看到每个Person对象都有自己独立的name属性，通过实例方法可以访问和修改对象的属性值。如果getter和setter方法被声明为静态方法，将无法访问实例变量name，并且无法实现每个对象独立的属性访问和修改功能。

静态变量和实例变量是Java中两种不同类型的变量，它们具有以下区别：

1. 存储位置：静态变量存储在静态存储区中，而实例变量存储在堆内存中的对象中。
2. 内存分配时间：静态变量在类加载时被分配内存空间，并在整个程序的执行期间保持不变。而实例变量在创建对象时动态分配内存空间，并且每个对象都有自己的一份实例变量。
3. 共享性：静态变量是类级别的变量，它的值对于所有该类的对象来说是共享的。而实例变量是对象级别的变量，每个对象都有自己独立的实例变量。
4. 访问方式：静态变量可以直接通过类名访问，无需创建对象。而实例变量需要通过对象引用来访问。
5. 生命周期：静态变量的生命周期与程序的生命周期相同，当程序终止时才会释放。而实例变量的生命周期与对象的生命周期相同，在对象被垃圾回收时释放。

下面是一个示例，演示了静态变量和实例变量的区别：

```java
javaCopy Codepublic class Example {
    static int staticVariable; // 静态变量
    int instanceVariable; // 实例变量

    public static void main(String[] args) {
        Example e1 = new Example();
        e1.staticVariable = 10;
        e1.instanceVariable = 20;

        Example e2 = new Example();
        e2.staticVariable = 30;
        e2.instanceVariable = 40;

        System.out.println("e1.staticVariable: " + e1.staticVariable); // 输出：30
        System.out.println("e1.instanceVariable: " + e1.instanceVariable); // 输出：20

        System.out.println("e2.staticVariable: " + e2.staticVariable); // 输出：30
        System.out.println("e2.instanceVariable: " + e2.instanceVariable); // 输出：40
    }
}
```

在上述示例中，我们定义了一个静态变量staticVariable和一个实例变量instanceVariable。在main方法中，我们创建了两个Example对象e1和e2，并分别给它们的静态变量和实例变量赋值。注意，无论我们通过e1还是e2来访问静态变量staticVariable，它们都是共享同一个静态变量。而每个对象都有自己的实例变量instanceVariable，它们的值是相互独立的。

在输出结果中，我们可以看到静态变量的值对于所有对象来说是共享的，而实例变量的值是独立的。

### 构造方法

构造的作用是用来初始化对象的状态 

#### 构造方法和成员方法的不同

1. 角色和用途：构造方法用于**创建和初始化对象时调用**，而成员方法用于执行对象的特定操作。
2. 方法名和返回类型：构造方法的方法名与类名相同，并且没有返回类型（包括void）。成员方法具有自定义的方法名，并且可以有返回类型（包括void）。
3. 调用方式：构造方法在创建对象时**自动调用**，无需显式调用。成员方法需要通过对象引用来调用。
4. 调用时机：构造方法**在使用new关键字创建对象时调用**，用于初始化对象的属性和状态。成员方法在对象创建后，可以随时通过对象引用进行调用。
5. 参数传递：构造方法可以接受参数，用于传递创建对象所需的初始化数据。成员方法也可以接受参数，用于在方法执行过程中传递数据或配置方法的行为。
6. 返回值：构造方法没有返回值，因为其目的是初始化对象而不是返回结果。成员方法可以有返回值，用于提供方法执行的结果。

下面是一个示例，演示了构造方法和成员方法的区别：

```java
javaCopy Codepublic class Example {
    private int value;

    // 构造方法
    public Example(int value) {
        this.value = value;
    }

    // 成员方法
    public void display() {
        System.out.println("Value: " + value);
    }

    public static void main(String[] args) {
        Example example = new Example(10); // 调用构造方法创建对象
        example.display(); // 调用成员方法执行操作
    }
}
```

#### 构造方法的格式

```Java
public class Student{
	修饰符 类名（参数）{
		方法体；
	}
}
特点：
    方法名与类名相同，大小写一致
    没有返回值类型，连void都没有
    没有具体的返回值（不能由return带回结果数据）
    
两种形式：空参+带参
    
在Java中，public static void main(String[] args)是一个特殊的方法，被称为主方法（Main Method）。它是程序的入口点，用于启动Java应用程序。主方法属于类中的静态成员方法，而不是构造方法。 
   
    
public class Student{
	private String name;
    private int age;
    
    public Student(){
		//空参构造方法
    }
    
    public Student(String name,int age){
		//带全部参数构造方法 
    }

}
```

#### 构造方法的注意事项

##### 构造方法的定义

- 如果没有定义构造方法，系统给出默认无参数构造方法
- 如果定义了构造方法，系统不再提供默认的构造方法

##### 构造方法的重载

#####  推荐使用方法：同时写无参的和有参的

### 标准的Javabean类

- 类名 见名知意
- 成员变量使用private修饰
- 提供至少两个构造方法
  - 无参构造方法
  - 带全部参数的构造方法
- 成员方法
  - 提供每一个成员变量对应的set()、get()
  - 如果还有其他行为，也还需要写上

快捷键：alt+fn+insert

### ！！！面向对象07三种情况课没看               

### 基本数据类型和引用数据类型

创建的任意对象、数组都是引用数据类型

<img src="D:\2024\Notes\Typora\Pictures\image-20240115195501942.png" alt="image-20240115195501942" style="zoom:50%;" />

### this的内存原理

区分成员变量和局部变量

**this本质：表示所在方法调用者的内存地址**

### 成员和局部

<img src="D:\2024\Notes\Typora\Pictures\image-20240115200421858.png" alt="image-20240115200421858" style="zoom:50%;" />

### 数组使用

<img src="D:\2024\Notes\Typora\Pictures\image-20240116194126274.png" alt="image-20240116194126274" style="zoom:50%;" />

## 字符串

### API和API帮助文档

ApplicationProgrammingInterface 应用程序编程接口

Java API :指的是JDK中提供的各种功能的Java类

### String概述

java.lang.String类代表字符串

字符串的内容是不会发生改变的，它的对象在创建以后不能修改

String是Java定义好的一个类，定义在java.lang中，所以使用的时候不需要导包

### String构造方法代码实现和内存分析

#### 创建String对象的两种方式

- 直接赋值

- new

  - 构造方法

    `public String() 创建空白字符串 不包含任何内容`

    `public String(String oringinal) 根据传入的字符串创建字符串对象`

    `public String(char[] chs) 根据字符数组，创建字符串对象`

    `public String(byte[]chs) 根据字节数组，创建字符串对象`

    ![image-20240116211809737](D:\2024\Notes\Typora\Pictures\image-20240116211809737.png)

#### Java的内存模型

当使用**双引号直接赋值的时候，系统会检查该字符串在串池中是否存在**

- **不存在：创建新的**
- **存在：复用**

### 字符串的比较方法 

![image-20240116213341909](D:\2024\Notes\Typora\Pictures\image-20240116213341909.png)

new在堆，赋值在串池

<img src="D:\2024\Notes\Typora\Pictures\image-20240125170704574.png" alt="image-20240125170704574" style="zoom:50%;" />

`boolean equals(待比较字符串)   完全一样结果true 否则false`

`boolean equalsIgnoreCase(待比较字符串) 忽略大小写`

```java
boolean result = s1.equals(s2);
boolean result2 = s1.equalsIgnoreCase(s2);
```

![image-20240203092945924](D:\2024\Notes\Typora\Pictures\image-20240203092945924.png)

#### 快捷键 CTRL+ALT+t Surround With

### 遍历字符串

```Java
//根据索引返回
public char charAt(int index);
//返回字符串长度
public int length();
```

### 手机号屏蔽

```java
String subtstring(int beginIndex,int endIndex);
//包左不包右，只有返回值才是截取的小串
```

```java
String subtsring(int beginIndex);
//截取到末尾
```

```java
String replace(旧值，新值);
```

```java
//“张无忌-15”
String[] arr = s.split("-"); 切割函数
String ageString = arr[1];//15
int age = Integer.parseInt(ageString);
```

### Stringbuilder的基本原理

- java.lang.StringBuilder

- StringBuilder是java已经写好的类，打印对象不是地址值，而是属性值 

- StringBuilder可以看作是一个容器，创建之后里面内容是可以改变的。

- 作用：提高字符串操作效率

- ​	**StringBuilder用于多线程不安全，如果要实现同步用StringBuffer**

  ​	面试的时候会问这两个有什么不同，记一下线程安全，效率，可不可变这三个特征分别是什么就行

```java
StringBuffer和StringBuilder是Java中用于处理可变字符串的类，它们在效率、安全性和是否可变等方面有一些区别。

效率：
StringBuffer是线程安全的，所有的方法都使用了synchronized关键字进行同步，因此在多线程环境下使用StringBuffer是安全的，但这也导致了一定的性能损失。
StringBuilder不是线程安全的，它的方法没有进行同步，因此在单线程环境下使用StringBuilder的效率相对更高

安全性：
StringBuffer是线程安全的，适用于多线程环境，在并发操作时可以避免数据不一致的问题。
StringBuilder不是线程安全的，适用于单线程环境，效率更高，但在并发环境下需要自行保证数据一致性。

可变性：
StringBuffer和StringBuilder都是可变的，可以通过调用其方法来修改字符串的内容。
String是不可变的，每次对String进行操作（拼接、替换等），都会生成一个新的String对象，原有的String对象不会被修改。
综上所述，如果在单线程环境下，并且不需要考虑线程安全性，推荐使用StringBuilder，因为它具有更高的效率。如果在多线程环境下，或者需要保证线程安全性，那么应该使用StringBuffer，尽管它的效率相对较低。
```

#### StringBuilder构造方法

```java
public StringBuilder();
//创建一个空白可变字符串，不包含任何内容
```

```Java
public StringBuilder(String Str);
//根据字符串内容，来创建可变字符串对象
```

```Java
StringBuilder sb = new StringBuilder("abc");
```

#### StringBuilder成员方法

```Java
public StringBuilder append(任意类型);
//添加数据，并返回内容本身
```

```Java
public StringBuilder reverse();
//反转容器中内容
容器中内容发生反转！
```

```java
public int length();
public String toString();
//把StringBuilder转换成String
```

#### 链式编程

![image-20240126114843902](D:\2024\Notes\Typora\Pictures\image-20240126114843902.png)

### StringJoiner

只能用于添加字符串类型 

#### StringJoiner构造方法                                                                 

```java
public StringJoiner(间隔符号);

public StringJoiner(间隔符号，开始符号，结束符号);
```

#### StringJoiner成员方法

```java
public StringJoiner Add(添加的内容);
public int length();
public String to String();
```

### 扩展底层原理

![image-20240126121343152](D:\2024\Notes\Typora\Pictures\image-20240126121343152.png)

![image-20240126122417845](D:\2024\Notes\Typora\Pictures\image-20240126122417845.png)

![image-20240126124010439](D:\2024\Notes\Typora\Pictures\image-20240126124010439.png)

## ArrayList

![image-20240126125715426](D:\2024\Notes\Typora\Pictures\image-20240126125715426.png)

创建集合的对象

泛型：限定集合中存储数据 的类型

```java
ArrayList<String> list = new ArrayList<Sting>();

//jdk7:
ArrayList<String> list = new ArrayList<>();
```

此时我们创建的是ArrayList的对象，而ArrayList这个类在底层做了一些处理，打印对象不是地址值，而是集合中存储的数据内容，在展示的时候会用[]把数据进行包裹

### ArrayList成员方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240126130350863.png" alt="image-20240126130350863" style="zoom:50%;" />

### **基本数据类型对应的包装类**

<img src="D:\2024\Notes\Typora\Pictures\image-20240126131342040.png" alt="image-20240126131342040" style="zoom:25%;" />

```java
ArrayList<Integer> list = new ArrayList<>();

list.add(1);
```

## 面向对象进阶

### static-静态变量

static表示静态，是一个修饰符，可以修饰成员方法和成员变量。用static修饰的就是类变量或者类方法

- 用static修饰的成员变量，叫做静态变量

  - 特点 
    - 被该类所有对象共享
    - 不属于对象，属于类
    - 静态变量是随着类的加载而加载的，优先于对象出现的。即静态变量是类的属性而不是实例的属性
  - 调用方式
    - 类名调用（推荐）
    - 对象名调用

- 用static修饰的成员方法，叫做静态方法

  #### static内存图

  ![image-20240126152247858](D:\2024\Notes\Typora\Pictures\image-20240126152247858.png)

### static-静态方法和工具类



- 静态方法
  - 特点
    - 多用在测试类和工具类中
    - Javabean类中很少会用
  - 调用方式
    - 类名调用（推荐）
    - 对象名调用

![image-20240126152903090](D:\2024\Notes\Typora\Pictures\image-20240126152903090.png)

- 工具类

  - 类名见名知意
  - 私有化构造方法
    - 目的：不让外界创建对象

  ```java
  public class ArrayUtil(){
  	privtae ArrayUtil(){}
  	
  	public static String printArr(int[] arr){
  		...
  	}
  }
  ```

### static的注意事项

- 静态方法只能访问静态变量和静态方法

  （静态方法只能访问静态）

- 非静态方法可以访问静态变量或者静态方法，也可以访问非静态的成员变量和非静态的成员方法

  （非静态方法可以访问所有）

- 静态方法中没有this关键字


###  继承的概述

​	Java中提供一个关键字extends，用这个关键字，我们可以让一个类和另一个类建立起继承关系。

```java
public class Student extends Person{}
```

​	Student称为子类(派生类)，Person称为父类（基类或超类）

#### 	好处

- 提高代码复用性
- 子类增加其他的功能，更加强大

#### 	使用条件

​	当类与类之间，存在相同（共性）的内容，并满足子类是父类中的一种，就可以考虑使用继承，来优化代码。

### 	继承的特点和继承体系的设计

- java只支持单继承，不继承多继承，但支持多层继承
- 每一个类都直接或者间接继承于Object
- **父类的private修饰子类不能继承，子类只能访问父类中的非私有成员**

### 子类继承父类中的内容

- 构造方法
  - 非私有和private都不能
- 成员变量
  - 非私有和private都能
  - private继承 但不能直接用
- 成员方法
  - 非私有能   private不能
  - **只有父类中的虚方法才能被子类继承！static修饰的成员方法不能被子类继承**
  - ![image-20240127170738706](D:\2024\Notes\Typora\Pictures\image-20240127170738706.png)
  - 虚方法表
    - 非priavte、static、final

### 继承中成员变量和成员方法的访问方法

#### 成员变量的访问特点

- 就近原则 先在局部位置找，本类成员位置找，父类成员位置找，逐级向上

- 出现重名成员变量

  ```java
  sout(name);
  sout(this.name);
  sout(super.name);
  ```

#### 成员方法的访问特点

- 就近原则 谁离我近 我就用谁

- super调用 直接访问父类

  ```java
  class Student extends Person{
  	public void lunch(){
  	//this先看本类，没有的话调用父类继承下来的
  		this.eat();
  		this.drink();
  		
  	//super直接调用父类的
  		super.eat();
  		super.drink();
  	}
  }
  ```

#### 方法的重写

当父类方法不能满足子类现在需求时，需要进行方法重写

- 书写格式

  - 在继承体系中，子类出现了和父类一模一样的方法声明，我们就称子类这个方法是重写的方法

- @Override重写注解

  - @Override是放在重写后的方法上，校验子类重写时语法是否正确

-  

  ```Java
  @Override
  public void eat(){
  	sout
  }
  ```

  <img src="D:\2024\Notes\Typora\Pictures\image-20240127193339506.png" alt="image-20240127193339506" style="zoom:50%;" />

  方法重写的本质是覆盖了虚方法表中的方法

- 方法重写注意事项

- ![image-20240127194019932](D:\2024\Notes\Typora\Pictures\image-20240127194019932.png)

### 继承中的构造方法和this super关键字

#### 构造方法中的访问特点

- **父类中的构造方法不会被子类继承**
- **子类中所有的构造方法默认先访问父类中的无参构造，再执行自己**
- <img src="D:\2024\Notes\Typora\Pictures\image-20240127200531564.png" alt="image-20240127200531564" style="zoom:33%;" />

#### this、super使用总结

<img src="D:\2024\Notes\Typora\Pictures\image-20240127202202823.png" alt="image-20240127202202823" style="zoom:33%;" />

### 认识多态

多态：同类型对象，表现出不同形态

**多态的表现形式：**

```java
父类类型 对象名称 = 子类对象；
```

**多态的前提：**

- 有继承关系

- 有父类引用指向子类对象

- 有方法重写

- ```java
  Fu f = new Zi();
  ```

**多态优势**

可以接受所有子类对象

### 多态中所有成员的特点

- 变量调用：编译看左边 运行也看左边
  - javac编译代码的时候，会看左边的父类中有没有这个变量，有的话编译成功
  - javac运行代码的时候，实际获取的就是左边父类中成员变量的值
- 方法调用：编译看左边 运行看右边
  - javac编译代码的时候，会看左边的父类中有没有这个方法，有的话编译成功
  - java运行代码时候，实际上运行的是子类中的方法

![image-20240129133449729](D:\2024\Notes\Typora\Pictures\image-20240129133449729.png)

### 多态的优势和弊端

#### 多态的优势

- 在多态形式下，右边对象可以实现解耦合，便于扩展和维护

  ```java
  Person p = new Student();
  p.work();
  //业务逻辑发生改变的时候，后续代码无需改变
  ```

- 定义方法的时候，使用父类型作为参数，可以接受所有子类对象，展现多态的扩展性及遍历

#### 多态的弊端

​	不能调用子类的特有功能

​	**解决方案**：变回子类类型就可以了

**instanceof**

<img src="D:\2024\Notes\Typora\Pictures\image-20240129134810031.png" alt="image-20240129134810031" style="zoom: 33%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240129135000874.png" alt="image-20240129135000874" style="zoom: 33%;" />

### 包和final

包就是文件夹 用来管理各种不同功能的Java类，方便后期代码维护

#### 包名规则

公司域名反写+包的作用 全部英文小写 见名知意 如com.heima.domain

![image-20240129144110559](D:\2024\Notes\Typora\Pictures\image-20240129144110559.png)

![image-20240129144236288](D:\2024\Notes\Typora\Pictures\image-20240129144236288.png)

- 使用同一个包中的类 和java.lang中的类时不需要导包，其他情况都需要
- 如果同时使用两个包中的类，需要使用全类名

#### final

- 修饰方法 **该方法是最终方法不能被重写**
- 修饰类     **该类是最终类 不能被继承**
- 修饰变量 **叫做常量 只能被赋值一次**

##### 常量

![image-20240129145230290](D:\2024\Notes\Typora\Pictures\image-20240129145230290.png)

### 权限修饰符和代码块

<img src="D:\2024\Notes\Typora\Pictures\image-20240129194518213.png" alt="image-20240129194518213" style="zoom:33%;" />

![image-20240129194808293](D:\2024\Notes\Typora\Pictures\image-20240129194808293.png)

实际开发中，一般只用private和public 成员变量私有，方法公开。特例：如果方法中的代码是抽取其他方法中共性代码，也私有

#### 代码块

- 局部代码块

  - 方法里面 提前结束变量生命周期

- 构造代码块

  <img src="D:\2024\Notes\Typora\Pictures\image-20240129195408403.png" alt="image-20240129195408403" style="zoom:30%;" />优先于构造方法实现

  ![image-20240129195756729](D:\2024\Notes\Typora\Pictures\image-20240129195756729.png)

- 静态代码块

  - 格式

    `static{}`

  - 特点

    需要通过static 关键字修饰，随着类的加载而加载，并且自动触发、只执行一次

  - 使用场景

     在类加载的时候，做一些数据初始化的时候使用

    ![image-20240129200121004](D:\2024\Notes\Typora\Pictures\image-20240129200121004.png)

  静态代码块是在类加载时执行的一段代码块，它用于初始化静态成员变量或执行其他需要在类加载时完成的操作。静态代码块在类被加载时只执行一次，并且在构造函数执行之前执行。

  静态代码块使用静态关键字来修饰，它的语法格式如下：

  ```
  javaCopy Codestatic {
      // 静态代码块的代码
  }
  ```

  静态代码块可以用于进行一些初始化操作，例如初始化静态变量或加载驱动程序等。它的执行顺序与出现的顺序相同，当类被加载时，静态代码块会按照出现的先后顺序执行。

  下面是一个示例代码，演示了如何使用静态代码块来初始化静态变量：

  ```java
  javaCopy Codepublic class MyClass {
      private static int count;
  
      static {
          count = 0;
          System.out.println("静态代码块执行");
      }
  
      public MyClass() {
          count++;
      }
  
      public static int getCount() {
          return count;
      }
  
      public static void main(String[] args) {
          MyClass obj1 = new MyClass();
          MyClass obj2 = new MyClass();
          System.out.println("对象数量：" + MyClass.getCount());
      }
  }
  ```

  上述代码中，静态代码块首先将 count 初始化为 0，并在执行时输出一条信息。然后，通过创建两个 MyClass 对象，每次创建对象时，count 会自增。最后，通过调用静态方法 getCount() 输出对象的数量。

  静态代码块在类加载时执行，所以会首先执行，输出的结果会是：

  ```java
  Copy Code静态代码块执行
  对象数量：2
  ```

![image-20240129201306271](D:\2024\Notes\Typora\Pictures\image-20240129201306271.png)

### 抽象类和抽象方法

#### 定义

- 抽象方法

  将共性的行为抽取到父类之后。由于每一个子类执行的内容是不一样，所以，在父类中不能确定具 体的方法体。该方法就可以定义为抽象方法

  ```java
  public abstract 返回值类型 方法名（参数列表）;
  ```

- 抽象类

  如果一个类中**存在抽象方法，那么该类就必须声明为抽象类**

  ```java
  public abstract class 类名{}
  ```

- 注意事项

  - 抽象类不能实例化
  - **抽象类中不一定有抽象方法 有抽象方法的类一定是抽象类**
  - 可以有构造方法
  - 抽象类的子类
    - 要么重写抽象类中的**所有**抽象方法
    - 要么抽象类

### 接口

接口是一种规则，是对行为的抽象

#### 接口的定义和使用

-  接口用关键字interface来定义

  `public interface 接口名{}`

- 接口不能实例化

- 接口和类之间是实现关系，通过implements关键字表示

  `public class 类名 implements 接口名{}`

- 接口的子类（实现类）

  - 要么重写接口中的所有抽象方法
  - 要么是抽象类

#### 注意事项

- 接口和类的实现关系，可以是单实现，也可以多实现

  `public class 类名 implements 接口1，接口2{}`

- 实现类还可以在继承一个类的同时实现多个接口

  `public class 类名 extends 父类 implements 接口1 接口2{}` 

  ```java
  public class Dog extends Animal implements Swim{...}
  ```

  ```java
  public interface Swim {
      public abstract void swim();
  }
  ```

#### 接口的细节

##### 接口中成员的特点

- 成员变量

  - 只能是常量
  - 默认修饰符public static final

- 构造方法 

  ​			没有

- 成员方法

  - jdk7前
    -  只能是抽象方法 
    -   默认修饰符public abstract

##### 接口和类之间的关系

<img src="D:\2024\Notes\Typora\Pictures\image-20240129212909812.png" alt="image-20240129212909812" style="zoom:33%;" />

#### JDK8开始接口中新增的方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240129213729784.png" alt="image-20240129213729784" style="zoom: 50%;" />

##### JDK8以后接口中新增的方法——默认方法

- 允许在接口中定义默认方法，需要用关键字default修饰
- 作用：解决接口升级的问题

- 接口中默认方法的定义格式

  ```java
  public default 返回值类型 方法名（参数列表）{}
  
  public default void show(){}
  ```

- 接口中默认方法的注意事项

  - 默认方法不是抽象方法，所以不强制被重写。但是如果被重写，重写的时候去掉default
  - public可以省略 **default不能省略**
  - 如果实现了多个接口，多个接口中存在相同名字的默认方法，子类就必须对该方法进行重写

- 在接口中定义默认方法有以下几个主要的用途：

  1. 向已有的接口添加新的方法：在接口中添加新方法会破坏已有的实现类，因为实现类需要实现接口中的所有方法。通过定义默认方法，可以在接口中添加新方法而不会破坏已有的实现类。默认方法提供了一个默认的方法实现，实现类可以选择性地重写默认方法。
  2. 提供接口的默认行为：默认方法为接口提供了默认的行为，这样在实现类中就不再需要强制实现该方法。这对于一些需要提供通用实现的接口非常有用。如果实现类没有自己特定的实现需求，就可以直接使用默认方法。
  3. 增强接口的功能：默认方法使得接口具有了一定的实现能力，相当于将接口从纯抽象变为了半抽象。通过在接口中定义默认方法，可以为接口添加一些通用的方法实现，从而提供更多的功能。
  4. 兼容旧版本代码：当一个接口需要添加新的方法时，如果直接修改接口，所有实现该接口的类都需要进行修改。而通过在接口中定义默认方法，可以避免对已有实现类的修改，从而保持与旧版本代码的兼容性。

  需要注意的是，默认方法并不是强制性的，实现类仍然可以选择性地重写默认方法，以满足自己的需求。默认方法主要是为了提供一种灵活的方式来扩展接口的功能，同时保持与已有代码的兼容性。

##### JDK8以后接口中新增的方法——静态方法

- 允许在接口中定义静态方法，需要用static修饰

- 接口中静态方法的定义格式

  ```java
  public static 返回值类型 方法名（参数列表）{}
  ```

- 接口中静态方法的注意事项

  - 静态方法**只能通过接口名调用**，不能通过实现类名或者对象名调用
  - public可以省略，static不能省略

#### JDK9新增的方法

​	接口中私有方法的定义格式

- 格式1

  ```java
  private 返回值类型 方法名（参数列表）{}
  private void show(){}
  //服务默认方法 不加default
  ```

- 格式2

  ```java
  private static 返回值类型 方法名（参数列表）{}
  private static void method(){}
  //服务静态方法
  ```

#### 接口的应用

- 接口代表规则，是行为的抽象。想要让哪个类拥有一个行为，就让这个类实现对应的接口就可以了

- 当一个方法的参数是接口时，可以传递接口所有实现类的对象，这种方式称为接口多态

- ```java
  接口类型 j = new 实现类对象();
  
  Swim s = new Swim(){
      @Override
      public void swim(){
          sout;
      }
  };
  ```

#### 适配器设计模式 

设计模式(Design pattern)就是解决问题的各种套路.适配器设计模式：解决接口与接口实现类之间的矛盾问题

##### 书写步骤

- 编写中间类XXXAdapter（最好用abstract防止创建对象）实现对应的接口，对接口中的抽象方法进行空实现。
- 让真正的实现类继承中间类，并重写需要用的方法

### 内部类

​	类的五大成员：**属性、方法、构造方法、代码块、内部类**

​	内部类：在一个类的里面再定义一个类 

<img src="D:\2024\Notes\Typora\Pictures\image-20240130090904908.png" alt="image-20240130090904908" style="zoom: 25%;" />

​	内部类的访问特点：

- 内部类可以直接访问外部类的成员，包括私有
- 外部类要访问内部类的成员，必须创建对象
- <img src="D:\2024\Notes\Typora\Pictures\image-20240130091213764.png" alt="image-20240130091213764" style="zoom:33%;" />

​	内部类分类：成员内部类、静态内部类、局部内部类、匿名内部类

#### 成员内部类（了解）

- 写在成员位置的，属于外部类的成员
- 成员内部类可以被一些修饰符所修饰
  - private、默认、proctected、static（变成静态内部类）、public

- 获取成员内部类对象  

  - 方式1：在外部类中编写方法，对外提供内部类对象

  ![image-20240130115307571](D:\2024\Notes\Typora\Pictures\image-20240130115307571.png)

  - 方式2：直接创建格式

    ```java
    外部类名.内部类名 对象名 = 外部类对象.内部类对象
    Outer.Inner abc = new Outer.new Inner();
    ```

-  <img src="D:\2024\Notes\Typora\Pictures\image-20240130120013190.png" alt="image-20240130120013190" style="zoom:50%;" />

- Outer.this相当于获取了外部类的地址

- <img src="D:\2024\Notes\Typora\Pictures\image-20240130120432809.png" alt="image-20240130120432809" style="zoom:50%;" />

#### 静态内部类（了解）

静态内部类只能访问外部类中的静态变量和静态方法，如果想要访问非静态的需要创建对象。

格式：

```java
外部类名.内部类名 对象名 = new 外部类名.内部类名();
Outer.Inner oi = new Outer.Inner;
```

调用非静态方法的格式：先创建对象 用对象调用

调用静态方法的格式：外部类名.内部类名.方法名();

#### 局部内部类（了解）

- 将内部类**定义在方法里面**就叫做局部内部类，类似于方法里面的局部变量

  public static final只能修饰成员变量，而不能修饰局部变量，局部内部同理

  <img src="D:\2024\Notes\Typora\Pictures\image-20240130144002980.png" alt="image-20240130144002980" style="zoom:33%;" />

  由于`method2()`是`Inner`类中的一个静态方法，而`Inner`类是在`show()`方法内部定义的局部内部类，所以无法通过对象调用该静态方法。只有通过类名才能调用静态方法。

- 外界是无法直接使用，需要在方法内部创建对象并使用。

- 该类**可以直接访问外部类的成员，也可以访问方法内的局部变量**

#### 匿名内部类（掌握）

匿名内部类本质上就是隐藏了名字的内部类,可写在成员位置，也可以写在局部位置

```java
new 类名或者接口名(){
	重写方法;
};
```

-  实现/继承关系
- 方法的重写
- 创建对象

```java
public static void method(Animal a){
	a.eat();
}

method(
	new Animal(){
		@Override
		public void eat(){
			System.out.println("狗吃骨头");
		}
	};
)
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240130151936558.png" alt="image-20240130151936558" style="zoom:25%;" />

## 常用API

### Math

<img src="D:\2024\Notes\Typora\Pictures\image-20240130152744770.png" alt="image-20240130152744770" style="zoom:33%;" />

### System

<img src="D:\2024\Notes\Typora\Pictures\image-20240130153316048.png" alt="image-20240130153316048" style="zoom:33%;" />

### ...

### Array

<img src="D:\2024\Notes\Typora\Pictures\image-20240130160130860.png" alt="image-20240130160130860" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240130160214618.png" alt="image-20240130160214618" style="zoom:75%;" />     

![image-20240130160559104](D:\2024\Notes\Typora\Pictures\image-20240130160559104.png)

<img src="D:\2024\Notes\Typora\Pictures\image-20240130160647771.png" alt="image-20240130160647771" style="zoom:67%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240130160728054.png" alt="image-20240130160728054" style="zoom:67%;" />

## 集合进阶

### **单列集合**顶层接口collection

![image-20240130162059038](D:\2024\Notes\Typora\Pictures\image-20240130162059038.png)

![image-20240201210521852](D:\2024\Notes\Typora\Pictures\image-20240201210521852.png)

![image-20240203093306737](D:\2024\Notes\Typora\Pictures\image-20240203093306737.png)List系列集合：添加的元素**有序（存取顺序一样)、可重复、有索引**的

Set系列集合：无序、不重复、无索引

![image-20240130162326850](D:\2024\Notes\Typora\Pictures\image-20240130162326850.png)

**Collection是一个接口，我们不能直接创建它的对象。只能创建它实现类的对象**

实现类：ArrayList

```java
Colletion<String> coll = new ArrayList<>();
```

#### 添加元素

List返回值永远true，Set中如已存在返回false

```java
coll.add("a");
```

#### 包含

```java
boolean result = coll.contain("b");
```

底层依赖equals方法进行判断是否存在的，所以，如果集合中存储的是自定义对象，也想通过contains方法来判断是否包含，那么在javabean类中，一定要重写equals方法。**因为object中的equals方法比较的是地址值**。

#### 迭代器遍历        

迭代器遍历不依赖索引

迭代器在Java中的类是Iterator，迭代器是集合专用的遍历方式

![image-20240130172303329](D:\2024\Notes\Typora\Pictures\image-20240130172303329.png)

<img src="D:\2024\Notes\Typora\Pictures\image-20240201123310499.png" alt="image-20240201123310499" style="zoom:50%;" />

##### 细节注意

- next报错NoSuchElementException
- 迭代器遍历完毕以后指针不会复位
- 循环中只能用一次next方法
- 迭代器遍历时，不能用集合的方法进行添加或者删除（迭代器会失效），应该用迭代器的办法删除

#### 增强for遍历

- 增强for底层就是迭代器

- 所有的单列集合和数组才能用增强for遍历

  ```java
  for(元素的数据类型 变量名：数组或者集合){
  }
  
  for(String s : list){
  	Sout
  }
  
  s为第三方变量，循环过程中一次表示每一个数据。对s进行更改与集合中数据更改没关系。
      
  快捷生成方式 集合名字+for 回车
  ```

- 修改增强for中的变量，不会改变集合中原来的数据

#### Lambda表达式遍历

![image-20240201130026101](D:\2024\Notes\Typora\Pictures\image-20240201130026101.png) 

Lambda表达式是Java 8引入的一个新特性，它可以使得代码更加简洁，易于阅读和编写。Lambda表达式是一种匿名函数，它没有名称，但可以传递参数和返回值，并且可以捕获在其范围内定义的变量。

Lambda表达式的语法非常简单，通常由三部分组成：参数列表、箭头符号和函数体。例如，下面是一个简单的Lambda表达式：

```
(x, y) -> x + y
```

这个Lambda表达式包含了两个参数x和y，箭头符号->表示参数列表到函数体的分隔符，函数体是一个简单的表达式，返回x和y的和。

对于单列集合的Lambda遍历，我们可以使用Java 8中新增的`forEach()`方法来实现。`forEach()`方法接收一个`Consumer`函数式接口作为参数，该接口定义了一个`accept()`方法用于接收集合中的每一个元素。

例如，我们有一个只包含整数的List集合，在Java 8之前，我们需要使用Iterator循环遍历集合：

```java
List<Integer> list = Arrays.asList(1, 2, 3);
for (Iterator<Integer> it = list.iterator(); it.hasNext();) {
    Integer i = it.next();
    System.out.println(i);
}
```

在Java 8中，我们可以使用Lambda表达式和`forEach()`方法来实现：

```java 
List<Integer> list = Arrays.asList(1, 2, 3);
list.forEach(i -> System.out.println(i));
```

在这个例子中，我们首先创建了一个只包含整数的List集合，然后使用`forEach()`方法和Lambda表达式遍历集合并输出每个元素。Lambda表达式`(i -> System.out.println(i))`接收一个参数i，表示集合中的每一个元素，函数体是一个简单的语句`System.out.println(i)`，用于输出元素的值。

希望这个简单的示例可以帮助您理解Lambda表达式和对单列集合的Lambda遍历。                                  

### List中常见的方法和五种遍历方式

- List是Collection的一种，Collection的方法List都继承了
- List集合因为有索引，所以多了很多索引操作办法

![image-20240201181401531](D:\2024\Notes\Typora\Pictures\image-20240201181401531.png)

**List是一个接口，我们不能直接创建它的对象。只能创建它实现类的对象**

- add 原来索引上的元素会依次后移

- <img src="D:\2024\Notes\Typora\Pictures\image-20240201181953109.png" alt="image-20240201181953109" style="zoom: 25%;" />删除元素 **手动装箱**

  在调用方法的时候，如果方法出现了重载现象 优先调用实参和形参类型一致的那个方法

#### List集合的遍历方式

##### 迭代器遍历

```java
Iterator<String> it = list.iterator();
while(it.hasNext()){
	String str = it.next();
	Sout(str);
}
```

##### 列表迭代器遍历

```java
//获取一个列表迭代器的对象，里面的指针默认也是指向0索引的

//额外添加一个方法：在遍历过程中，可以添加元素
ListIterator<String> it = list.listIterator();
while(it.hasNext()){
    String str = it.next();
    if("bbb".equals(str)){
        it.add("qqq");
    }
    sout(str);
}
```

##### 增强for遍历

```java
for(String s : list){
	sout(s);
}
```

##### Lambda表达式遍历

```java
list.forEach(new Consumer<String>(){
	@Override
	public void accept(Stirng s){
		sout(s);
	}
});


------->
 list.forEach((Stirng s)->{
		sout(s);
	}
);

------->
list.forEach(s->sout(s));
```

##### 普通for循环遍历（因为List集合存在索引）

```java
for(int i = 0; i < list.size(); i++){
	String s = list.get(i);
	sout(s);
}
```

##### 小结

<img src="D:\2024\Notes\Typora\Pictures\image-20240201190934829.png" alt="image-20240201190934829" style="zoom:33%;" />

#### ArrayList集合

<img src="D:\2024\Notes\Typora\Pictures\image-20240201195958756.png" alt="image-20240201195958756" style="zoom:33%;" />

ArrayList添加多个数据

**ArrayList集合底层原理**

<img src="D:\2024\Notes\Typora\Pictures\image-20240201200309581.png" alt="image-20240201200309581" style="zoom:33%;" />

#### LinkedList

- 底层数据结构是双链表，查询慢，增删快。但是如果操作的是首尾元素，也是快的

<img src="D:\2024\Notes\Typora\Pictures\image-20240201200814749.png" alt="image-20240201200814749" style="zoom:33%;" />

### 泛型类、泛型方法、泛型接口

泛型：JDK5中引入的特性，可以在**编译**阶段约束操作的数据类型，并进行检查

泛型的格式：<数据类型>

注意：泛型只能支持**引用数据类型**

指定泛型的具体类型后，传递数据的时候，可以传入该类类型的子类类型（不过最好不要）

#### 泛型类——类后面

当一个类中 某个变量的数据类型不确定时，就可以定义为泛型类

```java
修饰符 class 类名<类型>{

}

public class ArrayList<E>{//KTVE
	//创建该类对象时，E就确定
}
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240201204154473.png" alt="image-20240201204154473" style="zoom: 50%;" />

#### 泛型方法——方法上面

<img src="D:\2024\Notes\Typora\Pictures\image-20240201204607607.png" alt="image-20240201204607607" style="zoom: 25%;" />

```java
修饰符<类型> 返回值类型 方法名（类型 变量名）{

}
```

#### 泛型接口——接口后面

```java
修饰符 interface 接口名<类型>{
}
public interface List<E>{
}
```

如何使用带泛型接口？

- 实现类给出具体类型
- 实现类延续泛型，创建对象时再确定

#### 泛型的 通配符

- 泛型不具备继承性，但数据具备继承性

<img src="D:\2024\Notes\Typora\Pictures\image-20240201205744870.png" alt="image-20240201205744870" style="zoom: 50%;" />

通配符

<img src="D:\2024\Notes\Typora\Pictures\image-20240201210229526.png" alt="image-20240201210229526" style="zoom:50%;" />

```java
public static void method(ArrayList<? extends Ye> list){}
```

### 红黑树、红黑规则、添加树处理方案

红黑树：

- 是一个二叉查找树
- 不是高度平衡的
- 条件：特有的红黑规则

红黑规则：

- 每一个节点或红或黑
- 根节点必须是黑色
- 如果一个节点没有子节点或者父节点，则该节点相应的指针属性值为Nil，这些Nil视为叶节点，是黑色的
- 如果某一个节点是红色，子节点必须是黑色（不能出现两个红色节点相 邻）
- 对每一个节点，从该节点到其所有后代叶节点的简单路径上，均包含相同数目的黑色节点

添加树：

- 默认颜色：**添加节点默认是红色的（效率高）**
- 添加条件就一句话，如果一个节点按照查找二叉树排序后插入，其父节点和子节点均不为红色，也不是叶子或空，则他自身为红色

![image-20240202202552406](D:\2024\Notes\Typora\Pictures\image-20240202202552406.png)

### Set系列集合

- HashSet 无序、不重复、无索引
- LinkedHashSet 有序、不重复、无索引
- TreeSet 可排序、 不重复、无索引

Set接口中的方法基本上与Collection中的API一致

**Set是一个接口，我们不能直接创建它的对象。只能创建它实现类的对象**

#### HashSet

底层采用哈希表存储数据

- 如果想要集合中的元素可重复
  - 用ArrayList集合，基于数组的
- 如果想要对集合中的元素去重
  - 用HashSet集合，基于哈希表

哈希表

- JDK8 数组+链表
- JDK8 数组+链表+红黑树

<img src="D:\2024\Notes\Typora\Pictures\image-20240202212225672.png" alt="image-20240202212225672" style="zoom: 33%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240202212311293.png" alt="image-20240202212311293" style="zoom:33%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240202214038675.png" alt="image-20240202214038675" style="zoom:33%;" />

自定义对象**必须重写hashCode和equals！！！！！！！！**

<img src="D:\2024\Notes\Typora\Pictures\image-20240202214332531.png" alt="image-20240202214332531" style="zoom:33%;" />

###### LinkedHashSet

有序：保证存储和取出的元素顺序一致

![image-20240202215147680](D:\2024\Notes\Typora\Pictures\image-20240202215147680.png)

#### TreeSet

底层基于红黑树的数据结构实现排序的,增删改查性能良好

##### 排序方式1：

​	默认排序/自然排序：Javabean类实现Comparable接口指定比较规则![image-20240203091538585](D:\2024\Notes\Typora\Pictures\image-20240203091538585.png)

#####  排序方式2：

​	比较器排序：创建TreeSet对象时，传递比较器Comparator制定规则（快捷键 小括号里Ctrl+p）

<img src="D:\2024\Notes\Typora\Pictures\image-20240203092311300.png" alt="image-20240203092311300" style="zoom:33%;" />

###### 使用原则：

​	默认使用第一种，如果第一种不能满足当前需求，就使用第二种

### 双列集合的特点        



- 双列集合一次需要存一对数据，分别为键和值
- 键不能重复，值可以重复
- 键和值是一一对应的，每一个键只能找到自己对应的值
- 键+值这个整体 称为“键值对” “键值对对象”，在Java中叫做Entry对象

### 双列集合顶层接口MAP

![image-20240203164405809](D:\2024\Notes\Typora\Pictures\image-20240203164405809.png)

#### Map集合常用的API

<img src="D:\2024\Notes\Typora\Pictures\image-20240201212059827.png" alt="image-20240201212059827" style="zoom: 33%;" />

**Map是一个接口，我们不能直接创建它的对象。只能创建它实现类的对象**

##### put方法的细节 添加/覆盖

- 添加数据的时候，如果见不存在，那么直接添加，方法返回null
- 如果键是存在的，把原有键值对对象覆盖，会把覆盖的值进行返回

#### Map的遍历方式

##### 键找值

```java
Map<String,String> map = new HashMap<>();

map.put("","");

Set<String> keys = map.keySet();
for(String key : keys){
    String value = map.get(key);
    sout;
}
```

##### 键值对

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

Map<String,String> map = new HashMap<>();
map.put("","");

Set<Map.Entry<String, String>> entries = map.entrySet();
//左边部分由map.entrySet Ctrl+alt+v 自动生成

for(Map.Entry<String, String> entry :entries){
    String key = entry.getKey();
    String value = entry.getValue();
    sout;   
}


//链式编程改写
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

Map<String,String> map = new HashMap<>();
map.put("","");

for(Map.Entry<String, String> entry :map.entrySet();){
    String key = entry.getKey();
    String value = entry.getValue();
    sout;   
}
```

##### Lambda表达式

![image-20240203163719380](D:\2024\Notes\Typora\Pictures\image-20240203163719380.png)

```java
map.forEach(new BiConsumer<String,String(){
    @Override
    public void accept(String key, String value){
        sout;
    }
});

lambda快捷键 alt + ins
->
map.firEach((key,value)->sout));
```

#### HashMap的基本使用

- HashMap是Map里面的一个实现类
- 没有额外需要学习的特有方法，直接使用Map里面方法就可以了
- 特点都是**由键决定：无序、不重复、无索引**
- 依赖HashCode和equals方法，如果**键存储的是自定义对象，需要重写**
- HashMap跟HashSet底层原理一样，都是哈希表结构，底层原理：利用**键计算**哈希值，与值无关

#### LinkedHashMap

- 特点都是**由键决定：有序、不重复、无索引**。这里的有序**保证存储和取出的元素顺序一致**
- 原理：底层数据结构依然是哈希表，只是每个键值对元素有额外多了一个双链表的机制存储顺序                                                     

#### TreeMap

- TreeMap和TreeSet底层一样都是红黑树
- 由键决定：可排序、不重复、无索引
- 默认按照键从小到大排序，也可以自己规定键的排序规则

- 实现Comparable接口，指定比较规则
- 创建集合时传递Comparator比较器对象，指定比较规则

### 可变参数

Jdk5 可变参数 底层是java自动创建好的数组

方法形参的个数是可以发生变化的 0 1 2 3...

格式：属性类型...名字 `int...args`

```java
psvm{
	getsum(1,2,3);
}

public static int getsum(int...args){
	int sum = 0;
    for (int i : args){
        sum+=i;
    }
    return sum;
}
```

**注意事项**

- 形参列表中最多有一个可变参数
- 可变参数必须放在形参列表的最后一个

### 集合工具类Collections

 java.util.Collections 是集合工具类

作用 Collections不是集合 而是集合的工具类

```java
import java.util.ArrayList;
import java.util.Collections;

public class ShuffleExample {
    public static void main(String[] args) {
        // 创建一个列表
        ArrayList<String> list = new ArrayList<>();
        list.add("apple");
        list.add("banana");
        list.add("cherry");

        // 打印原始列表
        System.out.println("原始列表：" + list);

        // 使用Collections shuffle方法打乱列表
        Collections.shuffle(list);

        // 打印打乱后的列表
        System.out.println("打乱后的列表：" + list);
    }
}

```

Collections常用的API

<img src="D:\2024\Notes\Typora\Pictures\image-20240203214830964.png" alt="image-20240203214830964" style="zoom:33%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240203215013721.png" alt="image-20240203215013721" style="zoom:33%;" />

### 不可变集合超详解

不可变集合：不能被修改的集合

在List、Set、Map接口中，都存在静态的of方法，可以获取一个不变的集合。

<img src="D:\2024\Notes\Typora\Pictures\image-20240204084448191.png" alt="image-20240204084448191" style="zoom: 50%;" />

```java
List<String> list = list.of("zxs");
sout(list.get(0));
```

- 当我们要获取一个不可变的Set集合时，里面的参数一定要保证唯一性

- 创建Map的不可变集合

  - 键是不能重复的

  - Map里面的of方法，参数是有上限的，最多只能传递20个参数，10个键值对

  - 如果传递大于10个键值对

  - ![image-20240204094110808](D:\2024\Notes\Typora\Pictures\image-20240204094110808.png)

  - ![image-20240204094047207](D:\2024\Notes\Typora\Pictures\image-20240204094047207.png)

    简化

    ```java
    Map.ofEntries(hm.entrySet().toArrat(new Map.Entry[0]))
    ```

    简化

    ```java
    Map<String,String> map = Map.copyOf(hm);
    
    map.put("bbb","aaaa");
    ```

- <img src="D:\2024\Notes\Typora\Pictures\image-20240204094958918.png" alt="image-20240204094958918" style="zoom:50%;" />

## Stream流

### Stream流的思想和获取Stream流

<img src="D:\2024\Notes\Typora\Pictures\image-20240204100103728.png" alt="image-20240204100103728" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240204100719387.png" alt="image-20240204100719387" style="zoom:50%;" />

![image-20240204101202690](D:\2024\Notes\Typora\Pictures\image-20240204101202690.png)

- stream接口中的静态方法of的细节
  - 方法的形参是一个可变参数，可以传递一堆零散的数据，也可以传递数组
  - 但是数组必须是引用数据类型的，如果传递基本数据类型，会把整个数组当作一个元素，放到stream流中

### Stream流的中间方法

**<img src="D:\2024\Notes\Typora\Pictures\image-20240204140441850.png" alt="image-20240204140441850" style="zoom:50%;" />**

<img src="D:\2024\Notes\Typora\Pictures\image-20240204141528755.png" alt="image-20240204141528755" style="zoom:50%;" />

- distinct 元素去重，**依赖HashCode和Equals方法**

- concat ab两个流最好数据类型一致 不然合并以后生成父类 子类中的特有功能无法使用

- map  第一个类型：流中原本的数据类型 第二个类型：要转成之后的类型 当map方法执行完毕之后，流上的数据就变成了整数

  <img src="D:\2024\Notes\Typora\Pictures\image-20240204142609079.png" alt="image-20240204142609079" style="zoom:50%;" />

- ```java
  //“张无忌-15”
  list.stream().map(new Function<String,Integer>(){
      @Override
  	public Integer apply(String s){
          String[] arr = s.split("-"); 切割函数
          String ageString = arr[1];//15
          int age = Integer.parseInt(ageString);
       	return age;
      }
  }).forEach(s->sout());
  
  ```

### Stream流的终结方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240204143803097.png" alt="image-20240204143803097" style="zoom: 50%;" />

终结方法以后不能在使用中间方法

#### toArray

![image-20240204144519821](D:\2024\Notes\Typora\Pictures\image-20240204144519821.png)

#### 收集方法collect超详解

<img src="D:\2024\Notes\Typora\Pictures\image-20240204151155214.png" alt="image-20240204151155214" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240204151228316.png" alt="image-20240204151228316" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240204151252134.png" alt="image-20240204151252134" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240204151302032.png" alt="image-20240204151302032" style="zoom:50%;" />

lambda简化

<img src="D:\2024\Notes\Typora\Pictures\image-20240204151418301.png" alt="image-20240204151418301" style="zoom:50%;" />

### 重写toString()方法

Java所有类都是object的子类。所以，所有的Java对象都可以调用[object类](https://so.csdn.net/so/search?q=object类&spm=1001.2101.3001.7020)提供的方法。其中，toString()就是其中一个。下面讲解一下为什么会有重写toString()方法一说。

首先，我们先来创建一个Persion类，它只简单的包含 firstname 和 lastname，当然，加上它的setter 和 getter 法。放在com.bean包下：

```java
package com.bean;
 
public class Persion {
	private String firstname;
	private String lastname;
	public String getFirstname() {
		return firstname;
	}
	public void setFirstname(String firstname) {
		this.firstname = firstname;
	}
	public String getLastname() {
		return lastname;
	}
	public void setLastname(String lastname) {
		this.lastname = lastname;
	}
 
}
```

 接下来，新建一个类，名为Test，让它包含main函数：

```java
package com.override;
 
import com.bean.Persion;
public class Test {
 
	public static void main(String[] args) {
		Persion p = new Persion();
		p.setFirstname("Fire");
		p.setLastname("Water");
		System.out.println(p.toString());
	}
 
}
```

 可以看到，运行之后，结果为：

```java
com.bean.Persion@c17164
```

 　注：打印一个对象，可以直接System.out.println(p);其实java会自动调用p 的 toString() 方法。

 但是我们一般想要的结果并不是这样，因为object类的toString()方法总是返回对象的实现类类名 + @ + hashCode值。这显然不能满足我们的需求。像这里，我们是希望能打印出p的全名出来，这时，就需要重写toString()方法，因为重写了toString()之后，那么p在调用toString()方法的时候，会优先调用自己类里的toString()方法。

修改后的类如下：

```java
package com.bean;
 
public class Persion {
	private String firstname;
	private String lastname;
	public String getFirstname() {
		return firstname;
	}
	public void setFirstname(String firstname) {
		this.firstname = firstname;
	}
	public String getLastname() {
		return lastname;
	}
	public void setLastname(String lastname) {
		this.lastname = lastname;
	}
                //重写toString方法
	public String toString(){
		return firstname + " " + lastname;
	}
}
```

这时，打印出来的结果就是

```java
Fire Water
```

## 方法引用

- 把已经有的方法拿过来用，当作函数式接口中抽象方法的方法体
- ::方法引用符
- 注意事项
  - 需要有函数式接口
  - 被引用方法必须已经存在
  - 被引用方法的形参和返回值需要跟抽象方法保持一致
  - 被引用方法的功能要满足当前需求

```java
Arrays.sort(arr,FunctionDemo1::subtraction);
sout(Arrays.toString(arr));

public static int subtraction(int num1,int num2){
return num2-num1;
}
```

### 方法引用的分类

- 引用静态方法
- 引用成员方法
  - 引用其他类的成员方法
  - 引用本类的成员方法
  - 引用父类的成员方法
- 引用构造方法
- 其他调用方式
  - 使用类名引用成员方法
  - 引用数组的构建方法

![image-20240204201046381](D:\2024\Notes\Typora\Pictures\image-20240204201046381.png)

### 引用静态方法

```java
类名::静态方法
Integer::parseInt
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240204160108505.png" alt="image-20240204160108505" style="zoom:50%;" />

### 引用成员方法

```java
对象::成员方法
```

其他类

```
其它类对象::方法名
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240204160721587.png" alt="image-20240204160721587" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240204160731140.png" alt="image-20240204160731140" style="zoom:50%;" />

本类

```java
this::方法名（引用处不能是静态方法）
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240204160839650.png" alt="image-20240204160839650" style="zoom:50%;" />

例如上面在psvm里面调用 pscm是静态 所以不能用this

父类

```
super::方法名（引用处不能是静态方法）
```

### 引用构造方法

```java
类名::new
Student::new
```

### 使用类名引用成员方法

```java
类名::成员方法
String::substring
```

引用规则

- 需要有函数式接口
- 被引用方法必须已经存在
- **被引用方法的形参 需要跟抽象方法的第二个形参到最后一个形参保持一致**
- 被引用方法的功能要满足当前需求

抽象方法形参的详解：

- **第一个参数**：表示被引用方法的调用者，**决定了可以引用哪些类中的方法**。在Stream流中，第一个参数一般都表示流中的每一个数据。假设流中的数据是字符串，那么使用类名引用成员方法，只能引用String这个类中的方法
- 第二个参数到最后一个参数：跟被引用方法的形参保持一致，如果没有第二个参数，说明被引用的方法需要是无参的方法。

![image-20240204200133732](D:\2024\Notes\Typora\Pictures\image-20240204200133732.png)

### 引用数组的构建方法

```java
数据类型[]::new
int[]::new
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240204200828673.png" alt="image-20240204200828673" style="zoom:50%;" />

数组的类型，需要和流中的数据类型保持一致

### 实例

![image-20240204201749759](D:\2024\Notes\Typora\Pictures\image-20240204201749759.png)

## 异常 

###  体系介绍

<img src="D:\2024\Notes\Typora\Pictures\image-20240204205128291.png" alt="image-20240204205128291" style="zoom:33%;" />

### 编译时异常和运行时异常

<img src="D:\2024\Notes\Typora\Pictures\image-20240204205533741.png" alt="image-20240204205533741" style="zoom:33%;" />

### 异常的两个作用

- 异常是用来查询bug的关键参考信息
- 异常可以作为方法内部的一种特殊返回值，一边通知调用值底层的执行情况

### 异常的处理方式

#### JVM默认的处理方式

- 把异常的名称、异常原因及异常出现位置等信息输出在控制台
- 程序停止执行，后面代码不执行

#### 自己处理（捕获异常）

```java
try {
	可能出现异常的代码;
} catch(异常类名 变量名){
	异常的处理代码;
} 
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240204213041277.png" alt="image-20240204213041277" style="zoom: 50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240204214654911.png" alt="image-20240204214654911" style="zoom: 33%;" />

##### 异常中的常见方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240204214905675.png" alt="image-20240204214905675" style="zoom:33%;" />

**错误地输出语句——用来打印错误信息**

```java
System.err.prinln(123);
```

#### 抛出异常

- throws throw

![image-20240204215708914](D:\2024\Notes\Typora\Pictures\image-20240204215708914.png)

![image-20240204220137442](D:\2024\Notes\Typora\Pictures\image-20240204220137442.png)

### 自定义异常

- 定义异常类
- 写继承关系
  - 运行时：继承RuntimeException 表示由于参数错误而导致的问题
  - 编译时：继承Exception 提醒程序员检查本地信息
- 空参构造
- 带参构造

为了让控制台的报错信息见名知意

## File

<img src="D:\2024\Notes\Typora\Pictures\image-20240205091144148.png" alt="image-20240205091144148" style="zoom: 33%;" />

### File的成员方法(判断、获取)

<img src="D:\2024\Notes\Typora\Pictures\image-20240205091954416.png" alt="image-20240205091954416" style="zoom:33%;" />

#### length 

- 只能获取文件的大小，单位是字节
  - 如果单位是M G，可以不断÷1024
- 无法获取文件夹的大小 
  - 如果要获取文件夹大小，需要把文件夹里面的文件大小都加在一起

#### lastModified

```java
File f9 = new File(pathname);
long time = f9.lastModified();

SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH：mm：ss");
String dataStr=sdf.format(time);
```

### File的成员方法(创建、删除)

<img src="D:\2024\Notes\Typora\Pictures\image-20240205093138121.png" alt="image-20240205093138121" style="zoom: 50%;" />

#### creatNewFile

- 如果当前路径表示的文件是存在的，创建失败，方法返回false
- 如果父级路径是不存在的，那么方法会有日常IOException
- creatNewFile创建的一定是文件，如果路径中不包括后缀名，则创建一个没有后缀的文件

#### mkdir

- window路径唯一，如果当前路径已经存在，则创建失败，返回false
- mkdir方法只能创建单极文件夹，不能创建多级文件夹

#### mkdirs

#### delete

- **只能删除文件和空文件夹**

### File的成员方法（获取并遍历）

```java
public File[] listfiles() 
//获取当前该路径下所有文件
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240205094345921.png" alt="image-20240205094345921" style="zoom: 50%;" />

- 当调用者File表示的路径不存在时 返回null
- 当调用者File表示的路径是文件时 返回null
- 当调用者File表示的路径是一个空文件夹时，返回一个长度为0的数组
- 当调用者File表示的路径是一个有内容的文件夹时，将里面所有文件和文件夹的路径放在File数组中返回
- 当调用者File表示的路径是一个有隐藏文件的文件夹时，将里面所有文件和文件夹的路径放在File数组中返回，包括隐藏文件
- 当调用者File表示的路径是需要权限才能访问的文件夹时，返回null

<img src="D:\2024\Notes\Typora\Pictures\image-20240206164747060.png" alt="image-20240206164747060" style="zoom:50%;" />

## IO流

存储和读取数据的解决方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240206170228699.png" alt="image-20240206170228699" style="zoom: 33%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240206170611911.png" alt="image-20240206170611911" style="zoom:33%;" />

outputstream是抽象流

### 字节流

#### FileOutputStream

操作本地文件的字节输出流 可以把程序中的数据写到本地文件中

步骤

- 创建字节输出流对象
  - 参数是字符串表示的**路径** 或者File对象都可以
  - 如果文件不存在会创建一个新文件**，但是父级路径必须存在**
  - 如果文件已经存在则**清空文件**
- 写数据
  - write方法的参数是整数，是机械到文件中的是ascii码对应的
- 释放资源
  - 每次使用完流之后都要释放资源

```java
FileOutStream fos = new FileOoutStream("myio\\a.txt");

fos.write(97);
fos.close();

//a.txt: a
```

##### FileOutputStream写数据的三种方式

![image-20240206171749495](D:\2024\Notes\Typora\Pictures\image-20240206171749495.png)

```java
1
    byte[] bytes = {97,98,99};
    fos.write(bytes);

2
    String str = "abcdefg";
    byte[] bytes = str.getBytes();
    fos.write(bytes);
```

```java
3
    void write(byte[] b, int off, int len);
off(起始位置)
```

##### 换行和续写

- 换行写: 再次写出一个换行符就可以了
  - windows：\r\n
  - **fos.write(10)**
  - ![image-20240206172615555](D:\2024\Notes\Typora\Pictures\image-20240206172615555.png)
  - Linux：\n
  - Max: \r
- 续写：
  - write第二个参数写true（不写时默认false清空）

#### FileInputStream

步骤

- 创建字节输入流对象

  - 文件不存在，直接报错

- 读数据

  - 一次读一个字节，读出来的是数据在ASCII对应的数字
  - //一个字符一个字符读，读到文件末尾返回-1

  ```java
  int b1 = fis.read();
  
  sout(b1);
  //97
  ```

- 释放资源

  - 每次使用完流必须要释放资源

#### 文件拷贝的基本代码

<img src="D:\2024\Notes\Typora\Pictures\image-20240206200758525.png" alt="image-20240206200758525" style="zoom:50%;" />

##### 文件拷贝的弊端和解决方案

<img src="D:\2024\Notes\Typora\Pictures\image-20240206201126992.png" alt="image-20240206201126992" style="zoom:50%;" />

```java
//byte[] bytes = new byte[2];
byte[] bytes = new byte[1024*1024*5];
int len = fis.read(bytes)*
sout(new String(bytes, 0,* len));
```

#### IO流中不同JDK版本捕获异常的方式

<img src="D:\2024\Notes\Typora\Pictures\image-20240206204151138.png" alt="image-20240206204151138" style="zoom: 33%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240206205400148.png" alt="image-20240206205400148" style="zoom:33%;" />

### 字符集详解

<img src="D:\2024\Notes\Typora\Pictures\image-20240206212326546.png" alt="image-20240206212326546" style="zoom:33%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240206214351537.png" alt="image-20240206214351537" style="zoom:33%;" />

#### 为什么会有乱码

- 不要用字节流读取文本文件
- 编码解码时使用同一个码表 同一个编码方式

#### Java中编码和解码的代码实现

<img src="D:\2024\Notes\Typora\Pictures\image-20240206214749382.png" alt="image-20240206214749382" style="zoom:33%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240206215906689.png" alt="image-20240206215906689" style="zoom: 50%;" />

### 字符流

#### FileReader

字符流 = 字节流+字符集

特点

- 输入流 一次读一个字节，遇到中文时，一次读多字节
- 输出流 底层会把数据按照指定的编码方式进行编码，变成字节再写到文件中

 使用场景

 	对纯文件文本进行读写操作

<img src="D:\2024\Notes\Typora\Pictures\image-20240207141011446.png" alt="image-20240207141011446" style="zoom: 33%;" />

1. 创建字符输入流对象

   ```java
   public FileReader(File file)
   //创建字符输入流关联本地文件
       
   public FileReader(String pathname)
   //创建字符输入流关联本地文件
   ```

   ​	如果文件不存在，直接报错

2. 读取数据

   ```java
   public int read()
   //读取数据，读到末尾-1，返回整数，输出时需要强转
   
   public int read(char[] buffer)
   //读取多个数据读到buffer里 末尾-1
   //读取数据，解码，强转三步合并
   ```

   ​	按字节进行读取，遇到中文，一次读多个字节，**读取后解码，返回一个整数**（GBK一次读两个字节，UTF-8一次读三个字节）

   ​	读到文件末尾，read返回-1

3. 释放资源

   ```java
   public int close()
   //释放资源、关流
   ```

#### FileWriter

#### 构造方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240207143843501.png" alt="image-20240207143843501" style="zoom:50%;" />

构造方法1、2会清空文件内容

#### 成员方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240207144006135.png" alt="image-20240207144006135" style="zoom:50%;" />

#### 字符流原理解析

<img src="D:\2024\Notes\Typora\Pictures\image-20240207144822693.png" alt="image-20240207144822693" style="zoom: 33%;" />

##### flush和close方法

```java
public void flush()	将缓冲区中的数据，刷新到本地文件中
    flush刷新 刷新之后，还可以继续往文件中写数据
    
public void close() 释放资源、关流
    close关流 断开通道 无法再往文件中写出数据
```

### 缓冲流

<img src="D:\2024\Notes\Typora\Pictures\image-20240207145608813.png" alt="image-20240207145608813" style="zoom:33%;" />

#### 字节缓冲流

<img src="D:\2024\Notes\Typora\Pictures\image-20240207150137335.png" alt="image-20240207150137335" style="zoom:33%;" />

#### 字符缓冲流

##### 构造方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240207154333756.png" alt="image-20240207154333756" style="zoom:33%;" />

##### 特有方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240207154546591.png" alt="image-20240207154546591" style="zoom:33%;" />

readline方法读取的时候，一次读一整行，遇到回车换行结束，但是不会把回车换行读到内存中

<img src="D:\2024\Notes\Typora\Pictures\image-20240207160655328.png" alt="image-20240207160655328" style="zoom:33%;" />

### 转换流

![image-20240212112502676](D:\2024\Notes\Typora\Pictures\image-20240212112502676.png)

#### 输入

```java
InputStreamReader isr = new InputStreamReader(new FileInputStream("my\\file.txt",GBK));

int ch;
while((ch = isr.read())!=-1){
	sout((char)ch);
}

isr.close();               

//利用转换流按照指定字符编码读取（了解）
//JDk 11 替代方案：
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240207163022315.png" alt="image-20240207163022315" style="zoom: 67%;" />

```java
FileReader fr = new FileReader("my\\file.txt",Charset.forName("GBK"));

int ch;
while(ch = fr.read() != -1){
	sout((char)ch);
}

isr.close();
```

#### 输出

<img src="D:\2024\Notes\Typora\Pictures\image-20240207163523204.png" alt="image-20240207163523204" style="zoom: 50%;" />

### 序列流

利用序列化流/对象操作输出流 可以把Java中的对象写到本地中

```java
public ObjectOutputStream(OutputStream out)
//把基本流包装成高级流

public final void writeObject(Object obj)
//把对象序列化（写出）到文件中
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240207165710680.png" alt="image-20240207165710680" style="zoom: 67%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240207170024252.png" alt="image-20240207170024252" style="zoom: 67%;" />

### 反序列化流

可以把序列化到本地的对象 读取到程序中来

```java
public ObjectInputStream(InputStream out)
//把基本流包装成高级流

public Object readObject(Object obj)
//把对象序列化（读）到文件中
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240207171848241.png" alt="image-20240207171848241" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240207173133546.png" alt="image-20240207173133546" style="zoom: 33%;" />

### 序列化/反序列化流细节

<img src="D:\2024\Notes\Typora\Pictures\image-20240207174128496.png" alt="image-20240207174128496" style="zoom:33%;" />

### 字节打印流

​	分类

- PrintStream PrintWriter

​	特点

- 打印流只操作文件目的地，不操作数据源

- **特有的写出方法**可以实现，数据原样写出

  打印97 文件97

  打印true文件true

- **特有的写出方法**可以实现自动刷新，自动换行

  打印一次数据：写出+换行+刷新

#### 构造方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240207195300614.png" alt="image-20240207195300614" style="zoom:33%;" />

3.4：字节流底层**没有缓冲区，无所谓自动刷新与否**

#### 成员方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240207195422595.png" alt="image-20240207195422595" style="zoom:33%;" />

### 字符打印流

**字符流底层有缓冲区，想要自动刷新需要开启**、

<img src="D:\2024\Notes\Typora\Pictures\image-20240207200215943.png" alt="image-20240207200215943" style="zoom:33%;" />

```java
PrintStream ps = System.out;
ps.println("123");
ps.close();
```

<img src="D:\2024\Notes\Typora\Pictures\image-20240207205414700.png" alt="image-20240207205414700" style="zoom: 50%;" />

### 解压缩流

解压本质：把每一个ZipEntry按照层级拷贝到另一个本地文件夹中

<img src="D:\2024\Notes\Typora\Pictures\image-20240207210134889.png" alt="image-20240207210134889" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240207210051410.png" alt="image-20240207210051410" style="zoom:50%;" />

### 压缩流

把每一个（文件/文件夹）看成ZipEntry对象放到压缩包中

- 压缩单个文件

<img src="D:\2024\Notes\Typora\Pictures\image-20240207210515896.png" alt="image-20240207210515896" style="zoom:50%;" />

- 压缩文件夹

<img src="D:\2024\Notes\Typora\Pictures\image-20240207210603590.png" alt="image-20240207210603590" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240207210629892.png" alt="image-20240207210629892" style="zoom:50%;" />

### 常用工具包 

#### Commons-io

<img src="D:\2024\Notes\Typora\Pictures\image-20240207210745078.png" alt="image-20240207210745078" style="zoom:33%;" />

**使用步骤**

- 在项目中创建一个文件夹：lib（new directory）
- 将jar包复制粘贴到lib文件夹
- 右键点击jar包，选择Add as Library ->点击ok
- 在类中导包使用

<img src="D:\2024\Notes\Typora\Pictures\image-20240207212057573.png" alt="image-20240207212057573" style="zoom: 50%;" />

#### hutool

<img src="D:\2024\Notes\Typora\Pictures\image-20240207212255783.png" alt="image-20240207212255783" style="zoom: 33%;" />

IO工具类

<img src="D:\2024\Notes\Typora\Pictures\image-20240207212308480.png" alt="image-20240207212308480" style="zoom:33%;" />

## 多线程

- 进程 **程序的基本执行实体**
- 线程 **操作系统能够进行运算调度的最小单位**，被包含在进程中，是进程中的实际运作单位

### 并发和并行

1. 并发：**在同一时刻 有多个指令 在单个CPU上 交替执行**
2. 并行：**在同一时刻 有多个指令 在多个CPU上 同时执行**

### 实现方式

- 继承Thread类方式进行实现
- 实现Runnable接口方式进行实现
- 利用Callable接口和Future接口方式进行实现

<img src="D:\2024\Notes\Typora\Pictures\image-20240211112702164.png" alt="image-20240211112702164" style="zoom: 33%;" />

#### 多线程的第一种实现方式

继承Thread类方式进行实现

- 自己定义一个类继承Thread
- 重写run方法（书写线程要执行代码）
- 创建子类对象 并启动线程（start），run的话只是调用方法不会创建新线程执行

<img src="D:\2024\Notes\Typora\Pictures\image-20240211102809362.png" alt="image-20240211102809362" style="zoom: 50%;" />

#### 多线程的第二种实现方式

实现Runnable接口方式进行实现

- 自己定义一个类实现Runnable接口
- 重写里面的run方法
- 创建自己的类对象
- **创建一个Thread类的对象** 并开启线程

<img src="D:\2024\Notes\Typora\Pictures\image-20240211103339289.png" alt="image-20240211103339289" style="zoom:50%;" />

runnable是接口，thread是runnable的实现类，getName的方法在thread中，所以在自己定义的实现Runnable接口中不能直接使用getName方法 需要在run方法中定义一个thread对象获取当前对象，然后getName

<img src="D:\2024\Notes\Typora\Pictures\image-20240211103715324.png" alt="image-20240211103715324" style="zoom:50%;" />

#### 多线程的第三种实现方式

利用Callable接口和Future接口方式进行实现，**可以获取到多线程运行的结果**

- 创建一个类MyCallable实现Callable接口
- 重写call （有返回值，表示多线程运行的结果）
- 创建MyCallable对象（表示多线程要执行的任务）
- 创建FutureTask对象（作用管理多线程运行的结果）
- 创建Thread类的对象并启动（表示线程）

<img src="D:\2024\Notes\Typora\Pictures\image-20240211112542658.png" alt="image-20240211112542658" style="zoom: 33%;" />

### 常见的成员方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240211112835776.png" alt="image-20240211112835776" style="zoom: 33%;" />

#### getName setName

- 如果我们没有给线程设置名字，线程有默认名字

  Thread-X（X序号 从0开始）

- 使用第一种多线程实现方式时，setName需要重写父类里带名字哪一种构造方法才能实现带名字构造

#### currentThread

- JVM虚拟机启动以后，自动的启动多条线程，在以前我们写的所有代码都运行在main行程中，作用是调用main方法，并执行里面的代码

#### sleep

<img src="D:\2024\Notes\Typora\Pictures\image-20240211114219045.png" alt="image-20240211114219045" style="zoom: 50%;" />

#### 线程的优先级 setPriority getPriority

#### 守护线程 setDaemon

```java
final void setDaemon(boolean flag)
    t2.setDaemon(true);
```

当其他的非守护线程执行完毕之后，守护线程会陆续结束

#### 礼让线程 yield

<img src="D:\2024\Notes\Typora\Pictures\image-20240211115125339.png" alt="image-20240211115125339" style="zoom:50%;" />

#### 插入线程 join

<img src="D:\2024\Notes\Typora\Pictures\image-20240211115459619.png" alt="image-20240211115459619" style="zoom:50%;" />

### 线程的生命周期

<img src="D:\2024\Notes\Typora\Pictures\image-20240211115856932.png" alt="image-20240211115856932" style="zoom: 33%;" />

### 同步代码块

把操作共享数据的代码锁起来

```java
static Object obj = new Object();

public void run(){
    ...
       while(true){
            synchronized(锁obj){
                操作共享数据的代码
                    ticket++;
            }
            ...
       }
}


一般锁设为：当前类名.class 是当前类的字节码文件，唯一
    
    eg: MyThread.class
```

- 锁默认打开
- 锁对象一定是唯一的。如果一个类被用作每个线程的参数传递时，通常不需要在该类上使用 static 关键字。因为每个线程都会创建自己的参数对象实例，这样就不存在多个线程共享同一个静态变量的情况
- 同步代码块写在循环里面

### 同步方法

就是把synchronized关键字加到方法上

```java
修饰符synchronized 返回值类型 方法名（方法参数）{...}
```

- 同步方法是锁住方法里面所有的代码块
- 锁对象不能自己指定
  - 方法非静态：this
  - 方法静态：当前类的字节码文件对象

<img src="D:\2024\Notes\Typora\Pictures\image-20240211122158564.png" alt="image-20240211122158564" style="zoom: 50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240211122449296.png" alt="image-20240211122449296" style="zoom:50%;" />

### lock锁

<img src="D:\2024\Notes\Typora\Pictures\image-20240211174956113.png" alt="image-20240211174956113" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240211175139678.png" alt="image-20240211175139678" style="zoom:50%;" />

上述代码：厕所门锁了，然后又从厕所的窗子跑出去了。修改如下：

<img src="D:\2024\Notes\Typora\Pictures\image-20240211175556404.png" alt="image-20240211175556404" style="zoom:50%;" />

### 死锁

死锁是指两个或多个线程永久地阻塞等待对方释放所占用的资源，从而导致程序无法继续执行下去的情况。一般来说，死锁产生的原因有以下几种情况：

1. 互斥条件：一个资源每次只能被一个线程使用。如果一个线程获得了该资源，其他线程就不能再获得该资源。
2. 请求与保持条件：一个线程在持有一个资源的同时，还可以请求其他资源。
3. 不剥夺条件：已获得的资源在未经允许之前不能被其他线程剥夺。
4. 循环等待条件：多个线程之间形成一种循环等待资源的关系。

当以上四个条件同时满足时，就可能会产生死锁。例如：

- 线程 A 持有锁 L1，请求锁 L2；
- 线程 B 持有锁 L2，请求锁 L1。

此时两个线程都在等待对方释放资源，形成了死锁。

除此之外，还有一些可能导致死锁的场景，比如在并发编程中，使用多个 synchronized 块时，如果不同的线程以不同的顺序获取锁，也可能会导致死锁。

在遇到死锁时，可以采用以下几种方式来解决：

1. 预防死锁，尽量避免产生死锁的情况；
2. 检测死锁，通过算法和工具检测死锁，并进行相应的处理；
3. 恢复死锁，即强制终止一个或多个进程，释放其占用的资源，从而使其他进程可以继续执行。

因此，在编写并发程序时，需要特别注意以上情况，避免产生死锁。

### 等待唤醒机制（生产者与消费者）

- 消费等待

  ![image-20240211180415039](D:\2024\Notes\Typora\Pictures\image-20240211180415039.png)

- 生产者等待

  ![image-20240211180522153](D:\2024\Notes\Typora\Pictures\image-20240211180522153.png)

#### 常见方法

<img src="D:\2024\Notes\Typora\Pictures\image-20240211180552602.png" alt="image-20240211180552602" style="zoom:50%;" />

#### 消费者代码实现

![image-20240212081652570](D:\2024\Notes\Typora\Pictures\image-20240212081652570.png)

#### 生产者代码实现

<img src="D:\2024\Notes\Typora\Pictures\image-20240212081952712.png" alt="image-20240212081952712" style="zoom: 33%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240212082024466.png" alt="image-20240212082024466" style="zoom: 50%;" />

#### 阻塞队列实现等待唤醒机制

##### 阻塞队列的继承结构

<img src="D:\2024\Notes\Typora\Pictures\image-20240212082303185.png" alt="image-20240212082303185" style="zoom:50%;" />

**生产者和消费者必须使用同一个阻塞队列**

<img src="D:\2024\Notes\Typora\Pictures\image-20240212083801721.png" alt="image-20240212083801721" style="zoom:50%;" />

### 多线程的六种状态

<img src="D:\2024\Notes\Typora\Pictures\image-20240212083925624.png" alt="image-20240212083925624" style="zoom:50%;" />

虚拟机没有定义运行状态，所以在Java中只定义了以下六种状态。

<img src="D:\2024\Notes\Typora\Pictures\image-20240212084028613.png" alt="image-20240212084028613" style="zoom:50%;" />

### 线程池

#### 核心原理

- 创建一个空池子
- 提交任务时，池子会创建新的线程对象，任务执行完毕，线程归还给池子，下次再提交任务时，不需要创建新的线程，直接复用已有线程即可
- 如果提交任务时，池子中没有空闲线程，也无法创建新的线程，任务就会排队等待

#### 代码实现

- 创建线程池
- 提交任务
- 所有任务全部执行完毕，关闭线程池

Executors

<img src="D:\2024\Notes\Typora\Pictures\image-20240212084836638.png" alt="image-20240212084836638" style="zoom:50%;" />

```java
//1.获取线程池对象
ExecutorService pool1 = Executors.newCachedThreadPool();

//有上限的
ExecutorService pool1 = Executors.newFixedThreadPool();
```

```java
//2.提交任务
pool1.submit(new MyRunnable());
```

```java
//3.销毁线程池(一般不销毁)
pool1.shutdown();
```

#### 自定义线程池

<img src="D:\2024\Notes\Typora\Pictures\image-20240212090854135.png" alt="image-20240212090854135" style="zoom:50%;" />

```java
ThreadPoolExecutor threadPoolExecutor = new hreadPoolExecutor(七个参数）
```

![image-20240212091352774](D:\2024\Notes\Typora\Pictures\image-20240212091352774.png)

##### 任务拒绝策略

<img src="D:\2024\Notes\Typora\Pictures\image-20240212090733599.png" alt="image-20240212090733599" style="zoom:50%;" />

临时线程的优先级反而在队伍长度之后；正式线程+队伍中等待的+临时线程已满，则执行拒绝任务策略。即如下图结论

<img src="D:\2024\Notes\Typora\Pictures\image-20240212091534544.png" alt="image-20240212091534544" style="zoom:50%;" />

#### 最大并行数

```java
public class Thread {
    public static void main(String[] args) {
        int i = Runtime.getRuntime().availableProcessors();
        System.out.println(i);
   }
}

//本机16线程
```

#### 线程池多大合适？

- cpu密集型运算 最大并行数+1    17

- I/O密集型运算（读取本地文件/数据库较多）

  ![image-20240212092402194](D:\2024\Notes\Typora\Pictures\image-20240212092402194.png)

  16\*100\*100/50

#### 多线程的额外扩展内容（面试问题）

## 网络编程

计算机跟计算机之间通过网络进行数据传输

### 常见的软件架构

- c/s client/server 客户端/服务器
- b/s browser/server 浏览器/服务器

<img src="D:\2024\Notes\Typora\Pictures\image-20240212110202310.png" alt="image-20240212110202310" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240212110424151.png" alt="image-20240212110424151" style="zoom:50%;" />

### 网络编程三要素

- **IP** 设备在网络中的地址，是唯一的标识
- **端口号** 应用程序在设备中唯一的标识
- **协议** 数据在网络中从传输的规则
  - 常见协议：UDP TCP http https ftp

#### IP

<img src="D:\2024\Notes\Typora\Pictures\image-20240212111748277.png" alt="image-20240212111748277" style="zoom: 33%;" /><img src="D:\2024\Notes\Typora\Pictures\image-20240212112158761.png" alt="image-20240212112158761" style="zoom: 33%;" />

##### InetAddress类的使用

```java
static InetAddress getByName(String host)
//确定主机名称的IP地址，主机名称可以是机器名称，也可以是IP地址
```

```java
String getHoseName()
//获取此IP地址的主机名
```

```java
String getHostAddress()
//返回文本显示中的IP地址字符串
```

#### 端口号

<img src="D:\2024\Notes\Typora\Pictures\image-20240212113521212.png" alt="image-20240212113521212" style="zoom: 33%;" />

#### 协议

计算机网络中，连接和通信的规则被称为网络通信协议

<img src="D:\2024\Notes\Typora\Pictures\image-20240212170314006.png" alt="image-20240212170314006" style="zoom:50%;" />

##### UDP协议

- 用户数据报协议（User Data Protocol）

- UDP是**面向无连接**通信协议

  速度快，有大小限制一次最多发送64k，数据不安全，容易丢失数据

##### TCP协议

- 传输控制协议TCP（Transmission Control Protocol）

- TCP协议是**面向连接**的通信协议

  速度慢，没有大小限制，数据安全

### UDP协议

**先运行接收端，在运行发送端**

#### 发送数据

1. 创建DatagramSocket对象

   ```java
   DatagramSocket ds  = new DatagramSocket();
   ```

2. 打包数据

   ```java
   DatagramPacket dp  = new DatagramPacket(...)
   ```

3. 发送数据

4. 释放资源

<img src="D:\2024\Notes\Typora\Pictures\image-20240212114934593.png" alt="image-20240212114934593" style="zoom:50%;" />

#### 接收数据

1. 创建接收端的DatagramSocket对象
2. 接收打包好的数据
3. 解析数据包
4. 释放资源

<img src="D:\2024\Notes\Typora\Pictures\image-20240212115508880.png" alt="image-20240212115508880" style="zoom:50%;" />

聊天室

<img src="D:\2024\Notes\Typora\Pictures\image-20240212120120899.png" alt="image-20240212120120899" style="zoom:50%;" />

#### 三种通信方式

- 单播

  以前的代码就是单播

- 组播

  组播地址：224.0.0.0~239.255.255.255

  其中224.0.0.0~224.0.0.255为预留的组播地址

- 广播

  广播地址：255.255.255.255

##### 组播发送端代码

<img src="D:\2024\Notes\Typora\Pictures\image-20240212163631787.png" alt="image-20240212163631787" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240212163829606.png" alt="image-20240212163829606" style="zoom:50%;" />

##### 广播代码

<img src="D:\2024\Notes\Typora\Pictures\image-20240212163932891.png" alt="image-20240212163932891" style="zoom:50%;" />

### TCP协议

TCP通信协议是一种可靠的网络协议，他在通信的两端各建立一个Socket对象，通信之前要保证连接已经建立，通过Socket产生IO流来进行网络通信

<img src="D:\2024\Notes\Typora\Pictures\image-20240212164333105.png" alt="image-20240212164333105" style="zoom:50%;" />

#### 发送数据

<img src="D:\2024\Notes\Typora\Pictures\image-20240212165213965.png" alt="image-20240212165213965" style="zoom:50%;" />

#### 接收数据

<img src="D:\2024\Notes\Typora\Pictures\image-20240212165104700.png" alt="image-20240212165104700" style="zoom:50%;" />

#### 解决中文乱码问题

<img src="D:\2024\Notes\Typora\Pictures\image-20240212165532870.png" alt="image-20240212165532870" style="zoom:50%;" />

字节流通过转换流转换成字符流

为提高读速度，可以在上述红色框后加缓冲流

```java
BufferReader br = new BufferRead(is);

.....while((b = br.read()....)
```

### TCP 三次握手和四次挥手

<img src="D:\2024\Notes\Typora\Pictures\image-20240212170346647.png" alt="image-20240212170346647" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240212170411802.png" alt="image-20240212170411802" style="zoom:50%;" />

## 反射

反射允许对封装类的字段、方法和构造函数的信息进行编程访问

<img src="D:\2024\Notes\Typora\Pictures\image-20240212170756794.png" alt="image-20240212170756794" style="zoom:50%;" />

### 获取class对象的三种方式

- `Class.forName("全类名");`
- `类名.class`;
- `对象.getClass();`

<img src="D:\2024\Notes\Typora\Pictures\image-20240212171306915.png" alt="image-20240212171306915" style="zoom:50%;" />

### 反射获取构造方法（Constructor）

<img src="D:\2024\Notes\Typora\Pictures\image-20240212171516281.png" alt="image-20240212171516281" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240212172305649.png" alt="image-20240212172305649" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240212172353440.png" alt="image-20240212172353440" style="zoom:50%;" />

### 反射获取成员变量（Field）

<img src="D:\2024\Notes\Typora\Pictures\image-20240212172429334.png" alt="image-20240212172429334" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240212172918049.png" alt="image-20240212172918049" style="zoom:50%;" />

### 反射获取成员方法（ Method）

<img src="D:\2024\Notes\Typora\Pictures\image-20240212173014516.png" alt="image-20240212173014516" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240212173330037.png" alt="image-20240212173330037" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Pictures\image-20240212173339996.png" alt="image-20240212173339996" style="zoom:50%;" />

## 动态代理

**无侵入式的给代码增加额外的功能**

<img src="D:\2024\Notes\Typora\Pictures\image-20240212180058545.png" alt="image-20240212180058545" style="zoom:50%;" />

















