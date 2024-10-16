-------Object--------

### Object类的常见方法有哪些？

```java
/**
 * native 方法，用于返回当前运行时对象的 Class 对象，使用了 final 关键字修饰，故不允许子类重写。
 */
public final native Class<?> getClass()
/**
 * native 方法，用于返回对象的哈希码，主要使用在哈希表中，比如 JDK 中的HashMap。
 */
public native int hashCode()
/**
 * 用于比较 2 个对象的内存地址是否相等，String 类对该方法进行了重写以用于比较字符串的值是否相等。
 */
public boolean equals(Object obj)
/**
 * native 方法，用于创建并返回当前对象的一份拷贝。
 */
protected native Object clone() throws CloneNotSupportedException
/**
 * 返回类的名字实例的哈希码的 16 进制的字符串。建议 Object 所有的子类都重写这个方法。
 */
public String toString()
/**
 * native 方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。
 */
public final native void notify()
/**
 * native 方法，并且不能重写。跟 notify 一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。
 */
public final native void notifyAll()
/**
 * native方法，并且不能重写。暂停线程的执行。注意：sleep 方法没有释放锁，而 wait 方法释放了锁 ，timeout 是等待时间。
 */
public final native void wait(long timeout) throws InterruptedException
/**
 * 多了 nanos 参数，这个参数表示额外时间（以纳秒为单位，范围是 0-999999）。 所以超时的时间还需要加上 nanos 纳秒。。
 */
public final void wait(long timeout, int nanos) throws InterruptedException
/**
 * 跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念
 */
public final void wait() throws InterruptedException
/**
 * 实例被垃圾回收器回收的时候触发的操作
 */
protected void finalize() throws Throwable { }

```

### Native方法

在Java中**，native方法是一种特殊类型的方法，它允许Java代码调用外部的本地代码，即用C、C++或其他语言编写的代码**。native关键字是Java语言中的一种声明，用于标记一个方法的实现将在外部定义。
**在Java类中，native方法看起来与其他方法相似，只是其方法体由native关键字代替，没有实际的实现代码。**例如：

```java
public class NativeExample {
public native void nativeMethod();
}
```

要实现native方法，你需要完成以下步骤：

1. **生成JNI头文件：**使用javah工具从你的Java类生成C/C++的头文件，这个头文件包含了所有native方法的原型。
2. **编写本地代码：**使用C/C++编写本地方法的实现，并确保方法签名与生成的头文件中的原型匹配。
3. **编译本地代码：**将C/C++代码编译成动态链接库（DLL，在Windows上），共享库（SO，在Linux上）
4. **加载本地库：**在Java程序中，使用System.loadLibrary()方法来加载你编译好的本地库，这样JVM就能找到并调用native方法的实现了

### ==和equals的区别

- == 对于基本类型和引用类型的作用效果是不同的：
  - **对于基本数据类型来说，== 比较的是值。**
    **对于引用数据类型来说，== 比较的是对象的内存地址。**
- equals() 不能用于判断基本数据类型的变量，**只能用来判断两个对象是否相等**。
  equals()方法存在于Object类中，而Object类是所有类的直接或间接父类，因此所有的类都有equals()方法
  - 类没有重写 equals()方法：通过equals()比较该类的两个对象时，等价于通过“==”比较这两个对象，使用的默认是 Object类equals()方法。
  - 类重写了 equals()方法：一般我们都重写 equals()方法来比较两个对象中的**属性是否相等**；若它们的属性相等，则返回 true(即，认为这两个对象相等)。

String 中的 equals 方法是被重写过的，**因为 Object 的 equals 方法是比较的对象的内存地址，而 String 的 equals 方法比较的是对象的值。**当**创建 String 类型的对象时，虚拟机会在常量池中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用**。如果**没有就在常量池中重新创建一个 String 对象**

### 值传递&引用传递

- 值传递：方法接收的是实参值的**拷贝，会创建副本。**
- 引用传递：**方法接收的直接是实参所引用的对象在堆中的地址**，不会创建副本，**对形参的修改将影响到实参**。

很多程序设计语言（比如 C++、 Pascal )提供了两种参数传递的方式，不过，在 Java 中只有值传递。不**过对于引用类型来说，值是实参的地址**

- 如果参数是基本类型的话，很简单，传递的就是基本类型的字面量值的拷贝，会创建副本。
  如果参数是引用类型**，传递的就是实参所引用的对象在堆中地址值的拷贝，同样也会创建副本。**

### hashCode()有什么用，为什么要有？

hashCode() 的作用是获取哈希码（int 整数），也称为散列码。这个哈希码的作用是确定该对象在哈希表中的索引位置

- 如果两个对象的hashCode 值相等，那这两个对象不一定相等（哈希碰撞）。
- **如果两个对象的hashCode 值相等并且equals()方法也返回 true，我们才认为这两个对象相等。**
- 如果两个对象的hashCode 值不相等，我们就可以直接认为这两个对象不相等。

### 为什么重写equals()时必须重写hashCode()方法？

因为两个相等的对象的 hashCode 值必须是相等。也就是说如果 equals 方法判断两个对象是相等的，那这两个对象的 hashCode 值也要相等。

### -------String--------

### String、StringBuffer、StringBuilder区别？

|               |                            可变性                            |                          线程安全性                          |                             性能                             |
| :-----------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|    String     |                     `String` 是不可变的                      | `**String` 中的对象是不可变的，也就可以理解为常量，线程安全** | 每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象 |
| StringBuffer  | `StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串，不过没有使用 `final` 和 `private` 关键字修饰，最关键的是这个 `AbstractStringBuilder` 类还提供了很多修改字符串的方法比如 `append` 方法。 | `AbstractStringBuilder` 是 `StringBuilder` 与 `StringBuffer` 的公共父类，定义了一些字符串的基本操作，如 `expandCapacity`、`append`、`insert`、`indexOf` 等公共方法<br /><br />`S**tringBuffer` 对方法加了同步锁或者对调用的方法加了同步锁**，所以是**线程安全**的 | **`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。** |
| StringBuilder |                                                              | `StringBuilder` 并没有对方法进行加同步锁，所以是**非线程安全**的。 | 相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。 |

- 操作少量的数据: 适用 String
- 单线程操作字符串缓冲区下操作大量数据: 适用 StringBuilder
- 多线程操作字符串缓冲区下操作大量数据: 适用 StringBuffer

### String为什么是不可变的？

我们知道**被 final 关键字修饰的类不能被继承，修饰的方法不能被重写，修饰的变量是基本数据类型则值不能改变，修饰的变量是引用类型则不能再指向其他对象**。因此，**final 关键字修饰的数组保存字符串并不是 String 不可变的根本原因，因为这个数组保存的字符串是可变的（final 修饰引用类型变量的情况）。**

String 真正不可变有下面几点原因：

- **保存字符串的数组被 final 修饰且为私有的，**并且**String 类没有提供/暴露修改这个字符串的方法。**
- String 类**被 final 修饰导致其不能被继承，进而避免了子类破坏 String 不可变。**

### 字符串拼接用“+”还是用StringBuilder

- Java 语言本身并不支持运算符重载，“+”和“+=”是专门为 String 类重载过的运算符，也是 Java 中仅有的两个重载过的运算符。
- 字符串对象**通过“+”的字符串拼接方式，实际上是通过 StringBuilder 调用 append() 方法实现的，拼接完成之后调用 toString() 得到一个 String 对象 。**
- 在循环内使用“+”进行字符串的拼接的话，存在比较明显的缺陷：**编译器不会创建单个 StringBuilder 以复用，会导致创建过多的 StringBuilder 对象。**
- StringBuilder 对象是在**循环内部被创建的，这意味着每循环一次就会创建一个 StringBuilder 对象**。**如果直接使用 StringBuilder 对象进行字符串拼接的话，就不会存在这个问题了。**

### 字符串常量池了解吗

字符串常量池 是 JVM 为了提升性能和减少内存消耗针对字符串（String 类）专门开辟的一块区域，主要目的是为了避免字符串的重复创建。

### String s1 = new String("abc");这句话创建了几个字符串对象？

会创建 1 或 2 个字符串对象

1. 如果**字符串常量池中不存在字符串对象“abc”的引用**，那么它会在堆上创建两个字符串对象，其中一个字符串对象的引用会被保存在字符串常量池中。
2. 如果**字符串常量池中已存在字符串对象“abc”的引用**，则只会在堆中创建 1 个字符串对象“abc”。

详细来说

- 首先，代码中有一个new关键字，我们知道**new指令是创建一个类的实例对象并完成加载初始化的**，因此**这个字符串对象是在运行期才能确定的，创建的字符串对象是在堆内存上。**
- 其次，在String的构造方法中传递了一个字符串abc，由于这里的abc是被final修饰的属性，**所以它是一个字符串常量**。在首次构建这个对象时，JVM拿字面量"abc"**去字符串常量池试图获取其对应String对象的引用**。于是**在堆中创建了一个"abc"的String对象，****并将其引用保存到字符串常量池中，然后返回**；
  - 如果abc这个字符串常量不存在，则创建两个对象，**分别是abc这个字符串常量，以及new String这个实例对象。**
  - 如果abc这字符串常量存在，**则只会创建一个对象。**

### String #intern方法有什么作用？

S**tring.intern() 是一个 native（本地）方法，其作用是将指定的字符串对象的引用保存在字符串常量池中**，可以简单分为两种情况：

- 如果字符串常量池中**保存了对应的字符串对象的引用，就直接返回该引用。**
- 如果字符串常量池中**没有保存了对应的字符串对象的引用，那就在常量池中创建一个指向该字符串对象的引用并返回**

### String类型的变量和常量做"+"运算时候发生了什么？

```java
String str1 = "str";
String str2 = "ing";
String str3 = "str" + "ing";
String str4 = str1 + str2;
String str5 = "string";
System.out.println(str3 == str4);//false
System.out.println(str3 == str5);//true
System.out.println(str4 == str5);//false
```

- 对于编译期可以确定值的字符串，也就是常量字符串 ，jvm 会将其存入字符串常量池。并且，**字符串常量拼接得到的字符串常量在编译阶段就已经被存放字符串常量池，这个得益于编译器的优化。**

- 在编译过程中，Javac 编译器（下文中统称为编译器）会进行一个叫做 常量折叠(Constant Folding) 的代码优化

  - 常量折叠会把常量表达式的值求出来作为常量嵌在最终生成的代码中，这是 Javac 编译器会对源代码做的极少量优化措施之1
  - 并不是所有的常量都会进行折叠，只有编译器在程序编译期就可以确定值的常量才可以：
    - 基本数据类型( byte、boolean、short、char、int、float、long、double)以及字符串常量。
      final 修饰的基本数据类型和字符串变量
    - 字符串通过 “+”拼接得到的字符串、基本数据类型之间算数运算（加减乘除）、基本数据类型的位运算（<<、>>、>>> ）

- 引用的值在程序编译期是无法确定的，编译器无法对其进行优化。不过，**字符串使用 final 关键字声明之后，可以让编译器当做常量来处理**。被 final 关键字修饰之后的 String 会被编译器当做常量来处理，编译器在程序编译期就可以确定它的值，其效果就相当于访问常量。

  ```java
  final String str1 = "str";
  final String str2 = "ing";
  // 下面两个表达式其实是等价的
  String c = "str" + "ing";// 常量池中的对象
  String d = str1 + str2; // 常量池中的对象
  System.out.println(c == d);// true
  ```

### --------异常---------

### Exception和Error的区别

所有的异常都有一个共同的祖先 java.lang 包中的 Throwable 类。**Throwable 类有两个重要的子类:**

- Exception :**程序本身可以处理的异常**，可以通过 catch 来进行捕获。
  - **Exception 又可以分为 Checked Exception (受检查异常，必须处理) 和**
  - **Unchecked Exception (不受检查异常，可以不处理)。**
- Error：**Error 属于程序无法处理的错误** ，我们没办法通过 catch 来进行捕获不建议通过catch捕获 。
  例如 
  - **Java 虚拟机运行错误（Virtual MachineError）、**
  - **虚拟机内存不够错误(OutOfMemoryError)、**
  - **类定义错误（NoClassDefFoundError）等** 。
  - 这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止。

### Checked Exception 和 Unchecked Exception 有什么区别？

#### Checked Exception

**Checked Exception 即 受检查异常 ，Java 代码在编译过程中，如果受检查异常没有被 catch或者throws 关键字处理的话，就没办法通过编译。**

除了RuntimeException及其子类以外，其他的Exception类及其子类都属于受检查异常 。常见的受检查异常有：IO 相关的异常、ClassNotFoundException、SQLException...。

#### Unchecked Exception

Unchecked Exception 即 不受检查异常 ，Java 代码在编译过程中 ，我们即使不处理不受检查异常也可以正常通过编译。

- 常见的有（建议记下来，日常开发中会经常用到）：
  NullPointerException(空指针错误)
  IllegalArgumentException(参数错误比如方法入参类型错误)
  NumberFormatException（字符串转换为数字格式错误，IllegalArgumentException的子类）
  ArrayIndexOutOfBoundsException（数组越界错误）
  ClassCastException（类型转换错误）
  ArithmeticException（算术错误）
  SecurityException （安全错误比如权限不够）
  UnsupportedOperationException(不支持的操作错误比如重复创建同一用户)

### Throwable类常用方法有哪些？

- String getMessage(): 返回异常发生时的简要描述
- String toString(): 返回异常发生时的详细信息
- String getLocalizedMessage(): 返回异常对象的本地化信息。使用 Throwable 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 getMessage()返回的结果相同
- void printStackTrace(): 在控制台上打印 Throwable 对象封装的异常信息

### try-catch-finally如何使用

- try块：用于捕获异常。其后可接零个或多个 catch 块，如果没有 catch 块，则必须跟一个 finally 块。
- catch块：用于处理 try 捕获到的异常。
- finally 块：无论是否捕获或处理异常，finally 块里的语句都会被执行。当在 try 块或 catch 块中遇到 return 语句时，finally 语句块将在方法返回之前被执行。

不要在 finally 语句块中使用 return! 当 try 语句和 finally 语句中都有 return 语句时，try 语句块中的 return 语句会被忽略。这是因为 try 语句中的 return 返回值会先被暂存在一个本地变量中，当执行到 finally 语句中的 return 之后，这个本地变量的值就变为了 finally 语句中的 return 返回值。

### Finally中的代码一定会被执行吗

- 程序所在的线程死亡
- 关闭CPU
- finally 之前虚拟机被终止运行的话，finally 中的代码就不会被执行。

### 如何使用try-with-resources代替try-catch-finally？

- 适用范围（资源的定义）： 任何实现 java.lang.AutoCloseable或者 java.io.Closeable 的对象
- 关闭资源和 finally 块的执行顺序： 在 try-with-resources 语句中，任何 catch 或 finally 块在声明的资源关闭后运行

### 异常使用有哪些需要注意的地方？

- 不要把异常定义为静态变量，因为这样会导致异常栈信息错乱。每次手动抛出异常，我们都需要手动 new 一个异常对象抛出。
  抛出的异常信息一定要有意义。
- 建议抛出更加具体的异常比如字符串转换为数字格式错误的时候应该抛出NumberFormatException而不是其父类IllegalArgumentException。
- 避免重复记录日志：如果在捕获异常的地方已经记录了足够的信息（包括异常类型、错误信息和堆栈跟踪等），那么在业务代码中再次抛出这个异常时，就不应该再次记录相同的错误信息。重复记录日志会使得日志文件膨胀，并且可能会掩盖问题的实际原因，使得问题更难以追踪和解决

### --------泛型---------

### 什么是泛型，有什么作用

- Java 泛型（Generics） 是 JDK 5 中引入的一个新特性。它允许类、接口和方法在定义时使用一个或多个类型参数，这些类型参数在使用时可以被指定为具体的类型。
- 编译器可以对泛型参数进行检测，并且通过泛型参数可以指定传入的对象类型。比如 ArrayList<Person> persons = new ArrayList<Person>() 这行代码就指明了该 ArrayList 对象只能传入 Person 对象，如果传入其他类型的对象就会报错。

### 为什么需要泛型

1. 适用于多种数据类型执行相同的代码
2. 泛型中的类型在使用时指定，不需要强制类型转换

### 泛型的使用方式有哪几种？

泛型一般有三种使用方式:泛型类、泛型接口、泛型方法。

1. 泛型类
2. 泛型接口
3. 泛型方法

- public static < E > void printArray( E[] inputArray ) 一般被称为**静态泛型方法;**
- 在 java **中泛型只是一个占位符，必须在传递类型后才能使用。**
- **类在实例化时才能真正的传递类型参数**，由于**静态方法的加载先于类的实例化**，也就是说类中的泛型还没有传递真正的类型参数，静态的方法的加载就已经完成了，所以静态泛型方法是没有办法使用类上声明的泛型的。只能使用自己声明的 <E>

### 项目中哪里用到了泛型

- 自定义接口通用返回结果 CommonResult<T> 通过参数 T 可根据具体的返回类型动态指定结果的数据类型
- 定义 Excel 处理类 ExcelUtil<T> 用于动态指定 Excel 导出的数据类型
- 构建集合工具类（参考 Collections 中的 sort, binarySearch 方法）。

### --------反射---------

### 反射是什么？优缺点？

- 它赋予了我们在**运行时分析类以及执行类中方法的能力。**通过反射你可以获取任意一个类的所有属性和方法，你还可以调用这些方法和属性
  - 反射可以让我们的**代码更加灵活、为各种框架提供开箱即用的功能提**供了便利。
  - 反射让我们在运行时有了分析操作类的能力的同时，也**增加了安全问题，**比如可以无视泛型参数的安全检查（泛型参数的安全检查发生在编译时）。
  - 另外，反射的**性能也要稍差点**，不过，对于框架来说实际是影响不大的。
- 发射具有以下特性：
  - **运行时类信息访问：****反射机制允许程序在运行时获取类的完整结构信息，包括类名、包名、父类、实现的接口、构造函数、方法和字段等。**
  - **动态对象创建：**可以**使用反射API动态地创建对象实例，**即使在编译时不知道具体的类名。这是通过Class类的newInstance()方法或Constructor对象的newInstance()方法实现的。
  - **动态方法调用：**可以在**运行时动态地调用对象的方法**，包括私有方法。这通过Method类的invoke()方法实现，允许你传入对象实例和参数值来执行方法。
  - **访问和修改字段值：****反射还允许程序在运行时访问和修改对象的字段值**，即使是私有的。这是通过Field类的get()和set()方法完成的。


### 反射的应用场景

- 像 Spring/Spring Boot、MyBatis 等等框架中都大量使用了反射机制。这些框架中也大量使用了动态代理，而动态代理的实现也依赖反射。
- 像 Java 中的一大利器 注解 的实现也用到了反射。可以基于反射分析类，然后获取到类/属性/方法/方法的参数上的注解



1. 加载数据库驱动

   我们的项目底层数据库有时是用mysql，有时用oracle，需要动态地根据实际情况加载驱动类，这个时候反射就有用了，假设 com.mikechen.java.myqlConnection，com.mikechen.java.oracleConnection这两个类我们要用。
   这时候我们在使用 JDBC 连接数据库时使用 Class.forName()通过反射加载数据库的驱动程序，如果是mysql则传入mysql的驱动类，而如果是oracle则传入的参数就变成另一个了

2. 配置文件加载

   Spring 框架的 IOC（动态加载管理 Bean），Spring通过配置文件配置各种各样的bean，你需要用到哪些bean就配哪些，spring容器就会根据你的需求去动态加载，你的程序就能健壮地运行。
   Spring通过XML配置模式装载Bean的过程：

    	1. 将程序中所有XML或properties配置文件加载入内存
    	2. Java类里面解析xml或者properties里面的内容，得到对应实体类的字节码字符串以及相关的属性信息
    	3. 使用反射机制，根据这个字符串获得某个类的Class实例
    	4. 动态配置实例的属性

Java反射机制在spring框架中，很多地方都用到了反射，让我们来看看Spring的IoC和AOP是如何使用反射技术的。

##### 1、Spring框架的依赖注入（DI）和控制反转（IoC）

Spring 使用反射来实现其核心特性：依赖注入。

在Spring中，开发者可以通过XML配置文件或者基于注解的方式声明组件之间的依赖关系。当应用程序启动时，Spring容器会扫描这些配置或注解，然后利用反射来实例化Bean（即Java对象），并根据配置自动装配它们的依赖。

例如，当一个Service类需要依赖另一个DAO类时，开发者可以在Service类中使用@Autowired注解，而无需自己编写创建DAO实例的代码。Spring容器会在运行时解析这个注解，通过反射找到对应的DAO类，实例化它，并将其注入到Service类中。这样不仅降低了组件之间的耦合度，也极大地增强了代码的可维护性和可测试性。

##### 2、动态代理的实现

在需要对现有类的方法调用进行拦截、记录日志、权限控制或是事务管理等场景中，反射结合动态代理技术被广泛应用。

一个典型的例子是Spring AOP（面向切面编程）的实现。Spring AOP允许开发者定义切面（Aspect），这些切面可以横切关注点（如日志记录、事务管理），并将其插入到业务逻辑中，而不需要修改业务逻辑代码。

例如，为了给所有的服务层方法添加日志记录功能，可以定义一个切面，在这个切面中，Spring会使用JDK动态代理或CGLIB（如果目标类没有实现接口）来创建目标类的代理对象。这个代理对象在调用任何方法前或后，都会执行切面中定义的代码逻辑（如记录日志），而这一切都是在运行时通过反射来动态构建和执行的，无需硬编码到每个方法调用中。

这两个例子展示了反射机制如何在实际工程中促进松耦合、高内聚的设计，以及如何提供动态、灵活的编程能力，特别是在框架层面和解决跨切面问题时

### 反射实战

#### 获取Class对象的四种方式

如果我们动态获取到这些信息，我们需要依靠 Class 对象。Class 类对象将一个类的方法、变量等信息告诉运行的程序。Java 提供了四种方式获取 Class 对象:

1. 知道具体类的情况下

   `Class alunbarClass = TargetObject.class;`

   但是我们一般是不知道具体类的，基本都是通过遍历包下面的类来获取 Class 对象，通过此方式获取 Class 对象不会进行初始化

2. 通过class.forName()传入类的全路径获取

   `Class alunbarClass1 = Class.forName("cn.javaguide.TargetObject");`

3. 通过对象实例instance.getClass()获取

   `TargetObject o = new TargetObject();
   Class alunbarClass2 = o.getClass();`

4. 通过类加载器xxxClassLoader.loadClass()传入类路径获取

   `ClassLoader.getSystemClassLoader().loadClass("cn.javaguide.TargetObject");`

   通过类加载器获取 Class 对象**不会进行初始化，意味着不进行包括初始化等一系列步骤，静态代码块和静态对象不会得到执行**

#### 反射的一些基本操作

1. 创建一个我们要使用反射操作的类 TargetObject。
2. 使用反射操作这个类的方法以及参数

### 代理模式详解

### 代理模式

我们使用代理对象来代替对真实对象(real object)的访问，这样就可以在不修改原目标对象的前提下，提供额外的功能操作，**扩展目标对象的功能**，比如说在目标对象的某个方法执行前后你可以增加一些自定义的操作。

### 静态代理

- 静态代理中，我们对目标对象的每个方法的增强都是手动完成的（后面会具体演示代码），非常不灵活（**比如接口一旦新增加方法，目标对象和代理对象都要进行修**改）且麻烦**(需要对每个目标类都单独写一个代理类**）
- **从 JVM 层面来说， 静态代理在编译时就将接口、实现类、代理类这些都变成了一个个实际的 class 文件。**

实现步骤：

1. 定义一个接口及其实现类；
2. 创建一个**代理类同样实现这个接口**
3. **将目标对象注入进代理类，然后在代理类的对应方法调用目标类中的对应方法。**这样的话，我们就可以通过代理类屏蔽对目标对象的访问，并且可以在目标方法执行前后做一些自己想做的事情。

代码：

1.定义发送短信的接口

```java
public interface SmsService {
    String send(String message);
}
```

2.实现发送短信的接口

```java
public class SmsServiceImpl implements SmsService {
    public String send(String message) {
        System.out.println("send message:" + message);
        return message;
    }
}
```

3.创建代理类并同样实现发送短信的接口

```java
public class SmsProxy implements SmsService {

    private final SmsService smsService;

    public SmsProxy(SmsService smsService) {
        this.smsService = smsService;
    }

    @Override
    public String send(String message) {
        //调用方法之前，我们可以添加自己的操作
        System.out.println("before method send()");
        smsService.send(message);
        //调用方法之后，我们同样可以添加自己的操作
        System.out.println("after method send()");
        return null;
    }
}

```

4.实际使用

```java
public class Main {
    public static void main(String[] args) {
        SmsService smsService = new SmsServiceImpl();
        SmsProxy smsProxy = new SmsProxy(smsService);
        smsProxy.send("java");
    }
}
```

运行上述代码之后，控制台打印出：

```java
before method send()
send message:java
after method send()
```

可以输出结果看出，我们已经增加了 SmsServiceImpl 的send()方法

### 动态代理

- 动态代理更加灵活。我们不需要针对每个目标类都单独创建一个代理类，并且也不需要我们必须实现接口，**我们可以直接代理实现类( CGLIB 动态代理机制)。**
- **从 JVM 角度来说，动态代理是在运行时动态生成类字节码，并加载到 JVM 中的。**
- ，Spring AOP、RPC 框架应该是两个不得不提的，它们的实现都依赖了动态代理。

#### JDK动态代理机制

- 在 Java 动态代理机制中 InvocationHandler 接口和 Proxy 类是核心。

- Proxy 类中使用频率最高的方法是：newProxyInstance() ，这个方法主要用来生成一个代理对象。

- ```java
  public static Object newProxyInstance(ClassLoader loader,
                                        Class<?>[] interfaces,
                                        InvocationHandler h)
      throws IllegalArgumentException
  {
      ......
  }
  ```

  loader :**类加载器，用于加载代理对象。**
  interfaces : **被代理类实现的一些接口；**
  h : **实现了 InvocationHandler 接口的对象；**
  要实现动态代理的话，还必须需要**实现InvocationHandler 来自定义处理逻辑。** 当我们的动态代理对象调用一个方法时，**这个方法的调用就会被转发到实现InvocationHandler 接口类的 invoke 方法来调用。**

  ```java
  public interface InvocationHandler {
  
      /**
       * 当你使用代理对象调用方法的时候实际会调用到这个方法
       */
      public Object invoke(Object proxy, Method method, Object[] args)
          throws Throwable;
  }
  ```

  proxy :动态生成的代理类
  method : 与代理类对象调用的方法相对应
  args : 当前 method 方法的参数
  也就是说：**你通过Proxy 类的 newProxyInstance() 创建的代理对象在调用方法的时候，实际会调用到实现InvocationHandler 接口的类的 invoke()方法。 你可以在 invoke() 方法中自定义处理逻辑，比如在方法执行前后做什么事情**

##### JDK动态代理类使用步骤

1. 定义一个接口及其实现类；
2. 自定义 InvocationHandler 并重写invoke方法，在 invoke 方法中我们会调用原生方法（被代理类的方法）并自定义一些处理逻辑；
3. 通过 Proxy.newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h) 方法创建代理对象

##### 代码实例

1. 定义发送短信的接口

   ```java
   public interface SmsService {
       String send(String message);
   }
   ```

2. 实现发送短信的接口

   ```java
   public class SmsServiceImpl implements SmsService {
       public String send(String message) {
           System.out.println("send message:" + message);
           return message;
       }
   }
   ```

3. 定义一个JDK动态代理类

   ```java
   import java.lang.reflect.InvocationHandler;
   import java.lang.reflect.InvocationTargetException;
   import java.lang.reflect.Method;
   
   /**
    * @author shuang.kou
    * @createTime 2020年05月11日 11:23:00
    */
   public class DebugInvocationHandler implements InvocationHandler {
       /**
        * 代理类中的真实对象
        */
       private final Object target;
   
       public DebugInvocationHandler(Object target) {
           this.target = target;
       }
   
       @Override
       public Object invoke(Object proxy, Method method, Object[] args) throws InvocationTargetException, IllegalAccessException {
           //调用方法之前，我们可以添加自己的操作
           System.out.println("before method " + method.getName());
           Object result = method.invoke(target, args);
           //调用方法之后，我们同样可以添加自己的操作
           System.out.println("after method " + method.getName());
           return result;
       }
   }
   ```

   invoke() 方法: 当我们的动态代理对象调用原生方法的时候，最终实际上调用到的是 invoke() 方法，然后 invoke() 方法代替我们去调用了被代理对象的原生方法。

4. 获取代理对象的工厂类

   ```java
   public class JdkProxyFactory {
       public static Object getProxy(Object target) {
           return Proxy.newProxyInstance(
                   target.getClass().getClassLoader(), // 目标类的类加载器
                   target.getClass().getInterfaces(),  // 代理需要实现的接口，可指定多个
                   new DebugInvocationHandler(target)   // 代理对象对应的自定义 InvocationHandler
           );
       }
   }
   ```

   getProxy()：主要通过Proxy.newProxyInstance（）方法获取某个类的代理对象

5. 实际使用

   ```java
   SmsService smsService = (SmsService) JdkProxyFactory.getProxy(new SmsServiceImpl());
   smsService.send("java");
   
   before method send
   send message:java
   after method send
   ```

#### CGLIB动态代理机制

- **JDK 动态代理**有一个最致命的问题是其**只能代理实现了接口的类。**为了解决这个问题，我们可以用 CGLIB 动态代理机制来避免。

- CGLIBopen in new window(Code Generation Library)是一个基于ASMopen in new window的字节码生成库，它**允许我们在运行时对字节码进行修改和动态生成。**CGLIB **通过继承方式实现代理**。很多知名的开源框架都使用到了CGLIBopen in new window， 例如 Spring 中的 **AOP 模块中：如果目标对象实现了接口，则默认采用 JDK 动态代理，否则采用 CGLIB 动态代理。**

- 在 CGLIB 动态代理机制中 MethodInterceptor 接口和 Enhancer 类是核心。
  你需要自定义 MethodInterceptor 并重写 intercept 方法，intercept 用于拦截增强被代理类的方法。

  ```java
  public interface MethodInterceptor
  extends Callback{
      // 拦截被代理类中的方法
      public Object intercept(Object obj, java.lang.reflect.Method method, Object[] args,MethodProxy proxy) throws Throwable;
  }
  ```

  obj : 被代理的对象（需要增强的对象）
  method : 被拦截的方法（需要增强的方法）
  args : 方法入参
  proxy : 用于调用原始方法
  **你可以通过 Enhancer类来动态获取被代理类，当代理类调用方法的时候，实际调用的是 MethodInterceptor 中的 intercept 方法**

##### CGLIB动态代理类使用步骤

- 定义一个类；
- 自定义 MethodInterceptor 并重写 intercept 方法，intercept 用于拦截增强被代理类的方法，和 JDK 动态代理中的 invoke 方法类似；
- 通过 Enhancer 类的 create()创建代理类；

##### 代码示例

1. 使用一个阿里云发送短信的类

   ```java
   package github.javaguide.dynamicProxy.cglibDynamicProxy;
   
   public class AliSmsService {
       public String send(String message) {
           System.out.println("send message:" + message);
           return message;
       }
   }
   
   ```

2. 自定义MethodIntercepteor（方法拦截器）

   ```java
   import net.sf.cglib.proxy.MethodInterceptor;
   import net.sf.cglib.proxy.MethodProxy;
   
   import java.lang.reflect.Method;
   
   /**
    * 自定义MethodInterceptor
    */
   public class DebugMethodInterceptor implements MethodInterceptor {
   
   
       /**
        * @param o           被代理的对象（需要增强的对象）
        * @param method      被拦截的方法（需要增强的方法）
        * @param args        方法入参
        * @param methodProxy 用于调用原始方法
        */
       @Override
       public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
           //调用方法之前，我们可以添加自己的操作
           System.out.println("before method " + method.getName());
           Object object = methodProxy.invokeSuper(o, args);
           //调用方法之后，我们同样可以添加自己的操作
           System.out.println("after method " + method.getName());
           return object;
       }
   
   }
   
   ```

3. 获取代理类

   ```java
   import net.sf.cglib.proxy.Enhancer;
   
   public class CglibProxyFactory {
   
       public static Object getProxy(Class<?> clazz) {
           // 创建动态代理增强类
           Enhancer enhancer = new Enhancer();
           // 设置类加载器
           enhancer.setClassLoader(clazz.getClassLoader());
           // 设置被代理类
           enhancer.setSuperclass(clazz);
           // 设置方法拦截器
           enhancer.setCallback(new DebugMethodInterceptor());
           // 创建代理类
           return enhancer.create();
       }
   }
   ```

4. 实际使用

   ```java
   AliSmsService aliSmsService = (AliSmsService) CglibProxyFactory.getProxy(AliSmsService.class);
   aliSmsService.send("java");
   
   before method send
   send message:java
   after method send
   ```

#### JDK动态代理和CGLIB动态代理

- JDK 动态代理**只能代理实现了接口的类或者直接代理接口**，而 CGLIB 可以**代理未实现任何接口的类**。
- 另外， **CGLIB 动态代理是通过生成一个被代理类的子类来拦截被代理类的方法调用**，因此**不能代理声明为 final 类型的类和方法。**
  - 被代理类：AliSmsService。
    被代理的子类：由 CGLIB 自动生成的 AliSmsService 子类，这个子类包含了 DebugMethodInterceptor 的拦截逻辑。
- 就二者的效率来说，大部分情况都是 JDK 动态代理更优秀，随着 JDK 版本的升级，这个优势更加明显

### 静态代理和动态代理的对比

- 灵活性：
  - 动态代理**更加灵活，不需要必须实现接口，可以直接代理实现类**，并且可以**不需要针对每个目标类都创建一个代理类。**
  - 另外，静态代理中，接口一旦新增加方法，目标对象和代理对象都要进行修改，这是非常麻烦的！
- JVM 层面：静态代理在**编译时就将接口、实现类、代理类这些都变成了一个个实际的 class 文件**。而动态代理是在**运行时动态生成类字节码，并加载到 JVM 中的。**

### --------注解---------

### 什么是注解？解析方法有哪些？

Annotation （注解） 是 Java5 开始引入的新特性，可以看作是一种特殊的注释，**主要用于修饰类、方法或者变量，提供某些信息供程序在编译或者运行时使用。**

注解只有被解析之后才会生效，常见的解析方法有两种：

- **编译期直接扫描**：编译器在编译 Java 代码的时候扫描对应的注解并处理，比如某个方法使用@Override 注解，编译器在编译的时候就会检测当前的方法是否重写了父类对应的方法。
- **运行期通过反射处理**：像框架中自带的注解(比如 Spring 框架的 @Value、@Component)都是通过反射来进行处理的。

### Java注解的原理

- **注解本质是一个继承了Annotation的特殊接口，其具体实现类是Java运行时生成的动态代理类。**
- 我**们通过反射获取注解时，返回的是Java运行时生成的动态代理对象。**通过代理对象调用自定义注解的方法，会最终调用AnnotationInvocationHandler的invoke方法。该方法会从memberValues这个Map中索引出对应的值。而memberValues的来源是Java常量池

### Java注解的作用域

注解的作用域（Scope）指的是注解可以应用在哪些程序元素上，例如类、方法、字段等。Java注解的作用域可以分为三种：

1. 类级别作用域：用于描述类的注解，通常放置在类定义的上面，可以用来指定类的一些属性，如类的访问级别、继承关系、注释等。
2. 方法级别作用域：用于描述方法的注解，通常放置在方法定义的上面，可以用来指定方法的一些属性，如方法的访问级别、返回值类型、异常类型、注释等。
3. 字段级别作用域：用于描述字段的注解，通常放置在字段定义的上面，可以用来指定字段的一些属性，如字段的访问级别、默认值、注释等。

### --------SPI----------

### 什么是SPI

SPI 即 Service Provider Interface ，字面意思就是：“服务提供者的接口”，我的理解是：专门提供给服务提供者或者扩展框架功能的开发者去使用的一个接口。**SPI 将服务接口和具体的服务实现分离开来，将服务调用方和服务实现者解耦**，能够提升程序的扩展性、可维护性。**修改或者替换服务实现并不需要修改调用方。**

### SPI和API的区别

API（Application Programming Interface）

- 当实现方提供了接口和实现，我们可以通过**调用实现方的接口从而拥有实现方给我们提供的能力**，这就是 API。这种情况下，**接口和实现都是放在实现方的包中。调用方通过接口调用实现方的功能，而不需要关心具体的实现细节。**
- 当接口存在于调用方这边时，这就是 **SPI ** 。**由接口调用方确定接口规则，然后由不同的厂商根据这个规则对这个接口进行实现，从而提供服务。**

### SPI的优缺点

通过 SPI 机制能够大大地提高接口设计的灵活性，但是 SPI 机制也存在一些缺点，比如：

- **需要遍历加载所有的实现类，**不能做到按需加载，这样**效率还是相对较低**的。
- 当多个 ServiceLoader 同时 load 时，**会有并发问题。**

### -序列化和反序列化-

### 什么是序列化与反序列化

- 序列化：将数据结构或**对象转换成二进制字节流**的过程
- 反序列化：将在序列化过程中所生成的**二进制字节流转换成数据结构或者对象**的过程

对于 **Java 这种面向对象编程语言来说，我们序列化的都是对象（Object）也就是实例化后的类(Class)，**但是在 C**++这种半面向对象的语言中，struct(结构体)定义的是数据结构类型，而 class 对应的是对象类型。**

序列化的主要目的是通过网络传输对象或者说是将对象存储到文件系统、数据库、内存中。

- 对象在**进行网络传输（比如远程方法调用 RPC 的时候）之前需要先被序列化，**接收到序列化的对象之后需要再进行反序列化；
- 将**对象存储到文件之前需要进行序列化**，将对象从文件中读取出来需要进行反序列化；
- 将**对象存储到数据库（如 Redis）之前需要用到序列化**，将对象从缓存数据库中读取出来需要反序列化；
- 将**对象存储到内存之前需要进行序列化**，从内存中读取出来之后需要进行反序列化。

#### 序列化协议对应四层模型哪一层？

- OSI 七层协议模型中，**表示层做的事情主要就是对应用层的用户数据进行处理转换为二进制流**。反过来的话，就是将二进制流转换成应用层的用户数据。就对应的是序列化和反序列化。
- 因为，OSI 七层协议模型中的应用层、表示层和会话层对应的都是 TCP/IP 四层模型中的应用层，所以序列化协议属于 TCP/IP 协议**应用层的一部分**

### 如果有些字段不想进行序列化怎么办？

对于不想进行序列化的变量，使用 transient 关键字修饰。

- transient 关键字的作用是：**阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被 transient 修饰的变量值不会被持久化和恢复。**

关于 transient 还有几点注意：

- transien**t 只能修饰变量**，不能修饰类和方法。
- transient 修饰的变量，**在反序列化后变量值将会被置成类型的默认值**。例如，如果是修饰 int 类型，那么反序列后结果就是 0。
- **static 变量因为不属于任何对象(Object)，所以无论有没有 transient 关键字修饰，均不会被序列化**

### 常见的序列化协议？

- JDK 自带的序列化方式一般不会用 ，因为序列化效率低并且存在安全问题。

  - 问题

    - **不支持跨语言调用** : 如果调用的是其他语言开发的服务的时候就不支持了。
    - 性能差：相比于其他序列化框架性能更低，主要原因是**序列化之后的字节数组体积较大，导致传输成本加大。**
    - 存在安全问题：序列化和反序列化本身并不存在问题。**但当输入的反序列化的数据可被用户控制，那么攻击者即可通过构造恶意输入，让反序列化产生非预期的对象，在此过程中执行构造的任意代码**

  - ```java
    @AllArgsConstructor
    @NoArgsConstructor
    @Getter
    @Builder
    @ToString
    public class RpcRequest implements Serializable {
        private static final long serialVersionUID = 1905122041950251207L;
        private String requestId;
        private String interfaceName;
        private String methodName;
        private Object[] parameters;
        private Class<?>[] paramTypes;
        private RpcMessageTypeEnum rpcMessageTypeEnum;
    }
    ```

  - serialVersionUID 有什么作用？
    序列化号 serialVersionUID 属于**版本控制的作用**。反序列化时，**会检查 serialVersionUID 是否和当前类的 serialVersionUID 一致。如果 serialVersionUID 不一致则会抛出 InvalidClassException 异常。**强烈推荐每个序列化类都**手动指定**其 serialVersionUID，如果不手动指定，那么编译器会动态生成默认的 serialVersionUID。

  - serialVersionUID 不是被 static 变量修饰了吗？为什么还会被“序列化”？

    - **static 修饰的变量是静态变量，属于类而非类的实例，本身是不会被序列化的。*
      然而，serialVersionUID 是一个特例，**serialVersionUID 的序列化做了特殊处理**。**
      **当一个对象被序列化时，**serialVersionUID 会被写入到序列化的二进制流中；
      **在**反序列化时，也会解析它并做一致性判断**，以此来验证序列化对象的版本一致性。
      如果两者不匹配，反序列化过程将抛出 InvalidClassException，因为这通常意味着序列化的类的定义已经发生了更改，可能不再兼容

- 比较常用的序列化协议有 **Hessian、Kryo、Protobuf、ProtoStuff**，这些都是**基于二进制的序**列化协议。

  1. Hessian

     Hessian 是一个**轻量级的，自定义描述的二进制 RPC 协议**。Hessian 是一个**比较老的序列化**实现了，并且同样也是**跨语言**的。

     Dubbo2.x 默认启用的序列化方式是 Hessian2 ,但是，Dubbo 对 Hessian2 进行了修改，不过大体结构还是差不多

  2. **Kryo**

     Kryo 是一个高性能的序列化/反序列化工具，由于其**变长存储特性并使用了字节码生成机制，拥有较高的运行速度和较小的字节码体积。**
     另外，Kryo 已经是一种非常成熟的序列化实现了，已经在 Twitter、Groupon、Yahoo 以及多个著名开源项目（如 Hive、Storm）中广泛的使用。

  3. Protobuf

     Protobuf 出自于 Google，**性能还比较优秀，也支持多种语言，同时还是跨平台的**。就是在**使用中过于繁琐，**因为你需要自己定义 IDL 文件和生成对应的序列化代码。这样虽然不灵活，但是，另一方面导致 protobuf **没有序列化漏洞的风险。**

  4. ProtoStuff

     protostuff 基于 Google protobuf，但是提供了更多的功能和更简易的用法。虽然更加易用，但是不代表 ProtoStuff 性能更差。

- 如果追求性能的话，Protobuf 序列化框架会比较合适，Protobuf 的这种数据存储格式，不仅压缩存储数据的效果好， 在编码和解码的性能方面也很高效。Protobuf 的编码和解码过程结合.proto 文件格式，加上 Protocol Buffer 独特的编码格式，只需要简单的数据运算以及位移等操作就可以完成编码与解码。可以说 Protobuf 的整体性能非常优秀。

- 像 **JSON 和 XML 这种属于文本类序列化方式**。虽然可读性比较好，但是**性能较差**，一般不会选择

### 怎么把一个对象从一个jvm转移到另一个jvm

1. **使用序列化和反序列化：**将对象序列化为字节流，并将其发送到另一个 JVM，然后在另一个 JVM 中反序列化字节流恢复对象。这可以通过 Java 的 ObjectOutputStream 和 ObjectInputStream 来实现。
2. **使用消息传递机制**：利用消息传递机制，比如使用消息队列（如 RabbitMQ、Kafka）或者通过网络套接字进行通信，将对象从一个 JVM 发送到另一个。这需要自定义协议来序列化对象并在另一个 JVM 中反序列化。
3. **使用远程方法调用（RPC）：**可以使用远程方法调用框架，如 gRPC，来实现对象在不同 JVM 之间的传输。远程方法调用可以让你在分布式系统中调用远程 JVM 上的对象的方法。
4. **使用共享数据库或缓存：**将对象存储在共享数据库（如 MySQL、PostgreSQL）或共享缓存（如 Redis）中，让不同的 JVM 可以访问这些共享数据。这种方法适用于需要共享数据但不需要直接传输对象的场景

### 将对象转为二进制字节流具体怎么实现？

**序列化和反序列化，无论这些可逆操作是什么机制，都会有对应的处理和解析协议，例**如加密和解密，TCP的粘包和拆包，序列化机制是通过序列化协议来进行处理的，和 class 文件类似，它其实是定义了序列化后的字节流格式，然后对此格式进行操作，生成符合格式的字节流或者将字节流解析成对象。

**在Java中通过序列化对象流来完成序列化和反序列化：**

- ObjectOutputStream：通过writeObject(）方法做序列化操作。
- ObjectInputStrean：通过readObject()方法做反序列化操作。

**只有实现了Serializable或Externalizable接口的类的对象才能被序列化，否则抛出异常！**

#### 实现对象序列化

1. 让类实现Serailizable接口

   ```java
   import java.io.Serializable;
   public class MyClass implements Serializable {
   // class code
   }
   ```

2. 创建输出流并写入对象

   ```java
   import java.io.FileOutputStream;
   import java.io.ObjectOutputStream;
   MyClass obj = new MyClass();
   try {
   FileOutputStream fileOut = new FileOutputStream("object.ser");
   ObjectOutputStream out = new ObjectOutputStream(fileOut);
   out.writeObject(obj);
   out.close();
   fileOut.close();
   } catch (IOException e) {
   e.printStackTrace();
   }
   ```

#### 实现对象反序列化

1. 创建输入流并读取对象

   ```java
   import java.io.FileInputStream;
   import java.io.ObjectInputStream;
   MyClass newObj = null;
   try {
   FileInputStream fileIn = new FileInputStream("object.ser");
   ObjectInputStream in = new ObjectInputStream(fileIn);
   newObj = (MyClass) in.readObject();
   in.close();
   fileIn.close();
   } catch (IOException | ClassNotFoundException e) {
   e.printStackTrace();
   }
   ```

   通过以上步骤，对象obj会被序列化并写入到文件"object.ser"中，然后通过反序列化操作，从文件中读取字节流并恢复为对象newObj。这种方式可以方便地将对象转换为字节流用于持久化存储、网络传输等操作。需要注意的是，要确保类实现了Serializable接口，并且所有成员变量都是Serializable的才能被正确序列化。