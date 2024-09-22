[toc]



## 表的约束

mysql提供了诸多用于约束数据格式与存储条件的语法,也就是表的约束;

> 实际业务中,可能会考虑到性能等原因,不一定直接在mysql中约束,而是在上层(写业务代码层面)或逻辑上进行约束;虽然不一定在mysql中使用约束,但我们至少通过掌握mysql中的约束,使能更好地在上层理解和运用约束.

### 空属性

空属性有两个值：null（默认的）和not null(不为空)



数据库默认字段基本都是字段为空，但是实际开发时，尽可能保证字段不为空，因为数据为空没办法参与运算。

```
mysql> select null; 
+------+
| NULL | 
+------+
| NULL | 
+------+
1 row in set (0.00 sec)

mysql> select 1+null; 
+--------+
| 1+null | 
+--------+
|   NULL | 
+--------+
1 row in set (0.00 sec)
```





#### 非空约束（NOT NULL Constraint）

- 定义

非空约束是数据库中的一种约束，用于确保某个字段在创建记录时必须有值，不能为空。在MySQL中，这通常通过`NOT NULL`约束来实现。

- 用法

在创建或修改表结构时，可以在字段定义后添加`NOT NULL`约束来指定该字段不能为空。

```
mysql> create table myclass(
    -> class_name varchar(20) not null,
    -> class_room varchar(10) not null); 
Query OK, 0 rows affected (0.02 sec)

mysql> desc myclass; 
+------------+-------------+------+-----+---------+-------+ 
| Field      | Type        | Null | Key | Default | Extra | 
+------------+-------------+------+-----+---------+-------+ 
| class_name | varchar(20) | NO   |     | NULL    |       | 
| class_room | varchar(10) | NO   |     | NULL    |       | 
+------------+-------------+------+-----+---------+-------+
```



### 默认值



#### 定义

在MySQL中，默认值（Default Value）是指为表中的列指定的一个特定值，当在插入记录时该列没有提供值时，将自动使用此默认值。默认值可以是数字、字符串、日期或时间等数据类型，具体取决于列的数据类型。

#### 用途

1. ‌**简化数据输入**‌：通过为常见或默认值已知的列设置默认值，可以减少在插入记录时需要指定的字段数量。
2. ‌**保证数据一致性**‌：对于某些列，可能有一个公认或期望的默认值。通过为这些列设置默认值，可以确保在没有明确指定值时，数据库中的数据仍然保持一致。
3. ‌**避免NULL值**‌：在某些情况下，NULL值可能不是最佳选择，因为它们可能表示未知或缺失的数据。通过设置默认值，可以避免在不需要NULL值的情况下使用它们。

#### 语法

```
mysql> create table t1(
			 name varchar(30) not null, 
			 age tinyint unsigned default 18, 
	  	 gender char(1) not null default '男'
			 );
			 
mysql> desc t1;
+--------+---------------------+------+-----+---------+-------+
| Field  | Type                | Null | Key | Default | Extra |
+--------+---------------------+------+-----+---------+-------+
| name   | varchar(30)         | NO   |     | NULL    |       |
| age    | tinyint(3) unsigned | YES  |     | 18      |       |
| gender | char(1)             | NO   |     | 男      |       |
+--------+---------------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```





#### 演示

- 基本用法

```
mysql> insert into t1(name) values(1);
Query OK, 1 row affected (0.00 sec)

mysql> select * from t1;
+------+------+--------+
| name | age  | gender |
+------+------+--------+
| 1    |   18 | 男     |
+------+------+--------+
1 row in set (0.00 sec)
```



- 有缺省值,允许为空时

```
mysql> insert into t1(name,age,gender) values(2,NULL,'男');
Query OK, 1 row affected (0.00 sec)

mysql> select * from t1;
+------+------+--------+
| name | age  | gender |
+------+------+--------+
| 1    |   18 | 男     |
| 2    | NULL | 男     |
+------+------+--------+
2 rows in set (0.00 sec)
```

可以主动设置为NULL



- name约束不允许为空,没有设置缺省值

插入空:

```
mysql> insert into t1(name, age,gender) values(NULL,19,'女');
ERROR 1048 (23000): Column 'name' cannot be null
```

不插入:

```
mysql> insert into t1(age,gender) values(19,'女');
ERROR 1364 (HY000): Field 'name' doesn't have a default value
```

再看插入一个没有手动设置约束的,

```
mysql> alter table t1 add other varchar(10);
Query OK, 0 rows affected (0.06 sec)

mysql> show create table t1 \G;
*************************** 1. row ***************************
       Table: t1
Create Table: CREATE TABLE `t1` (
  `name` varchar(30) NOT NULL,											## 没有默认值
  `age` tinyint(3) unsigned DEFAULT '18',
  `gender` char(1) NOT NULL DEFAULT '男',
  `other` varchar(10) DEFAULT NULL									## 自动生成默认值为NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```

说明: 

1. 手动设置约束后,不会再自动生成默认值(新覆盖旧)

2. 如果没有显式插入某字段,则用的是缺省值,但如果该字段没有设置缺省值,则会报错(无法插入).





### 列描述：comment

**没有实际含义**，专门用来描述字段，保存在表创建语句中，用来给程序员或DBA来进行了解,相当于一种**软性约束**(给人看的,让人觉得应该怎么做,不该怎么做,感性约束)。



```
show create table 表名;									# desc看不见,只能通过show create查看
```



### zerofill



使用int类型时,发现int后面带了一个括号和数字int(N)

```
mysql> desc t2;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.01 sec)
```

这个数字就是zerofill属性的值,不过默认是没有设置zerofill的,没有设置zerofill时是无效的.



#### 语法:

 `列 int(N) zerofill` ;

```
mysql> alter table t2 modify id int zerofill;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc t2;
+-------+---------------------------+------+-----+---------+-------+
| Field | Type                      | Null | Key | Default | Extra |
+-------+---------------------------+------+-----+---------+-------+
| id    | int(10) unsigned zerofill | YES  |     | NULL    |       |
+-------+---------------------------+------+-----+---------+-------+
1 row in set (0.00 sec)
```



#### 效果

```
mysql> select * from t2;
+------------+
| id         |
+------------+
| 0000000001 |
| 0000000011 |
| 0000000111 |
+------------+
3 rows in set (0.00 sec)
```

可以发现括号内数字就是id的占位个数,占了11个位;其次,0会填充实际数值不够占位的位,缺多少填充多少



- 如果N小于数据的位数

```
mysql> delete from t2 ;
Query OK, 3 rows affected (0.00 sec)


mysql> alter table t2 modify id int(4) zerofill;
Query OK, 0 rows affected (0.00 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> insert into t2 values(1);
Query OK, 1 row affected (0.00 sec)

mysql> insert into t2 values(11);
Query OK, 1 row affected (0.00 sec)

mysql> insert into t2 values(111);
Query OK, 1 row affected (0.00 sec)

mysql> insert into t2 values(1111);
Query OK, 1 row affected (0.00 sec)

mysql> insert into t2 values(11111);
Query OK, 1 row affected (0.01 sec)

mysql> insert into t2 values(111111);
Query OK, 1 row affected (0.00 sec)

mysql> insert into t2 values(1111111);
Query OK, 1 row affected (0.00 sec)

mysql> select * from t2;
+---------+
| id      |
+---------+
|    0001 |
|    0011 |
|    0111 |
|    1111 |
|   11111 |
|  111111 |
| 1111111 |
+---------+
7 rows in set (0.00 sec)
```

够N则没有动作,小于N位会补上足够的0,是一种"**不够才会发生**"的行为



- 说明: zerofill不会影响数据的原始类型,只是在显示时以0占位填充方式显示;



- 默认生成的zerofile数字

默认有符号int是11,无符号是10,因为int类类型的数据范围是40亿和20亿,十进制一共10个数字,所以无符号是10;而有符号还有一个要用于表示符号,因此有符号是11.



### 主键 (primary key)

用来唯一的约束该字段里面的数据，**不能重复**，**不能为空**，一张表中最多**只能有一个主键**(唯一标识)；

主键所在的列通常是整数类型。



#### 语法

```
mysql> create table if not exists test_key(id int primary key comment '主键', name varchar(128));
Query OK, 0 rows affected (0.02 sec)

mysql> desc test_key;
+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| id    | int(11)      | NO   | PRI | NULL    |       |
| name  | varchar(128) | YES  |     | NULL    |       |
+-------+--------------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

表结构中 key列多了 **PRI** 属性



查看标准定义语句

```
mysql> show create table test_key\G;
*************************** 1. row ***************************
       Table: test_key
Create Table: CREATE TABLE `test_key` (
  `id` int(11) NOT NULL COMMENT '主键',									##  自动加上了not null
  `name` varchar(128) DEFAULT NULL,
  PRIMARY KEY (`id`)																		## 主键在字段下方定义了
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```



因此也可以这样定义

```
mysql> create table test(id int, primary key(id));
Query OK, 0 rows affected (0.01 sec)

mysql> desc test;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | NO   | PRI | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.00 sec)
```





#### 基本效果

唯一标识,不能重复

```
mysql> insert into test_key values(1,'zhang');
Query OK, 1 row affected (0.00 sec)

mysql> insert into test_key values(1,'li');
ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
```



#### 删除主键

```
mysql> alter table test drop primary key;
Query OK, 0 rows affected (0.04 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc test;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | NO   |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.00 sec)
```



#### 追加主键

```
mysql> alter table test add primary key(id);
Query OK, 0 rows affected (0.03 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc test;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| id    | int(11) | NO   | PRI | NULL    |       |
+-------+---------+------+-----+---------+-------+
1 row in set (0.00 sec)
```



注意,添加主键时,需要先保证该字段没有重复的数据出现(因此最好是在 建表前/插入数据前 把主键确定下来)

```
mysql> alter table test drop primary key;												## 先删除主键
Query OK, 0 rows affected (0.33 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> insert into test values(1);															## 插入两个重复数据
Query OK, 1 row affected (0.01 sec)

mysql> insert into test values(1);
Query OK, 1 row affected (0.01 sec)

mysql> alter table test add primary key(id);
ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'				## 添加主键

## 翻译: 重复 条目 1 对于主键
```



#### 复合主键

主键不仅仅能设置一个字段,还能多个字段构成一个主键

##### 语法

复合主键只能使用单独定义主键的方式定义

```
mysql> create table pick_course(													## 选课表(测试用,不一定符合实际业务)
	sid int unsigned comment '学生学号', 
	cid int unsigned comment'课程号', 
	score tinyint unsigned, primary key(sid,cid)
	);
Query OK, 0 rows affected (0.01 sec)

mysql> desc pick_course;
+-------+---------------------+------+-----+---------+-------+
| Field | Type                | Null | Key | Default | Extra |
+-------+---------------------+------+-----+---------+-------+
| sid   | int(10) unsigned    | NO   | PRI | NULL    |       |
| cid   | int(10) unsigned    | NO   | PRI | NULL    |       |
| score | tinyint(3) unsigned | YES  |     | NULL    |       |
+-------+---------------------+------+-----+---------+-------+
3 rows in set (0.00 sec)
```

这种两个以上主键标识的就是复合主键,复合主键的性质和一般主键是一样的,保证列复合结果不重复即可



### 自增长 auto_increment

auto_increment：当对应的字段，不给值，会自动的被系统触发，系统会从当前字段中**已经有的最大值** 
+1操作，得到一个新的不同的值。要**搭配主键**使用，作为逻辑主键。

自增长的特点: 

1. 任何一个字段要做自增长，前提是本身是一个索引（key一栏有值）
2. 自增长字段必须是整数 
3. 一张表最多**只能有一个自增长**
4. 默认从1开始



基本语法

```
mysql> create table t3(id int auto_increment, name varchar(32), primary key(id)  );
Query OK, 0 rows affected (0.01 sec)
```





#### 验证自增长属性的特点

##### 自增长基准值变化

建个带自增长属性的表

```
mysql> create table t3(id int auto_increment, name varchar(32), primary key(id)  );
Query OK, 0 rows affected (0.01 sec)

mysql> show create table t3\G;
*************************** 1. row ***************************
       Table: t3
Create Table: CREATE TABLE `t3` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```



给表添加两个数据,全列插入

```
mysql> insert into t3 (id,name) values(10,'a');
Query OK, 1 row affected (0.00 sec)

mysql> insert into t3 (id,name) values(1,'a');
Query OK, 1 row affected (0.00 sec)

mysql> select * from t3;
+----+------+
| id | name |
+----+------+
|  1 | a    |
| 10 | a    |
+----+------+
2 rows in set (0.00 sec)
```

此时查看表的结构

```
mysql> show create table t3\G;
*************************** 1. row ***************************
       Table: t3
Create Table: CREATE TABLE `t3` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8		## 多了一个属性 auto_increment=11
1 row in set (0.00 sec)
```





再插入一个数据,只对name插入,让id自增长

```
mysql> insert into t3 (name) values('b');
Query OK, 1 row affected (0.00 sec)

mysql> select * from t3;
+----+------+
| id | name |
+----+------+
|  1 | a    |
| 10 | a    |
| 11 | b    |
+----+------+
3 rows in set (0.00 sec)

mysql> show create table t3\G;
*************************** 1. row ***************************
       Table: t3
Create Table: CREATE TABLE `t3` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=12 DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```



插入一个比较小的数据,全列插入

```
mysql> insert into t3 values(2,'c');
Query OK, 1 row affected (0.00 sec)

mysql> select * from t3 ;
+----+------+
| id | name |
+----+------+
|  1 | a    |
|  2 | c    |
| 10 | a    |
| 11 | b    |
+----+------+
4 rows in set (0.00 sec)

mysql> show create table t3\G;
*************************** 1. row ***************************
       Table: t3
Create Table: CREATE TABLE `t3` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=12 DEFAULT CHARSET=utf8				## 自增长属性不变
1 row in set (0.00 sec)
```

自增长属性没有发生变化,因此,自增长值是一个max()值,有比他大的就能覆盖,否则不变或自增



- 删除最大元素也不会影响到自增长值

```
mysql> delete from t3 where id=11;
Query OK, 1 row affected (0.01 sec)

mysql> show create table t3\G;
*************************** 1. row ***************************
       Table: t3
Create Table: CREATE TABLE `t3` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=12 DEFAULT CHARSET=utf8				## 自增长属性不变
1 row in set (0.00 sec)
```



##### 搭配主键

```
mysql> create table t5 (id int auto_increment);
ERROR 1075 (42000): Incorrect table definition; there can be only one auto column and it must be defined as a key
## 不正确的表定义; (指代)只能有(只能只有)一个自动列;且必须定义成主键之类的
```







#### 语法

1. 默认,不指定缺省值创建

```
mysql> create table t3(
	id int auto_increment, 
	name varchar(32), 
	primary key(id)
	);
Query OK, 0 rows affected (0.01 sec)
```

2. 带自增长缺省值创建(注意写在列定义结束之后)

```
mysql> create table t4(
	id int primary key  auto_increment,
	name varchar(32)
	)auto_increment=50;
Query OK, 0 rows affected (0.01 sec)

mysql> show create table t4\G;
*************************** 1. row ***************************
       Table: t4
Create Table: CREATE TABLE `t4` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=50 DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```



#### 函数last_insert_id()

在插入后获取上次插入的值（批量插入获取的是第一个值）;

搭配auto_increment时,得到的就是自增长的值

```
mysql > select last_insert_id(); 
+------------------+
| last_insert_id() | 
+------------------+
|                1 | 
+------------------+
```

后续用例补充...



#### 浅谈索引

索引： 
    在关系数据库中，索引是一种单独的、物理的对数据库表中一列或多列的值进行排序的一种存储结构，它是某个表中一列或若干列值的集合和相应的指向表中物理标识这些值的数据页的逻辑指针清单。 
索引的作用相当于图书的目录，可以根据目录中的页码快速找到所需的内容。
    索引提供指向存储在表的指定列中的数据值的指针，然后根据您指定的排序顺序对这些指针排序。 
数据库使用索引以找到特定值，然后顺指针找到包含该值的行。这样可以使对应于表的SQL语句执行得 
更快，可快速访问数据库表中的特定信息。



### 唯一键 (unique key)

一张表中有往往有很多字段需要唯一性，数据不能重复，但是一张表中只能有一个主键：唯一键就可以解决表中有多个字段需要唯一性约束的问题。

唯一键的本质和主键差不多，唯一键允许为空，而且可以多个为空，空字段不做唯一性比较。 



#### 理解唯一键

1. 面向对象思想中,表的字段等价于对象的属性(表的各类属性 == 对象的属性), 描述表就相当于描述对象

2. 对象有众多属性,而众多属性中一定存在相当一部分是唯一的,这些唯一的属性就是表的唯一键;

   对于主键,用于标识一个对象(表),选择主键就是在众多唯一属性中选择一个作为主键;(这个唯一属性就是唯一键)



#### 与主键的差异

区别:唯一键可以为空(默认),也可以设置为非空(这样就和主键很像了)

**关于唯一键和主键的区别：** 
我们可以简单理解成，主键更多的是标识唯一性的。而唯一键更多的是保证在业务上，不要和别的信息出现重复。乍一听好像没啥区别

举一个例子

>假设一个场景
>比如在公司，我们需要一个员工管理系统，系统中有一个员工表，员工表中有两列信息，一个身份证号码，一个是员工工号，我们可以选择身份号码作为主键。
>
>而我们设计员工工号的时候，需要一种约束：而所有的员工工号都不能重复。
>
>具体指的是在公司的业务上不能重复，我们设计表的时候，需要这个约束，那么就可以将员工工号设计成为唯一键。
>
>一般而言，我们**建议将主键设计成为和当前业务无关的字段**，这样，当业务调整的时候，我们可以尽量不会对主键做过大的调整。



#### 语法

```
mysql> create table t6(id int unique);
Query OK, 0 rows affected (0.02 sec)

mysql> create table t7(id int, unique(id));
Query OK, 0 rows affected (0.01 sec)

mysql> create table t8(id int unique key);
Query OK, 0 rows affected (0.02 sec)
```

```
mysql> show create table t6\G;
*************************** 1. row ***************************
       Table: t6
Create Table: CREATE TABLE `t6` (
  `id` int(11) DEFAULT NULL,
  UNIQUE KEY `id` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)

ERROR:
No query specified
```



### 外键（Foreign Key）

外键外腱,和动物跟腱一样,系联不同对象的跟腱

表与表之间的关联 表与表之间的约束

‌

‌**定义**‌：
外键是数据库中的一个字段，它是另一个表的主键的引用。外键用于在两个表之间建立和维护引用完整性。它确保了表中的数据与另一个表中的数据相关联，并且保持一致性和准确性。

‌**作用**‌：

1. ‌**维护数据一致性**‌：通过外键约束，可以确保一个表中的数据引用另一个表的数据时，引用的数据是存在且有效的。这有助于防止无效或孤立的数据记录。
2. ‌**实现数据表之间的关联**‌：外键使得数据库中的表能够相互关联，从而支持更复杂的查询和操作，如连接查询（JOIN）。
3. ‌**支持级联操作**‌：外键约束还支持级联操作，如级联更新和级联删除。当对一个表的主键进行更新或删除时，可以自动地更新或删除另一个表中所有引用该主键的外键记录。

‌**重要性**‌：

外键是数据库设计中不可或缺的一部分，它们帮助开发者构建和维护数据库的完整性和一致性。通过外键，我们可以确保数据的准确性，防止数据冗余，并优化数据库的查询性能。此外，外键还有助于实现数据库表之间的逻辑关系，使得数据库的结构更加清晰和易于理解。



#### 主表与从表

‌**主表（Parent Table）**‌：
在数据库中，主表通常包含其他表所引用的主键。换句话说，如果一个表的主键被其他表的外键所引用，那么这个表就被称为主表。主表在数据库关系中扮演着核心的角色，因为它包含了其他表可能需要的参照数据。

‌**从表（Child Table 或 Dependent Table）**‌：
从表，也称为子表或依赖表，是包含外键的表。外键是指向另一个表（即主表）主键的字段。从表通过外键与主表建立关联，从而实现数据的引用和完整性约束。



‌**示例**‌：

假设我们有两个表：`students`（学生表）和`courses`（课程表）。`students`表有一个`student_id`作为主键，而`courses`表有一个`course_id`作为主键。如果我们想要记录每个学生选修的课程，我们可以在`courses`表中添加一个`student_id`作为外键，它引用了`students`表的`student_id`主键。

其中`students`表就是主表,`courses`是从表;因为`courses`表引用了`students`表的主键;



#### 语法

以学生信息,班级信息,以及学生-班级的关系为例,

首先需要有一张主表(class)

```
mysql> create table class(id tinyint unsigned primary key, name varchar(32) unique key);
Query OK, 0 rows affected (0.02 sec)
```



默认外键(自动生成外键名) -- 从表(student)

```
mysql> create table student(id char(12), name varchar(32), telphone varchar(32), class_id tinyint unsigned,primary key(id),unique key(telphone),foreign key(class_id) references class (id));
Query OK, 0 rows affected (0.02 sec)

mysql> show create table student\G;
*************************** 1. row ***************************
       Table: student
Create Table: CREATE TABLE `student` (
  `id` char(12) NOT NULL,
  `name` varchar(32) DEFAULT NULL,
  `telphone` varchar(32) DEFAULT NULL,
  `class_id` tinyint(3) unsigned DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `telphone` (`telphone`),
  KEY `class_id` (`class_id`),
  CONSTRAINT `student_ibfk_1` FOREIGN KEY (`class_id`) REFERENCES `class` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)

ERROR:
No query specified
```



自定义外键名(一般默认就够了)

```
mysql> create table student(
	id char(12), 
	name varchar(32), 
	telphone varchar(32), 
	class_id tinyint unsigned,
	primary key(id),
	unique key(telphone),
	constraint my_foreign_key_name foreign key(class_id) references class (id)
	);
Query OK, 0 rows affected (0.02 sec)

mysql> show create table student\G;
*************************** 1. row ***************************
       Table: student
Create Table: CREATE TABLE `student` (
  `id` char(12) NOT NULL,
  `name` varchar(32) DEFAULT NULL,
  `telphone` varchar(32) DEFAULT NULL,
  `class_id` tinyint(3) unsigned DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `telphone` (`telphone`),
  KEY `my_foreign_key_name` (`class_id`),
  CONSTRAINT `my_foreign_key_name` FOREIGN KEY (`class_id`) REFERENCES `class` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
1 row in set (0.00 sec)
```



实用:谁引用谁,被引用的对象就是主表,引用方是从表

 





### 综合案例

有一个商店的数据，记录客户及购物情况，有以下三个表组成： 

- 商品goods(商品编号goods_id，商品名goods_name, 单价unitprice, 商品类别category,  供应商
  provider) 
- 客户customer(客户号customer_id,姓名name,住址address,邮箱email,性别sex，身份证card_id) 
- 购买purchase(购买订单号order_id,客户号customer_id,商品号goods_id,购买数量nums)



要求：

- 每个表的主外键 
- 客户的姓名不能为空值 
- 邮箱不能重复 
- 客户的性别(男，女)



```
-- 创建数据库
create database if not exists bit32mall 
default character set utf8 ;

-- 选择数据库 
use bit32mall;

-- 创建数据库表
-- 商品
create table if not exists goods
(
    goods_id  int primary key auto_increment comment '商品编号', 
    goods_name varchar(32) not null comment '商品名称',
    unitprice  int  not null default 0  comment '单价，单位分', 
    category  varchar(12) comment '商品分类',
    provider  varchar(64) not null comment '供应商名称'
);

-- 客户
create table if not exists customer
(
    customer_id  int primary key auto_increment comment '客户编号', 
    name varchar(32) not null comment '客户姓名',
    address  varchar(256)  comment '客户地址',
    email  varchar(64)  unique key comment '电子邮箱',
        sex  enum('男','女') not null comment '性别', 
    card_id char(18) unique key comment '身份证'
);

-- 购买
create table if not exists purchase
(
    order_id  int primary key auto_increment comment '订单号', 
    customer_id int comment '客户编号',
    goods_id  int  comment '商品编号',  
    nums  int  default 0 comment '购买数量',
    foreign key (customer_id) references customer(customer_id), 
    foreign key (goods_id) references goods(goods_id)
);
```

