# JavaWeb

[视频课程]: https://www.bilibili.com/video/BV1Qf4y1T7Hx?share_source=copy_pc

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240215211325838.png" alt="image-20240215211325838" style="zoom:50%;" />

## JDBC

使用Java语言操作关系型数据库的一套API。（Java DataBase Connectivity），即Java数据库连接。

### 步骤

1. 创建工程，导入驱动jar包

2. 注册驱动 

   ```java
   Class.forName("com.mysql.cj.jdbc.Driver");
   ```

3. 获取连接

   ```java
   Connection conn = DriverManager.getConnection(url,username,password);
   ```

4. 定义SQL语句

   ```java
   String sql = "update...";
   ```

5. 获取执行SQL对象

   ```java
   Statement stmt = conn.createStatement();
   ```

6. 执行SQL

   ```java
   stmt.executeUpdate(sql);
   ```

7. 处理返回结果

8. 释放资源

   ```mysql
   ... .close();
   ```

### DriverManage

​	DriverManage（驱动管理类）作用：

- 注册驱动
- 获取数据库连接

​	提示：

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216103157256.png" alt="image-20240216103157256" style="zoom:50%;" />

#### 获取连接

```mysql
getConnection(String url,String user,String password);
方法返回数据类型 static Connection
```

  <img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216110621782.png" alt="image-20240216110621782" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216110854884.png" alt="image-20240216110854884" style="zoom:50%;" />

### Connection

Connection（数据库连接对象）作用：

- 获取执行SQL对象
- 管理事务

#### 获取执行SQL对象

- 普遍执行SQL对象

  ```java
  createStatement()
  方法返回数据类型 Statement 
  ```

- 预编译SQL的执行SQL对象:防止SQL注入

  ```java
  prepareStatement(sql)
  方法返回数据类型  PreparedStatement
  ```

- 执行存储过程的对象

  ```mysql
  prepareCall(sql)
  方法返回数据类型	CallableStatement
  ```

#### 事务管理

- MySQL事务管理

  <img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216111725342.png" alt="image-20240216111725342" style="zoom:50%;" />

- JDBC事务管理：Connection接口中定义了三个对应的方法

  ```java
  开启事务：setAutoCommit(boolean autoCommit)
      true自动提交事务 false手动提交事务
  提交事务：commit()
  回滚事务：rollback()
  ```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216112206693.png" alt="image-20240216112206693" style="zoom:50%;" />

### Statement

Statement作用：执行SQL语句

```java
int executeUpdate(Sql) 
//执行DML、DDL语句
//返回值
	1.DML语句影响的行数
	2.DDL语句执行后，执行成功也可能返回0
```

```java
ResultSet executeQuery(Sql)
//执行DQL语句
//返回值
	ResultSet结果集对象
```

（33-42）

## Maven

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216113537786.png" alt="image-20240216113537786" style="zoom:50%;" />

依赖管理其实就是管理你项目所依赖的第三方资源（jar包、插件）.Maven使用标准的坐标配置来管理各种依赖，只需要简单的配置就可以完成依赖管理

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216115615819.png" alt="image-20240216115615819" style="zoom:50%;" />

仓库分类：

- 本地仓库
- 中央仓库
- 远程仓库

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216121022857.png" alt="image-20240216121022857" style="zoom:50%;" />

### Maven基本使用

#### Maven常用命令

- compile
- clean
- test
- package
- install

#### Maven生命周期



# JavaWeb

https://www.bilibili.com/video/BV1UN411x7xe?p=3&share_source=copy_pc

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216172058566.png" alt="image-20240216172058566" style="zoom:50%;" />

## HTML&CSS

HTML是Hyper Text Markup Language的缩写。意思是超文本标记语言。它的作用是搭建网页结构，在网页上展示内容

- html文件是浏览器负责解析和展示
- html是纯文件文件，普通编辑工具都可以编辑

### HTML基础结构

> 1 文档声明

+ HTML文件中第一行的内容，用来告诉浏览器当前HTML文档的基本信息，其中最重要的就是当前HTML文档遵循的语法标准。这里我们只需要知道HTML有4和5这两个大的版本
+ HTML4版本的文档类型声明是：

```HTML
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
```

+ HTML5版本的文档类型声明是：

``` html
<!DOCTYPE html>
```

+ 现在主流的技术选型都是使用HTML5，之前的版本基本不用了。

> 2根标签	

+ html标签是整个文档的根标签，所有其他标签都必须放在html标签里面。

> 3头部元素

+ head标签用于定义文档的头部，其他头部元素都放在head标签里。头部元素包括title标签、script标签、style标签、link标签、meta标签等等。

> 4主体元素

+ body标签定义网页的主体内容，在浏览器窗口内显示的内容都定义到body标签内。

> 5注释

+ HTML注释的写法是

``` html
<!-- 注释内容 -->
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240216175211818.png" alt="image-20240216175211818" style="zoom: 33%;" />

### HTML概念词汇解释

> 标签

+ 代码中的一个 <> 叫做一个标签,有些标签成对出现,称之为双标签,有些标签单独出现,称之为单标签

> 属性

+ 一般在开始标签中,用于定义标签的一些特征

> 文本

+ 双标签中间的文字,包含空格换行等结构

> 元素

+ 经过浏览器解析后,**每一个完整的标签(标签+属性+文本)**可以称之为一个元素

### HTML的语法规则

1.  **根标签有且只能有一个**

2.  无论是双标签还是单标签**都需要正确关闭**

3.  标签可以嵌套但不能交叉嵌套

4.  注释语法为<!-- -->  ,注意不能嵌套

5.  属性必须有值，**值必须加引号**,H5中属性名和值相同时可以省略属性值

6.  HTML中**不严格区分字符串使用单双引号**

7.  HTML标签**不严格区分大小写,但是不能大小写混用**

8.  HTML中不允许自定义标签名,强行自定义则无效

####  标题标签

``` html
<body>
    <h1>一级标题</h1>
    <h2>二级标题</h2>
    <h3>三级标题</h3>
    <h4>四级标题</h4>
    <h5>五级标题</h5>
    <h6>六级标题</h6>
</body>
```

#### 段落标签

> 段落标签一般用于定义一些在页面上要显示的大段文字,多个段落标签之间实现**自动分段**的效果

``` html
<body>
    <p>
        记者从工信部了解到，近年来我国算力产业规模快速增长，年增长率近30%，算力规模排名全球第二。
    </p>
    <p>
        工信部统计显示，截至去年底，我国算力总规模达到180百亿亿次浮点运算/秒，存力总规模超过1000EB（1万亿GB）。
        国家枢纽节点间的网络单向时延降低到20毫秒以内，算力核心产业规模达到1.8万亿元。中国信息通信研究院测算，
        算力每投入1元，将带动3至4元的GDP经济增长。
    </p>
    <p> 
        近年来，我国算力基础设施发展成效显著，梯次优化的算力供给体系初步构建，算力基础设施的综合能力显著提升。
        当前，算力正朝智能敏捷、绿色低碳、安全可靠方向发展。
    </p>
</body>
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681180017304.png" alt="1681180017304" style="zoom: 67%;" />

#### 换行标签

> 单纯**实现换行的标签是br**,**如果想添加分隔线,可以使用hr标签**

``` html
<body>
        工信部统计显示，截至去年底，我国算力总规模达到180百亿亿次浮点运算/秒，存力总规模超过1000EB（1万亿GB）。
    <br>
        国家枢纽节点间的网络单向时延降低到20毫秒以内，算力核心产业规模达到1.8万亿元。
    <hr>
        中国信息通信研究院测算，算力每投入1元，将带动3至4元的GDP经济增长。
</body>
```

+ 效果

![1681180239241](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681180239241.png)

#### 列表标签

> 有序列表  分条列项展示数据的标签, 其每一项前面的符号带有顺序特征

+ 有序列表标签 ol
+ 列表项标签 li

``` html
<ol>
    <li>JAVA</li>
    <li>前端</li>
    <li>大数据</li>
</ol>
```

+ 效果

![1681194349015](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681194349015.png)

> 无序列表 分条列项展示数据的标签, 其每一项前面的符号不带有顺序特征

+ 无序列表标签 ul
+ 列表项标签 li

``` html
<ul>
    <li>JAVASE</li>
    <li>JAVAEE</li>
    <li>数据库</li>
</ul>
```

+ 效果

![1681194434091](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681194434091.png)

> 嵌套列表 列表和列表之前可以签到,实现某一项内容详细展示

``` html
<ol>
    <li>
        JAVA
        <ul>
            <li>JAVASE</li>
            <li>JAVAEE</li>
            <li>数据库</li>
        </ul>
    </li>
    <li>前端</li>
    <li>大数据</li>
</ol>
```

+ 效果

![1681194504371](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681194504371.png)

#### 超链接标签

> 点击后带有链接跳转的标签 ,也叫作a标签

+ href属性用于定义要跳转的目标资源的地址
  + href中可以使用**绝对路径**,以/开头,始终以一个固定路径作为基准路径作为出发点
  + href中也可以使用**相对路径**,不以/开头,以当前文件所在路径为出发点
    + ./表示的当前资源的所在路径，可以省略不写
    + ../父级路径
  + href中也可以定义**完整的URL**
  
+ target用于定义打开的方式
  +  _**blank 在新窗口**中打开目标资源
  +  _**self  在当前窗口**中打开目标资源

+ 代码

``` html
<body>
   <a href="01html的基本结构.html" target="_blank">相对路径本地资源连接</a> <br>
   <a href="/day01-html/01html的基本结构.html" target="_self">绝对路径本地资源连接</a> <br>
   <a href="http://www.atguigu.com" target="_blank">外部资源链接</a> <br>
   
</body>
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\chaolianjiex.gif" alt="chaolianjiex" style="zoom:67%;" />

#### 多媒体标签

> img(重点) 图片标签,用于在页面上引入图片

``` html
<!-- 
src
	用于定义图片的路径（url、相对路径、绝对路径）
title
	用于定义鼠标悬停时显示的文字
alt
	用于定义图片加载失败时显示的提示文字
-->
<img src="img/logo.png"  title="尚硅谷" alt="尚硅谷logo" />
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681196307844.png" alt="1681196307844" style="zoom:67%;" />

> audio 用于在页面上引入一段声音

``` html
   <!-- 
    src
        用于定义目标声音资源
    autoplay
        用于控制打开页面时是否自动播放
    controls
        用于控制是否展示控制面板
    loop
        用于控制是否进行循环播放
    --> 
   <audio src="img/music.mp3" autoplay="autoplay" controls="controls" loop="loop" />
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681196276582.png" alt="1681196276582" style="zoom: 67%;" />

> video 用于在页面上引入一段视频

``` html
<body>
   <!-- 
    src
        用于定义目标视频资源
    autoplay
        用于控制打开页面时是否自动播放
    controls
        用于控制是否展示控制面板
    loop
        用于控制是否进行循环播放
    --> 
   <video src="img/movie.mp4" autoplay="autoplay" controls="controls" loop="loop" width="400px" />
</body>
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681196233304.png" alt="1681196233304" style="zoom: 67%;" />

#### 表格标签(重点)

> 常规表格

+ table标签 代表表格
+ thead标签 代表表头 可以省略不写
+ tbody标签 代表表体 可以省略不写
+ tfoot标签 代表表尾  可以省略不写
+ tr标签 代表一行
+ td标签 代表行内的一格
+ th标签 自带加粗和居中效果的td

+ 代码

``` html
    <h3 style="text-align: center;">员工技能竞赛评分表</h3>
    <table  border="1px" style="width: 400px; margin: 0px auto;">
        <tr>
            <th>排名</th>
            <th>姓名</th>
            <th>分数</th>
        </tr>
        <tr>
            <td>1</td>
            <td>张小明</td>
            <td>100</td>
        </tr>
        <tr>
            <td>2</td>
            <td>李小东</td></td>
            <td>99</td>
        </tr>
        <tr>
            <td>3</td>
            <td>王小虎</td>
            <td>98</td>
        </tr>
    </table>
```

+ 展示效果

![1681196961386](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681196961386.png)

> 单元格跨行

+ 通过td的rowspan属性实现上下跨行

+ 代码

``` html
...
        <tr>
            <td>1</td>
            <td>张小明</td>
            <td>100</td>
            <td rowspan="3">     <!-- -->
                前三名升职加薪
            </td>
        </tr>
        <tr>
            <td>2</td>
            <td>李小东</td></td>
            <td>99</td>			<!--删了一格 -->
        </tr>
        <tr>
            <td>3</td>
            <td>王小虎</td>
            <td>98</td>		    <!--删了一格 -->
        </tr>
    </table>
```

+ 效果

![1681197062594](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681197062594.png)

> 单元格跨列

+ 通过td的colspan属性实现左右的跨列

+ 代码

``` html
    <h3 style="text-align: center;">员工技能竞赛评分表</h3>
    <table  border="1px" style="width: 400px; margin: 0px auto;">
        <tr>
            <th>排名</th>
            <th>姓名</th>
            <th>分数</th>
            <th>备注</th>
        </tr>
        <tr>
            <td>1</td>
            <td>张小明</td>
            <td>100</td>
            <td rowspan="6">
                前三名升职加薪
            </td>
        </tr>
        <tr>
            <td>2</td>
            <td>李小东</td></td>
            <td>99</td>
        </tr>
        <tr>
            <td>3</td>
            <td>王小虎</td>
            <td>98</td>
        </tr>
        <tr>
            <td>总人数</td>
            <td colspan="2">2000</td>
        </tr>
        <tr>
            <td>平均分</td>
            <td colspan="2">90</td>
        </tr>
        <tr>
            <td>及格率</td>
            <td colspan="2">80%</td>
        </tr>
    </table>
```

+ 效果

![1681197299564](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681197299564.png)

#### 表单标签(重点)

> 表单标签,可以实现让用户**在界面上输入各种信息并提交的一种标签.** 是向服务端发送数据主要的方式之一

+ **form标签**,表单标签,其内部用于定义可以让用户输入信息的表单项标签
  
  //提交表单 弹窗的三种方式
  
  <img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240226204207298.png" alt="image-20240226204207298" style="zoom:50%;" />
  
  + action, form标签的属性之一,用**于定义信息提交的服务器的地址**
  
    + URL
    + 相对路径
    + 绝对路径
  + method, form标签的属性之一,**用于定义信息的提交方式**
    + get    get方式, 数据会以键值对形式缀到url后,以?作为参数开始的标识,多个参数用&隔开。
  
      ​	**数据会暴露在地址栏上，相对不安全；地址栏长度有限制，所以提交的数据量不大。地址栏上只能是字符，不能提交文件；相对于post效率高**
  
      ```html
      url?key=value&key=value&key=value
      ```
    + post  post方式,数据会通过请求体发送,不会在缀到url后
  
      ​	**参数默认不会放到url后面，数据不会直接暴露到地址栏上，相对安全；数据是单独打包通过请求体发送，提交的数据量比较大；请求体中，可以是字符、字节数据、文件**
    + put
    + delete
  
+ <img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240217095113574.png" alt="image-20240217095113574" style="zoom:50%;" />
  
+ input标签,主要的**表单项标签**,可以用于定义表单项。**表达项标签一定要定义name属性，该属性用于明确提交时的参数名；还需要定义value属性，该属性用于明确提交时的实参**
  
  + name, input标签的属性之一**,用于定义提交的参数名**
  + type, input标签的属性之一,用于定义表单项类型
    + **text   单行普通文本框**
    + **password 密码框**
    + **submit 提交按钮**
    + **reset    重置按钮**  

``` html
   <form action="http://www.atguigu.com" method="get">
        用户名 <input type="text" name="username" /> <br>
        密&nbsp;&nbsp;&nbsp;码 <input type="password" name="password" /> <br>
        <input type="submit"  value="登录" />
        <input type="reset"  value="重置" />
   </form>
```

![1681198068548](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681198068548.png)

#### 常见表单项标签(重点)

> 单行文本框 test

``` html
个性签名：<input type="text" name="signal"/><br/>
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681198354039.png" alt="1681198354039" style="zoom:50%;" />



> 密码框 password

``` html
密码：<input type="password" name="secret"/><br/>
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681198393831.png" alt="1681198393831" style="zoom:50%;" />



> 单选框 radio

``` html 
你的性别是：
<input type="radio" name="sex" value="spring" />男
<input type="radio" name="sex" value="summer" checked="checked" />女
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681198448345.png" alt="1681198448345" style="zoom:50%;" />

+ 说明
  + **name属性相同的radio为一组，组内互斥**
  + 当用户选择了一个radio并提交表单，**这个radio的name属性和value属性组成一个键值对发送给服务器**
  + **设置checked="checked"属性设置默认被选中的radio**
  + **如果属性名和属性值一样的话，可以省略属性值，只写checked即可**

> 复选框 checkbox

``` html
你喜欢的球队是：
<input type="checkbox" name="team" value="Brazil"/>巴西
<input type="checkbox" name="team" value="German" checked/>德国
<input type="checkbox" name="team" value="France"/>法国
<input type="checkbox" name="team" value="China" checked="checked"/>中国
<input type="checkbox" name="team" value="Italian"/>意大利
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681198540737.png" alt="1681198540737" style="zoom:50%;" />

+ 说明
  + 设置checked="checked"属性设置默认被选中的checkbox

> 下拉框 option

``` html
你喜欢的运动是：
<select name="interesting">
    <option value="swimming">游泳</option>
    <option value="running">跑步</option>
    <option value="shooting" selected="selected">射击</option>
    <option value="skating">溜冰</option>
</select>
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681199376475.png" alt="1681199376475" style="zoom:50%;" />

+ 说明
  + 下拉列表用到了两种标签，其中select标签用来定义下拉列表，而option标签设置列表项。
  + name属性在select标签中设置,value属性在option标签中设置。
  + option标签的标签体是显示出来给用户看的，**提交到服务器的是value属性的值。**
  + 通过在option标签中**设置selected="selected"属性实现默认选中的效果。**

> 按钮 button

``` html
<button type="button">普通按钮</button>或<input type="button" value="普通按钮"/>
<button type="reset">重置按钮</button>或<input type="reset" value="重置按钮"/>
<button type="submit">提交按钮</button>或<input type="submit" value="提交按钮"/>
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681199471712.png" alt="1681199471712" style="zoom:50%;" />



+ 说明
  + 普通按钮: 点击后无效果，需要通过JavaScript绑定单击响应函数
  + 重置按钮: 点击后将表单内的所有表单项都恢复为默认值
  + 提交按钮: 点击后提交表单

> 隐藏域 hidden

``` html
<input type="hidden" name="userId" value="2233"/>
```

+ 说明
  + 通过表单隐藏域设置的表单项不会显示到页面上，用户看不到。但是提交表单时会一起被提交。用来设置一些需要和表单一起提交但是不希望用户看到的数据，例如：用户id等等。
  + ![image-20240217094112765](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240217094112765.png)readonly、disabled

> 多行文本框 testarea

``` html
自我介绍：<textarea name="desc"></textarea>
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681199589613.png" alt="1681199589613" style="zoom:50%;" />

+ 说明
  + textarea没有value属性，如果要设置默认值需要写在开始和结束标签之间。

> 文件标签 file

``` html
头像:<input type="file" name="file"/>
```

![1681199672580](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681199672580.png)

+ 说明
  + 不同浏览器显示的样式有微小差异

#### 布局相关标签

> **div标签  俗称"块",自己独占一行的元素，主要用于划分页面结构,做页面布局** 

> span标签 俗称"层",不会自己独占一行的元素，主要用于划分元素范围,配合CSS做页面元素样式的修饰，宽和高很多都是不生效的

+  代码

``` html
    <div style="width: 500px; height: 400px;background-color: cadetblue;">
        <div style="width: 400px; height: 100px;background-color: beige;margin: 10px auto;">
            <span style="color: blueviolet;">页面开头部分</span>
        </div> 
        <div style="width: 400px; height: 100px;background-color: blanchedalmond;margin: 10px auto;">
            <span style="color: blueviolet;">页面中间部分</span>
        </div> 
        <div style="width: 400px; height: 100px;background-color: burlywood;margin: 10px auto;">
            <span style="color: blueviolet;">页面结尾部分</span>
        </div> 
    </div>
```

+ 展示效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681200198741.png" alt="1681200198741" style="zoom:67%;" />

#### 特殊字符

> 对于有特殊含义的字符,需要通过转移字符来表示

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681200435834.png" alt="1681200435834"  />

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681200467767.png" alt="1681200467767"  />

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681200487125.png" alt="1681200487125"  />

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681200503798.png" alt="1681200503798"  />

``` html
    &lt;span&gt;  <br>
    &lt;a href="http://www.atguigu.com"&gt;尚&nbsp;硅&nbsp;谷&lt;/a&gt; <br>
    &amp;amp;  
```

![1681200662087](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681200662087.png)

### CSS的使用

> CSS  层叠样式表(英文全称：(Cascading Style Sheets)   能够对网页中元素位置的排版进行像素级精确控制，支持几乎所有的字体字号样式，拥有对网页对象和模型样式编辑的能力 ,简单来说,美化页面

### CSS引入方式

- 行内式
- 内嵌式
- 外部样式表

> 行内式,通过元素开始标签的style属性引入, 
>
> 样式语法为       样式名:样式值; 样式名:样式值;

```html
    <input 
        type="button" 
        value="按钮"
        style="
            display: block;
            width: 60px; 
            height: 40px; 
            background-color: rgb(140, 235, 100); 
            color: white;
            border: 3px solid green;
            font-size: 22px;
            font-family: '隶书';
            line-height: 30px;
            border-radius: 5px;
    "/> 
```

![1681201322584](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681201322584.png)

+ 缺点
  + html代码和css样式代码交织在一起,增加阅读难度和维护成本
  + css样式代码仅对当前元素有效,代码重复量高,复用度低

> 内嵌式

``` html
<head>
    <meta charset="UTF-8">
    <style>
        /* 通过选择器确定样式的作用范围 */
        input {
            display: block;
            width: 80px; 
            height: 40px; 
            background-color: rgb(140, 235, 100); 
            color: white;
            border: 3px solid green;
            font-size: 22px;
            font-family: '隶书';
            line-height: 30px;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <input type="button" value="按钮1"/> 
    <input type="button" value="按钮2"/> 
    <input type="button" value="按钮3"/> 
    <input type="button" value="按钮4"/> 
</body>
```

+ 效果

![1681201553427](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681201553427.png)

+ 说明
  + **内嵌式样式需要在head标签中,通过一对style标签定义CSS样式**
  + CSS样式的作用范围控制要依赖选择器
  + CSS的样式代码中注释的方式为  /*   */
  + 内嵌式虽然对样式代码做了抽取,但是CSS代码仍然在html文件中
  + 内嵌样式仅仅能作用于当前文件,代码复用度还是不够,不利于网站风格统一

> 连接式/外部样式表

+ 可以在项目单独创建css样式文件,专门用于存放CSS样式代码，只把选择器部分带过去

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681202361429.png" alt="1681202361429" style="zoom: 67%;" />

+ 在head标签中,通过link标签引入外部CSS样式即可	
  + href	引入路径
  + rel       引入类型 


``` html
<head>
    <meta charset="UTF-8">
    <link href="css/buttons.css" rel="stylesheet" type="text/css"/>
</head>
<body>
    <input type="button" value="按钮1"/> 
    <input type="button" value="按钮2"/> 
    <input type="button" value="按钮3"/> 
    <input type="button" value="按钮4"/> 
</body>
```

+ 说明
  + CSS样式代码从html文件中剥离,利于代码的维护
  + CSS样式文件可以被多个不同的html引入,利于网站风格统一

### CSS选择器

- 元素选择器
- id选择器
- class选择器

> 元素选择器

``` html
<head>
    <meta charset="UTF-8">
   <style>
    input {
        display: block;
        width: 80px; 
        height: 40px; 
        background-color: rgb(140, 235, 100); 
        color: white;
        border: 3px solid green;
        font-size: 22px;
        font-family: '隶书';
        line-height: 30px;
        border-radius: 5px;
    }
   </style>
</head>
<body>
    <input type="button" value="按钮1"/> 
    <input type="button" value="按钮2"/> 
    <input type="button" value="按钮3"/> 
    <input type="button" value="按钮4"/> 
    <button>按钮5</button>
</body>
```

+ sss效果

![1681203591080](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681203591080.png)

+ 说明
  + 根据标签名确定样式的作用范围
  + 语法为  元素名 {}
  + 样式**只能作用到同名标签上,其他标签不可用**
  + 相同的标签未必需要相同的样式,会造成样式的作用范围太大

> id选择器

+ 代码

``` html
<head>
    <meta charset="UTF-8">
   <style>
    #btn1 {
        display: block;
        width: 80px; 
        height: 40px; 
        background-color: rgb(140, 235, 100); 
        color: white;
        border: 3px solid green;
        font-size: 22px;
        font-family: '隶书';
        line-height: 30px;
        border-radius: 5px;
    }
   </style>
</head>
<body>
    <input id="btn1" type="button" value="按钮1"/> 
    <input id="btn2" type="button" value="按钮2"/> 
    <input id="btn3" type="button" value="按钮3"/> 
    <input id="btn4" type="button" value="按钮4"/> 
    <button id="btn5">按钮5</button>
</body>
```

+ 效果

![1681203748017](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681203748017.png)

+ 说明
  + 根据元素id属性的值确定样式的作用范围
  + 语法为  #id值 {}
  + id属性的值在页面上具有唯一性,**所以id选择器也只能影响一个元素的样式**
  + 因为id属性值不够灵活,所以使用该选择器的情况较少

> class选择器

+ 代码

``` html
<head>
    <meta charset="UTF-8">
   <style>
    .shapeClass {
        display: block;
        width: 80px; 
        height: 40px; 
        border-radius: 5px;
    }
    .colorClass{
        background-color: rgb(140, 235, 100); 
        color: white;
        border: 3px solid green;
    }
    .fontClass {
        font-size: 22px;
        font-family: '隶书';
        line-height: 30px;
    }

   </style>
</head>
<body>
    <input  class ="shapeClass colorClass fontClass"type="button" value="按钮1"/> 
    <input  class ="shapeClass colorClass" type="button" value="按钮2"/> 
    <input  class ="colorClass fontClass" type="button" value="按钮3"/> 
    <input  class ="fontClass" type="button" value="按钮4"/> 
    <button class="shapeClass colorClass fontClass" >按钮5</button>
</body>
```

+ 效果

![1681204269702](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681204269702.png)

+ 说明
  + 根据元素class属性的值确定样式的作用范围
  + 语法为  .class值 {}
  + class属性值可以有一个,也可以有多个,多个不同的标签也可以是使用相同的class值
  + 多个选择器的样式可以在同一个元素上进行叠加
  + 因为class选择器非常灵活,所以在CSS中,使用该选择器的情况较多

### CSS浮动

> CSS 的 Float（浮动）使元素脱离文档流，按照指定的方向（左或右发生移动），直到它的外边缘碰到包含框或另一个浮动框的边框为止。

+ 浮动设计的初衷为了解决文字环绕图片问题，浮动后一定不会将文字挡住，这是设计初衷。
+ 文档流是是文档中可显示对象在排列时所占用的位置/空间，而脱离文档流就是在页面中不占位置了。  

> 浮动原理

+  当把框 1 向右浮动时，它脱离文档流并且向右移动，直到它的右边缘碰到包含框的右边缘

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681260732580.png" alt="1681260732580" style="zoom: 80%;" />

+ 当框 1 向左浮动时，它脱离文档流并且向左移动，直到它的左边缘碰到包含框的左边缘。因为它不再处于文档流中，所以它不占据空间，实际上覆盖住了框 2，使框 2 从视图中消失。如果把所有三个框都向左移动，那么框 1 向左浮动直到碰到包含框，另外两个框向左浮动直到碰到前一个浮动框。**文本会从框2移到框3显示**

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681260842005.png" alt="1681260842005" style="zoom: 80%;" />

+  如果包含框太窄，无法容纳水平排列的三个浮动元素，那么其它浮动块向下移动，直到有足够的空间。如果浮动元素的高度不同，那么当它们向下移动时可能被其它浮动元素“卡住”

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681260887708.png" alt="1681260887708" style="zoom: 80%;" />

> 浮动的样式名:float

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681260937920.png" alt="1681260937920" style="zoom:80%;" />

``` html
<head>
    <meta charset="UTF-8">
   <style>
    .outerDiv {
        width: 500px;
        height: 300px;
        border: 1px solid green;
        background-color: rgb(230, 224, 224);
    }
    .innerDiv{
        width: 100px;
        height: 100px;
        border: 1px solid blue;
        float: left;
    }
    .d1{
        background-color: greenyellow;
       /*  float: right; */
    }
    .d2{
        background-color: rgb(79, 230, 124);
    }
    .d3{
        background-color: rgb(26, 165, 208);
    }
   </style>
</head>
<body>
   <div class="outerDiv">
        <div class="innerDiv d1">框1</div>
        <div class="innerDiv d2">框2</div>
        <div class="innerDiv d3">框3</div>
   </div> 
</body>
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681261311289.png" alt="1681261311289" style="zoom: 67%;" />

### CSS定位

- position
  - static
  - absolute
  - relative
  - fixed
- left
- right
- top
- bottom

> **position 属性指定了元素的定位类型。**

+ 这个属性定义建立元素布局所用的定位机制。任何元素都可以定位，不过绝对或固定元素会生成一个块级框，而不论该元素本身是什么类型。相对定位元素会相对于它在正常流中的默认位置偏移。

+ 元素可以使用的顶部，底部，左侧和右侧属性定位。然而，这些属性无法工作，除非是先设定position属性。他们也有不同的工作方式，这取决于定位方法。

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681261377875.png" alt="1681261377875" style="zoom: 80%;" />

> 静态定位

+ 说明
  + 不设置的时候的默认值就是static，静态定位，没有定位，元素出现在该出现的位置，块级元素垂直排列，行内元素水平排列
+ 代码

``` html
<head>
    <meta charset="UTF-8">
    <style>
        .innerDiv{
                width: 100px;
                height: 100px;
        }
        .d1{
            background-color: rgb(166, 247, 46);
            position: static;
        }
        .d2{
            background-color: rgb(79, 230, 124);
        }
        .d3{
            background-color: rgb(26, 165, 208);
        }
    </style>
</head>
<body>
        <div class="innerDiv d1">框1</div>
        <div class="innerDiv d2">框2</div>
        <div class="innerDiv d3">框3</div>
</body>
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681261602297.png" alt="1681261602297" style="zoom:50%;" />



> 绝对定位 

+ 说明
  + absolute ,通过 top left right bottom 指定元素在页面上的固定位置
  + **定位后元素会让出原来位置,其他元素可以占用**

+ 代码

``` html
<head>
    <meta charset="UTF-8">
    <style>
        .innerDiv{
                width: 100px;
                height: 100px;
        }
        .d1{
            background-color: rgb(166, 247, 46);
            position: absolute;
            left: 300px;
            top: 100px;
        }
        .d2{
            background-color: rgb(79, 230, 124);
        }
        .d3{
            background-color: rgb(26, 165, 208);
        }
    </style>
</head>
<body>
        <div class="innerDiv d1">框1</div>
        <div class="innerDiv d2">框2</div>
        <div class="innerDiv d3">框3</div>
</body>
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681261830830.png" alt="1681261830830" style="zoom:50%;" />

> 相对定位

+ 说明
  + relative 相对于自己原来的位置进行地位
  + **定位后保留原来的站位,其他元素不会移动到该位置**

+ 代码

``` html
<head>
    <meta charset="UTF-8">
    <style>
        .innerDiv{
                width: 100px;
                height: 100px;
        }
        .d1{
            background-color: rgb(166, 247, 46);
            position: relative;
            left: 30px;
            top: 30px;
        }
        .d2{
            background-color: rgb(79, 230, 124);
        }
        .d3{
            background-color: rgb(26, 165, 208);
        }
    </style>
</head>
<body>
        <div class="innerDiv d1">框1</div>
        <div class="innerDiv d2">框2</div>
        <div class="innerDiv d3">框3</div>
</body>
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681261993904.png" alt="1681261993904" style="zoom:50%;" />



> 固定定位

+ 说明
  + fixed 在浏览器窗口固定位置**,不会随着页面的上下移动而移动**
  + **元素定位后会让出原来的位置,其他元素可以占用**
+ 代码

``` html
<head>
    <meta charset="UTF-8">
    <style>
        .innerDiv{
                width: 100px;
                height: 100px;
        }
        .d1{
            background-color: rgb(166, 247, 46);
            position: fixed;
            right: 30px;
            top: 30px;
        }
        .d2{
            background-color: rgb(79, 230, 124);
        }
        .d3{
            background-color: rgb(26, 165, 208);
        }
    </style>
</head>
<body>
        <div class="innerDiv d1">框1</div>
        <div class="innerDiv d2">框2</div>
        <div class="innerDiv d3">框3</div>
        br*100+tab
</body>
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\fixeddingwei.gif" alt="fixeddingwei" style="zoom:50%;" />

###  CSS盒子模型

> 所有HTML元素可以看作盒子，在CSS中，"box model"这一术语是用来设计和布局时使用。

+ CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：**边距（margin），边框（border），填充（padding），和实际内容（content）**

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681262535006.png" alt="1681262535006" style="zoom:67%;" />

+ 说明：
  + Margin(外边距) - 清除边框外的区域，外边距是透明的。
  + Border(边框) - 围绕在内边距和内容外的边框。
  + Padding(内边距) - 清除内容周围的区域，内边距是透明的。(默认不会侵占内部内容的部分)
  + Content(内容) - 盒子的内容，显示文本和图像。

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681262585852.png" alt="1681262585852" style="zoom:67%;" />

+ 代码

``` html
    <head>
        <meta charset="UTF-8">
       <style>
        .outerDiv {
            width: 800px;
            height: 300px;
            border: 1px solid green;
            background-color: rgb(230, 224, 224);
            margin: 0px auto;
        }
        .innerDiv{
            width: 100px;
            height: 100px;
            border: 1px solid blue;
            float: left;
            /* margin-top: 10px;
            margin-right: 20px;
            margin-bottom: 30px;
            margin-left: 40px; */
            
            /*简写 顺时针 上右下左 */
            margin: 10px 20px 30px 40px;
           
        }
        .d1{
            background-color: greenyellow;
            /* padding-top: 10px;
            padding-right: 20px;
            padding-bottom: 30px;
            padding-left: 40px; */
            padding: 10px 20px 30px 40px;
        }
        .d2{
            background-color: rgb(79, 230, 124);
        }
        .d3{
            background-color: rgb(26, 165, 208);
        }
       </style>
    </head>
    <body>
       <div class="outerDiv">
            <div class="innerDiv d1">框1</div>
            <div class="innerDiv d2">框2</div>
            <div class="innerDiv d3">框3</div>
       </div> 
    </body>
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681263227281.png" alt="1681263227281" style="zoom: 67%;" />

+ 在浏览器上,通过F12工具查看盒子模型状态

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681263265604.png" alt="1681263265604" style="zoom: 67%;" />

## JavaScript

+ 脚本语言
  + JavaScript是一种解释型的脚本语言。不同于C、C++、Java等语言先编译后执行,	**JavaScript不会产生编译出来的字节码文件，**而是在程序的运行过程中对源文件逐行进行解释。

+ 基于对象
  + JavaScript是一种基于对象的脚本语言，它不仅可以创建对象，也能使用现有的对象。但是面向对象的三大特性：『封装』、『继承』、『多态』中，JavaScript能够实现封装，可以模拟继承，不支持多态，所以**它不是一门面向对象的编程语言。**
+ 弱类型
  + JavaScript中也有明确的数据类型，**但是声明一个变量后它可以接收任何类型的数据，并且会在程序执行过程中根据上下文自动转换类型。**
+ 事件驱动
  + JavaScript是一种采用事件驱动的脚本语言，它不需要经过Web服务器就可以对用户的输入做出响应。
+ 跨平台性
  + JavaScript脚本语言不依赖于操作系统，仅需要浏览器的支持。因此一个JavaScript脚本在编写后可以带到任意机器上使用，前提是机器上的浏览器支持JavaScrip t脚本语言。目前JavaScript已被大多数的浏览器所支持。

### JS的引入方式

- 内部脚本方式引入
- 外部脚本方式引入

> 内部脚本方式引入

+ 说明
  + 在页面中,通过一对script标签引入JS代码
  + script代码放置位置具备一定的随意性,一般放在head标签中居多

``` html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>小标题</title>
        <style>
            /* 通过选择器确定样式的作用范围 */
            .btn1 {
                display: block;
                width: 150px; 
                height: 40px; 
                background-color: rgb(245, 241, 129); 
                color: rgb(238, 31, 31);
                border: 3px solid rgb(238, 23, 66);
                font-size: 22px;
                font-family: '隶书';
                line-height: 30px;
                border-radius: 5px;
            }
        </style>
        <script>
            function suprise(){
                alert("Hello,我是惊喜")
            }
        </script>
    </head>
    <body>
        <button class="btn1" onclick="suprise()">点我有惊喜</button>
    </body>
</html>
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\jingxi.gif" alt="jingxi" style="zoom:67%;" />

> 外部脚本方式引入

+ 说明
  + 内部脚本仅能在当前页面上使用,代码复用度不高
  + 可以将脚本放在独立的js文件中,通过script标签引入外部脚本文件
  + 一对script标签要么用于定义内部脚本,要么用于引入外部js文件,不能混用
  + script标签如果用于引入外部js文件，中间最好不要有任何字符，包括空格和换行
  + 一个html文档中,可以有多个script标签 
+ 抽取脚本代码到独立的js文件中

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681270974917.png" alt="1681270974917" style="zoom:80%;" />

+ 在html文件中,通过script标签引入外部脚本文件

``` html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>小标题</title>
        <style>
            /* 通过选择器确定样式的作用范围 */
            .btn1 {
                display: block;
                width: 150px; 
                height: 40px; 
                background-color: rgb(245, 241, 129); 
                color: rgb(238, 31, 31);
                border: 3px solid rgb(238, 23, 66);
                font-size: 22px;
                font-family: '隶书';
                line-height: 30px;
                border-radius: 5px;
            }
        </style>
        <script src="js/button.js" type="text/javascript"></script>
    </head>

    <body>
        <button class="btn1" onclick="suprise()">点我有惊喜</button>
    </body>
</html>
```

### JS的数据类型和运算符

#### JS的数据类型

> 数值类型

+ 数值类型统一为 number,不区分整数和浮点数

> 字符串类型

+ 字符串类型为 string 和JAVA中的String相似,JS中不严格区分单双引号,都可以用于表示字符串

> 布尔类型

+ 布尔类型为boolean 和Java中的boolean相似,但是在JS的if语句中,**非空字符串会被转换为'真',非零数字也会被认为是'真'**

> 引用数据类型

+ 引用数据类型对象是Object类型, 各种对象和数组在JS中都是Object类型

> function类型

+ JS中的各种函数属于function数据类型

> 命名未赋值

+ js为弱类型语言,统一使用 var 声明对象和变量,在赋值时才确定真正的数据类型,变量如果只声明没有赋值的话,数据类型为undefined

> 赋予NULL值

+ 在JS中,如果给一个变量赋值为null,其数据类型是Object, 可以通过typeof关键字判断数据类型

#### JS的变量

> JS中的变量具有如下特征

+ 1 弱类型变量**,可以统一声明成var**,赋值时确定类型
+ 2 var声明的变量可以再次声明
+ 3 变量可以使用不同的数据类型多次赋值
+ 4 JS的语句可以以; 结尾,也可以不用;结尾
+ 5 变量标识符严格**区分大小写**
+ 6 标识符的命名规则参照JAVA
+ 7 如果使用了 一个没有声明的变量,那么运行时会报uncaught ReferenceError: *** is not defined   at index.html:行号:列号
+ 8 如果一个变量只声明,没赋值,那么值是undefined

#### JS的运算符

>  算数运算符  + - * /  %

+ 其中需要注意的是 / 和 % 
  + **/ 在除0时,结果是Infinity ,而不是报错**
  + **%在模0时,结果是NaN,意思为 not a number ,而不是报错**

> 复合算数运算符 ++ --  += -= *= /= %=

+ 符合算数运算符基本和JAVA一致,同样需要注意 /=和%=
  + **在/=0时,结果是Infinity ,而不是报错**
  + **在%=0时,结果是NaN,意思为 not a number ,而不是报错**

> 关系运算符  >   <  >= <= == === !=

+ 需要注意的是 == 和 === 差别
  + == 符号,**如果两端的数据类型不一致,会尝试将两端的数据转换成number,再对比number大小**
    + '123'  这种字符串可以转换成数字
    + true会被转换成1 false会被转换成0
  + ===  符号,**如果两端数据类型不一致,直接返回false,数据类型一致在比较是否相同**

> 逻辑运算符  || &&    

+ 几乎和JAVA中的一样,需要注意的是,这里直接就是短路的逻辑运算符,单个的 |   和 &  以及 ^ 是位运算符

> 条件运算符  条件? 值1  : 值2

+ 几乎和JAVA中的一样

> 位运算符  |  &  ^  <<  >>  >>>

+ 和 java中的类似(了解)

### JS的流程控制和函数

#### JS分支结构

> if结构

+ 这里的if结构几乎和JAVA中的一样,需要注意的是
  + if()中的**非空字符串会被认为是true**
  + **非空对象 会判断为true**
  + if()中的**非零数字会被认为是true**

+ 代码

``` javascript
if('false'){// 非空字符串 if判断为true
    console.log(true)
}else{
    console.log(false)
}
if(''){// 长度为0字符串 if判断为false
    //if(null)  判断为false
    console.log(true)
}else{
    console.log(false)
}
if(1){// 非零数字 if判断为true
    console.log(true)
}else{
    console.log(false)
}
if(0){
    console.log(true)
}else{
    console.log(false)
}
```

+ 结果

![1681285904625](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681285904625.png)

> switch结构

+ 几乎和JAVA的语法一致

+ 代码

``` javascript
var monthStr=prompt("请输入月份","例如:10 ");

//prompt返回的结果就是用户在窗口上输入的值，以string类型返回的

var month= Number.parseInt(monthStr)
switch(month){
    case 3:
    case 4:
    case 5:
        console.log("春季");
        break;
    case 6:
    case 7:
    case 8:
        console.log("夏季");
        break;
    case 9:
    case 10:
    case 11:
        console.log("秋季");
        break;
    case 1:
    case 2:
    case 12:
        console.log("冬季");
        break;
    default :
        console.log("月份有误")
}
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\switchex.gif" alt="switchex" style="zoom:67%;" />

#### JS循环结构

> while结构

+ 几乎和JAVA一致

+ 代码

```  javascript
/* 打印99 乘法表 */
var i = 1;
while(i <= 9){
    var j = 1;
    while(j <= i){ 
        document.write(j+"*"+i+"="+i*j+"&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;");
        j++;
    }
    document.write("<hr/>");
    i++;
}
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681287264843.png" alt="1681287264843" style="zoom: 80%;" />

> for循环

+ 几乎和JAVA一致

+ 代码

``` javascript
/* 打印99 乘法表 */
for(  var i = 1;i <= 9; i++){
    for(var j = 1;j <= i;j++){
        document.write(j+"*"+i+"="+i*j+"&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;");
    }
    document.write("<hr/>");
}
```

+ 效果

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681287264843.png" alt="1681287264843" style="zoom:50%;" />

> foreach循环

+ 迭代数组时,和java不一样

  <img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240225092319849.png" alt="image-20240225092319849" style="zoom:50%;" />

  + 括号中的临时变量**表示的是元素的索引,不是元素的值,**
  + **()中也不在使用: 分隔,而是使用 in 关键字**

+ 代码

``` javascript
var cities =["北京","上海","深圳","武汉","西安","成都"]
document.write("<ul>")
for(var index in  cities){
    document.write("<li>"+cities[index]+"</li>")
}
document.write("</ul>")
```

+ 效果

![1681287540562](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681287540562.png)

### JS函数声明

> JS中的方法,多称为函数,函数的声明语法和JAVA中有较大区别

+ 函数声明
  + 函数没有权限控制符
  + 不用声明函数的返回值类型,需要返回在函数体中直接return即可,也无需void关键字
  + 参数列表中,无需数据类型
  + 调用函数时,实参和形参的个数可以不一致
  + 声明函数时需要用function关键字
  + Js函数没有异常列表
+ 代码

``` javascript
/* 
语法1 
    function 函数名 (参数列表){函数体}
            */
function sum(a, b){
    return a+b;
}
var result =sum(10,20);
console.log(result)

/* 
语法2
    var 函数名 = function (参数列表){函数体}
            */
var add = function(a, b){
    return a+b;
}
var result = add(1,2);
console.log(result);
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240225093941342.png" alt="image-20240225093941342" style="zoom:50%;" />

+ 调用测试

![1681287984473](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681287984473.png)

### JS的对象和JSON

#### JS声明对象的语法

> 语法1 通过new Object()直接创建对象

+ 代码

```javascript
var person =new Object();
// 给对象添加属性并赋值
//*如果有该属性直接赋值
person.name="张小明";
person.age=10;
person.foods=["苹果","橘子","香蕉","葡萄"];
// 给对象添加功能函数
person.eat= function (){
    console.log(this.age+"岁的"+this.name+"喜欢吃:")
    for(var i = 0;i<this.foods.length;i++){
        console.log(this.foods[i])
    } 
}
//获得对象属性值
console.log(person.name)
console.log(person.age)
//调用对象方法
person.eat();
```

+ 效果

![1681288692792](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681288692792.png)



> 语法2 通过 {}形式创建对象

+ 语法为  var person ={"属性名":"属性值","属性名","属性值","函数名":函数}
+ 代码

``` javascript
var person ={
    "name":"张小明",
    "age":10,
    "foods":["苹果","香蕉","橘子","葡萄"],
    "eat":function (){
        console.log(this.age+"岁的"+this.name+"喜欢吃:")
        for(var i = 0;i<this.foods.length;i++){
            console.log(this.foods[i])
        } 
    }
}
//获得对象属性值
console.log(person.name)
console.log(person.age)
//调用对象方法
person.eat();
```

+ 效果

![1681288692792](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681288692792.png)

#### JSON格式

>  JSON（JavaScript Object Notation, JS对象简谱）是一种轻量级的数据交换格式。它基于ECMAScript（European Computer Manufacturers Association, 欧洲计算机协会的一个子集，采用完全独立于编程语言的文本格式来存储和表示数据。简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率 <font color="red">简单来说,JSON 就是一种字符串格式,这种格式无论是在前端还是在后端,都可以很容易的转换成对象,所以常用于前后端数据传递</font>

+ 说明

  + JSON的语法

    ​		var obj="{'属性名':'属性值','属性名':{'属性名':'属性值'},'属性名':['值1','值1','值3']}"

  + 属性名必须用""包裹上

  + 属性值 字符串必须用“”包好，数字可以不处理

  + JSON字符串一般用于传递数据,所以字符串中的函数就显得没有意义,在此不做研究

  + 通过JSON.parse()方法可以将一个JSON串转换成对象

  + 通过JSON.stringify()方法可以将一个对象转换成一个JSON格式的字符串

+ 代码

``` javascript
/* 定义一个JSON串 */
var personStr ='{"name":"张小明","age":20,"girlFriend":{"name":"铁铃","age":23},"foods":["苹果","香蕉","橘子","葡萄"],"pets":[{"petName":"大黄","petType":"dog"},{"petName":"小花","petType":"cat"}]}'
console.log(personStr)
console.log(typeof personStr)
/* 将一个JSON串转换为对象 */
var person =JSON.parse(personStr);
console.log(person)
console.log(typeof person)
/* 获取对象属性值 */
console.log(person.name)
console.log(person.age)
console.log(person.girlFriend.name)
console.log(person.foods[1])
console.log(person.pets[1].petName)
console.log(person.pets[1].petType)
```

``` javascript
/* 定义一个对象 */
var person={
    'name':'张小明',
    'age':20,
    'girlFriend':{
        'name':'铁铃',
        'age':23
    },
    'foods':['苹果','香蕉','橘子','葡萄'],
    'pets':[
        {
            'petName':'大黄',
            'petType':'dog'
        },
        {
            'petName':'小花',
            'petType':'cat'
        }
    ]
}

/* 获取对象属性值 */
console.log(person.name)
console.log(person.age)
console.log(person.girlFriend.name)
console.log(person.foods[1])
console.log(person.pets[1].petName)
console.log(person.pets[1].petType)
/* 将对象转换成JSON字符串 */
var personStr =JSON.stringify(person)
console.log(personStr)
console.log(typeof personStr)
```

+ 前后端传递数据

![1681292306466](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681292306466.png)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240226192641391.png" alt="image-20240226192641391" style="zoom:50%;" />

##### JSON和Map_List_Array之间的相互转换问题

#### JS常见对象

##### 数组

> 创建数组的四种方式

+ new Array()                                                   创建空数组
+ new Array(5)                                                 创建数组时给定长度
+ new Array(ele1,ele2,ele3,... ... ,elen);          创建数组时指定元素值
+ [ele1,ele2,ele3,... ... ,elen];                           相当于第三种语法的简写

> 数组的常见API

+ 在JS中,数组属于Object类型,其长度是可以变化的,更像JAVA中的集合

| 方法                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [concat()](https://www.runoob.com/jsref/jsref-concat-array.html) | 连接两个或更多的数组，并返回结果。                           |
| [copyWithin()](https://www.runoob.com/jsref/jsref-copywithin.html) | 从数组的指定位置拷贝元素到数组的另一个指定位置中。           |
| [entries()](https://www.runoob.com/jsref/jsref-entries.html) | 返回数组的可迭代对象。                                       |
| [every()](https://www.runoob.com/jsref/jsref-every.html)     | 检测数值元素的每个元素是否都符合条件。                       |
| [fill()](https://www.runoob.com/jsref/jsref-fill.html)       | 使用一个固定值来填充数组。                                   |
| [filter()](https://www.runoob.com/jsref/jsref-filter.html)   | 检测数值元素，并返回符合条件所有元素的数组。                 |
| [find()](https://www.runoob.com/jsref/jsref-find.html)       | 返回符合传入测试（函数）条件的数组元素。                     |
| [findIndex()](https://www.runoob.com/jsref/jsref-findindex.html) | 返回符合传入测试（函数）条件的数组元素索引。                 |
| [forEach()](https://www.runoob.com/jsref/jsref-foreach.html) | 数组每个元素都执行一次回调函数。                             |
| [from()](https://www.runoob.com/jsref/jsref-from.html)       | 通过给定的对象中创建一个数组。                               |
| [includes()](https://www.runoob.com/jsref/jsref-includes.html) | 判断一个数组是否包含一个指定的值。                           |
| [indexOf()](https://www.runoob.com/jsref/jsref-indexof-array.html) | 搜索数组中的元素，并返回它所在的位置。                       |
| [isArray()](https://www.runoob.com/jsref/jsref-isarray.html) | 判断对象是否为数组。                                         |
| [join()](https://www.runoob.com/jsref/jsref-join.html)       | 把数组的所有元素放入一个字符串。                             |
| [keys()](https://www.runoob.com/jsref/jsref-keys.html)       | 返回数组的可迭代对象，包含原始数组的键(key)。                |
| [lastIndexOf()](https://www.runoob.com/jsref/jsref-lastindexof-array.html) | 搜索数组中的元素，并返回它最后出现的位置。                   |
| [map()](https://www.runoob.com/jsref/jsref-map.html)         | 通过指定函数处理数组的每个元素，并返回处理后的数组。         |
| [pop()](https://www.runoob.com/jsref/jsref-pop.html)         | 删除数组的最后一个元素并返回删除的元素。                     |
| [push()](https://www.runoob.com/jsref/jsref-push.html)       | 向数组的末尾添加一个或更多元素，并返回新的长度。             |
| [reduce()](https://www.runoob.com/jsref/jsref-reduce.html)   | 将数组元素计算为一个值（从左到右）。                         |
| [reduceRight()](https://www.runoob.com/jsref/jsref-reduceright.html) | 将数组元素计算为一个值（从右到左）。                         |
| [reverse()](https://www.runoob.com/jsref/jsref-reverse.html) | 反转数组的元素顺序。                                         |
| [shift()](https://www.runoob.com/jsref/jsref-shift.html)     | 删除并返回数组的第一个元素。                                 |
| [slice()](https://www.runoob.com/jsref/jsref-slice-array.html) | 选取数组的一部分，并返回一个新数组。                         |
| [some()](https://www.runoob.com/jsref/jsref-some.html)       | 检测数组元素中是否有元素符合指定条件。                       |
| [sort()](https://www.runoob.com/jsref/jsref-sort.html)       | 对数组的元素进行排序。                                       |
| [splice()](https://www.runoob.com/jsref/jsref-splice.html)   | 从数组中添加或删除元素。                                     |
| [toString()](https://www.runoob.com/jsref/jsref-tostring-array.html) | 把数组转换为字符串，并返回结果。                             |
| [unshift()](https://www.runoob.com/jsref/jsref-unshift.html) | 向数组的开头添加一个或更多元素，并返回新的长度。             |
| [valueOf()](https://www.runoob.com/jsref/jsref-valueof-array.html) | 返回数组对象的原始值。                                       |
| [Array.of()](https://www.runoob.com/jsref/jsref-of-array.html) | 将一组值转换为数组。                                         |
| [Array.at()](https://www.runoob.com/jsref/jsref-at-array.html) | 用于接收一个整数值并返回该索引对应的元素，允许正数和负数。负整数从数组中的最后一个元素开始倒数。 |
| [Array.flat()](https://www.runoob.com/jsref/jsref-flat-array.html) | 创建一个新数组，这个新数组由原数组中的每个元素都调用一次提供的函数后的返回值组成。 |
| [Array.flatMap()](https://www.runoob.com/jsref/jsref-flatmap-array.html) | 使用映射函数映射每个元素，然后将结果压缩成一个新数组。       |

##### Boolean对象

> boolean对象的方法比较简单

| 方法                                                         | 描述                               |
| :----------------------------------------------------------- | :--------------------------------- |
| [toString()](https://www.runoob.com/jsref/jsref-tostring-boolean.html) | 把布尔值转换为字符串，并返回结果。 |
| [valueOf()](https://www.runoob.com/jsref/jsref-valueof-boolean.html) | 返回 Boolean 对象的原始值。        |

##### Date对象

> 和JAVA中的Date类比较类似

| 方法                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [getDate()](https://www.runoob.com/jsref/jsref-getdate.html) | 从 Date 对象返回一个月中的某一天 (1 ~ 31)。                  |
| [getDay()](https://www.runoob.com/jsref/jsref-getday.html)   | 从 Date 对象返回一周中的某一天 (0 ~ 6)。                     |
| [getFullYear()](https://www.runoob.com/jsref/jsref-getfullyear.html) | 从 Date 对象以四位数字返回年份。                             |
| [getHours()](https://www.runoob.com/jsref/jsref-gethours.html) | 返回 Date 对象的小时 (0 ~ 23)。                              |
| [getMilliseconds()](https://www.runoob.com/jsref/jsref-getmilliseconds.html) | 返回 Date 对象的毫秒(0 ~ 999)。                              |
| [getMinutes()](https://www.runoob.com/jsref/jsref-getminutes.html) | 返回 Date 对象的分钟 (0 ~ 59)。                              |
| [getMonth()](https://www.runoob.com/jsref/jsref-getmonth.html) | 从 Date 对象返回月份 (0 ~ 11)。//+1使用                      |
| [getSeconds()](https://www.runoob.com/jsref/jsref-getseconds.html) | 返回 Date 对象的秒数 (0 ~ 59)。                              |
| [getTime()](https://www.runoob.com/jsref/jsref-gettime.html) | 返回 1970 年 1 月 1 日至今的毫秒数。                         |
| [getTimezoneOffset()](https://www.runoob.com/jsref/jsref-gettimezoneoffset.html) | 返回本地时间与格林威治标准时间 (GMT) 的分钟差。              |
| [getUTCDate()](https://www.runoob.com/jsref/jsref-getutcdate.html) | 根据世界时从 Date 对象返回月中的一天 (1 ~ 31)。              |
| [getUTCDay()](https://www.runoob.com/jsref/jsref-getutcday.html) | 根据世界时从 Date 对象返回周中的一天 (0 ~ 6)。               |
| [getUTCFullYear()](https://www.runoob.com/jsref/jsref-getutcfullyear.html) | 根据世界时从 Date 对象返回四位数的年份。                     |
| [getUTCHours()](https://www.runoob.com/jsref/jsref-getutchours.html) | 根据世界时返回 Date 对象的小时 (0 ~ 23)。                    |
| [getUTCMilliseconds()](https://www.runoob.com/jsref/jsref-getutcmilliseconds.html) | 根据世界时返回 Date 对象的毫秒(0 ~ 999)。                    |
| [getUTCMinutes()](https://www.runoob.com/jsref/jsref-getutcminutes.html) | 根据世界时返回 Date 对象的分钟 (0 ~ 59)。                    |
| [getUTCMonth()](https://www.runoob.com/jsref/jsref-getutcmonth.html) | 根据世界时从 Date 对象返回月份 (0 ~ 11)。                    |
| [getUTCSeconds()](https://www.runoob.com/jsref/jsref-getutcseconds.html) | 根据世界时返回 Date 对象的秒钟 (0 ~ 59)。                    |
| getYear()                                                    | 已废弃。 请使用 getFullYear() 方法代替。                     |
| [parse()](https://www.runoob.com/jsref/jsref-parse.html)     | 返回1970年1月1日午夜到指定日期（字符串）的毫秒数。           |
| [setDate()](https://www.runoob.com/jsref/jsref-setdate.html) | 设置 Date 对象中月的某一天 (1 ~ 31)。                        |
| [setFullYear()](https://www.runoob.com/jsref/jsref-setfullyear.html) | 设置 Date 对象中的年份（四位数字）。                         |
| [setHours()](https://www.runoob.com/jsref/jsref-sethours.html) | 设置 Date 对象中的小时 (0 ~ 23)。                            |
| [setMilliseconds()](https://www.runoob.com/jsref/jsref-setmilliseconds.html) | 设置 Date 对象中的毫秒 (0 ~ 999)。                           |
| [setMinutes()](https://www.runoob.com/jsref/jsref-setminutes.html) | 设置 Date 对象中的分钟 (0 ~ 59)。                            |
| [setMonth()](https://www.runoob.com/jsref/jsref-setmonth.html) | 设置 Date 对象中月份 (0 ~ 11)。                              |
| [setSeconds()](https://www.runoob.com/jsref/jsref-setseconds.html) | 设置 Date 对象中的秒钟 (0 ~ 59)。                            |
| [setTime()](https://www.runoob.com/jsref/jsref-settime.html) | setTime() 方法以毫秒设置 Date 对象。                         |
| [setUTCDate()](https://www.runoob.com/jsref/jsref-setutcdate.html) | 根据世界时设置 Date 对象中月份的一天 (1 ~ 31)。              |
| [setUTCFullYear()](https://www.runoob.com/jsref/jsref-setutcfullyear.html) | 根据世界时设置 Date 对象中的年份（四位数字）。               |
| [setUTCHours()](https://www.runoob.com/jsref/jsref-setutchours.html) | 根据世界时设置 Date 对象中的小时 (0 ~ 23)。                  |
| [setUTCMilliseconds()](https://www.runoob.com/jsref/jsref-setutcmilliseconds.html) | 根据世界时设置 Date 对象中的毫秒 (0 ~ 999)。                 |
| [setUTCMinutes()](https://www.runoob.com/jsref/jsref-setutcminutes.html) | 根据世界时设置 Date 对象中的分钟 (0 ~ 59)。                  |
| [setUTCMonth()](https://www.runoob.com/jsref/jsref-setutcmonth.html) | 根据世界时设置 Date 对象中的月份 (0 ~ 11)。                  |
| [setUTCSeconds()](https://www.runoob.com/jsref/jsref-setutcseconds.html) | setUTCSeconds() 方法用于根据世界时 (UTC) 设置指定时间的秒字段。 |
| setYear()                                                    | 已废弃。请使用 setFullYear() 方法代替。                      |
| [toDateString()](https://www.runoob.com/jsref/jsref-todatestring.html) | 把 Date 对象的日期部分转换为字符串。                         |
| toGMTString()                                                | 已废弃。请使用 toUTCString() 方法代替。                      |
| [toISOString()](https://www.runoob.com/jsref/jsref-toisostring.html) | 使用 ISO 标准返回字符串的日期格式。                          |
| [toJSON()](https://www.runoob.com/jsref/jsref-tojson.html)   | 以 JSON 数据格式返回日期字符串。                             |
| [toLocaleDateString()](https://www.runoob.com/jsref/jsref-tolocaledatestring.html) | 根据本地时间格式，把 Date 对象的日期部分转换为字符串。       |
| [toLocaleTimeString()](https://www.runoob.com/jsref/jsref-tolocaletimestring.html) | 根据本地时间格式，把 Date 对象的时间部分转换为字符串。       |
| [toLocaleString()](https://www.runoob.com/jsref/jsref-tolocalestring.html) | 根据本地时间格式，把 Date 对象转换为字符串。                 |
| [toString()](https://www.runoob.com/jsref/jsref-tostring-date.html) | 把 Date 对象转换为字符串。                                   |
| [toTimeString()](https://www.runoob.com/jsref/jsref-totimestring.html) | 把 Date 对象的时间部分转换为字符串。                         |
| [toUTCString()](https://www.runoob.com/jsref/jsref-toutcstring.html) | 根据世界时，把 Date 对象转换为字符串。实例：`var today = new Date(); var UTCstring = today.toUTCString();` |
| [UTC()](https://www.runoob.com/jsref/jsref-utc.html)         | 根据世界时返回 1970 年 1 月 1 日 到指定日期的毫秒数。        |
| [valueOf()](https://www.runoob.com/jsref/jsref-valueof-date.html) | 返回 Date 对象的原始值。                                     |

##### Math

>  和JAVA中的Math类比较类似

| 方法                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [abs(x)](https://www.runoob.com/jsref/jsref-abs.html)        | 返回 x 的绝对值。                                            |
| [acos(x)](https://www.runoob.com/jsref/jsref-acos.html)      | 返回 x 的反余弦值。                                          |
| [asin(x)](https://www.runoob.com/jsref/jsref-asin.html)      | 返回 x 的反正弦值。                                          |
| [atan(x)](https://www.runoob.com/jsref/jsref-atan.html)      | 以介于 -PI/2 与 PI/2 弧度之间的数值来返回 x 的反正切值。     |
| [atan2(y,x)](https://www.runoob.com/jsref/jsref-atan2.html)  | 返回从 x 轴到点 (x,y) 的角度（介于 -PI/2 与 PI/2 弧度之间）。 |
| [ceil(x)](https://www.runoob.com/jsref/jsref-ceil.html)      | 对数进行上舍入。                                             |
| [cos(x)](https://www.runoob.com/jsref/jsref-cos.html)        | 返回数的余弦。                                               |
| [exp(x)](https://www.runoob.com/jsref/jsref-exp.html)        | 返回 Ex 的指数。                                             |
| [floor(x)](https://www.runoob.com/jsref/jsref-floor.html)    | 对 x 进行下舍入。                                            |
| [log(x)](https://www.runoob.com/jsref/jsref-log.html)        | 返回数的自然对数（底为e）。                                  |
| [max(x,y,z,...,n)](https://www.runoob.com/jsref/jsref-max.html) | 返回 x,y,z,...,n 中的最高值。                                |
| [min(x,y,z,...,n)](https://www.runoob.com/jsref/jsref-min.html) | 返回 x,y,z,...,n中的最低值。                                 |
| [pow(x,y)](https://www.runoob.com/jsref/jsref-pow.html)      | 返回 x 的 y 次幂。                                           |
| [random()](https://www.runoob.com/jsref/jsref-random.html)   | 返回 0 ~ 1 之间的随机数。                                    |
| [round(x)](https://www.runoob.com/jsref/jsref-round.html)    | 四舍五入。                                                   |
| [sin(x)](https://www.runoob.com/jsref/jsref-sin.html)        | 返回数的正弦。                                               |
| [sqrt(x)](https://www.runoob.com/jsref/jsref-sqrt.html)      | 返回数的平方根。                                             |
| [tan(x)](https://www.runoob.com/jsref/jsref-tan.html)        | 返回角的正切。                                               |
| [tanh(x)](https://www.runoob.com/jsref/jsref-tanh.html)      | 返回一个数的双曲正切函数值。                                 |
| [trunc(x)](https://www.runoob.com/jsref/jsref-trunc.html)    | 将数字的小数部分去掉，只保留整数部分。                       |

##### Number

> Number中准备了一些基础的数据处理函数

| 方法                                                         | 描述                                                 |
| :----------------------------------------------------------- | :--------------------------------------------------- |
| [isFinite](https://www.runoob.com/jsref/jsref-isfinite-number.html) | 检测指定参数是否为无穷大。                           |
| [isInteger](https://www.runoob.com/jsref/jsref-isinteger-number.html) | 检测指定参数是否为整数。                             |
| [isNaN](https://www.runoob.com/jsref/jsref-isnan-number.html) | 检测指定参数是否为 NaN。                             |
| [isSafeInteger](https://www.runoob.com/jsref/jsref-issafeInteger-number.html) | 检测指定参数是否为安全整数。                         |
| [toExponential(x)](https://www.runoob.com/jsref/jsref-toexponential.html) | 把对象的值转换为指数计数法。                         |
| [toFixed(x)](https://www.runoob.com/jsref/jsref-tofixed.html) | 把数字转换为字符串，结果的小数点后有指定位数的数字。 |
| [toLocaleString(locales, options)](https://www.runoob.com/jsref/jsref-tolocalestring-number.html) | 返回数字在特定语言环境下的表示字符串。               |
| [toPrecision(x)](https://www.runoob.com/jsref/jsref-toprecision.html) | 把数字格式化为指定的长度。                           |
| [toString()](https://www.runoob.com/jsref/jsref-tostring-number.html) | 把数字转换为字符串，使用指定的基数。                 |
| [valueOf()](https://www.runoob.com/jsref/jsref-valueof-number.html) | 返回一个 Number 对象的基本数字值。                   |

##### String

> 和JAVA中的String类似

| 方法                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [charAt()](https://www.runoob.com/jsref/jsref-charat.html)   | 返回在指定位置的字符。                                       |
| [charCodeAt()](https://www.runoob.com/jsref/jsref-charcodeat.html) | 返回在指定的位置的字符的 Unicode 编码。                      |
| [concat()](https://www.runoob.com/jsref/jsref-concat-string.html) | 连接两个或更多字符串，并返回新的字符串。                     |
| [endsWith()](https://www.runoob.com/jsref/jsref-endswith.html) | 判断当前字符串是否是以指定的子字符串结尾的（区分大小写）。   |
| [fromCharCode()](https://www.runoob.com/jsref/jsref-fromcharcode.html) | 将 Unicode 编码转为字符。                                    |
| [indexOf()](https://www.runoob.com/jsref/jsref-indexof.html) | 返回某个指定的字符串值在字符串中首次出现的位置。             |
| [includes()](https://www.runoob.com/jsref/jsref-string-includes.html) | 查找字符串中是否包含指定的子字符串。                         |
| [lastIndexOf()](https://www.runoob.com/jsref/jsref-lastindexof.html) | 从后向前搜索字符串，并从起始位置（0）开始计算返回字符串最后出现的位置。 |
| [match()](https://www.runoob.com/jsref/jsref-match.html)     | 查找找到一个或多个正则表达式的匹配。                         |
| [repeat()](https://www.runoob.com/jsref/jsref-repeat.html)   | 复制字符串指定次数，并将它们连接在一起返回。                 |
| [replace()](https://www.runoob.com/jsref/jsref-replace.html) | 在字符串中查找匹配的子串，并替换与正则表达式匹配的子串。     |
| [replaceAll()](https://www.runoob.com/jsref/jsref-replaceall.html) | 在字符串中查找匹配的子串，并替换与正则表达式匹配的所有子串。 |
| [search()](https://www.runoob.com/jsref/jsref-search.html)   | 查找与正则表达式相匹配的值。                                 |
| [slice()](https://www.runoob.com/jsref/jsref-slice-string.html) | 提取字符串的片断，并在新的字符串中返回被提取的部分。         |
| [split()](https://www.runoob.com/jsref/jsref-split.html)     | 把字符串分割为字符串数组。                                   |
| [startsWith()](https://www.runoob.com/jsref/jsref-startswith.html) | 查看字符串是否以指定的子字符串开头。                         |
| [substr()](https://www.runoob.com/jsref/jsref-substr.html)   | 从起始索引号提取字符串中指定数目的字符。                     |
| [substring()](https://www.runoob.com/jsref/jsref-substring.html) | 提取字符串中两个指定的索引号之间的字符。                     |
| [toLowerCase()](https://www.runoob.com/jsref/jsref-tolowercase.html) | 把字符串转换为小写。                                         |
| [toUpperCase()](https://www.runoob.com/jsref/jsref-touppercase.html) | 把字符串转换为大写。                                         |
| [trim()](https://www.runoob.com/jsref/jsref-trim.html)       | 去除字符串两边的空白。                                       |
| [toLocaleLowerCase()](https://www.runoob.com/jsref/jsref-tolocalelowercase.html) | 根据本地主机的语言环境把字符串转换为小写。                   |
| [toLocaleUpperCase()](https://www.runoob.com/jsref/jsref-tolocaleuppercase.html) | 根据本地主机的语言环境把字符串转换为大写。                   |
| [valueOf()](https://www.runoob.com/jsref/jsref-valueof-string.html) | 返回某个字符串对象的原始值。                                 |
| [toString()](https://www.runoob.com/jsref/jsref-tostring.html) | 返回一个字符串。                                             |

### 事件的绑定

>  HTML 事件可以是浏览器行为，也可以是用户行为。 当这些一些行为发生时,可以自动触发对应的JS函数的运行,我们称之为事件发生.JS的事件驱动指的就是行为触发代码运行的这种特点

#### 常见事件

> 鼠标事件

| 属性                                                         | 描述                                   |
| :----------------------------------------------------------- | :------------------------------------- |
| [onclick](https://www.runoob.com/jsref/event-onclick.html)   | 当用户点击某个对象时调用的事件句柄。   |
| [oncontextmenu](https://www.runoob.com/jsref/event-oncontextmenu.html) | 在用户点击鼠标右键打开上下文菜单时触发 |
| [ondblclick](https://www.runoob.com/jsref/event-ondblclick.html) | 当用户双击某个对象时调用的事件句柄。   |
| [onmousedown](https://www.runoob.com/jsref/event-onmousedown.html) | 鼠标按钮被按下。                       |
| [onmouseenter](https://www.runoob.com/jsref/event-onmouseenter.html) | 当鼠标指针移动到元素上时触发。         |
| [onmouseleave](https://www.runoob.com/jsref/event-onmouseleave.html) | 当鼠标指针移出元素时触发               |
| [onmousemove](https://www.runoob.com/jsref/event-onmousemove.html) | 鼠标被移动。                           |
| [onmouseover](https://www.runoob.com/jsref/event-onmouseover.html) | 鼠标移到某元素之上。                   |
| [onmouseout](https://www.runoob.com/jsref/event-onmouseout.html) | 鼠标从某元素移开。                     |
| [onmouseup](https://www.runoob.com/jsref/event-onmouseup.html) | 鼠标按键被松开。                       |

> 键盘事件

| 属性                                                         | 描述                       |
| :----------------------------------------------------------- | :------------------------- |
| [onkeydown](https://www.runoob.com/jsref/event-onkeydown.html) | 某个键盘按键被按下。       |
| [onkeypress](https://www.runoob.com/jsref/event-onkeypress.html) | 某个键盘按键被按下并松开。 |
| [onkeyup](https://www.runoob.com/jsref/event-onkeyup.html)   | 某个键盘按键被松开。       |

> 表单事件

| 属性                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [onblur](https://www.runoob.com/jsref/event-onblur.html)     | 元素失去焦点时触发                                           |
| [onchange](https://www.runoob.com/jsref/event-onchange.html) | 该事件在表单元素的内容改变时触发( <input>, <keygen>, <select>, 和 <textarea>) |
| [onfocus](https://www.runoob.com/jsref/event-onfocus.html)   | 元素获取焦点时触发                                           |
| [onfocusin](https://www.runoob.com/jsref/event-onfocusin.html) | 元素即将获取焦点时触发                                       |
| [onfocusout](https://www.runoob.com/jsref/event-onfocusout.html) | 元素即将失去焦点时触发                                       |
| [oninput](https://www.runoob.com/jsref/event-oninput.html)   | 元素获取用户输入时触发                                       |
| [onreset](https://www.runoob.com/jsref/event-onreset.html)   | 表单重置时触发                                               |
| [onsearch](https://www.runoob.com/jsref/event-onsearch.html) | 用户向搜索域输入文本时触发 ( <input="search">)               |
| [onselect](https://www.runoob.com/jsref/event-onselect.html) | 用户选取文本时触发 ( <input> 和 <textarea>)                  |
| [onsubmit](https://www.runoob.com/jsref/event-onsubmit.html) | 表单提交时触发                                               |

> 页面加载时间
>
> onload

#### 事件的绑定

##### 通过属性绑定

+ 代码

``` html
    <head>
        <meta charset="UTF-8">
        <title>小标题</title>
      
        <script>
            function testDown1(){
                console.log("down1")
            }
            function testDown2(){
                console.log("down2")
            }
            function testFocus(){
                console.log("获得焦点")
            }

            function testBlur(){
                console.log("失去焦点")
            }
            function testChange(input){
                console.log("内容改变为：")
                console.log(input.value);
            }
            function testMouseOver(){
                console.log("鼠标悬停")
            }
            function testMouseLeave(){
                console.log("鼠标离开")
            }
            function testMouseMove(){
                console.log("鼠标移动")
            }
        </script>
    </head>

    <body>
        <input type="text" 
        onkeydown="testDown1(),testDown2()"
        onfocus="testFocus()" 
        onblur="testBlur()" 
        onchange="testChange(this)"
        onmouseover="testMouseOver()" 
        onmouseleave="testMouseLeave()" 
        onmousemove="testMouseMove()" 
         />
    </body>
```

+ 说明

  + 通过事件属性绑定函数,在行为发生时会自动执行函数
  + 一个事件可以同时绑定多个函数

  + 一个元素可以同时绑定多个事件
  + 方法中**可以传入 this对象,代表当前元素**

##### 通过DOM编程绑定

``` html
    <head>
        <meta charset="UTF-8">
        <title>小标题</title>
      
        <script>
            // 页面加载完毕事件,浏览器加载完整个文档行为
            window.onload=function(){
                var in1 =document.getElementById("in1");
                // 通过DOM编程绑定事件
                in1.onchange=testChange
            }
            function testChange(){
                console.log("内容改变")
                console.log(event.target.value);
            }
        </script>
    </head>

    <body>
        <input id="in1" type="text" />
    </body>
```

#### 事件的触发

> 行为触发

+ 发生行为时触发

> DOM编程触发

+ 通过DOM编程,用代码触发,执行某些代码相当于发生了某些行为

+ 代码

``` html
    <head>
        <meta charset="UTF-8">
        <title>小标题</title>
      
        <script>
            // 页面加载完毕事件,浏览器加载完整个文档行为
            window.onload=function(){
                var in1 =document.getElementById("in1");
                // 通过DOM编程绑定事件
                in1.onchange=testChange

                var btn1 =document.getElementById("btn1");
                btn1.onclick=function (){
                    console.log("按钮点击了")
                    // 调用事件方法触发事件
                    in1.onchange()
                }
            }
            function testChange(){
                console.log("内容改变")
                console.log(event.target.value);
            }
        </script>
    </head>

    <body>
        <input id="in1" type="text" />
        <br>
        <button id="btn1">按钮</button>
    </body>
```

###  BOM编程

#### 什么是BOM

+ BOM是Browser Object Model的简写，即浏览器对象模型。

+ BOM由一系列对象组成，是访问、控制、修改浏览器的属性和方法(通过window对象及属性的一系列方法 控制浏览器行为的一种编程)

+ BOM没有统一的标准(每种客户端都可以自定标准)。

+ BOM编程是将浏览器窗口的各个组成部分抽象成各个对象,通过各个对象的API操作组件行为的一种编程

+ BOM编程的对象结构如下

  + window 顶级对象,代表整个浏览器窗口
    + location对象                 window对象的属性之一,代表浏览器的地址栏
    + history对象                   window对象的属性之一,代表浏览器的访问历史
    + screen对象                    window对象的属性之一,代表屏幕
    + navigator对象               window对象的属性之一,代表浏览器软件本身
    + document对象              window对象的属性之一,代表浏览器窗口目前解析的html文档
    + console对象                  window对象的属性之一,代表浏览器开发者工具的控制台
    + localStorage对象          window对象的属性之一,代表浏览器的本地数据持久化存储
    + sessionStorage对象      window对象的属性之一,代表浏览器的本地数据会话级存储

  <img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681267483366.png" alt="1681267483366" style="zoom:67%;" />

####  window对象的常见属性(了解)

| 属性                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [closed](https://www.runoob.com/jsref/prop-win-closed.html)  | 返回窗口是否已被关闭。                                       |
| [defaultStatus](https://www.runoob.com/jsref/prop-win-defaultstatus.html) | 设置或返回窗口状态栏中的默认文本。                           |
| [document](https://www.runoob.com/jsref/dom-obj-document.html) | 对 Document 对象的只读引用。(请参阅[对象](https://www.runoob.com/jsref/dom-obj-document.html)) |
| [frames](https://www.runoob.com/jsref/prop-win-frames.html)  | 返回窗口中所有命名的框架。该集合是 Window 对象的数组，每个 Window 对象在窗口中含有一个框架。 |
| [history](https://www.runoob.com/jsref/obj-history.html)     | 对 History 对象的只读引用。请参数 [History 对象](https://www.runoob.com/jsref/obj-history.html)。 |
| [innerHeight](https://www.runoob.com/jsref/prop-win-innerheight.html) | 返回窗口的文档显示区的高度。                                 |
| [innerWidth](https://www.runoob.com/jsref/prop-win-innerheight.html) | 返回窗口的文档显示区的宽度。                                 |
| [localStorage](https://www.runoob.com/jsref/prop-win-localstorage.html) | 在浏览器中存储 key/value 对。没有过期时间。                  |
| [length](https://www.runoob.com/jsref/prop-win-length.html)  | 设置或返回窗口中的框架数量。                                 |
| [location](https://www.runoob.com/jsref/obj-location.html)   | 用于窗口或框架的 Location 对象。请参阅 [Location 对象](https://www.runoob.com/jsref/obj-location.html)。 |
| [name](https://www.runoob.com/jsref/prop-win-name.html)      | 设置或返回窗口的名称。                                       |
| [navigator](https://www.runoob.com/jsref/obj-navigator.html) | 对 Navigator 对象的只读引用。请参数 [Navigator 对象](https://www.runoob.com/jsref/obj-navigator.html)。 |
| [opener](https://www.runoob.com/jsref/prop-win-opener.html)  | 返回对创建此窗口的窗口的引用。                               |
| [outerHeight](https://www.runoob.com/jsref/prop-win-outerheight.html) | 返回窗口的外部高度，包含工具条与滚动条。                     |
| [outerWidth](https://www.runoob.com/jsref/prop-win-outerheight.html) | 返回窗口的外部宽度，包含工具条与滚动条。                     |
| [pageXOffset](https://www.runoob.com/jsref/prop-win-pagexoffset.html) | 设置或返回当前页面相对于窗口显示区左上角的 X 位置。          |
| [pageYOffset](https://www.runoob.com/jsref/prop-win-pagexoffset.html) | 设置或返回当前页面相对于窗口显示区左上角的 Y 位置。          |
| [parent](https://www.runoob.com/jsref/prop-win-parent.html)  | 返回父窗口。                                                 |
| [screen](https://www.runoob.com/jsref/obj-screen.html)       | 对 Screen 对象的只读引用。请参数 [Screen 对象](https://www.runoob.com/jsref/obj-screen.html)。 |
| [screenLeft](https://www.runoob.com/jsref/prop-win-screenleft.html) | 返回相对于屏幕窗口的x坐标                                    |
| [screenTop](https://www.runoob.com/jsref/prop-win-screenleft.html) | 返回相对于屏幕窗口的y坐标                                    |
| [screenX](https://www.runoob.com/jsref/prop-win-screenx.html) | 返回相对于屏幕窗口的x坐标                                    |
| [sessionStorage](https://www.runoob.com/jsref/prop-win-sessionstorage.html) | 在浏览器中存储 key/value 对。 在关闭窗口或标签页之后将会删除这些数据。 |
| [screenY](https://www.runoob.com/jsref/prop-win-screenx.html) | 返回相对于屏幕窗口的y坐标                                    |
| [self](https://www.runoob.com/jsref/prop-win-self.html)      | 返回对当前窗口的引用。等价于 Window 属性。                   |
| [status](https://www.runoob.com/jsref/prop-win-status.html)  | 设置窗口状态栏的文本。                                       |
| [top](https://www.runoob.com/jsref/prop-win-top.html)        | 返回最顶层的父窗口。                                         |

![image-20240226213243065](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240226213243065.png)

#### window对象的常见方法(了解)

三种弹窗

- alert
- confirm
- prompt

| 方法                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [alert()](https://www.runoob.com/jsref/met-win-alert.html)   | 显示带有一段消息和一个确认按钮的警告框。                     |
| [atob()](https://www.runoob.com/jsref/met-win-atob.html)     | 解码一个 base-64 编码的字符串。                              |
| [btoa()](https://www.runoob.com/jsref/met-win-btoa.html)     | 创建一个 base-64 编码的字符串。                              |
| [blur()](https://www.runoob.com/jsref/met-win-blur.html)     | 把键盘焦点从顶层窗口移开。                                   |
| [clearInterval()](https://www.runoob.com/jsref/met-win-clearinterval.html) | 取消由 setInterval() 设置的 timeout。                        |
| [clearTimeout()](https://www.runoob.com/jsref/met-win-cleartimeout.html) | 取消由 setTimeout() 方法设置的 timeout。                     |
| [close()](https://www.runoob.com/jsref/met-win-close.html)   | 关闭浏览器窗口。                                             |
| [confirm()](https://www.runoob.com/jsref/met-win-confirm.html) | 显示带有一段消息以及确认按钮和取消按钮的对话框。             |
| [createPopup()](https://www.runoob.com/jsref/met-win-createpopup.html) | 创建一个 pop-up 窗口。                                       |
| [focus()](https://www.runoob.com/jsref/met-win-focus.html)   | 把键盘焦点给予一个窗口。                                     |
| [getSelection](https://www.runoob.com/jsref/met-win-getselection.html)() | 返回一个 Selection 对象，表示用户选择的文本范围或光标的当前位置。 |
| [getComputedStyle()](https://www.runoob.com/jsref/jsref-getcomputedstyle.html) | 获取指定元素的 CSS 样式。                                    |
| [matchMedia()](https://www.runoob.com/jsref/met-win-matchmedia.html) | 该方法用来检查 media query 语句，它返回一个 MediaQueryList对象。 |
| [moveBy()](https://www.runoob.com/jsref/met-win-moveby.html) | 可相对窗口的当前坐标把它移动指定的像素。                     |
| [moveTo()](https://www.runoob.com/jsref/met-win-moveto.html) | 把窗口的左上角移动到一个指定的坐标。                         |
| [open()](https://www.runoob.com/jsref/met-win-open.html)     | 打开一个新的浏览器窗口或查找一个已命名的窗口。               |
| [print()](https://www.runoob.com/jsref/met-win-print.html)   | 打印当前窗口的内容。                                         |
| [prompt()](https://www.runoob.com/jsref/met-win-prompt.html) | 显示可提示用户输入的对话框。                                 |
| [resizeBy()](https://www.runoob.com/jsref/met-win-resizeby.html) | 按照指定的像素调整窗口的大小。                               |
| [resizeTo()](https://www.runoob.com/jsref/met-win-resizeto.html) | 把窗口的大小调整到指定的宽度和高度。                         |
| scroll()                                                     | 已废弃。 该方法已经使用了 [scrollTo()](https://www.runoob.com/jsref/met-win-scrollto.html) 方法来替代。 |
| [scrollBy()](https://www.runoob.com/jsref/met-win-scrollby.html) | 按照指定的像素值来滚动内容。                                 |
| [scrollTo()](https://www.runoob.com/jsref/met-win-scrollto.html) | 把内容滚动到指定的坐标。                                     |
| [setInterval()](https://www.runoob.com/jsref/met-win-setinterval.html) | 按照指定的周期（以毫秒计）来调用函数或计算表达式。           |
| [setTimeout()](https://www.runoob.com/jsref/met-win-settimeout.html) | 在指定的毫秒数后调用函数或计算表达式。                       |
| [stop()](https://www.runoob.com/jsref/met-win-stop.html)     | 停止页面载入。                                               |
| [postMessage()](https://www.runoob.com/jsref/met-win-postmessage.html) | 安全地实现跨源通信。                                         |

#### 通过BOM编程控制浏览器行为演示

>  三种弹窗方式

``` html
    <head>
        <meta charset="UTF-8">
        <title>小标题</title>
      
        <script>
           function testAlert(){
                //普通信息提示框
                window.alert("提示信息");
           }
           function testConfirm(){
                //确认框
                var con =confirm("确定要删除吗?");
                if(con){
                    alert("点击了确定")
                }else{
                    alert("点击了取消")
                }
           }
           function testPrompt(){
                //信息输入对话框
                var res =prompt("请输入昵称","例如:张三");
                alert("您输入的是:"+res)
           }
        </script>
    </head>

    <body>
        <input type="button" value="提示框" onclick="testAlert()"/> <br>
        <input type="button" value="确认框" onclick="testConfirm()"/> <br>
        <input type="button" value="对话框" onclick="testPrompt()"/> <br>
    </body>
```

>  页面跳转

``` html
    <head>
        <meta charset="UTF-8">
        <title>小标题</title>
      
        <script>
           function goAtguigu(){
                var flag =confirm("即将跳转到尚硅谷官网,本页信息即将丢失,确定吗?")
                if(flag){
                    // 通过BOM编程地址栏url切换
                    window.location.href="http://www.atguigu.com"
                }
           }
          
        </script>
    </head>

    <body>
        <input type="button" value="跳转到尚硅谷" onclick="goAtguigu()"/> <br>
    </body>
```

#### 通过BOM编程实现会话级和持久级数据存储

+ 会话级数据 : 内存型数据,是浏览器在内存上临时存储的数据,浏览器关闭后,数据失去,通过window的sessionStorge属性实现
+ 持久级数据 : 磁盘型数据,是浏览器在磁盘上持久存储的数据,浏览器关闭后,数据仍在,通过window的localStorge实现
+ 可以用于将来存储一些服务端响应回来的数据,比如:token令牌,或者一些其他功能数据,根据数据的业务范围我们可以选择数据存储的会话/持久 级别

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script>
        function saveItem(){
            // 让浏览器存储一些会话级数据
            window.sessionStorage.setItem("sessionMsg","sessionValue")
            // 让浏览器存储一些持久级数据
            window.localStorage.setItem("localMsg","localValue")

            console.log("haha")
        }

        function removeItem(){
            // 删除数据
            sessionStorage.removeItem("sessionMsg")
            localStorage.removeItem("localMsg")
        }

        function readItem(){
            console.log("read")
            // 读取数据
            console.log("session:"+sessionStorage.getItem("sessionMsg"))
            console.log("local:"+localStorage.getItem("localMsg"))
        }
    </script>
</head>
<body>

    <button onclick="saveItem()">存储数据</button>
    <button onclick="removeItem()">删除数据</button>
    <button onclick="readItem()">读取数据</button>

</body>
</html>
```

+ 测试,存储数据后,再读取数据,然后关闭浏览器,获取数据,发现sessionStorge的数据没有了,localStorge的数据还在
+ 通过removeItem可以将这些数据直接删除
+ 在F12开发者工具的应用程序栏,可以查看数据的状态

### DOM编程

> 简单来说:DOM(Document Object Model)编程就是使用document对象的API完成对网页HTML文档进行动态修改,以实现网页数据和样式动态变化效果的编程.

+ document对象代表整个html文档，可用来访问页面中的所有元素，是最复杂的一个dom对象，可以说是学习好dom编程的关键所在。
+ 根据HTML代码结构特点,document对象本身是一种树形结构的文档对象。

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681269953136.png" alt="1681269953136" style="zoom:67%;" />

+ 上面的代码生成的树如下

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681269970254.png" alt="1681269970254"  />

+ DOM编程其实就是用window对象的document属性的相关API完成对页面元素的控制的编程

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681270260741.png" alt="1681270260741"  />

+ dom树中节点的类型
  + node  节点,所有结点的父类型
    + element   元素节点,node的子类型之一,代表一个完整标签
    + attribute  属性节点,node的子类型之一,代表元素的属性
    + text          文本节点,node的子类型之一,代表双标签中间的文本

#### 获取页面元素的几种方式

##### 在整个文档范围内查找元素结点

| 功能               | API                                     | 返回值           |
| ------------------ | --------------------------------------- | ---------------- |
| 根据id值查询       | document.getElementById(“id值”)         | 一个具体的元素节 |
| 根据标签名查询     | document.getElementsByTagName(“标签名”) | 元素节点数组     |
| 根据name属性值查询 | document.getElementsByName(“name值”)    | 元素节点数组     |
| 根据类名查询       | document.getElementsByClassName("类名") | 元素节点数组     |

##### 在具体元素节点范围内查找子节点

| 功能               | API                       | 返回值         |
| ------------------ | ------------------------- | -------------- |
| 查找子标签         | element.children          | 返回子标签数组 |
| 查找第一个子标签   | element.firstElementChild | 标签对象       |
| 查找最后一个子标签 | element.lastElementChild  | 节点对象       |

##### 查找指定子元素节点的父节点

| 功能                     | API                   | 返回值   |
| ------------------------ | --------------------- | -------- |
| 查找指定元素节点的父标签 | element.parentElement | 标签对象 |

##### 查找指定元素节点的兄弟节点

| 功能               | API                         | 返回值   |
| ------------------ | --------------------------- | -------- |
| 查找前一个兄弟标签 | node.previousElementSibling | 标签对象 |
| 查找后一个兄弟标签 | node.nextElementSibling     | 标签对象 |

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
   <script>
    /* 
    1 获得document  dom树
        window.document
    2 从document中获取要操作的元素
        1. 直接获取
            var el1 =document.getElementById("username") // 根据元素的id值获取页面上唯一的一个元素
            var els =document.getElementsByTagName("input") // 根据元素的标签名获取多个同名元素
            var els =document.getElementsByName("aaa") // 根据元素的name属性值获得多个元素
            var els =document.getElementsByClassName("a") // 根据元素的class属性值获得多个元素
        2. 间接获取
            var cs=div01.children // 通过父元素获取全部的子元素
            var firstChild =div01.firstElementChild  // 通过父元素获取第一个子元素
            var lastChild = div01.lastElementChild   // 通过父元素获取最后一个子元素
            var parent = pinput.parentElement  // 通过子元素获取父元素
            var pElement = pinput.previousElementSibling // 获取前面的第一个元素
            var nElement = pinput.nextElementSibling // 获取后面的第一个元素
    3 对元素进行操作
        1. 操作元素的属性
        2. 操作元素的样式
        3. 操作元素的文本
        4. 增删元素   
    */
   function fun1(){
        //1 获得document
        //2 通过document获得元素
        var el1 =document.getElementById("username") // 根据元素的id值获取页面上唯一的一个元素
        console.log(el1)
   }
   function fun2(){
        var els =document.getElementsByTagName("input") // 根据元素的标签名获取多个同名元素
        for(var i = 0 ;i<els.length;i++){
            console.log(els[i])
        }
   }
   function fun3(){
        var els =document.getElementsByName("aaa") // 根据元素的name属性值获得多个元素
        console.log(els)
        for(var i =0;i< els.length;i++){
            console.log(els[i])
        }
   }

   function fun4(){
    var els =document.getElementsByClassName("a") // 根据元素的class属性值获得多个元素
    for(var i =0;i< els.length;i++){
            console.log(els[i])
        }
   }

   function fun5(){
    // 先获取父元素
     var div01 = document.getElementById("div01")
     // 获取所有子元素
     var cs=div01.children // 通过父元素获取全部的子元素
     for(var i =0;i< cs.length;i++){
            console.log(cs[i])
     }

     console.log(div01.firstElementChild)  // 通过父元素获取第一个子元素
     console.log(div01.lastElementChild)   // 通过父元素获取最后一个子元素
   }

   function fun6(){
        // 获取子元素
        var pinput =document.getElementById("password")
        console.log(pinput.parentElement) // 通过子元素获取父元素
   }

   function fun7(){
        // 获取子元素
        var pinput =document.getElementById("password")
        console.log(pinput.previousElementSibling) // 获取前面的第一个元素
        console.log(pinput.nextElementSibling) // 获取后面的第一个元素
   }
   </script>
</head>
<body>
    <div id="div01">
        <input type="text" class="a" id="username" name="aaa"/>
        <input type="text" class="b" id="password" name="aaa"/>
        <input type="text" class="a" id="email"/>
        <input type="text" class="b" id="address"/>
    </div>
    <input type="text" class="a"/><br>

    <hr>
    <input type="button" value="通过父元素获取子元素" onclick="fun5()" id="btn05"/>
    <input type="button" value="通过子元素获取父元素" onclick="fun6()" id="btn06"/>
    <input type="button" value="通过当前元素获取兄弟元素" onclick="fun7()" id="btn07"/>
    <hr>

    <input type="button" value="根据id获取指定元素" onclick="fun1()" id="btn01"/>
    <input type="button" value="根据标签名获取多个元素" onclick="fun2()" id="btn02"/>
    <input type="button" value="根据name属性值获取多个元素" onclick="fun3()" id="btn03"/>
    <input type="button" value="根据class属性值获得多个元素" onclick="fun4()" id="btn04"/>
    
</body>
</html>
```

#### 操作元素属性值

##### 属性操作

| 需求       | 操作方式                   |
| ---------- | -------------------------- |
| 读取属性值 | 元素对象.属性名            |
| 修改属性值 | 元素对象.属性名=新的属性值 |

##### 内部文本操作

| 需求                         | 操作方式          |
| ---------------------------- | ----------------- |
| 获取或者设置标签体的文本内容 | element.innerText |
| 获取或者设置标签体的内容     | element.innerHTML |

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
   <script>
    /* 
    1 获得document  dom树
        window.document
    2 从document中获取要操作的元素
        1. 直接获取
            var el1 =document.getElementById("username") // 根据元素的id值获取页面上唯一的一个元素
            var els =document.getElementsByTagName("input") // 根据元素的标签名获取多个同名元素
            var els =document.getElementsByName("aaa") // 根据元素的name属性值获得多个元素
            var els =document.getElementsByClassName("a") // 根据元素的class属性值获得多个元素
        2. 间接获取
            var cs=div01.children // 通过父元素获取全部的子元素
            var firstChild =div01.firstElementChild  // 通过父元素获取第一个子元素
            var lastChild = div01.lastElementChild   // 通过父元素获取最后一个子元素
            var parent = pinput.parentElement  // 通过子元素获取父元素
            var pElement = pinput.previousElementSibling // 获取前面的第一个元素
            var nElement = pinput.nextElementSibling // 获取后面的第一个元素
    3 对元素进行操作
        1. 操作元素的属性   元素名.属性名=""
        2. 操作元素的样式   元素名.style.样式名=""  样式名"-" 要进行驼峰转换
        3. 操作元素的文本   元素名.innerText   只识别文本
                           元素名.innerHTML   同时可以识别html代码 
        4. 增删元素   
    */
   function changeAttribute(){
        var in1 =document.getElementById("in1")
        // 语法 元素.属性名=""
        // 获得属性值
        console.log(in1.type)
        console.log(in1.value)
        // 修改属性值
        in1.type="button"
        in1.value="嗨"
   }
   function changeStyle(){
        var in1 =document.getElementById("in1")
        // 语法  元素.style.样式名=""   原始样式名中的"-"符号 要转换驼峰式  background-color > backgroundColor
        in1.style.color="green"
        in1.style.borderRadius="5px"
        
   }
   function changeText(){
        var div01 =document.getElementById("div01")
        /* 
        语法  元素名.innerText   只识别文本
              元素名.innerHTML   同时可以识别html代码
        */
        console.log(div01.innerText)
        div01.innerHTML="<h1>嗨</h1>"
   }

   </script>
   <style>
    #in1{
        color: red;
    }
   </style>
</head>
<body>
    <input id="in1" type="text" value="hello">
    <div id="div01">
        hello
    </div>

    <hr>
    <button onclick="changeAttribute()">操作属性</button>
    <button onclick="changeStyle()">操作样式</button>
    <button onclick="changeText()">操作文本</button>
    
</body>
</html>
```

#### 增删元素

##### 对页面的元素进行增删操作

| API                                      | 功能                                       |
| ---------------------------------------- | ------------------------------------------ |
| document.createElement(“标签名”)         | 创建元素节点并返回，但不会自动添加到文档中 |
| document.createTextNode(“文本值”)        | 创建文本节点并返回，但不会自动添加到文档中 |
| element.appendChild(ele)                 | 将ele添加到element所有子节点后面           |
| parentEle.insertBefore(newEle,targetEle) | 将newEle插入到targetEle前面                |
| parentEle.replaceChild(newEle, oldEle)   | 用新节点替换原有的旧子节点                 |
| element.remove()                         | 删除某个标签                               |

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
   <script>
   /*
        4. 增删元素
            var element =document.createElement("元素名") // 创建元素
            父元素.appendChild(子元素)               // 在父元素中追加子元素
            父元素.insertBefore(新元素,参照元素)     // 在某个元素前增加元素
            父元素.replaceChild(新元素,被替换的元素) // 用新的元素替换某个子子元素
            元素.remove()                            // 删除当前元素
    */
   function addCs(){
        // 创建一个新的元素
        // 创建元素
        var csli =document.createElement("li") // <li></li>
        // 设置子元素的属性和文本 <li id="cs">长沙</li>
        csli.id="cs"
        csli.innerText="长沙"
        // 将子元素放入父元素中
        var cityul =document.getElementById("city")
        // 在父元素中追加子元素
        cityul.appendChild(csli)
   }
   function addCsBeforeSz(){
        // 创建一个新的元素
        // 创建元素
        var csli =document.createElement("li") // <li></li>
        // 设置子元素的属性和文本 <li id="cs">长沙</li>
        csli.id="cs"
        csli.innerText="长沙"
        // 将子元素放入父元素中
        var cityul =document.getElementById("city")
        // 在父元素中追加子元素
        //cityul.insertBefore(新元素,参照元素)
        var szli =document.getElementById("sz")
        cityul.insertBefore(csli,szli)
   }

   function replaceSz(){
        // 创建一个新的元素
        // 创建元素
        var csli =document.createElement("li") // <li></li>
        // 设置子元素的属性和文本 <li id="cs">长沙</li>
        csli.id="cs"
        csli.innerText="长沙"
        // 将子元素放入父元素中
        var cityul =document.getElementById("city")
        // 在父元素中追加子元素
        //cityul.replaceChild(新元素,被替换的元素)
        var szli =document.getElementById("sz")
        cityul.replaceChild(csli,szli)
   }

   function removeSz(){
        var szli =document.getElementById("sz")
        // 哪个元素调用了remove该元素就会从dom树中移除
        szli.remove()
   }

   function clearCity(){
        
        var cityul =document.getElementById("city")
        /*var all = cityul.children
        for(var i=0;i<all.length;i++)*/
        //不行，删除的时候，子元素序列号发生改变，容易发生漏删的情况

        /* var fc =cityul.firstChild
        while(fc != null ){
            fc.remove()
            fc =cityul.firstChild
        } */
        cityul.innerHTML=""
        //cityul.remove()
        
   }
   
   </script>
   
</head>
<body>
    <ul id="city">
        <li id="bj">北京</li>
        <li id="sh">上海</li>
        <li id="sz">深圳</li>
        <li id="gz">广州</li>
    </ul>

    <hr>
    <!-- 目标1 在城市列表的最后添加一个子标签  <li id="cs">长沙</li>  -->
    <button onclick="addCs()">增加长沙</button>
    <!-- 目标2 在城市列表的深圳前添加一个子标签  <li id="cs">长沙</li>  -->
    <button onclick="addCsBeforeSz()">在深圳前插入长沙</button>
    <!-- 目标3  将城市列表的深圳替换为  <li id="cs">长沙</li>  -->
    <button onclick="replaceSz()">替换深圳</button>
    <!-- 目标4  将城市列表删除深圳  -->
    <button onclick="removeSz()">删除深圳</button>
    <!-- 目标5  清空城市列表  -->
    <button onclick="clearCity()">清空</button>
    
</body>
</html>
```

### 正则表达式

> 正则表达式是描述字符模式的对象。正则表达式用于对字符串模式匹配及检索替换，是对字符串执行模式匹配的强大工具。

+ 语法 

``` javascript
var patt=new RegExp(pattern,modifiers);
或者更简单的方式:
var patt=/pattern/modifiers; 
```

> 修饰符

| 修饰符                                             | 描述                                                     |
| :------------------------------------------------- | :------------------------------------------------------- |
| [i](https://www.runoob.com/js/jsref-regexp-i.html) | 执行对大小写不敏感的匹配。                               |
| [g](https://www.runoob.com/js/jsref-regexp-g.html) | 执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）。 |
| m                                                  | 执行多行匹配。                                           |

> 方括号

| 表达式                                                       | 描述                               |
| :----------------------------------------------------------- | :--------------------------------- |
| [[abc\]](https://www.runoob.com/jsref/jsref-regexp-charset.html) | 查找方括号之间的任何字符。         |
| [[^abc\]](https://www.runoob.com/jsref/jsref-regexp-charset-not.html) | 查找任何不在方括号之间的字符。     |
| [0-9]                                                        | 查找任何从 0 至 9 的数字。         |
| [a-z]                                                        | 查找任何从小写 a 到小写 z 的字符。 |
| [A-Z]                                                        | 查找任何从大写 A 到大写 Z 的字符。 |
| [A-z]                                                        | 查找任何从大写 A 到小写 z 的字符。 |
| [adgk]                                                       | 查找给定集合内的任何字符。         |
| [^adgk]                                                      | 查找给定集合外的任何字符。         |
| (red\|blue\|green)                                           | 查找任何指定的选项。               |

> 元字符

| 元字符                                                       | 描述                                        |
| :----------------------------------------------------------- | :------------------------------------------ |
| [.](https://www.runoob.com/jsref/jsref-regexp-dot.html)      | 查找单个字符，除了换行和行结束符。          |
| [\w](https://www.runoob.com/jsref/jsref-regexp-wordchar.html) | 查找数字、字母及下划线。                    |
| [\W](https://www.runoob.com/jsref/jsref-regexp-wordchar-non.html) | 查找非单词字符。                            |
| [\d](https://www.runoob.com/jsref/jsref-regexp-digit.html)   | 查找数字。                                  |
| [\D](https://www.runoob.com/jsref/jsref-regexp-digit-non.html) | 查找非数字字符。                            |
| [\s](https://www.runoob.com/jsref/jsref-regexp-whitespace.html) | 查找空白字符。                              |
| [\S](https://www.runoob.com/jsref/jsref-regexp-whitespace-non.html) | 查找非空白字符。                            |
| [\b](https://www.runoob.com/jsref/jsref-regexp-begin.html)   | 匹配单词边界。                              |
| [\B](https://www.runoob.com/jsref/jsref-regexp-begin-not.html) | 匹配非单词边界。                            |
| \0                                                           | 查找 NULL 字符。                            |
| [\n](https://www.runoob.com/jsref/jsref-regexp-newline.html) | 查找换行符。                                |
| \f                                                           | 查找换页符。                                |
| \r                                                           | 查找回车符。                                |
| \t                                                           | 查找制表符。                                |
| \v                                                           | 查找垂直制表符。                            |
| [\xxx](https://www.runoob.com/jsref/jsref-regexp-octal.html) | 查找以八进制数 xxx 规定的字符。             |
| [\xdd](https://www.runoob.com/jsref/jsref-regexp-hex.html)   | 查找以十六进制数 dd 规定的字符。            |
| [\uxxxx](https://www.runoob.com/jsref/jsref-regexp-unicode-hex.html) | 查找以十六进制数 xxxx 规定的 Unicode 字符。 |

> 量词

| 量词                                                         | 描述                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [n+](https://www.runoob.com/jsref/jsref-regexp-onemore.html) | 匹配任何包含至少一个 n 的字符串。例如，/a+/ 匹配 "candy" 中的 "a"，"caaaaaaandy" 中所有的 "a"。 |
| [n*](https://www.runoob.com/jsref/jsref-regexp-zeromore.html) | 匹配任何包含零个或多个 n 的字符串。例如，/bo*/ 匹配 "A ghost booooed" 中的 "boooo"，"A bird warbled" 中的 "b"，但是不匹配 "A goat grunted"。 |
| [n?](https://www.runoob.com/jsref/jsref-regexp-zeroone.html) | 匹配任何包含零个或一个 n 的字符串。例如，/e?le?/ 匹配 "angel" 中的 "el"，"angle" 中的 "le"。 |
| [n{X}](https://www.runoob.com/jsref/jsref-regexp-nx.html)    | 匹配包含 X 个 n 的序列的字符串。例如，/a{2}/ 不匹配 "candy," 中的 "a"，但是匹配 "caandy," 中的两个 "a"，且匹配 "caaandy." 中的前两个 "a"。 |
| [n{X,}](https://www.runoob.com/jsref/jsref-regexp-nxcomma.html) | X 是一个正整数。前面的模式 n 连续出现至少 X 次时匹配。例如，/a{2,}/ 不匹配 "candy" 中的 "a"，但是匹配 "caandy" 和 "caaaaaaandy." 中所有的 "a"。 |
| [n{X,Y}](https://www.runoob.com/jsref/jsref-regexp-nxy.html) | X 和 Y 为正整数。前面的模式 n 连续出现至少 X 次，至多 Y 次时匹配。例如，/a{1,3}/ 不匹配 "cndy"，匹配 "candy," 中的 "a"，"caandy," 中的两个 "a"，匹配 "caaaaaaandy" 中的前面三个 "a"。注意，当匹配 "caaaaaaandy" 时，即使原始字符串拥有更多的 "a"，匹配项也是 "aaa"。 |
| [n$](https://www.runoob.com/jsref/jsref-regexp-ndollar.html) | 匹配任何结尾为 n 的字符串。                                  |
| [^n](https://www.runoob.com/jsref/jsref-regexp-ncaret.html)  | 匹配任何开头为 n 的字符串。                                  |
| [?=n](https://www.runoob.com/jsref/jsref-regexp-nfollow.html) | 匹配任何其后紧接指定字符串 n 的字符串。                      |
| [?!n](https://www.runoob.com/jsref/jsref-regexp-nfollow-not.html) | 匹配任何其后没有紧接指定字符串 n 的字符串。                  |

> RegExp对象方法

| 方法                                                         | 描述                                               |
| :----------------------------------------------------------- | :------------------------------------------------- |
| [compile](https://www.runoob.com/jsref/jsref-regexp-compile.html) | 在 1.5 版本中已废弃。 编译正则表达式。             |
| [exec](https://www.runoob.com/jsref/jsref-exec-regexp.html)  | 检索字符串中指定的值。返回找到的值，并确定其位置。 |
| [test](https://www.runoob.com/jsref/jsref-test-regexp.html)  | 检索字符串中指定的值。返回 true 或 false。         |
| [toString](https://www.runoob.com/jsref/jsref-regexp-tostring.html) | 返回正则表达式的字符串。                           |

> 支持正则的String的方法

| 方法                                                    | 描述                             |
| :------------------------------------------------------ | :------------------------------- |
| [search](https://www.runoob.com/js/jsref-search.html)   | 检索与正则表达式相匹配的值。     |
| [match](https://www.runoob.com/js/jsref-match.html)     | 找到一个或多个正则表达式的匹配。 |
| [replace](https://www.runoob.com/js/jsref-replace.html) | 替换与正则表达式匹配的子串。     |
| [split](https://www.runoob.com/js/jsref-split.html)     | 把字符串分割为字符串数组。       |

### 正则表达式体验

#### 验证

**注意**：这里是使用**正则表达式对象**来**调用**方法。

```javascript
// 创建一个最简单的正则表达式对象
var reg = /o/;
// 创建一个字符串对象作为目标字符串
var str = 'Hello World!';
// 调用正则表达式对象的test()方法验证目标字符串是否满足我们指定的这个模式，返回结果true
console.log("/o/.test('Hello World!')="+reg.test(str));
```

#### 匹配

```javascript
// 创建一个最简单的正则表达式对象
var reg = /o/;
// 创建一个字符串对象作为目标字符串 
var str = 'Hello World!';
// 在目标字符串中查找匹配的字符，返回匹配结果组成的数组
var resultArr = str.match(reg);
// 数组长度为1
console.log("resultArr.length="+resultArr.length);

// 数组内容是o
console.log("resultArr[0]="+resultArr[0]);
```

#### 替换

**注意**：这里是使用**字符串对象**来**调用**方法。

```javascript
// 创建一个最简单的正则表达式对象
var reg = /o/;
// 创建一个字符串对象作为目标字符串
var str = 'Hello World!';
var newStr = str.replace(reg,'@');
// 只有第一个o被替换了，说明我们这个正则表达式只能匹配第一个满足的字符串
console.log("str.replace(reg)="+newStr);//Hell@ World!

// 原字符串并没有变化，只是返回了一个新字符串
console.log("str="+str);//str=Hello World!
```

#### 全文查找

如果不使用g对正则表达式对象进行修饰，则使用正则表达式进行查找时，仅返回第一个匹配；使用g后，返回所有匹配。

```javascript
// 目标字符串
var targetStr = 'Hello World!';

// 没有使用全局匹配的正则表达式
var reg = /[A-Z]/;
// 获取全部匹配
var resultArr = targetStr.match(reg);
// 数组长度为1
console.log("resultArr.length="+resultArr.length);
// 遍历数组，发现只能得到'H'
for(var i = 0; i < resultArr.length; i++){
  console.log("resultArr["+i+"]="+resultArr[i]);
}
```

对比

```javascript
// 目标字符串
var targetStr = 'Hello World!';
// 使用了全局匹配的正则表达式
var reg = /[A-Z]/g;
// 获取全部匹配
var resultArr = targetStr.match(reg);
// 数组长度为2
console.log("resultArr.length="+resultArr.length);
// 遍历数组，发现可以获取到“H”和“W”
for(var i = 0; i < resultArr.length; i++){
  console.log("resultArr["+i+"]="+resultArr[i]);
}
```

####  忽略大小写

```javascript
//目标字符串
var targetStr = 'Hello WORLD!';

//没有使用忽略大小写的正则表达式
var reg = /o/g;
//获取全部匹配
var resultArr = targetStr.match(reg);
//数组长度为1
console.log("resultArr.length="+resultArr.length);
//遍历数组，仅得到'o'
for(var i = 0; i < resultArr.length; i++){
  console.log("resultArr["+i+"]="+resultArr[i]);
}
```

对比

```javascript
//目标字符串
var targetStr = 'Hello WORLD!';
//使用了忽略大小写的正则表达式
var reg = /o/gi;
//获取全部匹配
var resultArr = targetStr.match(reg);
//数组长度为2
console.log("resultArr.length="+resultArr.length);
//遍历数组，得到'o'和'O'
for(var i = 0; i < resultArr.length; i++){
  console.log("resultArr["+i+"]="+resultArr[i]);
}
```

#### 元字符使用

```javascript
var str01 = 'I love Java';
var str02 = 'Java love me';
// 匹配以Java开头
var reg = /^Java/g;
console.log('reg.test(str01)='+reg.test(str01)); // false
console.log("<br />");
console.log('reg.test(str02)='+reg.test(str02)); // true
```

```javascript
var str01 = 'I love Java';
var str02 = 'Java love me';
// 匹配以Java结尾
var reg = /Java$/g;
console.log('reg.test(str01)='+reg.test(str01)); // true
console.log("<br />");
console.log('reg.test(str02)='+reg.test(str02)); // false
```

#### 字符集合的使用

```javascript
//n位数字的正则
var targetStr="123456789";
var reg=/^[0-9]{0,}$/;
//或者 ： var reg=/^\d*$/;
var b = reg.test(targetStr);//true
```

```javascript
//数字+字母+下划线，6-16位
var targetStr="HelloWorld";
var reg=/^[a-z0-9A-Z_]{6,16}$/;
var b = reg.test(targetStr);//true
```

#### 常用正则表达式

| 需求     | 正则表达式                                                 |
| -------- | ---------------------------------------------------------- |
| 用户名   | /^\[a-zA-Z ]\[a-zA-Z-0-9]{5,9}\$/                          |
| 密码     | /^\[a-zA-Z0-9 \_-@#& \*]{6,12}\$/                          |
| 前后空格 | /^\s+\|\s+\$/g                                             |
| 电子邮箱 | /^\[a-zA-Z0-9 \_.-]+@(\[a-zA-Z0-9-]+\[.]{1})+\[a-zA-Z]+\$/ |



##  XML

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681452257379.png" alt="1681452257379" style="zoom:50%;" />

> XML是EXtensible Markup Language的缩写，翻译过来就是可扩展标记语言。所以很明显，XML和HTML一样都是标记语言，也就是说它们的基本语法都是标签。

+ **可扩展** 三个字表面上的意思是XML允许自定义格式。但这不代表你可以随便写。

+ 在XML基本语法规范的基础上，你使用的那些第三方应用程序、框架会通过XML约束的方式强制规定配置文件中可以写什么和怎么写

+ XML基本语法这个知识点的定位是：我们不需要从零开始，从头到尾的一行一行编写XML文档，而是在第三方应用程序、框架已提供的配置文件的基础上修改。要改成什么样取决于你的需求，而怎么改取决XML基本语法和具体的XML约束。

### 常见配置文件类型

1.  properties文件,例如druid连接池就是使用properties文件作为配置文件
2.  XML文件,例如Tomcat就是使用XML文件作为配置文件
3.  YAML文件,例如SpringBoot就是使用YAML作为配置文件
4.  json文件,通常用来做文件传输，也可以用来做前端或者移动端的配置文件
5.  等等...

#### properties配置文件

> 示例

```.properties
atguigu.jdbc.url=jdbc:mysql://localhost:3306/atguigu
atguigu.jdbc.driver=com.mysql.cj.jdbc.Driver
atguigu.jdbc.username=root
atguigu.jdbc.password=root
```

> 语法规范

-   由键值对组成
-   键和值之间的符号是等号
-   每一行都必须顶格写，前面不能有空格之类的其他符号

#### xml配置文件

> 示例

```xml
<?xml version="1.0" encoding="UTF-8"?>
<students>
    <student>
        <name>张三</name>
        <age>18</age>
    </student>
    <student>
        <name>李四</name>
        <age>20</age>
    </student>
</students>
```

> XML的基本语法

+ XML的基本语法和HTML的基本语法简直如出一辙。其实这不是偶然的，XML基本语法+HTML约束=HTML语法。在逻辑上HTML确实是XML的子集。

-   XML文档声明 这部分基本上就是固定格式，要注意的是文档声明一定要从第一行第一列开始写

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

-   根标签
    -   &#x20;根**标签有且只能有一个。**
-   标签关闭
    -   双标签：开始标签和结束标签必须成对出现。
    -   单标签：单标签在标签内关闭。
-   标签嵌套
    -   可以嵌套，但是不能交叉嵌套。
-   注释不能嵌套
-   **标签名、属性名建议使用小写字母**
-   属性
    -   **属性必须有值**
    -   **属性值必须加引号，单双都行**

> XML的约束(稍微了解)

将来我们主要就是根据XML约束中的规定来编写XML配置文件，而且会在我们编写XML的时候根据约束来提示我们编写, 而XML约束主要包括DTD和Schema两种。

-   DTD
-   Schema

Schema约束要求我们一个XML文档中，所有标签，所有属性都必须在约束中有明确的定义。

下面我们以web.xml的约束声明为例来做个说明：

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
```

### *DOM4J进行XML解析

#### DOM4J的使用步骤

1.  导入jar包 dom4j.jar
2.  创建解析器对象(SAXReader)
3.  解析xml 获得Document对象
4.  获取根节点RootElement
5.  获取根节点下的子节点

#### DOM4J的API介绍

1.创建SAXReader对象

```java
SAXReader saxReader = new SAXReader();
```

&#x20;2. 解析XML获取Document对象: 需要传入要解析的XML文件的字节输入流

```java
Document document = reader.read(inputStream);
```

&#x20;3\. 获取文档的根标签

```java
Element rootElement = documen.getRootElement()
```

&#x20;4\. 获取标签的子标签

```java
//获取所有子标签
List<Element> sonElementList = rootElement.elements();
//获取指定标签名的子标签
List<Element> sonElementList = rootElement.elements("标签名");
```

&#x20;5\. 获取标签体内的文本

```java
String text = element.getText();
```

&#x20;6\. 获取标签的某个属性的值

```java
String value = element.attributeValue("属性名");
```

## Tomcat

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240228174505140.png" alt="image-20240228174505140" style="zoom:50%;" />

### Tomcat目录及测试

> C:\Program4java\apache-tomcat-10.1.7 这个目录下直接包含Tomcat的bin目录，conf目录等，我们称之为**Tomcat的安装目录或根目录**。

- bin：该目录下存放的是二进制可执行文件，如果是安装版，那么这个目录下会有两个exe文件：tomcat10.exe、tomcat10w\.exe，前者是在控制台下启动Tomcat，后者是弹出GUI窗口启动Tomcat；如果是解压版，那么会有startup.bat和shutdown.bat文件，startup.bat用来启动Tomcat，但需要先配置JAVA\_HOME环境变量才能启动，shutdawn.bat用来停止Tomcat；

- conf：这是一个非常非常重要的目录，这个目录下有四个最为重要的文件：

  - **server.xml：配置整个服务器信息。例如修改端口号。默认HTTP请求的端口号是：8080**

  - tomcat-users.xml：存储tomcat用户的文件，这里保存的是tomcat的用户名及密码，以及用户的角色信息。可以按着该文件中的注释信息添加tomcat用户，然后就可以在Tomcat主页中进入Tomcat Manager页面了；

    ``` html
    <tomcat-users xmlns="http://tomcat.apache.org/xml"
                  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                  xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
                  version="1.0">	
    	<role rolename="admin-gui"/>
    	<role rolename="admin-script"/>
    	<role rolename="manager-gui"/>
    	<role rolename="manager-script"/>
    	<role rolename="manager-jmx"/>
    	<role rolename="manager-status"/>
    	<user 	username="admin" 
    			password="admin" 
    			roles="admin-gui,admin-script,manager-gui,manager-script,manager-jmx,manager-status"
    	/>
    </tomcat-users>
    ```

    web.xml：部署描述符文件，这个文件中注册了很多MIME类型，即文档类型。这些MIME类型是客户端与服务器之间说明文档类型的，如用户请求一个html网页，那么服务器还会告诉客户端浏览器响应的文档是text/html类型的，这就是一个MIME类型。客户端浏览器通过这个MIME类型就知道如何处理它了。当然是在浏览器中显示这个html文件了。但如果服务器响应的是一个exe文件，那么浏览器就不可能显示它，而是应该弹出下载窗口才对。MIME就是用来说明文档的内容是什么类型的！

  - context.xml：对所有应用的统一配置，通常我们不会去配置它。

- lib：Tomcat的类库，里面是一大堆jar文件。如果需要添加Tomcat依赖的jar文件，可以把它放到这个目录中，当然也可以把应用依赖的jar文件放到这个目录中，这个目录中的jar所有项目都可以共享之，但这样你的应用放到其他Tomcat下时就不能再共享这个目录下的jar包了，所以建议只把Tomcat需要的jar包放到这个目录下；

- logs：这个目录中都是日志文件，记录了Tomcat启动和关闭的信息，如果启动Tomcat时有错误，那么异常也会记录在日志文件中。

- temp：存放Tomcat的临时文件，这个目录下的东西可以在停止Tomcat后删除！

- **webapps：存放web项目的目录，其中每个文件夹都是一个项目**；如果这个目录下已经存在了目录，那么都是tomcat自带的项目。其中ROOT是一个特殊的项目，在地址栏中访问：http://127.0.0.1:8080，没有给出项目目录时，对应的就是ROOT项目.http://localhost:8080/examples，进入示例项目。其中examples"就是项目名，即文件夹的名字。

- work：运行时生成的文件，最终运行的文件都在这里。通过webapps中的项目生成的！可以把这个目录下的内容删除，再次运行时会生再次生成work目录。当客户端用户访问一个JSP文件时，Tomcat会通过JSP生成Java文件，然后再编译Java文件生成class文件，生成的java和class文件都会存放到这个目录下。

- LICENSE：许可证。

- NOTICE：说明文件。

### WEB项目的标准结构

app  本应用根目录

+ static 非必要目录,约定俗成的名字,一般在此处放静态资源 ( css  js  img)
+ WEB-INF  必要目录,必须叫WEB-INF,受保护的资源目录,浏览器通过url不可以直接访问的目录
  + classes     必要目录,src下源代码,配置文件,编译后会在该目录下,web项目中如果没有源码,则该目录不会出现
  + lib             必要目录,项目依赖的jar编译后会出现在该目录下,web项目要是没有依赖任何jar,则该目录不会出现
  + web.xml   必要文件,web项目的基本配置文件. 较新的版本中可以没有该文件,但是学习过程中还是需要该文件 
+  index.html  非必要文件,index.html/index.htm/index.jsp**为默认的欢迎页**

###  WEB项目部署的方式

> 方式1   直接将编译好的项目放在webapps目录下  (已经演示)

> 方式2   将编译好的项目打成war包放在webapps目录下,tomcat启动后会自动解压war包(其实和第一种一样)

> 方式3   可以将项目放在非webapps的其他目录下,在tomcat中通过配置文件指向app的实际磁盘路径

+ 在磁盘的自定义目录上准备一个app

![1681456447284](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681456447284.png)

+ 在tomcat的conf下创建Catalina/localhost目录,并在该目录下准备一个app.xml文件

``` xml
<!-- 
	path: 项目的访问路径,也是项目的上下文路径,就是在浏览器中,输入的项目名称
    docBase: 项目在磁盘中的实际路径
 -->
<Context path="/app" docBase="D:\mywebapps\app" />
```

+ 启动tomcat访问测试即可

### URL的组成部分和项目中资源的对应关系

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240228180047177.png" alt="image-20240228180047177" style="zoom:50%;" />

## HTTP

### 简介

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240227211656053.png" alt="image-20240227211656053" style="zoom:50%;" />

  <img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240227212250568.png" alt="image-20240227212250568" style="zoom:50%;" />

#### HTTP协议的会话方式

> 浏览器与服务器之间的通信过程要经历四个步骤

![](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1557672342250_1H8nt17MNz.png)

-   浏览器与WEB服务器的连接过程是短暂的，每次连接只处理一个请求和响应。对每一个页面的访问，浏览器与WEB服务器都要建立一次单独的连接。
-   浏览器到WEB服务器之间的所有通讯都是完全独立分开的请求和响应对。

#### HTTP1.0和HTTP1.1的区别

> 在HTTP1.0版本中，浏览器请求一个带有图片的网页，会由于下载图片而与服务器之间开启一个新的连接；但在HTTP1.1版本中，允许浏览器在拿到当前请求对应的全部资源后再断开连接，提高了效率。

![](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1557672415271_EgyN-GdbWY.png)

#### 在浏览器中通过F12工具抓取请求响应报文包

> 几乎所有的PC端浏览器都支持了F12开发者工具,只不过不同的浏览器工具显示的窗口有差异

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681522138051.png" alt="1681522138051" style="zoom:80%;" />

### 请求和响应报文                       

#### 报文的格式

> 主体上分为报文首部和报文主体,中间空行隔开

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681522962846.png" alt="1681522962846" style="zoom: 62%;" />

> 报文部首可以继续细分为  "行" 和 "头"

![1681522998417](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681522998417.png)

#### 请求报文

> 客户端发给服务端的报文

+ 请求报文格式
  -   请求首行（**请求行**）；    GET/POST   资源路径?参数   HTTP/1.1
  -   请求头信息（**请求头**）；
  -   空行；
  -   请求体；POST请求才有请求体

> 浏览器 f12 网络下查看请求数据包

![1681524200024](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681524200024.png)

> form表单发送GET请求特点

1、由于请求参数在请求首行中已经携带了，所以没有请求体，也没有请求空行
2、请求参数拼接在url地址中，地址栏可见\[url?name1=value1\&name2=value2]，不安全
3、由于参数在地址栏中携带，所以由大小限制\[地址栏数据大小一般限制为4k]，只能携带纯文本
4、get请求参数只能上传文本数据
5、没有请求体。所以封装和解析都快，效率高， 浏览器默认提交的请求都是get请求比如：地址栏输入回车,超链接,表单默认的提交方式

> 查看GET请求行,请求头,请求体

+ 请求行组成部分
  + 请求方式  GET
  + 访问服务器的资源路径?参数1=值1&参数2=值2 ... ...
  + 协议及版本 HTTP/1.1

``` http
GET /05_web_tomcat/login_success.html?username=admin&password=123213 HTTP/1.1
```

+ 请求头

```  http
-主机虚拟地址
Host: localhost:8080   
-长连接
Connection: keep-alive 
-请求协议的自动升级[http的请求，服务器却是https的，浏览器自动会将请求协议升级为https的]
Upgrade-Insecure-Requests: 1  
- 用户系统信息
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.75 Safari/537.36
- 浏览器支持的文件类型
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
- 当前页面的上一个页面的路径[当前页面通过哪个页面跳转过来的]：   可以通过此路径跳转回上一个页面， 广告计费，防止盗链
Referer: http://localhost:8080/05_web_tomcat/login.html
- 浏览器支持的压缩格式
Accept-Encoding: gzip, deflate, br
- 浏览器支持的语言
Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
```

+ 请求空行

+ 请求体
  + GET请求数据不放在请求体

> form表单发送post请求特点

1、POST请求有请求体，而GET请求没有请求体。
2、post请求数据在请求体中携带，请求体数据大小没有限制，可以用来上传所有内容\[文件、文本]
3、只能使用post请求上传文件
4、post请求报文多了和请求体相关的配置\[请求头]
5、地址栏参数不可见，相对安全
6、post效率比get低

+ POST请求要求将form标签的method的属性设置为post

![1681525012046](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681525012046.png)

> 查看post的请求行 请求头 请求体

+ 请求行组成部分
  + 请求方式 POST
  + 访问服务器的资源路径?参数1=值1&参数2=值2 ... ...
  + 协议及版本 HTTP/1.1

``` http
POST /05_web_tomcat/login_success.html HTTP/1.1
```

+ 请求头

```  http
Host: localhost:8080
Connection: keep-alive
Content-Length: 31     -请求体内容的长度
Cache-Control: max-age=0  -无缓存
Origin: http://localhost:8080
Upgrade-Insecure-Requests: 1  -协议的自动升级
Content-Type: application/x-www-form-urlencoded   -请求体内容类型[服务器根据类型解析请求体参数]
User-Agent: Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/68.0.3440.75 Safari/537.36
Accept:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Referer: http://localhost:8080/05_web_tomcat/login.html
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
Cookie:JSESSIONID-
```

+ 请求空行

+ 请求体:浏览器提交给服务器的数据

``` http
username=admin&password=1232131
```

#### 响应报文

> 响应报文格式

-   响应首行（**响应行**）； 协议/版本  状态码    状态码描述
-   响应头信息（**响应头**）；
-   空行；
-   响应体；

![1681525347456](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681525347456.png)

![1681525384347](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681525384347.png)

+ 响应行组成部分
  + 协议及版本 HTTP/1.1
  + 响应状态码 200
  + 状态描述   OK  (缺省)

``` http
HTTP/1.1 200 OK
说明：响应协议为HTTP1.1，响应状态码为200，表示请求成功； 
```

+ 响应头

``` http
Server: Apache-Coyote/1.1   服务器的版本信息
Accept-Ranges: bytes
ETag: W/"157-1534126125811"
Last-Modified: Mon, 13 Aug 2018 02:08:45 GMT
Content-Type: text/html    响应体数据的类型[浏览器根据类型解析响应体数据]
Content-Length: 157   响应体内容的字节数
Date: Mon, 13 Aug 2018 02:47:57 GMT  响应的时间，这可能会有8小时的时区差
```

+ 响应体

``` html
<!--需要浏览器解析使用的内容[如果响应的是html页面，最终响应体内容会被浏览器显示到页面中]-->

<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Insert title here</title>
  </head>
  <body>
    恭喜你，登录成功了...
  </body>
</html>
```

> 响应状态码:响应码对浏览器来说很重要，它告诉浏览器响应的结果。比较有代表性的响应码如下：

+ **200：** 请求成功，浏览器会把响应体内容（通常是html）显示在浏览器中；
+ **302：** 重定向，当响应码为302时，表示服务器要求浏览器重新再发一个请求，服务器会发送一个响应头Location指定新请求的URL地址；
+ **304：** 使用了本地缓存
+ **404：** 请求的资源没有找到，说明客户端错误的请求了不存在的资源；
+ **405：** 请求的方式不允许
+ **500：** 请求资源找到了，但服务器内部出现了错误；

> 更多的响应状态码

| 状态码 | 状态码英文描述                  | 中文含义                                                     |
| :----- | :------------------------------ | :----------------------------------------------------------- |
| 1**    |                                 |                                                              |
| 100    | Continue                        | 继续。客户端应继续其请求                                     |
| 101    | Switching Protocols             | 切换协议。服务器根据客户端的请求切换协议。只能切换到更高级的协议，例如，切换到HTTP的新版本协议 |
| 2**    |                                 |                                                              |
| 200    | OK                              | 请求成功。一般用于GET与POST请求                              |
| 201    | Created                         | 已创建。成功请求并创建了新的资源                             |
| 202    | Accepted                        | 已接受。已经接受请求，但未处理完成                           |
| 203    | Non-Authoritative Information   | 非授权信息。请求成功。但返回的meta信息不在原始的服务器，而是一个副本 |
| 204    | No Content                      | 无内容。服务器成功处理，但未返回内容。在未更新网页的情况下，可确保浏览器继续显示当前文档 |
| 205    | Reset Content                   | 重置内容。服务器处理成功，用户终端（例如：浏览器）应重置文档视图。可通过此返回码清除浏览器的表单域 |
| 206    | Partial Content                 | 部分内容。服务器成功处理了部分GET请求                        |
| 3**    |                                 |                                                              |
| 300    | Multiple Choices                | 多种选择。请求的资源可包括多个位置，相应可返回一个资源特征与地址的列表用于用户终端（例如：浏览器）选择 |
| 301    | Moved Permanently               | 永久移动。请求的资源已被永久的移动到新URI，返回信息会包括新的URI，浏览器会自动定向到新URI。今后任何新的请求都应使用新的URI代替 |
| 302    | Found                           | 临时移动。与301类似。但资源只是临时被移动。客户端应继续使用原有URI |
| 303    | See Other                       | 查看其它地址。与301类似。使用GET和POST请求查看               |
| 304    | Not Modified                    | 未修改。所请求的资源未修改，服务器返回此状态码时，不会返回任何资源。客户端通常会缓存访问过的资源，通过提供一个头信息指出客户端希望只返回在指定日期之后修改的资源 |
| 305    | Use Proxy                       | 使用代理。所请求的资源必须通过代理访问                       |
| 306    | Unused                          | 已经被废弃的HTTP状态码                                       |
| 307    | Temporary Redirect              | 临时重定向。与302类似。使用GET请求重定向                     |
| 4**    |                                 |                                                              |
| 400    | Bad Request                     | 客户端请求的语法错误，服务器无法理解                         |
| 401    | Unauthorized                    | 请求要求用户的身份认证                                       |
| 402    | Payment Required                | 保留，将来使用                                               |
| 403    | Forbidden                       | 服务器理解请求客户端的请求，但是拒绝执行此请求               |
| 404    | Not Found                       | 服务器无法根据客户端的请求找到资源（网页）。通过此代码，网站设计人员可设置"您所请求的资源无法找到"的个性页面 |
| 405    | Method Not Allowed              | 客户端请求中的方法被禁止                                     |
| 406    | Not Acceptable                  | 服务器无法根据客户端请求的内容特性完成请求                   |
| 407    | Proxy Authentication Required   | 请求要求代理的身份认证，与401类似，但请求者应当使用代理进行授权 |
| 408    | Request Time-out                | 服务器等待客户端发送的请求时间过长，超时                     |
| 409    | Conflict                        | 服务器完成客户端的 PUT 请求时可能返回此代码，服务器处理请求时发生了冲突 |
| 410    | Gone                            | 客户端请求的资源已经不存在。410不同于404，如果资源以前有现在被永久删除了可使用410代码，网站设计人员可通过301代码指定资源的新位置 |
| 411    | Length Required                 | 服务器无法处理客户端发送的不带Content-Length的请求信息       |
| 412    | Precondition Failed             | 客户端请求信息的先决条件错误                                 |
| 413    | Request Entity Too Large        | 由于请求的实体过大，服务器无法处理，因此拒绝请求。为防止客户端的连续请求，服务器可能会关闭连接。如果只是服务器暂时无法处理，则会包含一个Retry-After的响应信息 |
| 414    | Request-URI Too Large           | 请求的URI过长（URI通常为网址），服务器无法处理               |
| 415    | Unsupported Media Type          | 服务器无法处理请求附带的媒体格式                             |
| 416    | Requested range not satisfiable | 客户端请求的范围无效                                         |
| 417    | Expectation Failed              | 服务器无法满足Expect的请求头信息                             |
| 5**    |                                 |                                                              |
| 500    | Internal Server Error           | 服务器内部错误，无法完成请求                                 |
| 501    | Not Implemented                 | 服务器不支持请求的功能，无法完成请求                         |
| 502    | Bad Gateway                     | 作为网关或者代理工作的服务器尝试执行请求时，从远程服务器接收到了一个无效的响应 |
| 503    | Service Unavailable             | 由于超载或系统维护，服务器暂时的无法处理客户端的请求。延时的长度可包含在服务器的Retry-After头信息中 |
| 504    | Gateway Time-out                | 充当网关或代理的服务器，未及时从远端服务器获取请求           |
| 505    | HTTP Version not supported      | 服务器不支持请求的HTTP协议的版本，无法完成处理               |

## Servlet

### Servlet简介

#### 1.1 动态资源和静态资源

> 静态资源

+ 无需在程序运行时通过代码运行生成的资源,在程序运行之前就写好的资源. 例如:html css js img ,音频文件和视频文件

> 动态资源 

+ 需要在程序运行时通过代码运行生成的资源,在程序运行之前无法确定的数据,运行时动态生成,例如Servlet,Thymeleaf ... ...
+ 动态资源指的不是视图上的动画效果或者是简单的人机交互效果

> 生活举例

+ 去蛋糕店买蛋糕
  + 直接买柜台上已经做好的  : 静态资源
  + 和柜员说要求后现场制作  : 动态资源

#### 1.2 Servlet简介

> Servlet  (server applet) 是运行在服务端(tomcat)的Java小程序，是sun公司提供一套定义动态资源规范; 从代码层面上来讲Servlet就是一个接口

+ 用来接收、处理客户端请求、响应给浏览器的动态资源。在整个Web应用中，Servlet主要负责接收处理请求、协同调度功能以及响应数据。我们可以把Servlet称为Web应用中的**控制器**

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681544428055.png" alt="1681544428055" style="zoom:50%;" />

+ 不是所有的JAVA类都能用于处理客户端请求,**能处理客户端请求并做出响应的一套技术标准就是Servlet**
+ Servlet是运行在服务端的,所以 Servlet必须在WEB项目中开发且在Tomcat这样的服务容器中运行

> 请求响应与HttpServletRequest和HttpServletResponse之间的对应关系

![1681699577344](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681699577344.png)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240228165958876.png" alt="image-20240228165958876" style="zoom:50%;" />

### Servlet开发流程

#### 2.1 目标

> 校验注册时,用户名是否被占用. 通过客户端向一个Servlet发送请求,携带username,如果用户名是'atguigu',则向客户端响应 NO,如果是其他,响应YES

#### 2.2 开发过程

> 步骤1 开发一个web类型的module 

+ 过程参照之前

> 步骤2 开发一个UserServlet

``` java
public class UserServlet  extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求中的参数
        String username = req.getParameter("username");
        if("atguigu".equals(username)){
            //通过响应对象响应信息
            resp.getWriter().write("NO");
        }else{
            resp.getWriter().write("YES");
        }

    }
}
```

+ 自定义一个类,要继承HttpServlet类
+ 重写service方法,该方法主要就是用于处理用户请求的服务方法
+ HttpServletRequest 代表请求对象,是有请求报文经过tomcat转换而来的,通过该对象可以获取请求中的信息
+ HttpServletResponse 代表响应对象,该对象会被tomcat转换为响应的报文,通过该对象可以设置响应中的信息 
+ Servlet对象的生命周期(创建,初始化,处理服务,销毁)是由tomcat管理的,无需我们自己new
+ HttpServletRequest HttpServletResponse 两个对象也是有tomcat负责转换,在调用service方法时传入给我们用的

> 步骤3 在web.xml为UseServlet配置请求的映射路径

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <servlet>
        <!--给UserServlet起一个别名-->
        <servlet-name>userServlet</servlet-name>
        <servlet-class>com.atguigu.servlet.UserServlet</servlet-class>
    </servlet>


    <servlet-mapping>
        <!--关联别名和映射路径-->
        <servlet-name>userServlet</servlet-name>
        <!--可以为一个Servlet匹配多个不同的映射路径,但是不同的Servlet不能使用相同的url-pattern-->
        <url-pattern>/userServlet</url-pattern>
       <!-- <url-pattern>/userServlet2</url-pattern>-->
        <!--
            /        表示通配所有资源,不包括jsp文件
            /*       表示通配所有资源,包括jsp文件
            /a/*     匹配所有以a前缀的映射路径
            *.action 匹配所有以action为后缀的映射路径
        -->
       <!-- <url-pattern>/*</url-pattern>-->
    </servlet-mapping>

</web-app>
```

+ Servlet并不是文件系统中实际存在的文件或者目录,所以为了能够请求到该资源,我们需要为其配置映射路径
+ servlet的请求映射路径配置在web.xml中
+ servlet-name作为servlet的别名,可以自己随意定义,见名知意就好
+ url-pattern标签用于定义Servlet的请求映射路径
+ 一个servlet可以对应多个不同的url-pattern
+ 多个servlet不能使用相同的url-pattern
+ url-pattern中可以使用一些通配写法
  + /        表示通配所有资源,不包括jsp文件
  + /*      表示通配所有资源,包括jsp文件
  + /a/*     匹配所有以a前缀的映射路径
  + *.action 匹配所有以action为后缀的映射路径

> 步骤4 开发一个form表单,向servlet发送一个get请求并携带username参数

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="userServlet">
        请输入用户名:<input type="text" name="username" /> <br>
        <input type="submit" value="校验">
    </form>
</body>
</html>
```

> 启动项目,访问index.html ,提交表单测试

+ 使用debug模式运行测试

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681547333799.png" alt="1681547333799"  />

> 映射关系图

![1681550398774](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681550398774.png)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240228214538475.png" alt="image-20240228214538475" style="zoom:50%;" />

### Servlet注解方式配置

#### 3.1 @WebServlet注解源码

> 官方JAVAEEAPI文档下载地址

+  [Java EE - Technologies (oracle.com)](https://www.oracle.com/java/technologies/javaee/javaeetechnologies.html#javaee8) 

+  @WebServlet注解的源码阅读

``` java

package jakarta.servlet.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

/**
 * @since Servlet 3.0
 */     
@Target({ ElementType.TYPE })
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebServlet {

    /**
     * The name of the servlet
     * 相当于 servlet-name
     * @return the name of the servlet
     */
    String name() default "";

    /**
     * The URL patterns of the servlet
     * 如果只配置一个url-pattern ,则通过该属性即可,和urlPatterns属性互斥
     * @return the URL patterns of the servlet
     */
    String[] value() default {};

    /**
     * The URL patterns of the servlet
     * 如果要配置多个url-pattern ,需要通过该属性,和value属性互斥
     * @return the URL patterns of the servlet
     */
    String[] urlPatterns() default {};

    /**
     * The load-on-startup order of the servlet
     * 配置Servlet是否在项目加载时实例化
     * @return the load-on-startup order of the servlet
     */
    int loadOnStartup() default -1;

    /**
     * The init parameters of the servlet
     * 配置初始化参数
     * @return the init parameters of the servlet
     */
    WebInitParam[] initParams() default {};

    /**
     * Declares whether the servlet supports asynchronous operation mode.
     *
     * @return {@code true} if the servlet supports asynchronous operation mode
     * @see jakarta.servlet.ServletRequest#startAsync
     * @see jakarta.servlet.ServletRequest#startAsync( jakarta.servlet.ServletRequest,jakarta.servlet.ServletResponse)
     */
    boolean asyncSupported() default false;

    /**
     * The small-icon of the servlet
     *
     * @return the small-icon of the servlet
     */
    String smallIcon() default "";

    /**
     * The large-icon of the servlet
     *
     * @return the large-icon of the servlet
     */
    String largeIcon() default "";

    /**
     * The description of the servlet
     *
     * @return the description of the servlet
     */
    String description() default "";

    /**
     * The display name of the servlet
     *
     * @return the display name of the servlet
     */
    String displayName() default "";

}

```

#### 3.2 @WebServlet注解使用

> 使用@WebServlet注解替换Servlet配置

``` java
@WebServlet(
        name = "userServlet",
        //value = "/user",
        urlPatterns = {"/userServlet1","/userServlet2","/userServlet"},
        initParams = {@WebInitParam(name = "encoding",value = "UTF-8")},
        loadOnStartup = 6
)
public class UserServlet  extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String encoding = getServletConfig().getInitParameter("encoding");
        System.out.println(encoding);
        // 获取请求中的参数
        String username = req.getParameter("username");
        if("atguigu".equals(username)){
            //通过响应对象响应信息
            resp.getWriter().write("NO");
        }else{
            resp.getWriter().write("YES");
        }
    }
}
```

### Servlet生命周期

#### 4.1 生命周期简介

> 什么是Servlet的生命周期

-   应用程序中的对象不仅在空间上有层次结构的关系，在时间上也会因为处于程序运行过程中的不同阶段而表现出不同状态和不同行为——这就是对象的生命周期。
-   简单的叙述生命周期，就是对象在容器中从开始创建到销毁的过程。

> Servlet容器

+ Servlet对象是Servlet容器创建的，生命周期方法都是由容器(目前我们使用的是Tomcat)调用的。这一点和我们之前所编写的代码有很大不同。在今后的学习中我们会看到，越来越多的对象交给容器或框架来创建，越来越多的方法由容器或框架来调用，开发人员要尽可能多的将精力放在业务逻辑的实现上。

> Servlet主要的生命周期执行特点

| 生命周期           | 对应方法                                                 | 执行时机               | 执行次数 |
| ------------------ | -------------------------------------------------------- | ---------------------- | -------- |
| 构造对象（实例化） | 构造器                                                   | 第一次请求或者容器启动 | 1        |
| 初始化             | init()                                                   | 构造完毕后             | 1        |
| 处理服务           | service(HttpServletRequest req,HttpServletResponse resp) | 每次请求               | 多次     |
| 销毁               | destory()                                                | 容器关闭               | 1        |

#### 4.2 生命周期测试

> 开发servlet代码

``` java
package com.atguigu.servlet;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

public class ServletLifeCycle  extends HttpServlet {
    public ServletLifeCycle(){
        System.out.println("构造器");
    }

    @Override
    public void init() throws ServletException {
        System.out.println("初始化方法");
    }

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("service方法");
    }

    @Override
    public void destroy() {
        System.out.println("销毁方法");
    }
}

```

> 配置Servlet

``` xml
  
    <servlet>
        <servlet-name>servletLifeCycle</servlet-name>
        <servlet-class>com.atguigu.servlet.ServletLifeCycle</servlet-class>
        <!--load-on-startup
            如果配置的是正整数则表示容器在启动时就要实例化Servlet,
            数字表示的是实例化的顺序
        -->
        <load-on-startup>1</load-on-startup>
        <!-- 默认值是-1 含义是tomcat启动是不会实例化该Servlet --!>


    </servlet>
    <servlet-mapping>
        <servlet-name>servletLifeCycle</servlet-name>
        <url-pattern>/servletLiftCycle</url-pattern>
    </ser vlet-mapping>
```

+ 请求Servlet测试

#### 4.3 生命周期总结

1. 通过生命周期测试我们发现Servlet对象在容器中是单例的（Servlet对象在容器中是单例的意思是指在Servlet容器（比如Tomcat）中，每个Servlet类只会被实例化一次，并且该实例会在整个应用程序的生命周期内被共享和复用。换句话说，当有多个请求到达该Servlet时，它们会共享同一个Servlet实例，而不是每个请求都创建一个新的实例。）
2. 容器是可以处理并发的用户请求的,每个请求在容器中都会开启一个线程
3. 多个线程可能会使用相同的Servlet对象,所以在Servlet中,**我们不要轻易定义一些容易经常发生修改的成员变量**
4. load-on-startup中定义的正整数表示实例化顺序,如果数字重复了,容器会自行解决实例化顺序问题,但是应该避免重复
5. Tomcat容器中,已经定义了一些随系统启动实例化的servlet,我们自定义的servlet的load-on-startup尽量不要占用数字1-5

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240229161028716.png" alt="image-20240229161028716" style="zoom:50%;" />

### Servlet继承结构

#### 5.1 Servlet 接口

> 源码及功能解释

+ 通过idea查看: 此处略

> 接口及方法说明

+ Servlet 规范接口,所有的Servlet必须实现 
  + public void init(ServletConfig config) throws ServletException;   
    + 初始化方法,容器在构造servlet对象后,自动调用的方法,容器负责实例化一个ServletConfig对象,并在调用该方法时传入
    + ServletConfig对象可以为Servlet 提供初始化参数
  + public ServletConfig getServletConfig();
    + 获取ServletConfig对象的方法,后续可以通过该对象获取Servlet初始化参数
  + public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException;
    + 处理请求并做出响应的服务方法,每次请求产生时由容器调用
    + 容器创建一个ServletRequest对象和ServletResponse对象,容器在调用service方法时,传入这两个对象
  + public String getServletInfo();
    + 获取ServletInfo信息的方法
  + public void destroy();
    + Servlet实例在销毁之前调用的方法

#### 5.2 GenericServlet 抽象类

侧重service方法以外的方法处理

> 源码

+ 通过idea查看: 此处略

> 源码解释

+ GenericServlet 抽象类是对Servlet接口一些固定功能的粗糙实现,以及对service方法的再次抽象声明,并定义了一些其他相关功能方法
  + private transient ServletConfig config; 
    + 初始化配置对象作为属性
  + public GenericServlet() { } 
    + 构造器,为了满足继承而准备
  + **public void destroy() { }** 
    + 销毁方法的平庸实现
  + public String getInitParameter(String name) 
    + 获取初始参数的快捷方法
  + public Enumeration<String> getInitParameterNames() 
    + 返回所有初始化参数名的方法
  + **public ServletConfig getServletConfig()**
    +  获取初始Servlet初始配置对象ServletConfig的方法
  + public ServletContext getServletContext()
    +  获取上下文对象ServletContext的方法
  + public String getServletInfo() 
    + 获取Servlet信息的平庸实现
  + **public void init(ServletConfig config) throws ServletException()** 
    + 初始化方法的实现,并在此调用了init的重载方法
  + **public void init() throws ServletException** 
    + 重载init方法,为了让我们自己定义初始化功能的方法
  + public void log(String msg) 
  + public void log(String message, Throwable t)
    +  打印日志的方法及重载
  + **public abstract void service(ServletRequest req, ServletResponse res) throws ServletException, IOException;** 
    + 服务方法再次声明
  + public String getServletName() 
    + 获取ServletName的方法

#### 5.3 HttpServlet 抽象类

##### 侧重service方法的处理

> 源码

+ 通过idea查看: 此处略

> 解释

+ abstract class HttpServlet extends GenericServlet  HttpServlet抽象类,除了基本的实现以外,增加了更多的基础功能
  + private static final String METHOD_DELETE = "DELETE";
  + private static final String METHOD_HEAD = "HEAD";
  + private static final String METHOD_GET = "GET";
  + private static final String METHOD_OPTIONS = "OPTIONS";
  + private static final String METHOD_POST = "POST";
  + private static final String METHOD_PUT = "PUT";
  + private static final String METHOD_TRACE = "TRACE";
    + 上述属性用于定义常见请求方式名常量值
  + public HttpServlet() {}
    + 构造器,用于处理继承
  + **public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException**
    + 对服务方法的实现
    + 在该方法中,将请求和响应对象转换成对应HTTP协议的HttpServletRequest HttpServletResponse对象
    + 调用重载的service方法
  + **public void service(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException**
    + 重载的service方法,被重写的service方法所调用
    + 在该方法中,通过请求方式判断,调用具体的do***方法完成请求的处理
  + protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException
  + protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException
  + protected void doHead(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException
  + protected void doPut(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException
  + protected void doDelete(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException
  + protected void doOptions(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException
  + protected void doTrace(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException
    + 对应不同请求方式的处理方法
    + **除了doOptions和doTrace方法,其他的do*** 方法都在故意响应错误信息**因为do方法是处理项目中具体业务逻辑的方法，必须重写

### 自定义Servlet

> 继承关系图解

![1682299663047](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682299663047.png)

+ 自定义Servlet中,必须要对处理请求的方法进行重写
  + **要么重写service方法**
  + **要么重写doGet/doPost方法**（推荐） 

### ServletConfig和ServletContext

#### 6.1  ServletConfig的使用

> ServletConfig是什么

+ 为Servlet提供初始配置参数的一种对象,每个Servlet都有自己独立唯一的ServletConfig对象
+ 容器会为每个Servlet实例化一个ServletConfig对象,并通过Servlet生命周期的init方法传入给Servlet作为属性

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682302307081.png" alt="1682302307081"  />

> ServletConfig是一个接口,定义了如下API

``` java
package jakarta.servlet;
import java.util.Enumeration;
public interface ServletConfig {
    String getServletName();
    ServletContext getServletContext();
    String getInitParameter(String var1);
    Enumeration<String> getInitParameterNames();
}
```

| 方法名                  | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| getServletName()        | 获取\<servlet-name>HelloServlet\</servlet-name>定义的Servlet名称 |
| getServletContext()     | 获取ServletContext对象                                       |
| getInitParameter()      | 获取配置Servlet时设置的『初始化参数』，根据名字获取值        |
| getInitParameterNames() | 获取所有初始化参数名组成的Enumeration对象                    |

> ServletConfig怎么用,测试代码如下

+ 定义Servlet

``` java
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletConfig servletConfig = this.getServletConfig();
        // 根据参数名获取单个参数
        String value = servletConfig.getInitParameter("param1");
        System.out.println("param1:"+value);
        // 获取所有参数名
        Enumeration<String> parameterNames = servletConfig.getInitParameterNames();
        // 迭代并获取参数名
        while (parameterNames.hasMoreElements()) {
            String paramaterName = parameterNames.nextElement();
            System.out.println(paramaterName+":"+servletConfig.getInitParameter(paramaterName));
        }
    }
}



public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletConfig servletConfig = this.getServletConfig();
        // 根据参数名获取单个参数
        String value = servletConfig.getInitParameter("param1");
        System.out.println("param1:"+value);
        // 获取所有参数名
        Enumeration<String> parameterNames = servletConfig.getInitParameterNames();
        // 迭代并获取参数名
        while (parameterNames.hasMoreElements()) {
            String paramaterName = parameterNames.nextElement();
            System.out.println(paramaterName+":"+servletConfig.getInitParameter(paramaterName));
        }
    }
}
```

+ 配置Servlet

``` xml
  <servlet>
       <servlet-name>ServletA</servlet-name>
       <servlet-class>com.atguigu.servlet.ServletA</servlet-class>
       <!--配置ServletA的初始参数-->
       <init-param>
           <param-name>param1</param-name>
           <param-value>value1</param-value>
       </init-param>
       <init-param>
           <param-name>param2</param-name>
           <param-value>value2</param-value>
       </init-param>
   </servlet>

    <servlet>
        <servlet-name>ServletB</servlet-name>
        <servlet-class>com.atguigu.servlet.ServletB</servlet-class>
        <!--配置ServletB的初始参数-->
        <init-param>
            <param-name>param3</param-name>
            <param-value>value3</param-value>
        </init-param>
        <init-param>
            <param-name>param4</param-name>
            <param-value>value4</param-value>
        </init-param>
    </servlet>

    <servlet-mapping>
        <servlet-name>ServletA</servlet-name>
        <url-pattern>/servletA</url-pattern>
    </servlet-mapping>

    <servlet-mapping>
        <servlet-name>ServletB</servlet-name>
        
        <url-pattern>/servletB</url-pattern>
    </servlet-mapping>
```

+ 请求Servlet测试

略

#### 6.2 ServletContext的使用

> ServletContext是什么

+ ServletContext对象有称呼为上下文对象,或者叫应用域对象(后面统一讲解域对象)
+ 容器会为每个**app创建一个独立的唯一的ServletContext对象**
+ ServletContext对象为所有的Servlet所共享
+ ServletContext**可以为所有的Servlet提供初始配置参数**

![1682303205351](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682303205351.png)

> ServletContext怎么用

+ 配置ServletContext参数

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <context-param>
        <param-name>paramA</param-name>
        <param-value>valueA</param-value>
    </context-param>
    <context-param>
        <param-name>paramB</param-name>
        <param-value>valueB</param-value>
    </context-param>
</web-app>
```

+ 在Servlet中获取ServletContext并获取参数

``` java
package com.atguigu.servlet;

import jakarta.servlet.ServletConfig;
import jakarta.servlet.ServletContext;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.util.Enumeration;

public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       
        // 从ServletContext中获取为所有的Servlet准备的参数
        ServletContext servletContext = this.getServletContext();
        String valueA = servletContext.getInitParameter("paramA");
        System.out.println("paramA:"+valueA);
        // 获取所有参数名
        Enumeration<String> initParameterNames = servletContext.getInitParameterNames();
        // 迭代并获取参数名
        while (initParameterNames.hasMoreElements()) {
            String paramaterName = initParameterNames.nextElement();
            System.out.println(paramaterName+":"+servletContext.getInitParameter(paramaterName));
        }
    }
}
```

#### 6.3 ServletContext其他重要API

> 获取资源的真实路径

``` java
String realPath = servletContext.getRealPath("资源在web目录中的路径");
```

+ 例如我们的目标是需要获取项目中某个静态资源的路径，不是工程目录中的路径，而是**部署目录中的路径**；我们如果直接拷贝其在我们电脑中的完整路径的话其实是有问题的，因为如果该项目以后部署到公司服务器上的话，路径肯定是会发生改变的，所以我们需要使用代码动态获取资源的真实路径.  只要使用了servletContext动态获取资源的真实路径，**那么无论项目的部署路径发生什么变化，都会动态获取项目运行时候的实际路径**，所以就不会发生由于写死真实路径而导致项目部署位置改变引发的路径错误问题

> 获取项目的上下文路径

``` java
String contextPath = servletContext.getContextPath();
```

+ 项目的部署名称,也叫项目的上下文路径,在部署进入tomcat时所使用的路径,该路径是可能发生变化的,通过该API动态获取项目真实的上下文路径,可以**帮助我们解决一些后端页面渲染技术或者请求转发和响应重定向中的路径问题**

>  域对象的相关API

+ 域对象: 一些用于存储数据和传递数据的对象,传递数据不同的范围,我们称之为不同的域,不同的域对象代表不同的域,共享数据的范围也不同
+ **ServletContext代表应用,所以ServletContext域也叫作应用域,是webapp中最大的域,可以在本应用内实现数据的共享和传递**
+ webapp中的三大域对象,分别是**应用域,会话域,请求域**
+ `后续我们会将三大域对象统一进行讲解和演示`,三大域对象都具有的API如下

| API                                         | 功能解释            |
| ------------------------------------------- | ------------------- |
| void setAttribute(String key,Object value); | 向域中存储/修改数据 |
| Object getAttribute(String key);            | 获得域中的数据      |
| void removeAttribute(String key);           | 移除域中的数据      |

### HttpServle tRequest

#### 7.1 HttpServletRequest简介

> HttpServletRequest是什么

+ HttpServletRequest是一个接口,其父接口是ServletRequest
+ HttpServletRequest是Tomcat将请求报文转换封装而来的对象,在Tomcat调用service方法时传入
+ HttpServletRequest代表客户端发来的请求,所有请求中的信息都可以通过该对象获得

![1681699577344](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681699577344-1709194406296-1.png)

#### 7.2 HttpServletRequest常见API

> HttpServletRequest怎么用

+ 获取请求行信息相关(方式,请求的url,协议及版本)

| API                           | 功能解释                       |
| ----------------------------- | ------------------------------ |
| StringBuffer getRequestURL(); | 获取客户端请求的url            |
| String getRequestURI();       | 获取客户端请求项目中的具体资源 |
| int getServerPort();          | 获取客户端发送请求时的端口     |
| int getLocalPort();           | 获取本应用在所在容器的端口     |
| int getRemotePort();          | 获取客户端程序的端口           |
| String getScheme();           | 获取请求协议                   |
| String getProtocol();         | 获取请求协议及版本号           |
| String getMethod();           | 获取请求方式                   |

+ 获得请求头信息相关

| API                                   | 功能解释               |
| ------------------------------------- | ---------------------- |
| String getHeader(String headerName);  | 根据头名称获取请求头   |
| Enumeration<String> getHeaderNames(); | 获取所有的请求头名字   |
| String getContentType();              | 获取content-type请求头 |

+ 获得请求参数相关

| API                                                     | 功能解释                             |
| ------------------------------------------------------- | ------------------------------------ |
| String getParameter(String parameterName);              | 根据请求参数名获取请求单个参数值     |
| String[] getParameterValues(String parameterName);      | 根据请求参数名获取请求多个参数值数组 |
| Enumeration<String> getParameterNames();                | 获取所有请求参数名                   |
| Map<String, String[]> getParameterMap();                | 获取所有请求参数的键值对集合         |
| BufferedReader getReader() throws IOException;          | 获取读取请求体的字符输入流           |
| ServletInputStream getInputStream() throws IOException; | 获取读取请求体的字节输入流           |
| int getContentLength();                                 | 获得请求体长度的字节数               |

+ 其他API

| API                                          | 功能解释                    |
| -------------------------------------------- | --------------------------- |
| String getServletPath();                     | 获取请求的Servlet的映射路径 |
| ServletContext getServletContext();          | 获取ServletContext对象      |
| Cookie[] getCookies();                       | 获取请求中的所有cookie      |
| HttpSession getSession();                    | 获取Session对象             |
| void setCharacterEncoding(String encoding) ; | 设置请求体字符集            |

### HttpServletResponse

#### 8.1 HttpServletResponse简介

> HttpServletResponse是什么

+ HttpServletResponse是一个接口,其父接口是ServletResponse
+ HttpServletResponse是Tomcat预先创建的,在Tomcat调用service方法时传入
+ HttpServletResponse代表对客户端的响应,该对象会被转换成响应的报文发送给客户端,通过该对象我们可以设置响应信息

![1681699577344](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1681699577344-1709209621988-10.png)

#### 8.2 HttpServletResponse的常见API

> HttpServletRequest怎么用

+ 设置响应行相关

| API                        | 功能解释       |
| -------------------------- | -------------- |
| void setStatus(int  code); | 设置响应状态码 |


+ 设置响应头相关

| API                                                    | 功能解释                                         |
| ------------------------------------------------------ | ------------------------------------------------ |
| void setHeader(String headerName, String headerValue); | 设置/修改响应头键值对                            |
| void setContentType(String contentType);               | 设置content-type响应头及响应字符集(设置MIME类型) |

+ 设置响应体相关

| API                                                       | 功能解释                                                |
| --------------------------------------------------------- | ------------------------------------------------------- |
| PrintWriter getWriter() throws IOException;               | 获得向响应体放入信息的字符输出流                        |
| ServletOutputStream getOutputStream() throws IOException; | 获得向响应体放入信息的字节输出流                        |
| void setContentLength(int length);                        | 设置响应体的字节长度,其实就是在设置content-length响应头 |

+ 其他API

| API                                                          | 功能解释                                            |
| ------------------------------------------------------------ | --------------------------------------------------- |
| void sendError(int code, String message) throws IOException; | 向客户端响应错误信息的方法,需要指定响应码和响应信息 |
| void addCookie(Cookie cookie);                               | 向响应体中增加cookie                                |
| void setCharacterEncoding(String encoding);                  | 设置响应体字符集                                    |

> MIME类型

+ MIME类型,可以理解为文档类型,用户表示传递的数据是属于什么类型的文档
+ 浏览器可以根据MIME类型决定该用什么样的方式解析接收到的响应体数据
+ 可以这样理解: 前后端交互数据时,告诉对方发给对方的是 html/css/js/图片/声音/视频/... ...
+ tomcat/conf/web.xml中配置了常见文件的拓展名和MIMIE类型的对应关系
+ 常见的MIME类型举例如下

| 文件拓展名                  | MIME类型               |
| --------------------------- | ---------------------- |
| .html                       | text/html              |
| .css                        | text/css               |
| .js                         | application/javascript |
| .png /.jpeg/.jpg/... ...    | image/jpeg             |
| .mp3/.mpe/.mpeg/ ... ...    | audio/mpeg             |
| .mp4                        | video/mp4              |
| .m1v/.m1v/.m2v/.mpe/... ... | video/mpeg             |



### 请求转发和响应重定向

**同样能够实现页面跳转的普适情况下优先使用响应重定向**

#### 9.1 概述

> 什么是请求转发和响应重定向

+ 请求转发和响应重定向是web应用中间接访问项目资源的两种手段,也是Servlet控制页面跳转的两种手段

+ 请求转发通过HttpServletRequest实现,响应重定向通过HttpServletResponse实现

+ 请求转发生活举例: 张三找李四借钱,李四没有,李四找王五,让王五借给张三
+ 响应重定向生活举例:张三找李四借钱,李四没有,李四让张三去找王五,张三自己再去找王五借钱

#### 9.2 请求转发

> 请求转发运行逻辑图

![1682321228643](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682321228643.png)

> 请求转发特点(背诵)

+ 请求转发通过HttpServletRequest对象获取请求转发器实现
+ 请求转发是服务器内部的行为,对客户端是屏蔽的
+ 客户端只发送了一次请求,客户端地址栏不变
+ 服务端只产生了一对请求和响应对象,这一对请求和响应对象会继续传递给下一个资源
+ 因为全程只有一个HttpServletRequset对象,所以请求参数可以传递,请求域中的数据也可以传递
+ 请求转发可以转发给其他Servlet动态资源,也可以转发给一些静态资源以实现页面跳转
+ 请求转发可以转发给WEB-INF下受保护的资源
+ 请求转发不能转发到本项目以外的外部资源

> 请求转发测试代码

![1682323740343](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682323740343.png)

+ ServletA

``` java
@WebServlet("/servletA")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //  获取请求转发器
        //  转发给servlet  ok
        RequestDispatcher  requestDispatcher = req.getRequestDispatcher("servletB");
        //  转发给一个视图资源 ok
        //RequestDispatcher requestDispatcher = req.getRequestDispatcher("welcome.html");
        //  转发给WEB-INF下的资源  ok
        //RequestDispatcher requestDispatcher = req.getRequestDispatcher("WEB-INF/views/view1.html");
        //  转发给外部资源   no
        //RequestDispatcher requestDispatcher = req.getRequestDispatcher("http://www.atguigu.com");
        //  获取请求参数
        String username = req.getParameter("username");
        System.out.println(username);
        //  向请求域中添加数据
        req.setAttribute("reqKey","requestMessage");
        //  做出转发动作
        requestDispatcher.forward(req,resp);
    }
}
```

+ ServletB

``` java
@WebServlet("/servletB")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求参数
        String username = req.getParameter("username");
        System.out.println(username);
        // 获取请求域中的数据
        String reqMessage = (String)req.getAttribute("reqKey");
        System.out.println(reqMessage);
        // 做出响应
        resp.getWriter().write("servletB response");        
    }
}
```

+ 打开浏览器,输入以下url测试

``` http
http://localhost:8080/web03_war_exploded/servletA?username=atguigu
```

#### 9.3 响应重定向

> 响应重定向运行逻辑图

![1682322460011](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682322460011.png)

> 响应重定向特点(背诵)

+ 响应重定向通过HttpServletResponse对象的sendRedirect方法实现
+ 响应重定向是服务端通过302响应码和路径,告诉客户端自己去找其他资源,是在服务端提示下的,客户端的行为
+ 客户端至少发送了两次请求,客户端地址栏是要变化的
+ 服务端产生了多对请求和响应对象,且请求和响应对象不会传递给下一个资源
+ 因为全程产生了多个HttpServletRequset对象,所以请求参数不可以传递,请求域中的数据也不可以传递
+ 重定向可以是其他Servlet动态资源,也可以是一些静态资源以实现页面跳转
+ 重定向不可以到给WEB-INF下受保护的资源
+ 重定向可以到本项目以外的外部资源

> 响应重定向测试代码

![1682323740343](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682323740343.png)



+ ServletA

``` java
@WebServlet("/servletA")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //  获取请求参数
        String username = req.getParameter("username");
        System.out.println(username);
        //  向请求域中添加数据
        req.setAttribute("reqKey","requestMessage");
        //  响应重定向
        // 重定向到servlet动态资源 OK
        resp.sendRedirect("servletB");
        // 重定向到视图静态资源 OK
        //resp.sendRedirect("welcome.html");
        // 重定向到WEB-INF下的资源 NO
        //resp.sendRedirect("WEB-INF/views/view1");
        // 重定向到外部资源
        //resp.sendRedirect("http://www.atguigu.com");
    }
}
```

+ ServletB

``` java
@WebServlet("/servletB")
public class ServletB extends HttpServlet {
    @Override
    protected void service(*HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求参数
        String username = req.getParameter("username");
        System.out.println(username);
        // 获取请求域中的数据
        String reqMessage = (String)req.getAttribute("reqKey");
        System.out.println(reqMessage);
        // 做出响应
        resp.getWriter().write("servletB response");

    }
}
```

+ 打开浏览器,输入以下url测试

``` url
http://localhost:8080/web03_war_exploded/servletA?username=atguigu
```

### web乱码和路径问题总结

####  10.1 乱码问题

> 乱码问题产生的根本原因是什么

1. 数据的编码和解码使用的不是同一个字符集
2. 使用了不支持某个语言文字的字符集

> 各个字符集的兼容性



<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682326867396.png" alt="1682326867396" style="zoom:80%;" />

+ 由上图得知,上述字符集都兼容了ASCII
+ ASCII中有什么? 英文字母和一些通常使用的符号,所以这些东西无论使用什么字符集都不会乱码

##### 10.1.3.1 GET请求乱码

> GET请求方式乱码分析

+ GET方式提交参数的方式是将参数放到URL后面,如果使用的不是UTF-8,那么会对参数进行URL编码处理
+ HTML中的 <meta charset='字符集'/> 影响了GET方式提交参数的URL编码
+ tomcat10.1.7的URI编码默认为 UTF-8
+ 当GET方式提交的参数URL编码和tomcat10.1.7默认的URI编码不一致时,就会出现乱码

> GET请求方式乱码演示

+ 浏览器解析的文档的<meta charset="GBK" /> 

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682385870660.png" alt="1682385870660" style="zoom: 67%;" />

+ GET方式提交时,会对数据进行URL编码处理 ,是将GBK 转码为 "百分号码"

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682385997927.png" alt="1682385997927" style="zoom: 80%;" />

+ tomcat10.1.7 默认使用UTF-8对URI进行解析,造成前后端使用的字符集不一致,出现乱码

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682386110151.png" alt="1682386110151" style="zoom: 80%;" />

> GET请求方式乱码解决

+ 方式1  :设置GET方式提交的编码和Tomcat10.1.7的URI默认解析编码一致即可 (推荐)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682386298048.png" alt="1682386298048" style="zoom: 75%;" />



<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682386374464.png" alt="1682386374464" style="zoom:85%;" />



+ 方式2 : 设置Tomcat10.1.7的URI解析字符集和GET请求发送时所使用URL转码时的字符集一致即可,修改conf/server.xml中 Connecter 添加 URIEncoding="GBK"  (不推荐)

![1682386551684](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682386551684.png)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682386611945.png" alt="1682386611945" style="zoom: 50%;" />

##### 10.1.3.2 POST方式请求乱码

> POST请求方式乱码分析

+ POST请求将参数放在请求体中进行发送
+ 请求体使用的字符集受到了<meta charset="字符集"/> 的影响
+ Tomcat10.1.7 默认使用UTF-8字符集对请求体进行解析
+ 如果请求体的URL转码和Tomcat的请求体解析编码不一致,就容易出现乱码

> POST方式乱码演示

+ POST请求请求体受到了<meta charset="字符集"/> 的影响

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682387258428.png" alt="1682387258428" style="zoom:67%;" />

+ 请求体中,将GBK数据进行 URL编码

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682387349916.png" alt="1682387349916" style="zoom: 85%;" />

+ 后端默认使用UTF-8解析请求体,出现字符集不一致,导致乱码

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682387412704.png" alt="1682387412704" style="zoom: 67%;" />

> POST请求方式乱码解决

+ 方式1 : 请求时,使用UTF-8字符集提交请求体 (推荐)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682387836615.png" alt="1682387836615" style="zoom: 67%;" />

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682387857587.png" alt="1682387857587" style="zoom:88%;" />

+ 方式2 : 后端在获取参数前,设置解析请求体使用的字符集和请求发送时使用的字符集一致 (不推荐)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682388026978.png" alt="1682388026978" style="zoom: 75%;" />

##### 10.1.3.3 响应乱码问题

> 响应乱码分析

+ 在Tomcat10.1.7中,向响应体中放入的数据默认使用了工程编码 UTF-8
+ 浏览器在接收响应信息时,使用了不同的字符集或者是不支持中文的字符集就会出现乱码

> 响应乱码演示

+ 服务端通过response对象向响应体添加数据

![1682388204239](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682388204239.png)

+ 浏览器接收数据解析乱码

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682388599014.png" alt="1682388599014" style="zoom:80%;" />

> 响应乱码解决

+ 方式1 : 手动设定浏览器对本次响应体解析时使用的字符集(不推荐)
  + edge和 chrome浏览器没有提供直接的比较方便的入口,不方便

+ 方式2: 后端通过设置响应体的字符集和浏览器解析响应体的默认字符集一致(不推荐)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682389063225.png" alt="1682389063225" style="zoom: 75%;" />



方式3: 通过设置content-type响应头,告诉浏览器以指定的字符集解析响应体(推荐)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682389263627.png" alt="1682389263627"  />



<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682389317234.png" alt="1682389317234" style="zoom: 67%;" />



#### 10.2  路径问题

> 相对路径和绝对路径

+ 相对路径
  + 相对路径的规则是: 以当前资源所在的路径为出发点去寻找目标资源
  + 相对路径不以 / 开头
  + 在file协议下,使用的是磁盘路径
  + 在http协议下,使用的是url路径
  + 相对路径中可以使用 ./表示当前资源所在路径,可以省略不写
  + 相对路径中可以使用../表示当前资源所在路径的上一层路径,需要时要手动添加

+ 绝对路径
  + 绝对路径的规则是: 使用以一个固定的路径做出出发点去寻找目标资源,和当前资源所在的路径没有关系
  + 绝对路径要以/ 开头
  + 绝对路径的写法中,不以当前资源的所在路径为出发点,所以不会出现  ./ 和../
  + 不同的项目和不同的协议下,绝对路径的基础位置可能不同,要通过测试确定
  + 绝对路径的好处就是:无论当前资源位置在哪,寻找目标资源路径的写法都一致
+ 应用场景
  1. 前端代码中,href src action 等属性
  2. 请求转发和重定向中的路径

##### 10.2.1 前端路径问题

> 前端项目结构

![1682390999417](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682390999417.png)

###### 10.2.1.1  相对路径情况分析

**不以斜线/开头**

> 相对路径情况1:web/index.html中引入web/static/img/logo.png

+ 访问index.html的url为   :  http://localhost:8080/web03_war_exploded/index.html
+ 当前资源为                      :  index.html
+ 当前资源的所在路径为  : http://localhost:8080/web03_war_exploded/
+ 要获取的目标资源url为 :  http://localhost:8080/web03_war_exploded/static/img/logo.png
+ index.html中定义的了    : `<img src="static/img/logo.png"/>`
+ 寻找方式就是在当前资源所在路径(http://localhost:8080/web03_war_exploded/)后拼接src属性值(static/img/logo.png),正好是目标资源正常获取的url(http://localhost:8080/web03_war_exploded/static/img/logo.png)

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    
    <img src="static/img/logo.png">
</body>
</html>

```

> 相对路径情况2:web/a/b/c/test.html中引入web/static/img/logo.png

+ 访问test.html的url为      :  http://localhost:8080/web03_war_exploded/a/b/c/test.html
+ 当前资源为                      :  test.html
+ 当前资源的所在路径为  : http://localhost:8080/web03_war_exploded/a/b/c/
+ 要获取的目标资源url为 :  http://localhost:8080/web03_war_exploded/static/img/logo.png
+ test.html中定义的了       : `<img src="../../../static/img/logo.png"/>`
+ 寻找方式就是在当前资源所在路径(http://localhost:8080/web03_war_exploded/a/b/c/)后拼接src属性值(../../../static/img/logo.png),其中 ../可以抵消一层路径,正好是目标资源正常获取的url(http://localhost:8080/web03_war_exploded/static/img/logo.png)

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!-- ../代表上一层路径 -->
    <img src="../../../static/img/logo.png">
</body>
</html>
```

> 相对路径情况3:web/WEB-INF/views/view1.html中引入web/static/img/logo.png

+ view1.html在WEB-INF下,需要通过Servlet请求转发获得

``` java
@WebServlet("/view1Servlet")
public class View1Servlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        RequestDispatcher requestDispatcher = req.getRequestDispatcher("WEB-INF/views/view1.html");
        requestDispatcher.forward(req,resp);
    }
}
```

+ 访问view1.html的url为   :  http://localhost:8080/web03_war_exploded/view1Servlet
+ 当前资源为                      :  view1Servlet
+ 当前资源的所在路径为  : http://localhost:8080/web03_war_exploded/
+ 要获取的目标资源url为 :  http://localhost:8080/web03_war_exploded/static/img/logo.png
+ view1.html中定义的了    : `<img src="static/img/logo.png"/>`
+ 寻找方式就是在当前资源所在路径(http://localhost:8080/web03_war_exploded/)后拼接src属性值(static/img/logo.png),正好是目标资源正常获取的url(http://localhost:8080/web03_war_exploded/static/img/logo.png)

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<img src="static/img/logo.png">
</body>
</html>
```

###### 10.2.1.2 绝对路径情况分析

**以斜线开头/ 不同的项目中 固定的路径出发点可能不一致，可以测试一下**

> 绝对路径情况1:web/index.html中引入web/static/img/logo.png

+ 访问index.html的url为   :  http://localhost:8080/web03_war_exploded/index.html
+ 绝对路径的基准路径为  :  http://localhost:8080
+ 要获取的目标资源url为 :  http://localhost:8080/web03_war_exploded/static/img/logo.png
+ index.html中定义的了    : `<img src="/web03_war_exploded/static/img/logo.png"/>`
+ 寻找方式就是在基准路径(http://localhost:8080)后面拼接src属性值(/web03_war_exploded/static/img/logo.png),得到的正是目标资源访问的正确路径

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!-- 绝对路径写法 -->
    <img src="/web03_war_exploded/static/img/logo.png">
</body>
</html>


```

> 绝对路径情况2:web/a/b/c/test.html中引入web/static/img/logo.png

+ 访问test.html的url为   :  http://localhost:8080/web03_war_exploded/a/b/c/test.html
+ 绝对路径的基准路径为  :  http://localhost:8080
+ 要获取的目标资源url为 :  http://localhost:8080/web03_war_exploded/static/img/logo.png
+ test.html中定义的了    : `<img src="/web03_war_exploded/static/img/logo.png"/>`
+ 寻找方式就是在基准路径(http://localhost:8080)后面拼接src属性值(/web03_war_exploded/static/img/logo.png),得到的正是目标资源访问的正确路径

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!-- 绝对路径写法 -->
    <img src="/web03_war_exploded/static/img/logo.png">
</body>
</html>
```

> 绝对路径情况3:web/WEB-INF/views/view1.html中引入web/static/img/logo.png

+ view1.html在WEB-INF下,需要通过Servlet请求转发获得

``` java
@WebServlet("/view1Servlet")
public class View1Servlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        RequestDispatcher requestDispatcher = req.getRequestDispatcher("WEB-INF/views/view1.html");
        requestDispatcher.forward(req,resp);
    }
}
```

+ 访问view1.html的url为   :  http://localhost:8080/web03_war_exploded/view1Servlet
+ 绝对路径的基准路径为  :  http://localhost:8080
+ 要获取的目标资源url为 :  http://localhost:8080/web03_war_exploded/static/img/logo.png
+ view1.html中定义的了    : `<img src="/web03_war_exploded/static/img/logo.png"/>`
+ 寻找方式就是在基准路径(http://localhost:8080)后面拼接src属性值(/static/img/logo.png),得到的正是目标资源访问的正确路径

``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

<img src="/web03_war_exploded/static/img/logo.png">
</body>
</html>
```

###### 10.2.1.3 base标签的使用

> base标签定义页面相对路径公共前缀

+ base 标签定义在head标签中,用于定义相对路径的公共前缀
+ base 标签定义**的公共前缀只在相对路径上有效,绝对路径中无效**
+ **如果相对路径开头有 ./ 或者../修饰,则base标签对该路**径同样无效

> index.html 和a/b/c/test.html 以及view1Servlet 中的路径处理

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!--定义相对路径的公共前缀,将相对路径转化成了绝对路径-->
    <base href="/web03_war_exploded/">
</head>
<body>
    <img src="static/img/logo.png">
</body>
</html>
```

###### 10.2.1.4 缺省项目上下文路径

> 项目上下文路径变化问题

+ 通过 base标签虽然解决了相对路径转绝对路径问题,但是base中定义的是项目的上下文路径
+ 项目的上下文路径是可以随意变化的
+ 一旦项目的上下文路径发生变化,所有base标签中的路径都需要改

> 解决方案

+ 将项目的上下文路径进行缺省设置,设置为 /,所有的绝对路径中就不必填写项目的上下文了,直接就是/开头即可

##### 10.2.2 重定向中的路径问题

> 目标 :由/x/y/z/servletA重定向到a/b/c/test.html

``` java
@WebServlet("/x/y/z/servletA")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        
    }
}

```

###### 10.2.2.1相对路径写法

+ 访问ServletA的url为   :  http://localhost:8080/web03_war_exploded/x/y/z/servletA
+ 当前资源为                      :  servletA
+ 当前资源的所在路径为  : http://localhost:8080/web03_war_exploded/x/x/z/
+ 要获取的目标资源url为 :  http://localhost:8080/web03_war_exploded/a/b/c/test.html
+ ServletA重定向的路径    :  ../../../a/b/c/test/html
+ 寻找方式就是在当前资源所在路径(http://localhost:8080/web03_war_exploded/x/y/z/)后拼接(../../../a/b/c/test/html),形成(http://localhost:8080/web03_war_exploded/x/y/z/../../../a/b/c/test/html)每个../抵消一层目录,正好是目标资源正常获取的url(http://localhost:8080/web03_war_exploded/a/b/c/test/html)

``` java
@WebServlet("/x/y/z/servletA")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 相对路径重定向到test.html
        resp.sendRedirect("../../../a/b/c/test.html");
    }
}
```

###### 10.2.2.2绝对路径写法

+ 访问ServletA的url为   :  http://localhost:8080/web03_war_exploded/x/y/z/servletA

+ 绝对路径的基准路径为  :  http://localhost:8080

+ 要获取的目标资源url为 :  http://localhost:8080/web03_war_exploded/a/b/c/test.html

+ ServletA重定向的路径    : /web03_war_exploded/a/b/c/test.html

+ 寻找方式就是在基准路径(http://localhost:8080)后面拼接(/web03_war_exploded/a/b/c/test.html),得到( http://localhost:8080/web03_war_exploded/a/b/c/test.html)正是目标资源访问的正确路径

+ 绝对路径中需要填写项目上下文路径,但是上下文路径是变换的

  + 可以通过 ServletContext的getContextPath()获取上下文路径
  + 可以将项目上下文路径定义为 / 缺省路径,那么路径中直接以/开头即可

  ``` java
  //绝对路径中,要写项目上下文路径
  //resp.sendRedirect("/web03_war_exploded/a/b/c/test.html");
  // 通过ServletContext对象动态获取项目上下文路径
  //resp.sendRedirect(getServletContext().getContextPath()+"/a/b/c/test.html");
  // 缺省项目上下文路径时,直接以/开头即可
  resp.sendRedirect("/a/b/c/test.html");
  ```


##### 10.2.3 请求转发中的路径问题

> 目标 :由x/y/servletB请求转发到a/b/c/test.html

``` java
@WebServlet("/x/y/servletB")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

    }
}
```

###### 10.2.3.1 相对路径写法

+ 访问ServletB的url为       :  http://localhost:8080/web03_war_exploded/x/y/servletB

+ 当前资源为                      :  servletB

+ 当前资源的所在路径为  : http://localhost:8080/web03_war_exploded/x/x/

+ 要获取的目标资源url为 :  http://localhost:8080/web03_war_exploded/a/b/c/test.html

+ ServletA请求转发路径    :  ../../a/b/c/test/html

+ 寻找方式就是在当前资源所在路径(http://localhost:8080/web03_war_exploded/x/y/)后拼接(../../a/b/c/test/html),形成(http://localhost:8080/web03_war_exploded/x/y/../../a/b/c/test/html)每个../抵消一层目录,正好是目标资源正常获取的url(http://localhost:8080/web03_war_exploded/a/b/c/test/html)

  ``` java
  @WebServlet("/x/y/servletB")
  public class ServletB extends HttpServlet {
      @Override
      protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          RequestDispatcher requestDispatcher = req.getRequestDispatcher("../../a/b/c/test.html");
          requestDispatcher.forward(req,resp);
      }
  }
  
  
  ```

###### 10.2.3.2绝对路径写法

+ 请求转发只能转发到项目内部的资源,其绝对路径无需添加项目上下文路径

+ 请求转发绝对路径的基准路径相当于http://localhost:8080/web03_war_exploded

+ 在项目上下文路径为缺省值时,也无需改变,直接以/开头即可

  ```java
  @WebServlet("/x/y/servletB")
  public class ServletB extends HttpServlet {
      @Override
      protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          RequestDispatcher requestDispatcher = req.getRequestDispatcher("/a/b/c/test.html");
          requestDispatcher.forward(req,resp);
      }
  }
  ```

###### 10.2.3.3目标资源内相对路径处理

+ 此时需要注意,请求转发是服务器行为,浏览器不知道,地址栏不变化,相当于我们访问test.html的路径为http://localhost:8080/web03_war_exploded/x/y/servletB

+ 那么此时 test.html资源的所在路径就是http://localhost:8080/web03_war_exploded/x/y/所以test.html中相对路径要基于该路径编写,如果使用绝对路径则不用考虑

  ``` html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
      <!--
  		当前资源路径是     http://localhost:8080/web03_war_exploded/x/y/servletB
          当前资源所在路径是  http://localhost:8080/web03_war_exploded/x/y/
          目标资源路径=所在资源路径+src属性值 
  		http://localhost:8080/web03_war_exploded/x/y/../../static/img/logo.png
          http://localhost:8080/web03_war_exploded/static/img/logo.png
  		得到目标路径正是目标资源的访问路径	
      -->
  <img src="../../static/img/logo.png">
  </body>
  </html>
  ```



### MVC架构模式

**高内聚低耦合 开闭原则**

>  MVC（Model View Controller）是软件工程中的一种**`软件架构模式`**，它把软件系统分为**`模型`**、**`视图`**和**`控制器`**三个基本部分。用一种业务逻辑、数据、界面显示分离的方法组织代码，将业务逻辑聚集到一个部件里面，在改进和个性化定制界面及用户交互的同时，不需要重新编写业务逻辑。

+ **M**：Model 模型层,具体功能如下
  1. 存放和数据库对象的实体类以及一些用于存储非数据库表完整相关的VO对象
  2. 存放一些对数据进行逻辑运算操作的的一些业务处理代码

+ **V**：View 视图层,具体功能如下
  1. 存放一些视图文件相关的代码 html css js等
  2. 在前后端分离的项目中,后端已经没有视图文件,该层次已经衍化成独立的前端项目

+ **C**：Controller 控制层,具体功能如下
  1. 接收客户端请求,获得请求数据
   2. 将准备好的数据响应给客户端

> MVC模式下,项目中的常见包

+ M:
  1. 实体类包(pojo /entity /bean) 专门存放和数据库对应的实体类和一些VO对象
  2. 数据库访问包(dao/mapper)  专门存放对数据库不同表格CURD方法封装的一些类
  3. 服务包(service)                       专门存放对数据进行业务逻辑运算的一些类

+ C:
  1. 控制层包(controller)

+ V:
  1. web目录下的视图资源 html css js img 等
  2. 前端工程化后,在后端项目中已经不存在了



非前后端分离的MVC

![1690349913931](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1690349913931.png)

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240310173312644.png" alt="image-20240310173312644" style="zoom:50%;" />

前后端分离的MVC

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240310172344252.png" alt="image-20240310172344252" style="zoom:50%;" />

## 会话管理

> HTTP是无状态协议

+ 无状态就是不保存状态,即无状态协议(stateless),HTTP协议自身不对请求和响应之间的通信状态进行保存,也就是说,在HTTP协议这个级别,协议对于发送过的请求或者响应都不做持久化处理
+ 简单理解:浏览器发送请求,服务器接收并响应,但是服务器不记录请求是否来自哪个浏览器,服务器没记录浏览器的特征,就是客户端的状态

### 会话管理手段

> Cookie和Session配合解决

+ cookie是在客户端保留少量数据的技术,主要通过响应头向客户端响应一些客户端要保留的信息
+ session是在服务端保留更多数据的技术,主要通过HttpSession对象保存一些和客户端相关的信息
+ cookie和session配合记录请求状态

### Cookie

> cookie是一种客户端会话技术,cookie由服务端产生,它是服务器存放在浏览器的一小份数据,浏览器以后每次访问该服务器的时候都会将这小份数据携带到服务器去。

+ 服务端创建cookie,将cookie放入响应对象中,Tomcat容器将cookie转化为set-cookie响应头,响应给客户端
+ 客户端在收到cookie的响应头时,在下次请求该服务的资源时,会以cookie请求头的形式携带之前收到的Cookie
+ cookie是一种键值对格式的数据,从tomcat8.5开始可以保存中文,但是不推荐
+ 由于cookie是存储于客户端的数据,比较容易暴露,一般不存储一些敏感或者影响安全的数据

> 原理图

![1682411089082](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682411089082.png)



#### 1.2.2 Cookie的使用

> servletA向响应中增加Cookie

``` java
@WebServlet("/servletA")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 创建Cookie
        Cookie cookie1 =new Cookie("c1","c1_message");
        Cookie cookie2 =new Cookie("c2","c2_message");
        // 将cookie放入响应对象
        resp.addCookie(cookie1);
        resp.addCookie(cookie2);
    }
}
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682411522695.png" alt="1682411522695" style="zoom:80%;" />

> servletB从请求中读取Cookie

``` java
@WebServlet("/servletB")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取请求中的cookie
        Cookie[] cookies = req.getCookies();
        //迭代cookies数组
        if (null != cookies && cookies.length!= 0) {
            for (Cookie cookie : cookies) {
               //没有cookie cookie是null不是0 System.out.println(cookie.getName()+":"+cookie.getValue());
            }
        }
    }
}
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682411757748.png" alt="1682411757748" style="zoom:67%;" />

#### 1.2.2  Cookie的时效性

> 默认情况下Cookie的有效期是一次会话范围内，我们可以通过cookie的setMaxAge()方法让Cookie持久化保存到浏览器上

-   会话级Cookie
    -   服务器端并没有明确指定Cookie的存在时间
    -   在浏览器端，Cookie数据存在于内存中
    -   只要浏览器还开着，Cookie数据就一直都在
    -   浏览器关闭，内存中的Cookie数据就会被释放
-   持久化Cookie
    -   服务器端明确设置了Cookie的存在时间
    -   在浏览器端，Cookie数据会被保存到硬盘上
    -   Cookie在硬盘上存在的时间根据服务器端限定的时间来管控，不受浏览器关闭的影响
    -   持久化Cookie到达了预设的时间会被释放

> cookie.setMaxAge(int expiry)参数单位是秒，表示cookie的持久化时间，如果设置参数为0，表示将浏览器中保存的该cookie删除

+ servletA设置一个Cookie为持久化cookie

``` java
@WebServlet("/servletA")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 创建Cookie
        Cookie cookie1 =new Cookie("c1","c1_message");
        cookie1.setMaxAge(60);
        Cookie cookie2 =new Cookie("c2","c2_message");
        // 将cookie放入响应对象
        resp.addCookie(cookie1);
        resp.addCookie(cookie2);
    }
}
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682470547651.png" alt="1682470547651" style="zoom: 67%;" />

+ servletB接收Cookie,浏览器中间发生一次重启再请求servletB测试

``` java
@WebServlet("/servletB")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //获取请求中的cookie
        Cookie[] cookies = req.getCookies();
        //迭代cookies数组
        if (null != cookies && cookies.length!= 0) {
            for (Cookie cookie : cookies) {
                System.out.println(cookie.getName()+":"+cookie.getValue());
            }
        }
    }
}
```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682470652577.png" alt="1682470652577" style="zoom: 70%;" />

#### 1.2.3 Cookie的提交路径

> **访问互联网资源时不能每次都需要把所有Cookie带上。访问不同的资源时,可以携带不同的cookie,我们可以通过cookie的setPath(String path) 对cookie的路径进行设置**

+ 从ServletA中获取cookie

``` java
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 创建Cookie
        Cookie cookie1 =new Cookie("c1","c1_message");
        // 设置cookie的提交路径
        cookie1.setPath("/web03_war_exploded/servletB");
        Cookie cookie2 =new Cookie("c2","c2_message");
        // 将cookie放入响应对象
        resp.addCookie(cookie1);
        resp.addCookie(cookie2);
    }
}

```

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682471183183.png" alt="1682471183183" style="zoom:80%;" />

+ 向ServletB请求时携带携带了 c1

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682471232199.png" alt="1682471232199" style="zoom:95%;" />

+ 向其他资源请求时就不携带c1了

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682471342313.png" alt="1682471342313" style="zoom:80%;" />

###  Session

>  HttpSession是一种保留更多信息在服务端的一种技术,**服务器会为每一个客户端开辟一块内存空间,即session对象. 客户端在发送请求时,都可以使用自己的session.** 这样服务端就可以通过session来记录某个客户端的状态了

+ 服务端在为客户端创建session时**,会同时将session对象的id,即JSESSIONID以cookie的形式放入响应对象**
+ 后端创建完session后,客户端会收到一个特殊的cookie,叫做JSESSIONID
+ 客户端下一次请求时携带JSESSIONID,后端收到后,根据JSESSIONID找到对应的session对象
+ 通过该机制,服务端通过session就可以存储一些专门针对某个客户端的信息了
+ session也是域对象(后续详细讲解)

> 原理图如

![1682413051408](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682413051408.png)

#### HttpSession的使用

> 用户提交form表单到ServletA,携带用户名,ServletA获取session 将用户名存到Session,用户再请求其他任意Servlet,获取之前存储的用户

+ 定义表单页,提交用户名,提交后

``` html
    <form action="servletA" method="post">
        用户名:
        <input type="text" name="username">
        <input type="submit" value="提交">
    </form>
```

+ 定义ServletA,将用户名存入session

``` java
@WebServlet("/servletA")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求中的参数
        String username = req.getParameter("username");
        // 获取session对象
        HttpSession session = req.getSession();
         // 获取Session的ID
        String jSessionId = session.getId();
        System.out.println(jSessionId);
        // 判断session是不是新创建的session
        boolean isNew = session.isNew();
        System.out.println(isNew);
        // 向session对象中存入数据
        session.setAttribute("username",username);

    }
}
```

+ 响应中收到了一个JSESSIONID的cookie

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682476311432.png" alt="1682476311432" style="zoom:80%;" />

+ 定义其他Servlet,从session中读取用户名

``` java
@WebServlet("/servletB")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取session对象
        HttpSession session = req.getSession();
         // 获取Session的ID
        String jSessionId = session.getId();
        System.out.println(jSessionId);
        // 判断session是不是新创建的session
        boolean isNew = session.isNew();
        System.out.println(isNew);
        // 从session中取出数据
        String username = (String)session.getAttribute("username");
        System.out.println(username);
    }
}
```

+ 请求中携带了一个JSESSIONID的cookie

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682476350602.png" alt="1682476350602" style="zoom:80%;" />

> getSession方法的处理逻辑

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682477914654.png" alt="1682477914654" style="zoom: 80%;" />

#### HttpSession时效性

> 为什么要设置session的时效

+ 用户量很大之后，Session对象相应的也要创建很多。如果一味创建不释放，那么服务器端的内存迟早要被耗尽。
+ 客户端关闭行为无法被服务端直接侦测,或者客户端较长时间不操作也经常出现,类似这些的情况,就需要对session的时限进行设置了

> 默认的session最大闲置时间(两次使用同一个session中的间隔时间) 在tomcat/conf/web.xml配置为30分钟

![1682478412527](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682478412527.png)

> 我们可以自己在当前项目的web.xml对最大闲置时间进行重新设定

![1682478633650](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682478633650.png)

> 也可以通过HttpSession的API 对最大闲置时间进行设定

``` java
// 设置最大闲置时间
session.setMaxInactiveInterval(60);
```

> 也可以直接让session失效

``` java
// 直接让session失效
session.invalidate();
```

### 三大域对象

#### 1.4.1 域对象概述

> 域对象: 一些用于存储数据和传递数据的对象,传递数据不同的范围,我们称之为不同的域,不同的域对象代表不同的域,共享数据的范围也不同

+ web项目中,我们一定要熟练使用的域对象分别是 请求域,会话域,应用域
+ 请求域对象是HttpServletRequest ,传递数据的范围是一次请求之内及请求转发
+ 会话域对象是HttpSession,传递数据的范围是一次会话之内,可以跨多个请求
+ 应用域对象是ServletContext,传递数据的范围是本应用之内,可以跨多个会话

> 生活举例: 热水器摆放位置不同,使用的范围就不同

1. 摆在张三工位下,就只有张三一个人能用
2. 摆在办公室的公共区,办公室内的所有人都可以用
3. 摆在楼层的走廊区,该楼层的所有人都可以用

> 三大域对象的数据作用范围图解

+ 请求域

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682480592506.png" alt="1682480592506" style="zoom: 60%;" />

+ 会话域

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682480716152.png" alt="1682480716152" style="zoom:60%;" />

+ 应用域

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682480913847.png" alt="1682480913847" style="zoom:60%;" />

+ 所有域在一起

<img src="D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682488186891.png" alt="1682488186891" style="zoom:60%;" />

#### 1.4.2 域对象的使用

> 域对象的API

| API                                         | 功能                    |
| ------------------------------------------- | ----------------------- |
| void setAttribute(String name,String value) | 向域对象中添加/修改数据 |
| Object getAttribute(String name);           | 从域对象中获取数据      |
| removeAttribute(String name);               | 移除域对象中的数据      |

> API测试

+ ServletA向三大域中放入数据

``` java
@WebServlet("/servletA")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 向请求域中放入数据
        req.setAttribute("request","request-message");
        //req.getRequestDispatcher("servletB").forward(req,resp);
        // 向会话域中放入数据
        HttpSession session = req.getSession();
        session.setAttribute("session","session-message");
        // 向应用域中放入数据
        ServletContext application = getServletContext();
        application.setAttribute("application","application-message");

    }
}

```

+ ServletB从三大于中取出数据

``` java
@WebServlet("/servletB")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 从请求域中获取数据
        String reqMessage =(String)req.getAttribute("request");
        System.out.println(reqMessage);
        
        // 从会话域中获取数据
        HttpSession session = req.getSession();
        String sessionMessage =(String)session.getAttribute("session");
        System.out.println(sessionMessage);
        // 从应用域中获取数据
        ServletContext application = getServletContext();
        String applicationMessage =(String)application.getAttribute("application");
        System.out.println(applicationMessage);
    }
}
```

+ 请求转发时,请求域可以传递数据`请求域内一般放本次请求业务有关的数据,如:查询到的所有的部门信息`
+ 同一个会话内,不用请求转发,会话域可以传递数据`会话域内一般放本次会话的客户端有关的数据,如:当前客户端登录的用户` 
+ 同一个APP内,不同的客户端,应用域可以传递数据`应用域内一般放本程序应用有关的数据 如:Spring框架的IOC容器`

### 过滤器

#### 2.1 过滤器概述

> Filter,即过滤器,是JAVAEE技术规范之一,作用目标资源的请求进行过滤的一套技术规范,是Java Web项目中`最为实用的技术之一`

+ Filter接口定义了过滤器的开发规范,所有的过滤器都要实现该接口
+ Filter的工作位置是项目中所有目标资源之前,容器在创建HttpServletRequest和HttpServletResponse对象后,会先调用Filter的doFilter方法
+ Filter的doFilter方法可以控制请求是否继续,如果放行,则请求继续,如果拒绝,则请求到此为止,由过滤器本身做出响应
+ Filter不仅可以对请求做出过滤,也可以在目标资源做出响应前,对响应再次进行处理
+ Filter是GOF中责任链模式的典型案例
+ Filter的常用应用包括但不限于: 登录权限检查,解决网站乱码,过滤敏感字符,日志记录,性能分析... ...

> 过滤器开发中应用的场景

+ 日志的记录
+ 性能的分析
+ 乱码的处理
+ 事务的控制
+ 登录的控制
+ 跨域的处理
+ ... ...

> 过滤器工作位置图解

![1682494494396](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682494494396.png)

> Filter接口API

+ 源码

``` java
package jakarta.servlet;
import java.io.IOException;

public interface Filter {
    default public void init(FilterConfig filterConfig) throws ServletException {
    }
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException;
    default public void destroy() {
    }
}

```

+ API目标

| API                                                          | 目标                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| default public void init(FilterConfig filterConfig)          | 初始化方法,由容器调用并传入初始配置信息filterConfig对象      |
| public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) | 过滤方法,核心方法,过滤请求,决定是否放行,响应之前的其他处理等都在该方法中 |
| default public void destroy()                                | 销毁方法,容器在回收过滤器对象之前调用的方法                  |

#### 2.2 过滤器使用

> 目标:开发一个日志记录过滤器

+ 用户请求到达目标资源之前,记录用户的请求资源路径
+ 响应之前记录本次请求目标资源运算的耗时
+ 可以选择将日志记录进入文件,为了方便测试,这里将日志直接在控制台打印

>  定义一个过滤器类,编写功能代码

``` java
package com.atguigu.filters;


import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;
public class LoggingFilter  implements Filter {

    private SimpleDateFormat dateFormat =new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        // 参数父转子
        HttpServletRequest request =(HttpServletRequest)  servletRequest;
        HttpServletResponse  response =(HttpServletResponse)  servletResponse;
        // 拼接日志文本
        String requestURI = request.getRequestURI();
        String time = dateFormat.format(new Date());
        String beforeLogging =requestURI+"在"+time+"被请求了";
        // 打印日志
        System.out.println(beforeLogging);
        // 获取系统时间
        long t1 = System.currentTimeMillis();
        // 放行请求
        filterChain.doFilter(request,response);
        // 获取系统时间
        long t2 = System.currentTimeMillis();
        //  拼接日志文本
        String afterLogging =requestURI+"在"+time+"的请求耗时:"+(t2-t1)+"毫秒";
        // 打印日志
        System.out.println(afterLogging);

    }
}

```

+ 说明
  + doFilter方法中的请求和响应对象是以父接口的形式声明的,实际传入的实参就是HttpServletRequest和HttpServletResponse子接口级别的,可以安全强转
  + filterChain.doFilter(request,response); 这行代码的功能是放行请求,如果没有这一行代码,则请求到此为止
  + filterChain.doFilter(request,response);在放行时需要传入request和response,意味着请求和响应对象要继续传递给后续的资源,这里没有产生新的request和response对象

> 定义两个Servlet作为目标资源

+ ServletA

``` java 
@WebServlet(urlPatterns = "/servletA",name = "servletAName")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 处理器请求
        System.out.println("servletA处理请求的方法,耗时10毫秒");
        // 模拟处理请求耗时
        try {
            Thread.sleep(10);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }

    }
}
```

+ ServletB

``` java 
@WebServlet(urlPatterns = "/servletB", name = "servletBName")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 处理器请求
        System.out.println("servletB处理请求的方法,耗时15毫秒");
        // 模拟处理请求耗时
        try {
            Thread.sleep(15);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }
}

```

> 配置过滤器以及过滤器的过滤范围

+ web.xml

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <!--配置filter,并为filter起别名-->
   <filter>
       <filter-name>loggingFilter</filter-name>
       <filter-class>com.atguigu.filters.LoggingFilter</filter-class>
   </filter>
    <!--为别名对应的filter配置要过滤的目标资源-->
    <filter-mapping>
        <filter-name>loggingFilter</filter-name>
        <!--通过映射路径确定过滤资源-->
        <url-pattern>/servletA</url-pattern>
        <!--通过后缀名确定过滤资源-->
        <url-pattern>*.html</url-pattern>
        <!--通过servlet别名确定过滤资源-->
        <servlet-name>servletBName</servlet-name>

    </filter-mapping>
</web-app>
```

+ 说明

  + filter-mapping标签中定义了过滤器对那些资源进行过滤
  + 子标签url-pattern通过映射路径确定过滤范围
    + /servletA  精确匹配,表示对servletA资源的请求进行过滤
    + *.html 表示对以.action结尾的路径进行过滤
    + /* 表示对所有资源进行过滤
    + 一个filter-mapping下可以配置多个url-pattern
  + 子标签servlet-name通过servlet别名确定对那些servlet进行过滤
    + 使用该标签确定目标资源的前提是servlet已经起了别名
    + 一个filter-mapping下可以定义多个servlet-name
    + 一个filter-mapping下,servlet-name和url-pattern子标签可以同时存在

  

> 过滤过程图解

![1682496991032](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682496991032.png)

#### 2.3 过滤器生命周期

> 过滤器作为web项目的组件之一,和Servlet的生命周期类似,略有不同,没有servlet的load-on-startup的配置,默认就是系统启动立刻构造

| 阶段       | 对应方法                                                     | 执行时机      | 执行次数 |
| ---------- | ------------------------------------------------------------ | ------------- | -------- |
| 创建对象   | 构造器                                                       | web应用启动时 | 1        |
| 初始化方法 | void init(FilterConfig filterConfig)                         | 构造完毕      | 1        |
| 过滤请求   | void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) | 每次请求      | 多次     |
| 销毁       | default void destroy()                                       | web应用关闭时 | 1次      |

> 测试代码

``` java
package com.atguigu.filters;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebServlet;

import java.io.IOException;


@WebServlet("/*")
public class LifeCycleFilter implements Filter {
    public LifeCycleFilter(){
        System.out.println("LifeCycleFilter constructor method invoked");
    }
    
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("LifeCycleFilter init method invoked");
        
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("LifeCycleFilter doFilter method invoked");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {
        System.out.println("LifeCycleFilter destory method invoked");
    }
}

```

#### 2.4 过滤器链的使用

> 一个web项目中,可以同时定义多个过滤器,多个过滤器对同一个资源进行过滤时,工作位置有先后,整体形成一个工作链,称之为过滤器链

+ 过滤器链中的过滤器的顺序由filter-mapping顺序决定
+ 每个过滤器过滤的范围不同,针对同一个资源来说,过滤器链中的过滤器个数可能是不同的
+ 如果某个Filter是使用ServletName进行匹配规则的配置，那么这个Filter执行的优先级要更低

> 图解过滤器链

![1682556566084](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682556566084.png)

> 过滤器链功能测试

+ 定义三个过滤器,对目标资源Servlet的请求进行过滤

+ 目标Servlet资源代码

``` java
package com.atguigu.servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

@WebServlet("/servletC")
public class ServletC extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("servletC service method  invoked");
    }
}

```

+ 三个过滤器代码

``` java
public class Filter1  implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("filter1 before chain.doFilter code invoked");

        filterChain.doFilter(servletRequest,servletResponse);

        System.out.println("filter1 after  chain.doFilter code invoked");

    }
}


public class Filter2 implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("filter2 before chain.doFilter code invoked");

        filterChain.doFilter(servletRequest,servletResponse);

        System.out.println("filter2 after  chain.doFilter code invoked");

    }
}


public class Filter3 implements Filter {
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("filter3 before chain.doFilter code invoked");

        filterChain.doFilter(servletRequest,servletResponse);

        System.out.println("filter3 after  chain.doFilter code invoked");

    }
}
```

+ 过滤器配置代码

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">
    <filter>
        <filter-name>filter1</filter-name>
        <filter-class>com.atguigu.filters.Filter1</filter-class>
    </filter>

    <filter>
        <filter-name>filter2</filter-name>
        <filter-class>com.atguigu.filters.Filter2</filter-class>
    </filter>

    <filter>
        <filter-name>filter3</filter-name>
        <filter-class>com.atguigu.filters.Filter3</filter-class>
    </filter>

    <!--filter-mapping的顺序决定了过滤器的工作顺序-->
    <filter-mapping>
        <filter-name>filter1</filter-name>
        <url-pattern>/servletC</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>filter2</filter-name>
        <url-pattern>/servletC</url-pattern>
    </filter-mapping>

    <filter-mapping>
        <filter-name>filter3</filter-name>
        <url-pattern>/servletC</url-pattern>
    </filter-mapping>

</web-app>
```

> 工作流程图解

![1682497251883](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682497251883.png)

#### 2.5 注解方式配置过滤器

> @WebFilter注解的使用

+ 源码

``` java
package jakarta.servlet.annotation;

import jakarta.servlet.DispatcherType;
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface WebFilter {
    String description() default "";

    String displayName() default "";

    WebInitParam[] initParams() default {};

    String filterName() default "";

    String smallIcon() default "";

    String largeIcon() default "";

    String[] servletNames() default {};

    String[] value() default {};

    String[] urlPatterns() default {};

    DispatcherType[] dispatcherTypes() default {DispatcherType.REQUEST};

    boolean asyncSupported() default false;
}

```

+ 一个比较完整的Filter的XML配置

``` xml
<!--配置filter,并为filter起别名-->
<filter>
    <filter-name>loggingFilter</filter-name>
    <filter-class>com.atguigu.filters.LoggingFilter</filter-class>
    <!--配置filter的初始参数-->
    <init-param>
        <param-name>dateTimePattern</param-name>
        <param-value>yyyy-MM-dd HH:mm:ss</param-value>
    </init-param>
</filter>
<!--为别名对应的filter配置要过滤的目标资源-->
<filter-mapping>
    <filter-name>loggingFilter</filter-name>
    <!--通过映射路径确定过滤资源-->
    <url-pattern>/servletA</url-pattern>
    <!--通过后缀名确定过滤资源-->
    <url-pattern>*.html</url-pattern>
    <!--通过servlet别名确定过滤资源-->
    <servlet-name>servletBName</servlet-name>
</filter-mapping>
```

+ 将xml配置转换成注解方式实现

``` java
package com.atguigu.filters;


import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;
import jakarta.servlet.annotation.WebInitParam;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;



@WebFilter(
        filterName = "loggingFilter",
        initParams = {@WebInitParam(name="dateTimePattern",value="yyyy-MM-dd HH:mm:ss")},
        urlPatterns = {"/servletA","*.html"},
        servletNames = {"servletBName"}
)
public class LoggingFilter  implements Filter {
    private SimpleDateFormat dateFormat ;

    /*init初始化方法,通过filterConfig获取初始化参数
    * init方法中,可以用于定义一些其他初始化功能代码
    * */
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // 获取初始参数
        String dateTimePattern = filterConfig.getInitParameter("dateTimePattern");
        // 初始化成员变量
        dateFormat=new SimpleDateFormat(dateTimePattern);
    }
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        // 参数父转子
        HttpServletRequest request =(HttpServletRequest)  servletRequest;
        HttpServletResponse  response =(HttpServletResponse)  servletResponse;
        // 拼接日志文本
        String requestURI = request.getRequestURI();
        String time = dateFormat.format(new Date());
        String beforeLogging =requestURI+"在"+time+"被请求了";
        // 打印日志
        System.out.println(beforeLogging);
        // 获取系统时间
        long t1 = System.currentTimeMillis();
        // 放行请求
        filterChain.doFilter(request,response);
        // 获取系统时间
        long t2 = System.currentTimeMillis();
        String afterLogging =requestURI+"在"+time+"的请求耗时:"+(t2-t1)+"毫秒";
        // 打印日志
        System.out.println(afterLogging);

    }
}
```

### 监听器

#### 3.1 监听器概述

> 监听器：专门用于对域对象对象身上发生的事件或状态改变进行监听和相应处理的对象

+ 监听器是GOF设计模式中,观察者模式的典型案例
+ 观察者模式: 当被观察的对象发生某些改变时, 观察者自动采取对应的行动的一种设计模式

+ 监听器使用的感受类似JS中的事件,被观察的对象发生某些情况时,自动触发代码的执行
+ 监听器并不监听web项目中的所有组件,仅仅是对三大域对象做相关的事件监听

> 监听器的分类

+ web中定义八个监听器接口作为监听器的规范,这八个接口按照不同的标准可以形成不同的分类

+ 按监听的对象划分
  + 应用域application域监听器 ServletContextListener  ServletContextAttributeListener 
  + 会话域session域监听器 HttpSessionListener  HttpSessionAttributeListener  HttpSessionBindingListener  HttpSessionActivationListener  
  + 请求域request域监听器 ServletRequestListener  ServletRequestAttributeListener 
+ 按监听的事件分
  + 域对象的创建和销毁监听器 ServletContextListener   HttpSessionListener   ServletRequestListener  
  + 域对象数据增删改事件监听器 ServletContextAttributeListener  HttpSessionAttributeListener   ServletRequestAttributeListener 
  + 其他监听器  HttpSessionBindingListener  HttpSessionActivationListener  

#### 3.2 监听器的六个主要接口

##### 3.2.1 application域监听器

> ServletContextListener  监听ServletContext对象的创建与销毁

| 方法名                                      | 作用                     |
| ------------------------------------------- | ------------------------ |
| contextInitialized(ServletContextEvent sce) | ServletContext创建时调用 |
| contextDestroyed(ServletContextEvent sce)   | ServletContext销毁时调用 |

+ ServletContextEvent对象代表从ServletContext对象身上捕获到的事件，通过这个事件对象我们可以获取到ServletContext对象。

> ServletContextAttributeListener 监听ServletContext中属性的添加、移除和修改

| 方法名                                               | 作用                                 |
| ---------------------------------------------------- | ------------------------------------ |
| attributeAdded(ServletContextAttributeEvent scab)    | 向ServletContext中添加属性时调用     |
| attributeRemoved(ServletContextAttributeEvent scab)  | 从ServletContext中移除属性时调用     |
| attributeReplaced(ServletContextAttributeEvent scab) | 当ServletContext中的属性被修改时调用 |

+ ServletContextAttributeEvent对象代表属性变化事件，它包含的方法如下：

| 方法名              | 作用                     |
| ------------------- | ------------------------ |
| getName()           | 获取修改或添加的属性名   |
| getValue()          | 获取被修改或添加的属性值 |
| getServletContext() | 获取ServletContext对象   |

> 测试代码

+ 定义监听器

``` java
package com.atguigu.listeners;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebListener;


@WebListener
public class ApplicationListener implements ServletContextListener , ServletContextAttributeListener {
    // 监听初始化
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        ServletContext application = sce.getServletContext();
        System.out.println("application"+application.hashCode()+" initialized");
    }
    // 监听销毁
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        ServletContext application = sce.getServletContext();
        System.out.println("application"+application.hashCode()+" destroyed");
    }

    // 监听数据增加
    @Override
    public void attributeAdded(ServletContextAttributeEvent scae) {
        String name = scae.getName();
        Object value = scae.getValue();
        ServletContext application = scae.getServletContext();
        System.out.println("application"+application.hashCode()+" add:"+name+"="+value);
    }

    // 监听数据移除
    @Override
    public void attributeRemoved(ServletContextAttributeEvent scae) {
        String name = scae.getName();
        Object value = scae.getValue();
        ServletContext application = scae.getServletContext();
        System.out.println("application"+application.hashCode()+" remove:"+name+"="+value);
    }
    // 监听数据修改
    @Override
    public void attributeReplaced(ServletContextAttributeEvent scae) {
        String name = scae.getName();
        Object value = scae.getValue();
        ServletContext application = scae.getServletContext();
        Object newValue = application.getAttribute(name);
        System.out.println("application"+application.hashCode()+" change:"+name+"="+value+" to "+newValue);
    }
}

```

+ 定义触发监听器的代码

``` java
//实际上，在 Servlet 中并不需要显式地创建 ApplicationListener 类的实例。通过使用 @WebListener 注解，容器会自动发现并初始化该监听器。在示例中，@WebListener 注解已经标注在 ApplicationListener 类上，因此当应用启动时，Servlet 容器会自动创建 ApplicationListener 的实例，并注册它作为应用程序的监听器。




// ServletA用于向application域中放入数据
@WebServlet(urlPatterns = "/servletA",name = "servletAName")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 向application域中放入数据
        ServletContext application = this.getServletContext();
        application.setAttribute("k1","v1");
        application.setAttribute("k2","v2");
    }
}


// ServletB用于向application域中修改和移除数据
@WebServlet(urlPatterns = "/servletB", name = "servletBName")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext appliation  = getServletContext();
        //  修改application域中的数据
        appliation.setAttribute("k1","value1");
        //  删除application域中的数据
        appliation.removeAttribute("k2");
    }
}
```

##### 3.2.2 session域监听器

> HttpSessionListener  监听HttpSession对象的创建与销毁

| 方法名                                 | 作用                      |
| -------------------------------------- | ------------------------- |
| sessionCreated(HttpSessionEvent hse)   | HttpSession对象创建时调用 |
| sessionDestroyed(HttpSessionEvent hse) | HttpSession对象销毁时调用 |

+ HttpSessionEvent对象代表从HttpSession对象身上捕获到的事件，通过这个事件对象我们可以获取到触发事件的HttpSession对象。

> HttpSessionAttributeListener 监听HttpSession中属性的添加、移除和修改

| 方法名                                        | 作用                              |
| --------------------------------------------- | --------------------------------- |
| attributeAdded(HttpSessionBindingEvent se)    | 向HttpSession中添加属性时调用     |
| attributeRemoved(HttpSessionBindingEvent se)  | 从HttpSession中移除属性时调用     |
| attributeReplaced(HttpSessionBindingEvent se) | 当HttpSession中的属性被修改时调用 |

+ HttpSessionBindingEvent对象代表属性变化事件，它包含的方法如下：

| 方法名       | 作用                          |
| ------------ | ----------------------------- |
| getName()    | 获取修改或添加的属性名        |
| getValue()   | 获取被修改或添加的属性值      |
| getSession() | 获取触发事件的HttpSession对象 |

> 测试代码

+ 定义监听器

``` java
package com.atguigu.listeners;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebListener;
import jakarta.servlet.http.*;


@WebListener
public class SessionListener implements HttpSessionListener, HttpSessionAttributeListener {
    // 监听session创建
    @Override
    public void sessionCreated(HttpSessionEvent se) {
        HttpSession session = se.getSession();
        System.out.println("session"+session.hashCode()+" created");
    }

    // 监听session销毁
    @Override
    public void sessionDestroyed(HttpSessionEvent se) {
        HttpSession session = se.getSession();
        System.out.println("session"+session.hashCode()+" destroyed");
    }
    // 监听数据增加
    @Override
    public void attributeAdded(HttpSessionBindingEvent se) {
        String name = se.getName();
        Object value = se.getValue();
        HttpSession session = se.getSession();
        System.out.println("session"+session.hashCode()+" add:"+name+"="+value);
    }
    // 监听数据移除
    @Override
    public void attributeRemoved(HttpSessionBindingEvent se) {
        String name = se.getName();
        Object value = se.getValue();
        HttpSession session = se.getSession();
        System.out.println("session"+session.hashCode()+" remove:"+name+"="+value);
    }
    // 监听数据修改
    @Override
    public void attributeReplaced(HttpSessionBindingEvent se) {
        String name = se.getName();
        Object value = se.getValue();
        HttpSession session = se.getSession();
        Object newValue = session.getAttribute(name);
        System.out.println("session"+session.hashCode()+" change:"+name+"="+value+" to "+newValue);
    }

}
```

+ 定义触发监听器的代码

``` java
// servletA用于创建session并向session中放数据
@WebServlet(urlPatterns = "/servletA",name = "servletAName")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 创建session,并向session中放入数据
        HttpSession session = req.getSession();

        session.setAttribute("k1","v1");
        session.setAttribute("k2","v2");
    }
}


// servletB用于修改删除session中的数据并手动让session不可用
@WebServlet(urlPatterns = "/servletB", name = "servletBName")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        //  修改session域中的数据
        session.setAttribute("k1","value1");
        //  删除session域中的数据
        session.removeAttribute("k2");
        // 手动让session不可用
        session.invalidate();
    }
}
```

##### 3.2.3 request域监听器

> ServletRequestListener 监听ServletRequest对象的创建与销毁

| 方法名                                      | 作用                         |
| ------------------------------------------- | ---------------------------- |
| requestInitialized(ServletRequestEvent sre) | ServletRequest对象创建时调用 |
| requestDestroyed(ServletRequestEvent sre)   | ServletRequest对象销毁时调用 |

+ ServletRequestEvent对象代表从HttpServletRequest对象身上捕获到的事件，通过这个事件对象我们可以获取到触发事件的HttpServletRequest对象。另外还有一个方法可以获取到当前Web应用的ServletContext对象。

> ServletRequestAttributeListener 监听ServletRequest中属性的添加、移除和修改

| 方法名                                               | 作用                                 |
| ---------------------------------------------------- | ------------------------------------ |
| attributeAdded(ServletRequestAttributeEvent srae)    | 向ServletRequest中添加属性时调用     |
| attributeRemoved(ServletRequestAttributeEvent srae)  | 从ServletRequest中移除属性时调用     |
| attributeReplaced(ServletRequestAttributeEvent srae) | 当ServletRequest中的属性被修改时调用 |

+ ServletRequestAttributeEvent对象代表属性变化事件，它包含的方法如下：

| 方法名               | 作用                             |
| -------------------- | -------------------------------- |
| getName()            | 获取修改或添加的属性名           |
| getValue()           | 获取被修改或添加的属性值         |
| getServletRequest () | 获取触发事件的ServletRequest对象 |

+ 定义监听器

``` java
package com.atguigu.listeners;

import jakarta.servlet.*;
import jakarta.servlet.annotation.WebListener;


@WebListener
public class RequestListener implements ServletRequestListener , ServletRequestAttributeListener {
    // 监听初始化
    @Override
    public void requestInitialized(ServletRequestEvent sre) {
        ServletRequest request = sre.getServletRequest();
        System.out.println("request"+request.hashCode()+" initialized");
    }

    // 监听销毁
    @Override
    public void requestDestroyed(ServletRequestEvent sre) {
        ServletRequest request = sre.getServletRequest();
        System.out.println("request"+request.hashCode()+" destoryed");
    }


    // 监听数据增加
    @Override
    public void attributeAdded(ServletRequestAttributeEvent srae) {
        String name = srae.getName();
        Object value = srae.getValue();
        ServletRequest request = srae.getServletRequest();
        System.out.println("request"+request.hashCode()+" add:"+name+"="+value);
    }

    //  监听数据移除
    @Override
    public void attributeRemoved(ServletRequestAttributeEvent srae) {
        String name = srae.getName();
        Object value = srae.getValue();
        ServletRequest request = srae.getServletRequest();
        System.out.println("request"+request.hashCode()+" remove:"+name+"="+value);
    }
    // 监听数据修改
    @Override
    public void attributeReplaced(ServletRequestAttributeEvent srae) {
        String name = srae.getName();
        Object value = srae.getValue();
        ServletRequest request = srae.getServletRequest();
        Object newValue = request.getAttribute(name);
        System.out.println("request"+request.hashCode()+" change:"+name+"="+value+" to "+newValue);
    }
}
```

+ 定义触发监听器的代码

``` java
//  servletA向请求域中放数据
@WebServlet(urlPatterns = "/servletA",name = "servletAName")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 向request中增加数据
        req.setAttribute("k1","v1");
        req.setAttribute("k2","v2");
        // 请求转发
        req.getRequestDispatcher("servletB").forward(req,resp);
    }
}

// servletB修改删除域中的数据
@WebServlet(urlPatterns = "/servletB", name = "servletBName")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //  修改request域中的数据
        req.setAttribute("k1","value1");
        //  删除session域中的数据
        req.removeAttribute("k2");

    }
}
```

#### 3.3 session域的两个特殊监听器

##### 3.3.3  session绑定监听器

> HttpSessionBindingListener 监听当前监听器对象在Session域中的增加与移除

| 方法名                                      | 作用                              |
| ------------------------------------------- | --------------------------------- |
| valueBound(HttpSessionBindingEvent event)   | 该类的实例被放到Session域中时调用 |
| valueUnbound(HttpSessionBindingEvent event) | 该类的实例从Session中移除时调用   |

+ HttpSessionBindingEvent对象代表属性变化事件，它包含的方法如下：

| 方法名       | 作用                          |
| ------------ | ----------------------------- |
| getName()    | 获取当前事件涉及的属性名      |
| getValue()   | 获取当前事件涉及的属性值      |
| getSession() | 获取触发事件的HttpSession对象 |

> 测试代码

+ 定义监听器

``` java
package com.atguigu.listeners;

import jakarta.servlet.http.HttpSession;
import jakarta.servlet.http.HttpSessionBindingEvent;
import jakarta.servlet.http.HttpSessionBindingListener;

public class MySessionBindingListener  implements HttpSessionBindingListener {
    //  监听绑定
    @Override
    public void valueBound(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        String name = event.getName();
        System.out.println("MySessionBindingListener"+this.hashCode()+" binding into session"+session.hashCode()+" with name "+name);
    }

    // 监听解除绑定
    @Override
    public void valueUnbound(HttpSessionBindingEvent event) {
        HttpSession session = event.getSession();
        String name = event.getName();
        System.out.println("MySessionBindingListener"+this.hashCode()+" unbond outof session"+session.hashCode()+" with name "+name);
    }
}
```

+ 定义触发监听器的代码

``` java
@WebServlet(urlPatterns = "/servletA",name = "servletAName")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        // 绑定监听器
        session.setAttribute("bindingListener",new MySessionBindingListener());
        // 解除绑定监听器
        session.removeAttribute("bindingListener");
    }
}
```

##### 3.3.4 钝化活化监听器

> HttpSessionActivationListener  监听某个对象在Session中的序列化与反序列化。

| 方法名                                    | 作用                                  |
| ----------------------------------------- | ------------------------------------- |
| sessionWillPassivate(HttpSessionEvent se) | 该类实例和Session一起钝化到硬盘时调用 |
| sessionDidActivate(HttpSessionEvent se)   | 该类实例和Session一起活化到内存时调用 |

+ HttpSessionEvent对象代表事件对象，通过getSession()方法获取事件涉及的HttpSession对象。

> 什么是钝化活化

+ session对象在服务端是以对象的形式存储于内存的,session过多,服务器的内存也是吃不消的
+ 而且一旦服务器发生重启,所有的session对象都将被清除,也就意味着session中存储的不同客户端的登录状态丢失
+ 为了分摊内存 压力并且为了保证session重启不丢失,我们可以设置将session进行钝化处理
+ 在关闭服务器前或者到达了设定时间时,对session进行序列化到磁盘,这种情况叫做session的钝化
+ 在服务器启动后或者再次获取某个session时,将磁盘上的session进行反序列化到内存,这种情况叫做session的活化


> 如何配置钝化活化

+ 在web目录下,添加 META-INF下创建Context.xml

![1682565824241](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1682565824241.png)

+ 文件中配置钝化

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<Context>
    <Manager className="org.apache.catalina.session.PersistentManager" maxIdleSwap="1">
        <Store className="org.apache.catalina.session.FileStore" directory="d:\mysession"></Store>
    </Manager>
</Context>
```

+  请求servletA,获得session,并存入数据,然后重启服务器

``` java
@WebServlet(urlPatterns = "/servletA",name = "servletAName")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        // 添加数据
        session.setAttribute("k1","v1");
    }
}
```

+ 请求servletB获取session,获取重启前存入的数据

``` java
@WebServlet(urlPatterns = "/servletB", name = "servletBName")
public class ServletB extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        Object v1 = session.getAttribute("k1");
        System.out.println(v1);

    }
}
```

> 如何监听钝化活化

+ 定义监听器

``` java
package com.atguigu.listeners;

import jakarta.servlet.http.HttpSession;
import jakarta.servlet.http.HttpSessionActivationListener;
import jakarta.servlet.http.HttpSessionEvent;

import java.io.Serializable;

public class ActivationListener  implements HttpSessionActivationListener, Serializable {
    //  监听钝化
    @Override
    public void sessionWillPassivate(HttpSessionEvent se) {
        HttpSession session = se.getSession();
        System.out.println("session with JSESSIONID "+ session.getId()+" will passivate");
    }

    //  监听活化
    @Override
    public void sessionDidActivate(HttpSessionEvent se) {
        HttpSession session = se.getSession();
        System.out.println("session with JSESSIONID "+ session.getId()+" did activate");
    }
}

```

+ 定义触发监听器的代码

``` java
@WebServlet(urlPatterns = "/servletA",name = "servletAName")
public class ServletA extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        // 添加数据
        session.setAttribute("k1","v1");
        // 添加钝化活化监听器
        session.setAttribute("activationListener",new ActivationListener());
    }
}
```

### ajax

什么是ajax

+ AJAX = Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

+ AJAX 不是新的编程语言，而是一种使用现有标准的新方法。

+ AJAX 最大的优点是**在不重新加载整个页面的情况下，可以与服务器交换数据并更新部分网页内容。**

+ AJAX 不需要任何浏览器插件，但需要用户允许 JavaScript 在浏览器上执行。 

+ XMLHttpRequest 只是实现 Ajax 的一种方式。

**ajax工作原理：**

![](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image_bjXPJoLb6a-1690508517199.png)

+ 简单来说,我们之前发的请求通过类似  form表单标签,a标签 这种方式,现在通过 运行js代码动态决定什么时候发送什么样的请求
+ 通过运行JS代码发送的请求浏览器可以不用跳转页面 ,我们可以在JS代码中决定是否要跳转页面
+ 通过运行JS代码发送的请求,接收到返回结果后,我们可以将结果通过dom编程渲染到页面的某些元素上,实现局部更新

#### 如何实现ajax请求

> 原生**javascript方式进行ajax(了解):**

``` html
<script>
  function loadXMLDoc(){
    var xmlhttp=new XMLHttpRequest();
      // 设置回调函数处理响应结果
    xmlhttp.onreadystatechange=function(){
      if (xmlhttp.readyState==4 && xmlhttp.status==200)
      {
        document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
      }
    }
      // 设置请求方式和请求的资源路径
    xmlhttp.open("GET","/try/ajax/ajax_info.txt",true);
      // 发送请求
    xmlhttp.send();
  }
</script> 
```

## 前端工程化

### es6

ECMAScript 6，简称ES6，是**JavaScript**语言的一次重大更新。它于**2015**年发布，是原来的ECMAScript标准的第六个版本。ES6带来了大量的新特性，包括箭头函数、模板字符串、let和const关键字、解构、默认参数值、模块系统等等，大大提升了JavaScript的开发体验。`由于VUE3中大量使用了ES6的语法,所以ES6成为了学习VUE3的门槛之一`

#### es6的变量和模板字符串

> ES6 新增了`let`和`const`，用来声明变量,使用的细节上也存在诸多差异

+ let 和var的差别

  1、let 不能重复声明

  2、let有块级作用域，非函数的花括号遇见let会有块级作用域，也就是只能在花括号里面访问。

  3、let不会预解析进行变量提升

  4、let 定义的全局变量不会作为window的属性

  5、let在es6中推荐优先使用

```html
<script>
    //1. let只有在当前代码块有效代码块. 代码块、函数、全局
    {
      let a = 1
      var b = 2
    }   
    console.log(a);  // a is not defined   花括号外面无法访问
    console.log(b);  // 可以正常输出

    //2. 不能重复声明
    let name = '天真'
    let name = '无邪'

    //3. 不存在变量提升（先声明，在使用）
    console.log(test) //可以     但是值为undefined
    var test = 'test'
    console.log(test1) //不可以  let命令改变了语法行为，它所声明的变量一定要在声明后使用，否则报错。
    let test1 = 'test1' 


    //4、不会成为window的属性   
    var a = 100
    console.log(window.a) //100
    let b = 200
    console.log(window.b) //undefined

    //5. 循环中推荐使用
    for (let i = 0; i < 10; i++) {
      // ...
    }
    console.log(i);
</script>
```

+ const和var的差异

  1、新增const和let类似，只是const定义的变量不能修改

  2、**并不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。**

```html
<script>
    //声明场景语法,建议变量名大写区分
    const PI = 3.1415926;

    //1.常量声明必须有初始化值
    //const A ; //报错

    //2.常量值不可以改动
    //const A  = 'atguigu'
    //A  = 'xx' //不可改动

    //3.和let一样，块儿作用域
    {
        const A = 'atguigu'
        console.log(A);
    }
    //console.log(A);

    //4.对应数组和对象元素修改，不算常量修改，修改值，不修改地址
    const TEAM = ['刘德华','张学友','郭富城'];
    TEAM.push('黎明');
    TEAM=[] // 报错
    console.log(TEAM)
</script>
```

> 模板字符串（template string）是增强版的字符串，用反引号（`）标识  

1、字符串中可以出现换行符

2、可以使用 ${xxx} 形式输出变量和拼接变量

``` html
<script>
    // 1 多行普通字符串
    let ulStr =
        '<ul>'+
        '<li>JAVA</li>'+
        '<li>html</li>'+
        '<li>VUE</li>'+
        '</ul>'
    console.log(ulStr)    
    // 2 多行模板字符串
    let ulStr2 = `
        <ul>
        	<li>JAVA</li>
        	<li>html</li>
        	<li>VUE</li>
        </ul>`
    console.log(ulStr2)        
    // 3  普通字符串拼接
    let name ='张小明'
    let infoStr =name+'被评为本年级优秀学员'  
    console.log(infoStr)
    // 4  模板字符串拼接
    let infoStr2 =`${name}被评为本年级优秀学员`
    console.log(infoStr2)
</script>
```

#### es6的解构表达式

> **数组解构赋值**

+ 可以通过数组解构将数组中的值赋值给变量，语法为：

```javascript
let [a, b, c] = [1, 2, 3]; //新增变量名任意合法即可，本质是按照顺序进行初始化变量的值
console.log(a); // 1
console.log(b); // 2
console.log(c); // 3
```

+ 该语句将数组 \[1, 2, 3] 中的第一个值赋值给 a 变量，第二个值赋值给 b 变量，第三个值赋值给 c 变量。
  可以使用默认值为变量提供备选值，在数组中缺失对应位置的值时使用该默认值。例如：

```javascript
let [a, b, c, d = 4] = [1, 2, 3];
console.log(d); // 4
```

> **对象解构赋值**

+ 可以通过对象解构将对象中的值赋值给变量，语法为：

```javascript
let {a, b} = {a: 1, b: 2};
//新增变量名必须和属性名相同，本质是初始化变量的值为对象中同名属性的值
//等价于 let a = 对象.a  let b = 对象.b
  
console.log(a); // 1
console.log(b); // 2
```

+ 该语句将对象 {a: 1, b: 2} 中的 a 属性值赋值给 a 变量，b 属性值赋值给 b 变量。
  可以为标识符分配不同的变量名称，使用 : 操作符指定新的变量名。例如：

```javascript
let {a: x, b: y} = {a: 1, b: 2};
console.log(x); // 1
console.log(y); // 2
```

> **函数参数解构赋值**

+ 解构赋值也可以用于函数参数。例如：

```javascript
function add([x, y]) {
  return x + y;
}
add([1, 2]); // 3
```

+ 该函数接受一个数组作为参数，将其中的第一个值赋给 x，第二个值赋给 y，然后返回它们的和。

+ ES6 解构赋值让变量的初始化更加简单和便捷。通过解构赋值，我们可以访问到对象中的属性，并将其赋值给对应的变量，从而提高代码的可读性和可维护性。

#### es6的箭头函数

> ES6 允许使用“箭头” 义函数。语法类似Java中的Lambda表达式

##### 声明和特点

```html
<script>

    //ES6 允许使用“箭头”（=>）定义函数。
    //1. 函数声明
    let fn1 = function(){}
    let fn2 = ()=>{} //箭头函数,此处不需要书写function关键字
    let fn3 = x =>{} //单参数可以省略(),多参数无参数不可以!
    let fn4 = x => console.log(x) //只有一行方法体可以省略{};
    let fun5 = x => x + 1 //当函数体只有一句返回值时，可以省略花括号和 return 语句
    //2. 使用特点 箭头函数this关键字
    // 在 JavaScript 中，this 关键字通常用来引用函数所在的对象，
    // 或者在函数本身作为构造函数时，来引用新对象的实例。
    // 但是在箭头函数中，this 的含义与常规函数定义中的含义不同，
    // 并且是由箭头函数定义时的上下文来决定的，而不是由函数调用时的上下文来决定的。
    // ！！箭头函数没有自己的this，this指向的是外层上下文环境的this
    
    let person ={
        name:"张三",
        showName:function (){
            console.log(this) //  这里的this是person
            console.log(this.name)
        },
        viewName: () =>{
            console.log(this) //  这里的this是window
            console.log(this.name)
        }
    }
    person.showName()
    person.viewName()
 
    //this应用
    function Counter() {
        this.count = 0;
        setInterval(() => {
            // 这里的 this 是上一层作用域中的 this，即 Counter实例化对象
            this.count++;
            console.log(this.count);
        }, 1000);
    }
    let counter = new Counter();

</script>
```

##### 实践和应用场景

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        #xdd{
            display: inline-block;
            width: 200px;
            height: 200px;
            background-color: red;
        }
    </style>
</head>
<body>
    <div id="xdd"></div>
    <script>
       let xdd = document.getElementById("xdd");
       // 方案1 
       xdd.onclick = function(){
            console.log(this)
            let _this= this;  //this 是xdd
            //开启定时器
            setTimeout(function(){
                console.log(this)
                //变粉色
                _this.style.backgroundColor = 'pink';
            },2000);
        }
        // 方案2
        xdd.onclick = function(){
            console.log(this)
            //开启定时器
            setTimeout(()=>{
                console.log(this)// 使用setTimeout() 方法所在环境时的this对象
                //变粉色
                this.style.backgroundColor = 'pink';
            },2000);
        }
    </script>
</body>
</html>
```

##### rest和spread

> rest参数,在形参上使用 和JAVA中的可变参数几乎一样

``` html
<script>
    // 1 参数列表中多个普通参数  普通函数和箭头函数中都支持
    let fun1 = function (a,b,c,d=10){console.log(a,b,c,d)}
    let fun2 = (a,b,c,d=10) =>{console.log(a,b,c,d)}
    fun1(1,2,3)
    fun2(1,2,3,4)
    // 2 ...作为参数列表,称之为rest参数 普通函数和箭头函数中都支持 ,因为箭头函数中无法使用arguments,rest是一种解决方案
    let fun3 = function (...args){console.log(args)}
    let fun4 = (...args) =>{console.log(args)}
    fun3(1,2,3)
    fun4(1,2,3,4)
    // rest参数在一个参数列表中的最后一个只,这也就无形之中要求一个参数列表中只能有一个rest参数
    //let fun5 =  (...args,...args2) =>{} // 这里报错
</script>
```

> spread参数,在实参上使用rest

```html
<script>
    let arr =[1,2,3]
    //let arrSpread = ...arr;// 这样不可以,...arr必须在调用方法时作为实参使用
    let fun1 =(a,b,c) =>{
        console.log(a,b,c)
    }
    // 调用方法时,对arr进行转换 转换为1,2,3 
    fun1(...arr)
    //应用场景1 合并数组
    let arr2=[4,5,6]
    let arr3=[...arr,...arr2]
    console.log(arr3)
    //应用场景2 合并对象属性
    let p1={name:"张三"}
    let p2={age:10}
    let p3={gender:"boy"}
    let person ={...p1,...p2,...p3}
    console.log(person)

</script>
```

#### es6的对象创建和拷贝

##### 对象创建的语法糖

> ES6中新增了对象创建的语法糖,支持了class extends constructor等关键字,让ES6的语法和面向对象的语法更加接近

``` javascript
class Person{
      // 属性
      #n;
      age;
      get name(){
          return this.n;
      }
      set name(n){
          this.n =n;
      }
      // 实例方法
      eat(food){
          console.log(this.age+"岁的"+this.n+"用筷子吃"+food)
      }
      // 静态方法
      static sum(a,b){
          return a+b;
      }
      // 构造器
      constructor(name,age){
          this.n=name;
          this.age = age;

      }
  }
  let person =new Person("张三",10);
  // 访问对象属性
  // 调用对象方法
  console.log(person.name)
  console.log(person.n)
  person.name="小明"
  console.log(person.age)
  person.eat("火锅")
  console.log(Person.sum(1,2))

  class Student extends  Person{
      grade ;
      score ;
      study(){

      }
      constructor(name,age ) {
          super(name,age);
      }
  }

  let stu =new Student("学生小李",18);
  stu.eat("面条")
```

#### 对象的深拷贝和浅拷贝

> 对象的拷贝,快速获得一个和已有对象相同的对象的方式

+ 浅拷贝

``` html
<script>
    let arr  =['java','c','python']
    let person ={
        name:'张三',
        language:arr
    }
    // 浅拷贝,person2和person指向相同的内存
    let person2 = person;
    person2.name="小黑"
    console.log(person.name)
</script>
```

+ 深拷贝

``` html
<script>
    let arr  =['java','c','python']
    let person ={
        name:'张三',
        language:arr
    }
    // 深拷贝,通过JSON和字符串的转换形成一个新的对象
    let person2 = JSON.parse(JSON.stringify(person))
    person2.name="小黑"
    console.log(person.name)
    console.log(person2.name) 
</script>
```

#### es6的模块化处理

##### 模块化介绍

> 模块化是一种组织和管理前端代码的方式，将代码拆分成小的模块单元，使得代码更易于维护、扩展和复用。它包括了定义、导出、导入以及管理模块的方法和规范。前端模块化的主要优势如下：

1.  提高代码可维护性：通过将代码拆分为小的模块单元，使得代码结构更为清晰，可读性更高，便于开发者阅读和维护。
2.  提高代码可复用性：通过将重复使用的代码变成可复用的模块，减少代码重复率，降低开发成本。
3.  提高代码可扩展性：通过模块化来实现代码的松耦合，便于更改和替换模块，从而方便地扩展功能。

> 目前，前端模块化有多种规范和实现，包括 CommonJS、AMD 和 ES6 模块化。ES6 模块化是 JavaScript 语言的模块标准，使用 import 和 export 关键字来实现模块的导入和导出。现在，大部分浏览器都已经原生支持 ES6 模块化，因此它成为了最为广泛使用的前端模块化标准. 

+ ES6模块化的几种暴露和导入方式
  1. 分别导出
  2. 统一导出
  3. 默认导出
+ `ES6中无论以何种方式导出,导出的都是一个对象,导出的内容都可以理解为是向这个对象中添加属性或者方法`

##### 分别导出

![1684461046181](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1684461046181.png)

+ module.js 向外分别暴露成员

``` javascript
//1.分别暴露
// 模块想对外导出,添加export关键字即可!
// 导出一个变量
export const PI = 3.14
// 导出一个函数
export function sum(a, b) {
  return a + b;
}
// 导出一个类
export class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  sayHello() {
    console.log(`Hello, my name is ${this.name}, I'm ${this.age} years old.`);
  }
}
```

+ app.js 导入module.js中的成员

``` javascript
/* 
    *代表module.js中的所有成员
    m1代表所有成员所属的对象
*/
import * as m1 from './module.js'
// 使用暴露的属性
console.log(m1.PI)
// 调用暴露的方法
let result =m1.sum(10,20)
console.log(result)
// 使用暴露的Person类
let person =new m1.Person('张三',10)
person.sayHello()
```

+ index.html作为程序启动的入口  导入 app.js  

``` html
<!-- 导入JS文件 添加type='module' 属性,否则不支持ES6的模块化 -->
<script src="./app.js" type="module" /> 
```

##### 统一导出

![1684461701620](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1684461701620.png)

+ module.js向外统一导出成员

``` javascript
//2.统一暴露
// 模块想对外导出,export统一暴露想暴露的内容!
// 定义一个常量
const PI = 3.14
// 定义一个函数
function sum(a, b) {
  return a + b;
}
// 定义一个类
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  sayHello() {
    console.log(`Hello, my name is ${this.name}, I'm ${this.age} years old.`);
  }
}
// 统一对外导出(暴露)
export {
	PI,
    sum,
    Person
}
```

+ app.js导入module.js中的成员

``` javascript
/* 
    {}中导入要使用的来自于module.js中的成员
    {}中导入的名称要和module.js中导出的一致,也可以在此处起别名
    {}中如果定义了别名,那么在当前模块中就只能使用别名
    {}中导入成员的顺序可以不是暴露的顺序
    一个模块中可以同时有多个import
    多个import可以导入多个不同的模块,也可以是同一个模块
*/
//import {PI ,Person ,sum }  from './module.js'
//import {PI as pi,Person as People,sum as add}  from './module.js'
import {PI ,Person ,sum,PI as pi,Person as People,sum as add}  from './module.js'
// 使用暴露的属性
console.log(PI)
console.log(pi)
// 调用暴露的方法
let result1 =sum(10,20)
console.log(result1)
let result2 =add(10,20)
console.log(result2)
// 使用暴露的Person类
let person1 =new Person('张三',10)
person1.sayHello()
let person2 =new People('李四',11)
person2.sayHello()
```

##### 默认导出

![1684463528680](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\1684463528680.png)

+ modules混合向外导出

``` javascript
// 3默认和混合暴露
/* 
    默认暴露语法  export default sum
    默认暴露相当于是在暴露的对象中增加了一个名字为default的属性
    三种暴露方式可以在一个module中混合使用

*/
export const PI = 3.14
// 导出一个函数
function sum(a, b) {
  return a + b;
}
// 导出一个类
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  sayHello() {
    console.log(`Hello, my name is ${this.name}, I'm ${this.age} years old.`);
  }
}

// 导出默认
export default sum
// 统一导出
export {
   Person
}

```

+ app.js 的default和其他导入写法混用

``` javascript
/* 
    *代表module.js中的所有成员
    m1代表所有成员所属的对象
*/
import * as m1 from './module.js'
import {default as add} from './module.js' // 用的少
import add2 from './module.js' // 等效于 import {default as add2} from './module.js'

// 调用暴露的方法
let result =m1.default(10,20)
console.log(result)
let result2 =add(10,20)
console.log(result2)
let result3 =add2(10,20)
console.log(result3)

// 引入其他方式暴露的内容
import {PI,Person} from './module.js'
// 使用暴露的Person类
let person =new Person('张三',10)
person.sayHello()
// 使用暴露的属性
console.log(PI)
```

### 前端工程化环境搭建

> Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时环境，可以使 JavaScript 运行在服务器端。使用 Node.js，可以方便地开发服务器端应用程序，如 Web 应用、API、后端服务，还可以通过 Node.js 构建命令行工具等。相比于传统的服务器端语言（如 PHP、Java、Python 等），Node.js 具有以下特点：

-   单线程，但是采用了事件驱动、异步 I/O 模型，可以处理高并发请求。
-   轻量级，使用 C++ 编写的 V8 引擎让 Node.js 的运行速度很快。
-   模块化，Node.js 内置了大量模块，同时也可以通过第三方模块扩展功能。
-   跨平台，可以在 Windows、Linux、Mac 等多种平台下运行。

> Node.js 的核心是其管理事件和异步 I/O 的能力。Node.js 的异步 I/O 使其能够处理大量并发请求，并且能够避免在等待 I/O 资源时造成的阻塞。此外，Node.js 还拥有高性能网络库和文件系统库，可用于搭建 WebSocket 服务器、上传文件等。`在 Node.js 中，我们可以使用 JavaScript 来编写服务器端程序，这也使得前端开发人员可以利用自己已经熟悉的技能来开发服务器端程序，同时也让 JavaScript 成为一种全栈语言。`Node.js 受到了广泛的应用，包括了大型企业级应用、云计算、物联网、游戏开发等领域。常用的 Node.js 框架包括 Express、Koa、Egg.js 等，它们能够显著提高开发效率和代码质量。



> NPM全称Node Package Manager，是Node.js包管理工具，是全球最大的模块生态系统，里面所有的模块都是开源免费的；也是Node.js的包管理工具，相当于后端的Maven 。

![image-20240313195454905](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240313195454905.png)

### !!!Vue3

Vue (发音为 /vjuː/，类似 **view**) 是一款用于构建用户界面的 JavaScript 框架。它基于标准 HTML、CSS 和 JavaScript 构建，并提供了一套声明式的、组件化的编程模型，帮助你高效地开发用户界面。

 **Vue的两个核心功能：**

-   **声明式渲染**：Vue 基于标准 HTML 拓展了一套模板语法，使得我们可以声明式地描述最终输出的 HTML 和 JavaScript 状态之间的关系。
-   **响应性**：Vue 会自动跟踪 JavaScript 状态并在其发生变化时响应式地更新 DOM  

#### Vite创建Vue3工程化项目

![image-20240313212243007](D:\2024\Notes\Typora\Study\JavaWeb_Notes\JavaWeb.assets\image-20240313212243007.png)

```
启动
npm run dev
关闭
ctrl^c 
```

-   public/ 目录：用于存放一些公共资源，如 HTML 文件、图像、字体等，这些资源会被直接复制到构建出的目标目录中。
-   src/ 目录：存放项目的源代码，包括 JavaScript、CSS、Vue 组件、图像和字体等资源。在开发过程中，这些文件会被 Vite 实时编译和处理，并在浏览器中进行实时预览和调试。以下是src内部划分建议：
    1.  `assets/` 目录：用于存放一些项目中用到的静态资源，如图片、字体、样式文件等。
    2.  `components/` 目录：用于存放组件相关的文件。组件是代码复用的一种方式，用于抽象出一个可复用的 UI 部件，方便在不同的场景中进行重复使用。
    3.  `layouts/` 目录：用于存放布局组件的文件。布局组件通常负责整个应用程序的整体布局，如头部、底部、导航菜单等。
    4.  `pages/` 目录：用于存放页面级别的组件文件，通常是路由对应的组件文件。在这个目录下，可以创建对应的文件夹，用于存储不同的页面组件。
    5.  `plugins/` 目录：用于存放 Vite 插件相关的文件，可以按需加载不同的插件来实现不同的功能，如自动化测试、代码压缩等。
    6.  `router/` 目录：用于存放 Vue.js 的路由配置文件，负责管理视图和 URL 之间的映射关系，方便实现页面之间的跳转和数据传递。
    7.  `store/` 目录：用于存放 Vuex 状态管理相关的文件，负责管理应用程序中的数据和状态，方便统一管理和共享数据，提高开发效率。
    8.  `utils/` 目录：用于存放一些通用的工具函数，如日期处理函数、字符串操作函数等。
-   vite.config.js 文件：Vite 的配置文件，可以通过该文件配置项目的参数、插件、打包优化等。该文件可以使用 CommonJS 或 ES6 模块的语法进行配置。
-   package.json 文件：标准的 Node.js 项目配置文件，包含了项目的基本信息和依赖关系。其中可以通过 scripts 字段定义几个命令，如 dev、build、serve 等，用于启动开发、构建和启动本地服务器等操作。
-   Vite 项目的入口为 src/main.js 文件，这是 Vue.js 应用程序的启动文件，也是整个前端应用程序的入口文件。在该文件中，通常会引入 Vue.js 及其相关插件和组件，同时会创建 Vue 实例，挂载到 HTML 页面上指定的 DOM 元素中。

#### .vue文件

+ 传统的页面有.html文件.css文件和.js文件三个文件组成(多文件组件) 
+ vue将这文件合并成一个.vue文件(Single-File Component，简称 SFC,单文件组件)

+ .vue文件对js/css/html统一封装,这是VUE中的概念 该文件由三个部分组成    `<script>    <template>    <style>`
  + template标签     代表组件的html部分代码	代替传统的.html文件
  + script标签           代表组件的js代码 代替传统的.js文件
  + style标签            代表组件的css样式代码 代替传统的.css文件	

> 工程化vue项目如何组织这些组件?

+ index.html是项目的入口,其中 `<div id ='app'></div>`是用于挂载所有组建的元素
+ index.html中的script标签引入了一个main.js文件,具体的挂载过程在main.js中执行
+ main.js是vue工程中非常重要的文件,他决定这项目使用哪些依赖,导入的第一个组件
+ App.vue是vue中的核心组件,所有的其他组件都要通过该组件进行导入,该组件通过路由

### !!!Axios
