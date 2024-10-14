# Mysql

## 数据库基础概念

|      名称      |                             全称                             | 简称                               |
| :------------: | :----------------------------------------------------------: | ---------------------------------- |
|     数据库     |             存储数据的仓库，数据有组织的进行存储             | DataBase（DB）                     |
| 数据库管理系统 |                  操纵和管理数据库的大型软件                  | DataBase Management System（DBMS） |
|      SQL       | 操作关系型数据库的编程语言，定义了一套操作关系型数据库统一标准 | Structured Query Lang（SQL）       |

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240212183911506.png" alt="image-20240212183911506" style="zoom: 33%;" /><img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240212184035104.png" alt="image-20240212184035104" style="zoom:33%;" />   

## SQL

### 数据类型

#### 数值类型

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240212203653624.png" alt="image-20240212203653624" style="zoom:67%;" />

```mysql
age tinyint unsigned;
--12	
score doublt(4,1);
--80.5
```

#### 字符串类型

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240212203922192.png" alt="image-20240212203922192" style="zoom:67%;" />

```mysql
char(10);
--空格补位 性能好
varchar(10);
--variable 根据实际计算空间 性能差
```

#### 日期时间类型

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240212204423073.png" alt="image-20240212204423073" style="zoom:67%;" />

### 通用语法及分类

#### 语法

- MySQL数据库的SQL语句不区分大小写，关键字建议使用大写
- 注释
  - 单行注释：`--注释内容`或`#注释内容（MySQL特有）`
  - 多行注释：`/*注释内容*/`

#### 分类

| 分类 |            全称            |                          说明                          |
| :--: | :------------------------: | :----------------------------------------------------: |
| DDL  |  Data Definition Language  |  数据定义语言，用来定义数据库对象（数据库，表，字段）  |
| DML  | Data Manipulation Language |     数据操作语言，用来对数据库表中的数据进行增删改     |
| DQL  |    Data Query language     |         数据查询语言，用来查询数据库中表的记录         |
| DCL  |   Data Control Language    | 数据控制语言，用来创建数据库用户、控制数据库的访问权限 |

### 表结构操作DDL

#### 数据库操作

##### 查询

```mysql
--查询所有数据库
SHOW DATABASES;

--查询当前数据库
SHOW DATABASE();
```

##### 创建

```mysql
--创建数据库
CREATE DATABASE [IF NOT EXISTS] 数据库名 [DEFAULT CHARSET 字符集] [COLLATE 排序规则];

--[DEFAULT CHARSET 字符集]
--eg:	default charset utf8mb4
```

##### 删除

```mysql
--删除数据库
DROP DATABASE [IF EXISTS] 数据库名;
```

##### 使用

```mysql
--切换至数据库
USE 数据库名;
```

#### 表操作

##### **查询**

###### 查询当前数据库所有表

```mysql
--查询表
SHOW TABLES;
```

###### 查询表结构

```mysql
--查询表中内容
DESC 表名;
--describe
```

###### 查询指定表的建表语句

```mysql
--查询建表语句
SHOW CREATE TABLE 表名;
```

##### **创建**

```mysql
--建表
CREATE TABLE 表名{
	字段1 字段1类型[COMMENT 字段1注释],
	字段2 字段2类型[COMMENT 字段2注释],
	......
	字段n 字段n类型[COMMENT 字段n注释]
}[COMMENT 表注释];
```

​	[...]为可选参数，最后一个字段后面没有逗号。

##### 修改

###### 添加字段

```mysql
--添加表中变量
ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释] [约束];
```

###### 修改数据类型

```mysql
--修改表中变量数据类型
ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);
```

###### 修改字段名和字段类型

```mysql
--修改表中变量名和数据类型
ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];
```

###### 修改表名

```mysql
--修改表名
ALTER TABLE 表名 RENAME TO 新表名;
```

##### 删除

```mysql
--删除表中变量
ALTER TABLE 表名 DROP 字段名;
```

```mysql
--删除表
DROP TABLE 表名;
```

```mysql
--删除指定表，并重新创建该表
TRUNCATE TABLE 表名；
```

#### 小结

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240212214848615.png" alt="image-20240212214848615" style="zoom: 33%;" />

#### 图形化界面工具

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240213094131623.png" alt="image-20240213094131623" style="zoom:50%;" />

### 表内容操作DML

#### 插入Insert

##### 给指定字段添加数据

```mysql
INSERT INTO 表名(字段名1,字段名2,...) VALUES(值1,值2...);
```

##### 给全部字段添加数据

```mysql
INSERT INTO 表名 VALUES(值1,值2,...);
```

##### 批量添加数据

```mysql
INSERT INTO 表名(字段名1,字段名2,...) VALUES (值1,值2,...),(值1,值2,...),(值1,值2,...);
```

```mysql
INSERT INTO 表名 VALUES (值1,值2,...),(值1,值2,...),(值1,值2,...);
```

##### 注意

- 插入数据时，指定的字段顺序需要与之的顺序是一一对应的
- 字符串和日期型数据应该包含在引号中
- 插入的数据大小，应该在字段的规定范围内

#### 修改Update

```mysql
UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2,...[WHERE 条件];
```

修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表所有数据。

```mysql
UPDATE employee set name = '1' where id = 1;
```

#### 删除Delete

```mysql
DELETE FROM 表名 [WHERE 条件];
```

- DELETE如果没有条件，删除整张表所有数据
- DELETE语句不能删除某一个字段的值（可使用UPDATE完成该操作）

#### 小结

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240213111443138.png" alt="image-20240213111443138" style="zoom:50%;" />

### 表查询操作DQL

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240213111657321.png" alt="image-20240213111657321" style="zoom:50%;" />

#### 基础查询

##### 查询多个字段

```mysql
SELECT 字段1,字段2,字段3,... FROM 表名;
SELEct * FROM 表名;--尽量不写
```

##### 设置别名

```mysql
SELECT 字段1 [AS 别名1],字段2 [AS 别名2]... FROM表名;
--as可以省略
--起了别名之后，只能根据别名查询？？？？
```

##### 去除重复记录

```mysql
SELECT DISTINCT 字段列表 FROM 表名;
--相当于筛选显示 并不直接从表中删除
```

#### 条件查询

```mysql
SELECT 字段列表 FROM 表名 WHERE 条件列表;
```

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240213114511782.png" alt="image-20240213114511782" style="zoom:50%;" />

```mysql
--查询姓名为两个字的员工信息
select * from emp where name like '--';

--查询身份证最后一位是x的员工信息
select * from emp where idcard like '%X';
select * from emp where idcard like '-----------------X';
```

#### 聚合函数

把一列数据作为一个整体，进行纵向计算。

**常见聚合函数**

| 函数  |   功能   |
| :---: | :------: |
| count | 统计数量 |
|  max  |  最大值  |
|  min  |  最小值  |
|  avg  |  平均值  |
|  sum  |   求和   |

```mysql
SELECT 聚合函数{字段列表} FROM 表名;

--eg
select count{*} from employee;
select count{idcard} from employee;
```

**注意**：null值不参与所有聚合函数运算

#### 分组查询

```mysql
SELECT 字段列表 FROM 表名 [WHERE 条件] GROUP BY 分组字段名 [HAVING 分组后过滤条件];
```

```mysql
--根据性别分组，统计男性员工和女性员工的数量
select gender, count(*) from emp group by gender;

--根据性别分组，统计男性员工和女性员工的平均年龄
select gender, avg(age) from emp group by gender;
```

##### where与having区别

- 执行时机不同
  - where是分组之前进行过滤，不满足where条件不参与分组；having是分组之后对结果进行过滤
- 判断条件不同
  - where不能对聚合函数进行判断，having可以

```mysql
--查询年龄小于45的员工，并根据工作地址分组，获取员工数量大于等于3的工作地址

select workaddress, count(*) from emp where age <45 group by workaddress;

--这个其实就是先通过from后面的语句得到相应的数据，然后再通过group by将刚刚得到的数据进行分组，最后再执行前面的select的语句对每一组的数据都做那样的处理，最后将分组后的数据展示出来

select workaddress, count(*) from emp where age <45 group by workaddress having count(*) >= 3;
--用having对分组之后员工数量大于等于3的进行过滤

--起别名
select workaddress, count(*) address_count from emp where age <45 group by workaddress having address_count >= 3;
```

##### 注意

- 执行顺序：where > 聚合函数 > having
- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无意义。

#### 排序查询

```mysql
SELECT 字段列表 FROM 表名 OPRDER BY 字段1 排序方式1,字段2 排序方式2;
```

##### 排序方式

- ASC 升序（默认值）（Ascending Order）
- DESC 降序（Descending Order）

##### 注意 

如果是多字段排序，第一字段相同时才会排序第二字段

```mysql
select * from emp order by age asc , date asc;
```

#### 分页查询

```mysql
SELECT 字段列表 FROM 表名 LIMIT 起始索引，查询记录数;
```

##### 注意

- 起始索引从0开始，起始索引=（查询页码-1）* 每页显示记录
- 分页查询是数据库的方言，不同数据库有不同的实现，MySql是LIMIT
- 如果查询的是第一页数据，起始索引可以省略，直接简写为`limit 每页显示记录`。

```mysql
select * from emp where gender = '男' order by age limit 5;
```

#### 执行顺序

编写顺序：

```mysql
select	from	where	group by	having	order by	limit
```

执行顺序：

```mysql
from	where	group by	having	select	order by	limit
```

select是取目标数据集合的过程 

###  控制操作DCL

#### 管理用户

##### 查询用户

```mysql
USE mysql;
SELECT * FROM user;
```

##### 创建用户

```mysql
CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
```

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240213124413008.png" alt="image-20240213124413008" style="zoom:50%;" />

##### 修改用户密码

```mysql
ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';
```

##### 删除用户

```mysql
DROP USER '用户名'@'主机名';
```

#### 权限控制

|        权限        |        说明        |
| :----------------: | :----------------: |
| ALL,ALL PRIVILEGES |      所有权限      |
|       SELECT       |      查询数据      |
|       INSERT       |      插入数据      |
|       UPDATE       |      修改数据      |
|       DELETE       |      删除数据      |
|       ALTER        |       修改表       |
|        DROP        | 删除数据库/表/视图 |
|       CREATE       |   创建数据库/表    |

##### 查询权限

```MYSQL
SHOW GRANTS FOR '用户名'@'主机名';
```

##### 授予权限

```mysql
GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';

--GRANT USAGE ON *.* TO 'heima'@'%';没有授予任何权限
```

##### 撤销权限

```mysql
REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';
```

## 函数

一段可以直接被另一段程序调用的程序或代码

### 字符串函数

|               函数               |                         功能                          |
| :------------------------------: | :---------------------------------------------------: |
|       CONCAT(S1,S2,...SN)        |                      字符串拼接                       |
|            LOWER(str)            |                        转小写                         |
|            UPPER(str)            |                        转大写                         |
|         LPAD(str,n,pad)          | 左填充，用字符串pad对str左边进行填充，达到n个字符长度 |
|         RPAD(str,n,pad)          | 右填充，用字符串pad对str右边进行填充，达到n个字符长度 |
| TRIM(str)  trim:修剪，修整，减少 |              去掉字符串头部和尾部的空格               |
|     SUBSTRING(str,start,len)     |    返回从字符串str从start位置起的len个长度的字符串    |

```mysql
select concat('hello','mysql');

--hello mysql
```

```mysql
select lpad('01',5,'-');

#----01
```

```mysql
select trim(' hello mysql ');

--hello mysql
```

### 数值函数

|    函数    |               功能               |
| :--------: | :------------------------------: |
|  CELL(x)   |             向上取整             |
|  FLOOR(x)  |             向下取整             |
|  MOD(x,y)  |           返回x/y的模            |
|   RAND()   |        返回0~1内的随机数         |
| ROUND(x,y) | 求参数x的四舍五入值，保留y位小数 |

```mysql
select mod(5,4) --1
select mod(3,4) --3
select mod(6,4) --2
```

### 日期函数

| 函数                              | 功能                                              |
| --------------------------------- | ------------------------------------------------- |
| CURDATE()                         | 返回当前日期                                      |
| CURTIME()                         | 返回当前时间                                      |
| NOW()                             | 返回当前日期和时间                                |
| YEAR(date)                        | 获取date的年份                                    |
| MONTH(date)                       | 获取date的月份                                    |
| DAY(date)                         | 获取date的日期                                    |
| DATE_ADD(date,INTERVAL expr type) | 返回一个日期/时间值加上一个时间间隔expr后的时间值 |
| DATEDIFF(date1,date2)             | 返回起始时间date1和结束时间date2之间的天数        |

```mysql
select daye_add(now(),INTERVAL 70 year);

--2094-02-13 10:35:50
```

### 流程函数

| 函数                                                       | 功能                                                    |
| ---------------------------------------------------------- | ------------------------------------------------------- |
| IF(value,t,f)                                              | 如果value为true则返回t，否则返回f                       |
| IFNULL(value1,value2)                                      | 如果value1不为空，返回value1，否则返回value2            |
| CASE WHEN [val1] THEN [res1] ... ELSE [default] END        | 如果val1为true返回res1...否则返回default默认值          |
| CASE [expr] WHEN [val1] THEN [res1] ... ELSE [default] END | 如果expr的值等于val1，返回rest1...否则返回default默认值 |

## 约束

约束是作用于表中字段上的规则，用于限制存储在表中的数据

目的：保证数据库中数据的正确、有效性和完整性

|             约束              |                           描述                           |   关键字    |
| :---------------------------: | :------------------------------------------------------: | :---------: |
|           非空约束            |                限制该字段的数据不能为null                |  NOT NULL   |
|           唯一约束            |          保证该字段的所有数据都是唯一、不重复的          |   UNIQUE    |
|           主键约束            |         主键是一行数据的唯一标识，要求非空且唯一         | PRIMARY KEY |
|           默认约束            |      保存数据时，如果未指定该字段的值，则采用默认值      |   DEFAULT   |
| 检查约束(mysql8.0.16版本以后) |                 保证字段值满足某一个条件                 |    CHECK    |
|           外键约束            | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性 | FOREIGN KEY |

**约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束**

### 约束演示

**自动增长 AUTO_INCREMENT **

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240214105438097.png" alt="image-20240214105438097" style="zoom:50%;" />

### 外键约束

外键是用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。

#### 添加外键

```mysql
CREATE TABLE 表名(
	字段名 数据类型
	...
	[CONSTRAINT] [外键名称] FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名)
);
```

```mysql
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY(外键字段名) REFERENCES 主表(主表列名);
```

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240214110335137.png" alt="image-20240214110335137" style="zoom:50%;" />

####  删除外键

```mysql
ALTER TABLE 表名 DROP FOREIGN KEY 外键名称;

--alter table emp drop foreign key fk_emp_dept_id;
```

### 外键删除更新行为

| 行为                  | 说明                                                         |
| --------------------- | ------------------------------------------------------------ |
| NO ACTION（默认行为） | 在父表中删除/更新对应记录时，首先检查该纪录是否有对应外键，**如果有则不允许删除/更新。** |
| RESTRICT              | 在父表中删除/更新对应记录时，首先检查该纪录是否有对应外键，**如果有则不允许删除/更新。** |
| CASCADE（级联）       | 在父表中删除/更新对应记录时，首先检查该纪录是否有对应外键，如果有**则也删除/更新外键在子表中的记录。** |
| SET NULL              | 在父表中删除对应记录时，首先检查该纪录是否有对应外键，**如果有则设置子表中该外键值为NULL（这就要求该外键值可以取NULL）。** |
| SET DEFAULT           | 父表有变更时，**子表将外键列设置成一个默认的值**（innodb不支持） |

```MYSQL
ALTER TABLE 表名 ADD CONSTRAINT 外键名称 FOREIGN KEY(外键字段名) REFERENCES 主表(主表字段名) ON UPDATE CASCADE ON DELETE CASCADE; 
```

## 多表查询

```mysql
select * from emp , dept where emp.dept_id = dept.id;
```

- 连接查询
  - 内连接 相当于查询a、b交集部分数据
  - 外连接
    - 左外连接 查询左表所有数据 以及两张表交集部分数据
    - 右外连接 查询右表所有数据 以及两张表交集部分数据
  - 自连接 当前表与自身的连接查询 自连接必须使用表的别名
- 子查询

### 多表关系

- 一对多（多对一）
- 多对多
- 一对一

### 连接查询

#### 内连接

- 隐式内连接

  ```mysql
  SELECT 字段列表 FROM 表1，表2 WHERE 条件...;
  ```

- 显式内连接

  ```mysql
  SELECT 字段列表 FROM 表1 [INNER] JOIN 表2 ON 连接条件...；
  ```

内连接查询的是两张表的交集部分

#### 外连接

​	**外连接有7种情况 可以看康师傅的mysql**

- 左外连接

  ```mysql
  SELECT 字段列表 FROM 表1 LEFT[OUTER] JOIN 表2 ON 条件...;
  ```

  

- 右外连接

  ```mysql
  SELECT 字段列表 FROM 表1 RIGHT[OUTER] JOIN 表2 ON 条件...;
  ```

#### 自连接

```mysql
SELECT 字段列表 FROM 表A 别名A JOIN 表A 别名B ON 条件...;
```

自连接查询可以是内连接查询，也可以是外连接查询。

```mysql
select a.name , b.name from emp a , emp b wh--ere a.managerid = b.id;
--使用内连接实现的自连接，必须要求managerid和id都非空且值相等才查出来
```

```mysql
select a.name,b.name from emp a left join emp b on a.managerid = b.id;
--第二个用左外连接，除了要求字段值匹配以外，哪怕managerid为空也能查出来
```

### 联合查询

//联合查询相当于纵向拼接

对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集。

```mysql
SELECT 字段列表 FROM 表A...
UNION[ALL]
SELECT 字段列表 FROM 表B...;
```

- UNION直接对结果进行合并，UNION ALL对合并结果进行去重
- 对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致

### 子查询 

Sql语句中嵌套SELECT语句，称为嵌套查询，又称为子查询。

```mysql
SELECT * FROM t1 WHERE column1 = (SELECT column1 FROM t2);
```

子查询外部的语句可以是INSERT/UPDATE/DELETE/SELECT中的任何一个。

#### 分类

​	根据子查询结果不同，分为：

- 标量子查询（子查询结果为单个值）
- 列子查询（子查询结果为一列）
- 行子查询（子查询结果为一行）
- 表子查询（子查询结果为多行多列）

​	根据子查询位置，分为：

- WHERE之后
- FROM之后
- SELECT之后

#### 标量子查询

子查询返回的结果是单个值（数字、字符串、日期等），最简单的形式，这种子查询称为标量子查询

常用操作符：`= <>(不等于) > >= < <=`

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240215180627760.png" alt="image-20240215180627760" style="zoom:50%;" />

#### 列子查询

常用操作符：

| 操作符 |                 描述                 |
| :----: | :----------------------------------: |
|   IN   |     在指定的集合范围之内，多选一     |
| NOT IN |        不在指定的集合范围之内        |
|  ANY   | 子查询返回列表中，有任意一个满足即可 |
|  SOME  |              与ANY等同               |
|  ALL   |   子查询返回列表的所有值都必须满足   |

```mysql
select * from emp where salary > all(select salary from emp) where dept_id = (select id from dept where name = '财务部');
```

#### 行子查询

常用操作符：`=、<>(不等于)、IN、NOT IN`

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240215181730847.png" alt="image-20240215181730847" style="zoom:50%;" />

#### 表子查询

常用操作符：IN

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240215181929329.png" alt="image-20240215181929329" style="zoom:50%;" />

## 【*重点】事务

[MySQL事务(transaction) (有这篇就足够了..)-CSDN博客](https://blog.csdn.net/qq_56880706/article/details/122653735?ops_request_misc=%7B%22request%5Fid%22%3A%22170799777116800185874776%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=170799777116800185874776&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-122653735-null-null.142^v99^control&utm_term=事务&spm=1018.2226.3001.4187)

事务是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作**作为一个整体一起向系统提交或撤销中操作请求**，即这些操作**要么同时成功，要么同时失败**。

默认MySQL的事务是自动提交的，也就是说，当执行一条DML语句，MySQL会立即隐式的提交事务。

### 事务操作

- 方式一：

  - 查看/设置事务提交方式

  ```mysql
  SELECT @@autocommit;--查看是否开启自动提交
  SET @@autocommit = 0;
  ```

  - 提交事务

  ```mysql
  COMMIT;
  ```

  - 回滚事务

  ```mysql
  ROLLBACK;
  ```

可以对应word文档理解，回滚相当于撤回，提交相当于保存。

```mysql
--步骤一：更改自动提交
SELECT @@autocommit;--查看是否开启自动提交
SET @@autocommit = 0;
--步骤二：编写事务中的sql语句（insert、update、delete）
--这里实现一下"李二给王五转账"的事务过程
update t_account set balance = 50 where vname = "李二";
update t_account set balance = 130 where vname = "王五";
#步骤三：结束事务
commit; #提交事务
#rollback; #回滚事务:就是事务不执行，回滚到事务执行前的状态 
--！！！！出错的时候不commit 直接rollback，此时临时数据发生改变，但是数据库中数据还没改变
```

- 方式2

  - 开启事务

  ```mysql
  START TRANSACTION 或 BEGIN
  --开启事务以后就不会自动提交了，需要手动提交 但此时没有对autocommit参数进行改变，所以还是默认值1，自动提交
  --autocommint虽然被设置成了1；但是这里使用了start transaction,语句片段变了手动
  ```

  - 提交事务

  ```mysql
  COMMIT;
  ```

  - 回滚事务

  ```mysql
  ROLLBACK;
  ```

### **事务四大特性ACID**

- **原子性（Atomicity）**

  **事务是不可分割的最小操作单元，要么全部成功，要么全部失败。**

- **一致性（Consistency）**

  **事务完成时，必须使所有数据都保持一致状态。**

  **（转完帐以后总金额不变）**

- **隔离性（Isolation）**

  **数据库系统提供的隔离机制，保证事务在不受外部并发操作。影响的独立环境下运行**

- **持久性（Durability）**

  **事务一旦提交或者回滚，对数据库中的数据改变是永久的。**

### 并发事务问题

|    问题    |                             描述                             |
| :--------: | :----------------------------------------------------------: |
|    脏读    |           一个事务读到另外一个事务还没有提交的数据           |
| 不可重复读 | 一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读 |
|    幻读    | 一个事务按照条件查询数据时，没有对应的数据行。<br />但是在插入数据时，又发现这行数据已经存在，好像出现了幻影 |

### 事务隔离级别

|     隔离级别     |       脏读       |    不可重复读    |       幻读       |                     备注                     |
| :--------------: | :--------------: | :--------------: | :--------------: | :------------------------------------------: |
| Read uncommitted |                  |                  |                  | 级别最低，解决问题最少，安全性最低；性能最好 |
|  Read committed  | 能解决，不会发生 |                  |                  |                  Oracle默认                  |
| Repeatable Read  | 能解决，不会发生 | 能解决，不会发生 |                  |                  Mysql默认                   |
|   Serializable   | 能解决，不会发生 | 能解决，不会发生 | 能解决，不会发生 |                    串行化                    |

```mysql
--查看事务隔离级别
SELECT @@TRANSACTION_ISOLAION;

--	设置事务隔离级别
SET [SESSION|GLOBAL] TRANSACTION ISOLATION LEVEL {READ UNCOMMITTED | Read committed | Repeatable Read | Serializable}

//session 会话级别 只对当前客户端窗口有效
//global 针对所有客户端窗口有效
```

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240215204828997.png" alt="image-20240215204828997" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240215205109270.png" alt="image-20240215205109270" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240215205338580.png" alt="image-20240215205338580" style="zoom:50%;" />

<img src="D:\2024\Notes\Typora\Study\Java_Mysql\Mysql.assets\image-20240215205537992.png" alt="image-20240215205537992" style="zoom:50%;" />

左侧查4没查到，右侧加的时候阻塞，左侧加完提交，右侧可以继续加，检测到有重复，显示不能加。























































































































### 















