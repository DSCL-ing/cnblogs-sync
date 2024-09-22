[toc]

## 表的增删改查 

CRUD : Create(创建), Retrieve(读取)，Update(更新)，Delete（删除）

### Create 

create == insert

标准语法:

```
INSERT [INTO] table_name 
    [(column [, column] ...)] 
    VALUES (value_list) [, (value_list)] ... 
    
其中value_list: value, [, value] ...
```

案例:

```
-- 创建一张学生表
CREATE TABLE students (
    id INT UNSIGNED PRIMARY KEY AUTO_INCREMENT,
    sn INT NOT NULL UNIQUE COMMENT '学号',
    name VARCHAR(20) NOT NULL, 
    qq VARCHAR(20)
);
```



#### 指定列插入

```
INSERT INTO students(id,sn,name,qq) VALUES (100, 10000, '唐三藏', NULL);
Query OK, 1 row affected (0.02 sec)

INSERT INTO students(id,sn,name,qq) VALUES (101, 10001, '孙悟空', '11111');
Query OK, 1 row affected (0.02 sec)

-- 查看插入结果
SELECT * FROM students; 
+-----+-------+-----------+-------+
| id  | sn    | name      | qq    | 
+-----+-------+-----------+-------+
| 100 | 10000 | 唐三藏     | NULL  | 
| 101 | 10001 | 孙悟空     | 11111 |
+-----+-------+-----------+-------+
2 rows in set (0.00 sec)
```



#### 单行数据+全列插入

全列插入时可以省略列名,表示每个列都插入

```
value_list 数量必须和定义表的列的数量及顺序一致

INSERT INTO students VALUES (100, 10000, '唐三藏', NULL);
Query OK, 1 row affected (0.02 sec)

INSERT INTO students VALUES (101, 10001, '孙悟空', '11111');
Query OK, 1 row affected (0.02 sec)

-- 查看插入结果
SELECT * FROM students; 
+-----+-------+-----------+-------+
| id  | sn    | name      | qq    | 
+-----+-------+-----------+-------+
| 100 | 10000 | 唐三藏     | NULL  | 
| 101 | 10001 | 孙悟空     | 11111 |
+-----+-------+-----------+-------+
2 rows in set (0.00 sec)
```



#### 多行数据+全列插入

```
INSERT INTO students (id, sn, name) VALUES 
    (102, 20001, '曹孟德'), 
    (103, 20002, '孙仲谋');
Query OK, 2 rows affected (0.02 sec) 
Records: 2  Duplicates: 0  Warnings: 0

SELECT * FROM students; 
+-----+-------+-----------+-------+
| id  | sn    | name      | qq    | 
+-----+-------+-----------+-------+
| 100 | 10000 | 唐三藏     | NULL  | 
| 101 | 10001 | 孙悟空     | 11111 | 
| 102 | 20001 | 曹孟德     | NULL  | 
| 103 | 20002 | 孙仲谋     | NULL  |
+-----+-------+-----------+-------+
4 rows in set (0.00 sec)
```



#### 插入否则更新

由于 **主键** 或者 **唯一键** 对应的值已经存在而导致插入失败

```
-- 主键冲突
INSERT INTO students (id, sn, name) VALUES (100, 10010, '唐大师');
ERROR 1062 (23000): Duplicate entry '100' for key 'PRIMARY'

-- 唯一键冲突
INSERT INTO students (sn, name) VALUES (20001, '曹阿瞒');
ERROR 1062 (23000): Duplicate entry '20001' for key 'sn'
```

可以选择性的进行同步更新操作 

标准语法：

```
INSERT ... ON DUPLICATE KEY UPDATE 
    column = value [, column = value] ... 
```

案例

```
INSERT INTO students (id, sn, name) VALUES (100, 10010, '唐大师')
    ON DUPLICATE KEY UPDATE sn = 10010, name = '唐大师';
Query OK, 2 rows affected (0.47 sec)

-- 0 row affected:      ### 表中有冲突数据，但冲突数据的值和 update 的值相等 
-- 1 row affected:      ### 表中没有冲突数据，数据被插入
-- 2 row affected:      ### 表中有冲突数据，并且数据已经被更新
-- 通过 MySQL 函数获取受到影响的数据行数
SELECT ROW_COUNT(); 
+-------------+
| ROW_COUNT() | 
+-------------+
|           2 | 
+-------------+
1 row in set (0.00 sec)

-- ON DUPLICATE KEY 当发生重复key的时候
```



#### 替换 (replace)

```
-- 主键 或者 唯一键 没有冲突，则直接插入； 
-- 主键 或者 唯一键 如果冲突，则删除后再插入
REPLACE INTO students (sn, name) VALUES (20001, '曹阿瞒');
Query OK, 2 rows affected (0.00 sec)

-- 1 row affected:      表中没有冲突数据，数据被插入 
-- 2 row affected:      表中有冲突数据，删除后重新插入
```





### Retrieve 



retrieve == query



#### 标准语法

```
SELECT  
 [DISTINCT] {* | {column [, column] ...} 
 [FROM table_name] 
 [WHERE ...] 
 [ORDER BY column [ASC | DESC], ...] 
 LIMIT ... 
```



案例:

```
-- 创建表结构 
CREATE TABLE exam_result ( 
 id INT UNSIGNED PRIMARY KEY AUTO_INCREMENT, 
 name VARCHAR(20) NOT NULL COMMENT '同学姓名', 
 chinese float DEFAULT 0.0 COMMENT '语文成绩', 
 math float DEFAULT 0.0 COMMENT '数学成绩', 
 english float DEFAULT 0.0 COMMENT '英语成绩' 
); 
 
-- 插入测试数据 
INSERT INTO exam_result (name, chinese, math, english) VALUES 
 ('唐三藏', 67, 98, 56), 
 ('孙悟空', 87, 78, 77), 
 ('猪悟能', 88, 98, 90), 
 ('曹孟德', 82, 84, 67), 
 ('刘玄德', 55, 85, 45), 
 ('孙权', 70, 73, 78), 
 ('宋公明', 75, 65, 30); 
Query OK, 7 rows affected (0.00 sec) 
Records: 7  Duplicates: 0  Warnings: 0 
```



#### SELECT列

> 查询结果返回表格,表现形式为笛卡尔积:详见数据库原理

##### 全列查询

```
-- 通常情况下不建议使用 * 进行全列查询 
-- 1. 查询的列越多，意味着需要传输的数据量越大； 
-- 2. 可能会影响到索引的使用。
 
SELECT * FROM exam_result; 
+----+-----------+-------+--------+--------+ 
| id | name      | chinese | math | english | 
+----+-----------+-------+--------+--------+ 
|  1 | 唐三藏     |    67 |     98 |     56 | 
|  2 | 孙悟空     |    87 |     78 |     77 | 
|  3 | 猪悟能     |    88 |     98 |     90 | 
|  4 | 曹孟德     |    82 |     84 |     67 | 
|  5 | 刘玄德     |    55 |     85 |     45 | 
|  6 | 孙权       |    70 |     73 |     78 | 
|  7 | 宋公明     |    75 |     65 |     30 | 
+----+-----------+-------+--------+--------+ 
7 rows in set (0.00 sec) 
```





##### 限制显示条目 limit (分页查询)

> 网页中每页 3 条记录: 按 id 进行分页，分别显示 第 1、2、3 页 

###### 基本语法:

```
-- 起始下标为 0 
 
-- 从 s 开始，筛选 n 条结果 
SELECT ... FROM table_name [WHERE ...] [ORDER BY ...] LIMIT s, n

-- 从 0 开始，筛选 n 条结果
SELECT ... FROM table_name [WHERE ...] [ORDER BY ...] LIMIT n;; 

-- 从 s 开始，筛选 n 条结果，比第二种用法更明确，建议使用 
SELECT ... FROM table_name [WHERE ...] [ORDER BY ...] LIMIT n OFFSET s; 
```



基本案例:

- 显示前四条数据(默认从0开始)

```
mysql> select name, math+english+chinese total from exam_result limit 4;
+--------+-------+
| name   | total |
+--------+-------+
| 唐三藏 |   221 |
| 孙悟空 |   242 |
| 猪悟能 |   276 |
| 曹孟德 |   233 |
+--------+-------+
4 rows in set (0.02 sec)
```



- 从下标为2开始,显示4条数据

```
mysql> select id, name, math+english+chinese total from exam_result limit 4 offset 2;
+----+--------+-------+
| id | name   | total |
+----+--------+-------+
|  3 | 猪悟能 |   276 |
|  4 | 曹孟德 |   233 |
|  5 | 刘玄德 |   185 |
|  6 | 孙权   |   221 |
+----+--------+-------+
4 rows in set (0.02 sec)
```

- 以区间方式 从下标为2开始,显示4条数据

```
mysql> select id, name, math+english+chinese total from exam_result limit 2,4;
+----+--------+-------+
| id | name   | total |
+----+--------+-------+
|  3 | 猪悟能 |   276 |
|  4 | 曹孟德 |   233 |
|  5 | 刘玄德 |   185 |
|  6 | 孙权   |   221 |
+----+--------+-------+
4 rows in set (0.02 sec)
```

> limit 是左闭右开区间

> select * 时,如果未知总数量,最好限制一下回显条目数量,大约在1000条即可







##### 指定列查询

```
-- 指定列的顺序不需要按定义表的顺序来 
 
SELECT id, name, english FROM exam_result; 
+----+-----------+--------+ 
| id | name      | english | 
+----+-----------+--------+ 
|  1 | 唐三藏     |     56 | 
|  2 | 孙悟空     |     77 | 
|  3 | 猪悟能     |     90 | 
|  4 | 曹孟德     |     67 | 
|  5 | 刘玄德     |     45 | 
|  6 | 孙权       |     78 | 
|  7 | 宋公明     |     30 | 
+----+-----------+--------+ 
7 rows in set (0.00 sec) 
```



##### select 查询字段为表达式

以下案例能够说明 select能够计算各种表达式

- 常量表达式

```
mysql> select 10 from exam_result;
+----+
| 10 |
+----+
| 10 |
| 10 |
| 10 |
| 10 |
| 10 |
| 10 |
| 10 |
+----+
7 rows in set (0.00 sec)
```

- 常量表达式**笛卡尔积**

```
mysql> select id,name,10 from exam_result;
+----+-----------+----+
| id | name      | 10 |
+----+-----------+----+
|  1 | 唐三藏    | 10 |
|  2 | 孙悟空    | 10 |
|  3 | 猪悟能    | 10 |
|  4 | 曹孟德    | 10 |
|  5 | 刘玄德    | 10 |
|  6 | 孙权      | 10 |
|  7 | 宋公明    | 10 |
+----+-----------+----+
7 rows in set (0.00 sec)
```

- 基本数值运算

```
mysql> select id,name,1+1 from exam_result;
+----+-----------+-----+
| id | name      | 1+1 |
+----+-----------+-----+
|  1 | 唐三藏    |   2 |
|  2 | 孙悟空    |   2 |
|  3 | 猪悟能    |   2 |
|  4 | 曹孟德    |   2 |
|  5 | 刘玄德    |   2 |
|  6 | 孙权      |   2 |
|  7 | 宋公明    |   2 |
+----+-----------+-----+
7 rows in set (0.00 sec)
```

- 能够计算1+1,则也能够计算字段值的运算

```
mysql> select id,name,math+100 from exam_result;
+----+-----------+----------+
| id | name      | math+100 |
+----+-----------+----------+
|  1 | 唐三藏    |      198 |
|  2 | 孙悟空    |      178 |
|  3 | 猪悟能    |      198 |
|  4 | 曹孟德    |      184 |
|  5 | 刘玄德    |      185 |
|  6 | 孙权      |      173 |
|  7 | 宋公明    |      165 |
+----+-----------+----------+
7 rows in set (0.00 sec)
```

- 计算总成绩

```
mysql> select id,name,math+chinese+english from exam_result;
+----+-----------+----------------------+
| id | name      | math+chinese+english |
+----+-----------+----------------------+
|  1 | 唐三藏    |                  221 |
|  2 | 孙悟空    |                  242 |
|  3 | 猪悟能    |                  276 |
|  4 | 曹孟德    |                  233 |
|  5 | 刘玄德    |                  185 |
|  6 | 孙权      |                  221 |
|  7 | 宋公明    |                  170 |
+----+-----------+----------------------+
7 rows in set (0.00 sec)
```



##### 表达式重命名

如果计算成绩的综合,会发现表达式`math+chinese+english`的返回的结果表格中列名很长;可以使用`as`对表达式进行重命名.

```
mysql> select id,name,math+chinese+english as total from exam_result;
+----+-----------+-------+
| id | name      | total |
+----+-----------+-------+
|  1 | 唐三藏    |   221 |
|  2 | 孙悟空    |   242 |
|  3 | 猪悟能    |   276 |
|  4 | 曹孟德    |   233 |
|  5 | 刘玄德    |   185 |
|  6 | 孙权      |   221 |
|  7 | 宋公明    |   170 |
+----+-----------+-------+
7 rows in set (0.00 sec)
```



还可以`省略as`

```
mysql> select id 编号,name 姓名,math+chinese+english total from exam_result;
+--------+-----------+-------+
| 编号   | 姓名      | total |
+--------+-----------+-------+
|      1 | 唐三藏    |   221 |
|      2 | 孙悟空    |   242 |
|      3 | 猪悟能    |   276 |
|      4 | 曹孟德    |   233 |
|      5 | 刘玄德    |   185 |
|      6 | 孙权      |   221 |
|      7 | 宋公明    |   170 |
+--------+-----------+-------+
7 rows in set (0.00 sec)
```



##### 去重

- 原数据:

```
mysql> select math from exam_result;
+------+
| math |
+------+
|   98 |
|   78 |
|   98 |
|   84 |
|   85 |
|   73 |
|   65 |
+------+
7 rows in set (0.00 sec)
```

- 去重数据

```
mysql> select distinct math from exam_result;
+------+
| math |
+------+
|   98 |
|   78 |
|   84 |
|   85 |
|   73 |
|   65 |
+------+
6 rows in set (0.00 sec)
```



#### WHERE 条件

> 相当于 if

##### 比较运算符

| 运算符            | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| >, >=, <, <=      | 大于，大于等于，小于，小于等于                               |
| =                 | 等于，NULL 不安全，例如 NULL = NULL 的结果是 NULL            |
| <=>               | 等于，NULL 安全，例如 NULL <=> NULL 的结果是 TRUE(1)         |
| !=, <>            | 不等于                                                       |
| between a0 AND a1 | 范围匹配，[a0, a1]，如果 a0 <= value <= a1，返回TRUE(1)      |
| IN (option, ...)  | 如果是 option 中的任意一个，返回 TRUE(1)                     |
| IS NULL           | 是 NULL                                                      |
| IS NOT NULL       | 不是 NULL                                                    |
| LIKE              | 模糊匹配。% 表示任意多个（包括 0 个）任意字符；_ 表示任意一个字符 |



##### 逻辑运算符

| 运算符 | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| AND    | 多个条件必须都为 TRUE(1)，结果才是 TRUE(1); 逻辑与,相当于&&  |
| OR     | 任意一个条件为 TRUE(1), 结果为 TRUE(1);     逻辑或,相当于\|\| |
| NOT    | 条件为 TRUE(1)，结果为 FALSE(0);            逻辑非,相当于 !  |



##### 案例:

- 英语不及格的同学及英语成绩 ( < 60 ) 

- 语文成绩在 [80, 90] 分的同学及语文成绩

  ```
  mysql> select chinese from exam_result where chinese>=60 and chinese<90;
  +---------+
  | chinese |
  +---------+
  |      67 |
  |      87 |
  |      88 |
  |      82 |
  |      70 |
  |      75 |
  +---------+
  6 rows in set (0.00 sec)
  ```

  - between

  ```
  mysql> select chinese from exam_result where chinese between 60 and 89;
  +---------+
  | chinese |
  +---------+
  |      67 |
  |      87 |
  |      88 |
  |      82 |
  |      70 |
  |      75 |
  +---------+
  6 rows in set (0.00 sec)
  ```

- 数学成绩是 58 或者 59 或者 98 或者 99 分的同学及数学成绩

  ```
  mysql> select name 姓名, math 数学 from exam_result WHERE math=58 or math=59 or math=98 or math=99;
  +-----------+--------+
  | 姓名      | 数学   |
  +-----------+--------+
  | 唐三藏    |     98 |
  | 猪悟能    |     98 |
  +-----------+--------+
  2 rows in set (0.00 sec)
  ```

  - in

  ```
  mysql> select name 姓名, math 数学 from exam_result WHERE math in (58,59,98,99);
  +-----------+--------+
  | 姓名      | 数学   |
  +-----------+--------+
  | 唐三藏    |     98 |
  | 猪悟能    |     98 |
  +-----------+--------+
  2 rows in set (0.00 sec)
  ```

  

- 姓孙的同学 ; 孙X同学 (X为一个汉字)

  ```
  mysql> select name 姓名 FROM exam_result WHERE name  like "孙%" ;
  +-----------+
  | 姓名      |
  +-----------+
  | 孙悟空    |
  | 孙权      |
  +-----------+
  2 rows in set (0.00 sec)
  ```

  ```
  mysql> select name 姓名 FROM exam_result WHERE name  like "孙_" ;
  +--------+
  | 姓名   |
  +--------+
  | 孙权   |
  +--------+
  1 row in set (0.00 sec)
  ```

  

- 语文成绩好于英语成绩的同学 

  ```
  mysql> SELECT chinese 语文, english 英语 FROM exam_result WHERE chinese > english;
  +--------+--------+
  | 语文   | 英语   |
  +--------+--------+
  |     67 |     56 |
  |     87 |     77 |
  |     82 |     67 |
  |     55 |     45 |
  |     75 |     30 |
  +--------+--------+
  5 rows in set (0.00 sec)
  ```

- 总分在 200 分以下的同学

  ```
  mysql> SELECT name 姓名, chinese+math+english total  FROM exam_result WHERE total<200;
  ERROR 1054 (42S22): Unknown column 'total' in 'where clause'
  mysql> SELECT name 姓名, chinese+math+english total  FROM exam_result WHERE chinese+math+english<200;
  +-----------+-------+
  | 姓名      | total |
  +-----------+-------+
  | 刘玄德    |   185 |
  | 宋公明    |   170 |
  +-----------+-------+
  2 rows in set (0.00 sec)
  ```

  > - 不能使用别名计算,别名只在返回结果的列名生效;

  ![image-20240910140853526](MySQL%20CRUD%E4%B8%8E%E5%A4%8D%E5%90%88%E6%9F%A5%E8%AF%A2.assets/image-20240910140853526.png)

- 语文成绩 > 80 并且不姓孙的同学

  ```
  mysql> SELECT name 姓名, chinese 语文  FROM exam_result WHERE name not like '孙%' and chinese>80;
  +-----------+--------+
  | 姓名      | 语文   |
  +-----------+--------+
  | 猪悟能    |     88 |
  | 曹孟德    |     82 |
  +-----------+--------+
  2 rows in set (0.00 sec)
  ```

  

- 孙X同学，否则要求总成绩 > 200 并且 语文成绩 < 数学成绩 并且 英语成绩 > 80 

> 实际就是 孙X同学 或 总成绩 > 200 并且 语文成绩 < 数学成绩 并且 英语成绩 > 80

```
mysql> SELECT *,chinese+math+english total FROM exam_result WHERE name like '孙_' or (chinese+math+english>200 and chinese<math and english>80);
+----+-----------+---------+------+---------+-------+
| id | name      | chinese | math | english | total |
+----+-----------+---------+------+---------+-------+
|  3 | 猪悟能    |      88 |   98 |      90 |   276 |
|  6 | 孙权      |      70 |   73 |      78 |   221 |
+----+-----------+---------+------+---------+-------+
2 rows in set (0.00 sec)
```



#### 结果排序 Order by

##### 基本语法

```
-- ASC 为升序（从小到大） 			## ascending order
-- DESC 为降序（从大到小） 		## descending order
-- 默认为 ASC 
 
SELECT ... FROM table_name [WHERE ...]  
 ORDER BY column [ASC|DESC], [...]; 
```

注意: 没有 **ORDER BY** 子句的查询，返回的顺序是未定义的，永远不要依赖这个顺序

> descend缩写与descript缩写相同

##### 基本案例:

- 同学及数学成绩，按数学成绩升序显示

```
mysql> select name,math from exam_result order by math asc;
+--------+------+
| name   | math |
+--------+------+
| 宋公明 |   65 |
| 孙权   |   73 |
| 孙悟空 |   78 |
| 曹孟德 |   84 |
| 刘玄德 |   85 |
| 唐三藏 |   98 |
| 猪悟能 |   98 |
+--------+------+
7 rows in set (0.02 sec)

## 降序
mysql> select name,math from exam_result order by math desc;
+--------+------+
| name   | math |
+--------+------+
| 唐三藏 |   98 |
| 猪悟能 |   98 |
| 刘玄德 |   85 |
| 曹孟德 |   84 |
| 孙悟空 |   78 |
| 孙权   |   73 |
| 宋公明 |   65 |
+--------+------+
7 rows in set (0.02 sec)
```

- NULL 视为比任何值都小，升序出现在最上面，降序出现在最下面

```
mysql> select * from class order by name asc;
+----+------+
| id | name |
+----+------+
|  3 | NULL |
|  1 | a    |
|  2 | b    |
+----+------+
3 rows in set (0.02 sec)

mysql> select * from class order by name desc;
+----+------+
| id | name |
+----+------+
|  2 | b    |
|  1 | a    |
|  3 | NULL |
+----+------+
3 rows in set (0.02 sec)
```



- 多字段排序，排序优先级随书写顺序 (相同时怎么排)

查询同学各门成绩，依次按 数学降序，英语降序，语文降序的方式显示(

```
mysql> select name, math, english, chinese from exam_result order by math desc;
+--------+------+---------+---------+
| name   | math | english | chinese |
+--------+------+---------+---------+
| 唐三藏 |   98 |      56 |      67 |
| 猪悟能 |   98 |      90 |      88 |
| 刘玄德 |   85 |      45 |      55 |
| 曹孟德 |   84 |      67 |      82 |
| 孙悟空 |   78 |      77 |      87 |
| 孙权   |   73 |      78 |      70 |
| 刘备   |   73 |      78 |      76 |
| 宋公明 |   65 |      30 |      75 |
+--------+------+---------+---------+
8 rows in set (0.02 sec)

mysql> select name, math, english, chinese from exam_result order by math desc, english desc;
+--------+------+---------+---------+
| name   | math | english | chinese |
+--------+------+---------+---------+
| 猪悟能 |   98 |      90 |      88 |
| 唐三藏 |   98 |      56 |      67 |
| 刘玄德 |   85 |      45 |      55 |
| 曹孟德 |   84 |      67 |      82 |
| 孙悟空 |   78 |      77 |      87 |
| 孙权   |   73 |      78 |      70 |
| 刘备   |   73 |      78 |      76 |
| 宋公明 |   65 |      30 |      75 |
+--------+------+---------+---------+
8 rows in set (0.02 sec)

mysql> select name, math, english, chinese from exam_result order by math desc, english desc, chinese desc;
+--------+------+---------+---------+
| name   | math | english | chinese |
+--------+------+---------+---------+
| 猪悟能 |   98 |      90 |      88 |
| 唐三藏 |   98 |      56 |      67 |
| 刘玄德 |   85 |      45 |      55 |
| 曹孟德 |   84 |      67 |      82 |
| 孙悟空 |   78 |      77 |      87 |
| 刘备   |   73 |      78 |      76 |
| 孙权   |   73 |      78 |      70 |
| 宋公明 |   65 |      30 |      75 |
+--------+------+---------+---------+
8 rows in set (0.02 sec)
```

- 可以使用列别名

order by属于对结果进行处理了,即在select之后,因此可以使用别名

> 一定是先有数据,才能进行排序

```
mysql> select name, math+english+chinese total from exam_result order by total;
+--------+-------+
| name   | total |
+--------+-------+
| 宋公明 |   170 |
| 刘玄德 |   185 |
| 唐三藏 |   221 |
| 孙权   |   221 |
| 刘备   |   227 |
| 曹孟德 |   233 |
| 孙悟空 |   242 |
| 猪悟能 |   276 |
+--------+-------+
8 rows in set (0.02 sec)
```



### Update

update是比较危险的行为,使用时要谨慎;

体现在:如果忘记添加条件,可能会导致所有数据被覆盖;



#### 语法

```
UPDATE table_name SET column = expr [, column = expr ...] 	##express为表达式
 [WHERE ...] [ORDER BY ...] [LIMIT ...] 
```

>  update 的基本原理是: 筛选出数据,再对筛选出的数据做修改;
>  	相当于update隐藏执行了一系列select操作,语法后的where,order by,limit都是提供给select使用;最后再执行update操作;

#### 基本案例

- 将孙悟空同学的数学成绩变更为 80 分(一次更新一列)

```
mysql> select name, math from score where name='孙悟空';
+--------+------+
| name   | math |
+--------+------+
| 孙悟空 |   78 |
+--------+------+
1 row in set (0.02 sec)

mysql> update score set math=80 where name='孙悟空';
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select name, math from score where name='孙悟空';
+--------+------+
| name   | math |
+--------+------+
| 孙悟空 |   80 |
+--------+------+
1 row in set (0.02 sec)
```



- 将曹孟德同学的数学成绩变更为 60 分，语文成绩变更为 70 分(一次更新多列)

````
```
mysql> select name, math, chinese from score where name='曹孟德';
+--------+------+---------+
| name   | math | chinese |
+--------+------+---------+
| 曹孟德 |   84 |      82 |
+--------+------+---------+
1 row in set (0.02 sec)

mysql> update score set math=60,chinese=70 where name='曹孟德';
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> select name, math, chinese from score where name='曹孟德';
+--------+------+---------+
| name   | math | chinese |
+--------+------+---------+
| 曹孟德 |   60 |      70 |
+--------+------+---------+
1 row in set (0.02 sec)
````




- 将总成绩倒数前三的 3 位同学的数学成绩加上 30 分

````mysql
## 查看倒数前三的信息
mysql> select name, math, chinese+math+english total from score order by total asc limit 3;
+--------+------+-------+
| name   | math | total |
+--------+------+-------+
| 宋公明 |   65 |   170 |
| 刘玄德 |   85 |   185 |
| 曹孟德 |   60 |   197 |
+--------+------+-------+
3 rows in set (0.02 sec)


mysql> update score set math=math+30 order by math+chinese+english asc limit 3;
Query OK, 3 rows affected (0.02 sec)
Rows matched: 3  Changed: 3  Warnings: 0

mysql> select name, math, chinese+math+english total from score order by total asc limit 3;
+--------+------+-------+
| name   | math | total |
+--------+------+-------+
| 宋公明 |   95 |   200 |
| 刘玄德 |  115 |   215 |
| 唐三藏 |   98 |   221 |
+--------+------+-------+
3 rows in set (0.02 sec)
````



- 将所有同学的语文成绩更新为原来的 2 倍 

> 注意：更新全表的语句慎用！
>
> 没有 条件 子句，则更新全表

```
mysql> select name, chinese from score;
+-----------+---------+
| name      | chinese |
+-----------+---------+
| 唐三藏    |      67 |
| 孙悟空    |      87 |
| 猪悟能    |      88 |
| 曹孟德    |      70 |
| 刘玄德    |      55 |
| 孙权      |      70 |
| 宋公明    |      75 |
| 刘备      |      76 |
+-----------+---------+
8 rows in set (0.00 sec)

mysql> update score set chinese = chinese*2;
Query OK, 8 rows affected (0.00 sec)
Rows matched: 8  Changed: 8  Warnings: 0

mysql> select name, chinese from score;
+-----------+---------+
| name      | chinese |
+-----------+---------+
| 唐三藏    |     134 |
| 孙悟空    |     174 |
| 猪悟能    |     176 |
| 曹孟德    |     140 |
| 刘玄德    |     110 |
| 孙权      |     140 |
| 宋公明    |     150 |
| 刘备      |     152 |
+-----------+---------+
8 rows in set (0.00 sec)
```



### Delete

#### 语法

```
DELETE FROM  table_name [WHERE ...] [ORDER BY ...] [LIMIT ...] 
```

> 语法类似Update, Update需要修改字段,因此多了set; Delete只会删除整行,只需确定哪些行即可.

#### 基本案例

- 删除孙悟空同学的考试成绩 

```mysql
mysql> delete from score where name = '孙悟空';
Query OK, 1 row affected (0.00 sec)
```

#### delete删除整张表数据

注意：删除整表操作要慎用！

```
mysql> alter table score modify id int unsigned auto_increment not null unique key;
Query OK, 7 rows affected (0.04 sec)
Records: 7  Duplicates: 0  Warnings: 0

mysql> show create table score\G;
*************************** 1. row ***************************
       Table: score
Create Table: CREATE TABLE `score` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL COMMENT '同学姓名',
  `chinese` float DEFAULT '0' COMMENT '语文成绩',
  `math` float DEFAULT '0' COMMENT '数学成绩',
  `english` float DEFAULT '0' COMMENT '英语成绩',
  UNIQUE KEY `id` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8
1 row in set (0.00 sec)


mysql> delete from score;
Query OK, 7 rows affected (0.00 sec)

mysql> show create table score\G;
*************************** 1. row ***************************
       Table: score
Create Table: CREATE TABLE `score` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL COMMENT '同学姓名',
  `chinese` float DEFAULT '0' COMMENT '语文成绩',
  `math` float DEFAULT '0' COMMENT '数学成绩',
  `english` float DEFAULT '0' COMMENT '英语成绩',
  UNIQUE KEY `id` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8				## 表格信息不变,只删除了数据
1 row in set (0.00 sec)

```



#### truncate删除整张表数据(截断表)

##### 语法：

```mysql
TRUNCATE [TABLE] table_name 
```

##### 注意：

这个操作慎用

1. 只能对整表操作，不能像 DELETE 一样针对部分数据操作；
2. 实际上 MySQL 不对数据操作，所以比 DELETE 更快，但是TRUNCATE在删除数据的时候，并不经过真正的事
   物，所以无法回滚
3. 会重置 AUTO_INCREMENT 项

##### 案例

删除前的表信息

```mysql
mysql> select * from score;
+----+-----------+---------+------+---------+
| id | name      | chinese | math | english |
+----+-----------+---------+------+---------+
|  1 | 唐三藏    |      67 |   98 |      56 |
|  2 | 孙悟空    |      87 |   78 |      77 |
|  3 | 猪悟能    |      88 |   98 |      90 |
|  4 | 曹孟德    |      82 |   84 |      67 |
|  5 | 刘玄德    |      55 |   85 |      45 |
|  6 | 孙权      |      70 |   73 |      78 |
|  7 | 宋公明    |      75 |   65 |      30 |
|  8 | 刘备      |      76 |   73 |      78 |
+----+-----------+---------+------+---------+
8 rows in set (0.00 sec)

mysql> show create table score\G;
*************************** 1. row ***************************
       Table: score
Create Table: CREATE TABLE `score` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL COMMENT '同学姓名',
  `chinese` float DEFAULT '0' COMMENT '语文成绩',
  `math` float DEFAULT '0' COMMENT '数学成绩',
  `english` float DEFAULT '0' COMMENT '英语成绩',
  UNIQUE KEY `id` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8
1 row in set (0.00 sec)

```

删除后

```mysql
mysql> truncate  score;
Query OK, 0 rows affected (0.01 sec)

mysql> select * from score;
Empty set (0.00 sec)

mysql> show create table score\G;
*************************** 1. row ***************************
       Table: score
Create Table: CREATE TABLE `score` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL COMMENT '同学姓名',
  `chinese` float DEFAULT '0' COMMENT '语文成绩',
  `math` float DEFAULT '0' COMMENT '数学成绩',
  `english` float DEFAULT '0' COMMENT '英语成绩',
  UNIQUE KEY `id` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8										## 表被初始化了
1 row in set (0.00 sec)
```



### 查询结果插入 Insert ... Select ...

语法

```
INSERT INTO table_name [(column [, column ...])] SELECT ... 
```

案例：删除表中的的重复复记录，重复的数据只能有一份

```
创建原数据表 
 
CREATE TABLE duplicate_table (id int, name varchar(20)); 
Query OK, 0 rows affected (0.01 sec) 
 
-- 插入测试数据 
INSERT INTO duplicate_table VALUES 
 (100, 'aaa'), 
 (100, 'aaa'), 
 (200, 'bbb'), 
 (200, 'bbb'), 
 (200, 'bbb'), 
 (300, 'ccc'); 
Query OK, 6 rows affected (0.00 sec) 
Records: 6  Duplicates: 0  Warnings: 0 
```

步骤:

```
-- 创建一张空表 no_duplicate_table，结构和 duplicate_table 一样 
CREATE TABLE no_duplicate_table LIKE duplicate_table; 
Query OK, 0 rows affected (0.00 sec) 

-- 将 duplicate_table 的去重数据插入到 no_duplicate_table 
INSERT INTO no_duplicate_table SELECT DISTINCT * FROM duplicate_table; 
Query OK, 3 rows affected (0.00 sec) 
Records: 3  Duplicates: 0  Warnings: 0 
 
-- 通过重命名表，实现原子的去重操作 
RENAME TABLE duplicate_table TO old_duplicate_table,  
 no_duplicate_table TO duplicate_table; 
Query OK, 0 rows affected (0.00 sec) 
 
-- 查看最终结果 
SELECT * FROM duplicate_table; 
+------+------+ 
| id   | name | 
+------+------+ 
|  100 | aaa  | 
|  200 | bbb  | 
|  300 | ccc  | 
+------+------+ 
3 rows in set (0.00 sec) 
```

> 传输文件相关操作时(拷贝,移动),推荐做法是先复制一份临时文件,最后以重命名方式就能实现原子操作.



### 分组聚合查询

#### 分组

>  在select中使用group by 子句可以对指定列进行分组查询

语法:

```
select column1, column2, .. from table group by column; 
```





#### 聚合函数

| 函数                   | 说明                                        |
| ---------------------- | ------------------------------------------- |
| COUNT([DISTINCT] expr) | 返回查询到的数据的 数量                     |
| SUM([DISTINCT] expr)   | 返回查询到的数据的 总和，不是数字没有意义   |
| AVG([DISTINCT] expr)   | 返回查询到的数据的 平均值，不是数字没有意义 |
| MAX([DISTINCT] expr)   | 返回查询到的数据的 最大值，不是数字没有意义 |
| MIN([DISTINCT] expr)   | 返回查询到的数据的 最小值，不是数字没有意义 |

- 服务于group by(分组)

>  不加group by 其实也是分组,只不过是单独的一单大组,可以理解为以建表约束的规则进行分组,即以原始表格直接聚合

- distinct

**去重之后**再进行聚合函数计算



#### 描述

分组:对某一组字段,不同的值为不同的组,相同的值为一组.

聚合:即合并,对某一组字段,相同的值可以合并(聚合)在一起.

分组聚合:在某种表达式条件下,对指定一组字段分组,然后分别对各组的指定的其他列进行**统计**,然后聚合一起形成一条新的记录;因为统计后得到的值在该组内都是相同的,因此该组能够合并到一起(未指定的列需要舍弃掉:因为不相同不能聚合);



分组聚合:可以理解成按分组的条件进行分表,每个组对应一个表(逻辑),分成一个个子表,然后再对各个子表进行聚合查询

> 查询结果,以及中间筛选的结果, 都可以认为是一张表(逻辑),这样的语义能够更好理解mysql

having 与 where

主要区别在于条件筛选的阶段不同

where用于对原始表格进行筛选,

having 用于对分组聚合后的表(逻辑)进行筛选,(优先级在select之后).



优先级:

from -> where -> group by -> select-> having ;





## 复合查询

准备工作，创建一个雇员信息表（来自oracle 9i的经典测试表）

- EMP员工表 
- DEPT部门表 
- SALGRADE工资等级表

### scott_data.sql

```
DROP database IF EXISTS `scott`;
CREATE database IF NOT EXISTS `scott` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

USE `scott`;

DROP TABLE IF EXISTS `dept`;
CREATE TABLE `dept` (
  `deptno` int(2) unsigned zerofill NOT NULL COMMENT '部门编号',
  `dname` varchar(14) DEFAULT NULL COMMENT '部门名称',
  `loc` varchar(13) DEFAULT NULL COMMENT '部门所在地点'
);


DROP TABLE IF EXISTS `emp`;
CREATE TABLE `emp` (
  `empno` int(6) unsigned zerofill NOT NULL COMMENT '雇员编号',
  `ename` varchar(10) DEFAULT NULL COMMENT '雇员姓名',
  `job` varchar(9) DEFAULT NULL COMMENT '雇员职位',
  `mgr` int(4) unsigned zerofill DEFAULT NULL COMMENT '雇员领导编号',
  `hiredate` datetime DEFAULT NULL COMMENT '雇佣时间',
  `sal` decimal(7,2) DEFAULT NULL COMMENT '工资月薪',
  `comm` decimal(7,2) DEFAULT NULL COMMENT '奖金',
  `deptno` int(2) unsigned zerofill DEFAULT NULL COMMENT '部门编号'
);


DROP TABLE IF EXISTS `salgrade`;
CREATE TABLE `salgrade` (
  `grade` int(11) DEFAULT NULL COMMENT '等级',
  `losal` int(11) DEFAULT NULL COMMENT '此等级最低工资',
  `hisal` int(11) DEFAULT NULL COMMENT '此等级最高工资'
);


insert into dept (deptno, dname, loc)
values (10, 'ACCOUNTING', 'NEW YORK');
insert into dept (deptno, dname, loc)
values (20, 'RESEARCH', 'DALLAS');
insert into dept (deptno, dname, loc)
values (30, 'SALES', 'CHICAGO');
insert into dept (deptno, dname, loc)
values (40, 'OPERATIONS', 'BOSTON');

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7369, 'SMITH', 'CLERK', 7902, '1980-12-17', 800, null, 20);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7499, 'ALLEN', 'SALESMAN', 7698, '1981-02-20', 1600, 300, 30);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7521, 'WARD', 'SALESMAN', 7698, '1981-02-22', 1250, 500, 30);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7566, 'JONES', 'MANAGER', 7839, '1981-04-02', 2975, null, 20);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7654, 'MARTIN', 'SALESMAN', 7698, '1981-09-28', 1250, 1400, 30);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7698, 'BLAKE', 'MANAGER', 7839, '1981-05-01', 2850, null, 30);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7782, 'CLARK', 'MANAGER', 7839, '1981-06-09', 2450, null, 10);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7788, 'SCOTT', 'ANALYST', 7566, '1987-04-19', 3000, null, 20);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7839, 'KING', 'PRESIDENT', null, '1981-11-17', 5000, null, 10);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7844, 'TURNER', 'SALESMAN', 7698,'1981-09-08', 1500, 0, 30);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7876, 'ADAMS', 'CLERK', 7788, '1987-05-23', 1100, null, 20);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7900, 'JAMES', 'CLERK', 7698, '1981-12-03', 950, null, 30);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7902, 'FORD', 'ANALYST', 7566, '1981-12-03', 3000, null, 20);

insert into emp (empno, ename, job, mgr, hiredate, sal, comm, deptno)
values (7934, 'MILLER', 'CLERK', 7782, '1982-01-23', 1300, null, 10);

insert into salgrade (grade, losal, hisal) values (1, 700, 1200);
insert into salgrade (grade, losal, hisal) values (2, 1201, 1400);
insert into salgrade (grade, losal, hisal) values (3, 1401, 2000);
insert into salgrade (grade, losal, hisal) values (4, 2001, 3000);
insert into salgrade (grade, losal, hisal) values (5, 3001, 9999);

```



### 案例

- 查询工资高于500或岗位为MANAGER的雇员，同时还要满足他们的姓名首字母为大写的J

法一:

```
mysql> select * from emp where (sal>500 or job='MANAGER') and ename like 'J%';
+--------+-------+---------+------+---------------------+---------+------+--------+
| empno  | ename | job     | mgr  | hiredate            | sal     | comm | deptno |
+--------+-------+---------+------+---------------------+---------+------+--------+
| 007566 | JONES | MANAGER | 7839 | 1981-04-02 00:00:00 | 2975.00 | NULL |     20 |
| 007900 | JAMES | CLERK   | 7698 | 1981-12-03 00:00:00 |  950.00 | NULL |     30 |
+--------+-------+---------+------+---------------------+---------+------+--------+
2 rows in set (0.00 sec)
```

法二(函数):

```
mysql> select * from emp where (sal>500 or job='MANAGER') and substring(ename,1,1)='J';
+--------+-------+---------+------+---------------------+---------+------+--------+
| empno  | ename | job     | mgr  | hiredate            | sal     | comm | deptno |
+--------+-------+---------+------+---------------------+---------+------+--------+
| 007566 | JONES | MANAGER | 7839 | 1981-04-02 00:00:00 | 2975.00 | NULL |     20 |
| 007900 | JAMES | CLERK   | 7698 | 1981-12-03 00:00:00 |  950.00 | NULL |     30 |
+--------+-------+---------+------+---------------------+---------+------+--------+
2 rows in set (0.00 sec)
```



- 按照部门号升序而雇员的工资降序排序

```
mysql> select ename,sal,deptno from emp order by deptno asc, sal desc;
+--------+---------+--------+
| ename  | sal     | deptno |
+--------+---------+--------+
| KING   | 5000.00 |     10 |
| CLARK  | 2450.00 |     10 |
| MILLER | 1300.00 |     10 |
| SCOTT  | 3000.00 |     20 |
| FORD   | 3000.00 |     20 |
| JONES  | 2975.00 |     20 |
| ADAMS  | 1100.00 |     20 |
| SMITH  |  800.00 |     20 |
| BLAKE  | 2850.00 |     30 |
| ALLEN  | 1600.00 |     30 |
| TURNER | 1500.00 |     30 |
| WARD   | 1250.00 |     30 |
| MARTIN | 1250.00 |     30 |
| JAMES  |  950.00 |     30 |
+--------+---------+--------+
14 rows in set (0.00 sec)
```



- 使用年薪进行降序排序(月薪*12+奖金)

```
mysql> select ename,sal,comm,sal*12+ifnull(comm,0) 年薪 from emp order by 年薪;
+--------+---------+---------+----------+
| ename  | sal     | comm    | 年薪     |
+--------+---------+---------+----------+
| SMITH  |  800.00 |    NULL |  9600.00 |
| JAMES  |  950.00 |    NULL | 11400.00 |
| ADAMS  | 1100.00 |    NULL | 13200.00 |
| WARD   | 1250.00 |  500.00 | 15500.00 |
| MILLER | 1300.00 |    NULL | 15600.00 |
| MARTIN | 1250.00 | 1400.00 | 16400.00 |
| TURNER | 1500.00 |    0.00 | 18000.00 |
| ALLEN  | 1600.00 |  300.00 | 19500.00 |
| CLARK  | 2450.00 |    NULL | 29400.00 |
| BLAKE  | 2850.00 |    NULL | 34200.00 |
| JONES  | 2975.00 |    NULL | 35700.00 |
| SCOTT  | 3000.00 |    NULL | 36000.00 |
| FORD   | 3000.00 |    NULL | 36000.00 |
| KING   | 5000.00 |    NULL | 60000.00 |
+--------+---------+---------+----------+
14 rows in set (0.00 sec)
```





- 显示每个部门的平均工资和最高工资

```
mysql> select deptno,format(avg(sal),2),max(sal)  from emp group by deptno;
# 语义: 按部门分组(分成多张逻辑子表),然后分别对各个组(每张逻辑子表)计算平均值和最大值,平均值保留两位小数.
+--------+--------------------+----------+
| deptno | format(avg(sal),2) | max(sal) |
+--------+--------------------+----------+
|     10 | 2,916.67           |  5000.00 |
|     20 | 2,175.00           |  3000.00 |
|     30 | 1,566.67           |  2850.00 |
+--------+--------------------+----------+
3 rows in set (0.00 sec)
```



- 显示平均工资低于2000的部门号和它的平均工资

```
mysql> select deptno, avg(sal) from emp group by deptno having avg(sal)<2000;
# 题意:以部门号为单位,即按部门号分组,然后 聚合查询;
+--------+-------------+
| deptno | avg(sal)    |
+--------+-------------+
|     30 | 1566.666667 |
+--------+-------------+
1 row in set (0.00 sec)
```



- 显示每种岗位的雇员总数，平均工资

```
mysql> select job, count(*), format(avg(sal),2) from emp group by job;
# 语义: 以岗位类型进行分组(分表), 分别对各组计算记录数就是雇员总数;
+-----------+----------+--------------------+
| job       | count(*) | format(avg(sal),2) |
+-----------+----------+--------------------+
| ANALYST   |        2 | 3,000.00           |
| CLERK     |        4 | 1,037.50           |
| MANAGER   |        3 | 2,758.33           |
| PRESIDENT |        1 | 5,000.00           |
| SALESMAN  |        4 | 1,400.00           |
+-----------+----------+--------------------+
5 rows in set (0.00 sec)
```





### 子查询

子查询是指嵌入在其他sql语句中的select语句，也叫嵌套查询

- 显示工资最高的员工的名字和工作岗位

```
mysql> select ename, job from emp where sal=(select max(sal) from emp);
+-------+-----------+
| ename | job       |
+-------+-----------+
| KING  | PRESIDENT |
+-------+-----------+
1 row in set (0.00 sec)
```





- 显示工资高于平均工资的员工信息

```
mysql> select * from emp where sal>(select avg(sal) from emp);
+--------+-------+-----------+------+---------------------+---------+------+--------+
| empno  | ename | job       | mgr  | hiredate            | sal     | comm | deptno |
+--------+-------+-----------+------+---------------------+---------+------+--------+
| 007566 | JONES | MANAGER   | 7839 | 1981-04-02 00:00:00 | 2975.00 | NULL |     20 |
| 007698 | BLAKE | MANAGER   | 7839 | 1981-05-01 00:00:00 | 2850.00 | NULL |     30 |
| 007782 | CLARK | MANAGER   | 7839 | 1981-06-09 00:00:00 | 2450.00 | NULL |     10 |
| 007788 | SCOTT | ANALYST   | 7566 | 1987-04-19 00:00:00 | 3000.00 | NULL |     20 |
| 007839 | KING  | PRESIDENT | NULL | 1981-11-17 00:00:00 | 5000.00 | NULL |     10 |
| 007902 | FORD  | ANALYST   | 7566 | 1981-12-03 00:00:00 | 3000.00 | NULL |     20 |
+--------+-------+-----------+------+---------------------+---------+------+--------+
6 rows in set (0.00 sec)
```



## 多表连接

### 笛卡尔积

![image-20240920000650365](MySQL%20CRUD%E4%B8%8E%E5%A4%8D%E5%90%88%E6%9F%A5%E8%AF%A2.assets/image-20240920000650365.png)



### 不同的表做笛卡尔积

#### 案例

- 显示雇员名、雇员工资以及所在部门的名字因为上面的数据来自EMP和DEPT表，因此要联合查询

```
mysql> select  ename, sal, dname from emp, dept where emp.deptno = dept.deptno;
+--------+---------+------------+
| ename  | sal     | dname      |
+--------+---------+------------+
| SMITH  |  800.00 | RESEARCH   |
| ALLEN  | 1600.00 | SALES      |
| WARD   | 1250.00 | SALES      |
| JONES  | 2975.00 | RESEARCH   |
| MARTIN | 1250.00 | SALES      |
| BLAKE  | 2850.00 | SALES      |
| CLARK  | 2450.00 | ACCOUNTING |
| SCOTT  | 3000.00 | RESEARCH   |
| KING   | 5000.00 | ACCOUNTING |
| TURNER | 1500.00 | SALES      |
| ADAMS  | 1100.00 | RESEARCH   |
| JAMES  |  950.00 | SALES      |
| FORD   | 3000.00 | RESEARCH   |
| MILLER | 1300.00 | ACCOUNTING |
+--------+---------+------------+
14 rows in set (0.00 sec)
```



- 显示部门号为10的部门名，员工名和工资

```
mysql> select dname, ename, sal from emp,dept where emp.deptno=10;
+------------+--------+---------+
| dname      | ename  | sal     |
+------------+--------+---------+
| ACCOUNTING | CLARK  | 2450.00 |
| ACCOUNTING | KING   | 5000.00 |
| ACCOUNTING | MILLER | 1300.00 |
| RESEARCH   | CLARK  | 2450.00 |
| RESEARCH   | KING   | 5000.00 |
| RESEARCH   | MILLER | 1300.00 |
| SALES      | CLARK  | 2450.00 |
| SALES      | KING   | 5000.00 |
| SALES      | MILLER | 1300.00 |
| OPERATIONS | CLARK  | 2450.00 |
| OPERATIONS | KING   | 5000.00 |
| OPERATIONS | MILLER | 1300.00 |
+------------+--------+---------+
12 rows in set (0.00 sec)
```





- 显示各个员工的姓名，工资，及工资级别

法一:

```
mysql> select ename,sal,grade, losal, hisal from emp,salgrade where sal>=losal and sal <hisal;
+--------+---------+-------+-------+-------+
| ename  | sal     | grade | losal | hisal |
+--------+---------+-------+-------+-------+
| SMITH  |  800.00 |     1 |   700 |  1200 |
| ALLEN  | 1600.00 |     3 |  1401 |  2000 |
| WARD   | 1250.00 |     2 |  1201 |  1400 |
| JONES  | 2975.00 |     4 |  2001 |  3000 |
| MARTIN | 1250.00 |     2 |  1201 |  1400 |
| BLAKE  | 2850.00 |     4 |  2001 |  3000 |
| CLARK  | 2450.00 |     4 |  2001 |  3000 |
| KING   | 5000.00 |     5 |  3001 |  9999 |
| TURNER | 1500.00 |     3 |  1401 |  2000 |
| ADAMS  | 1100.00 |     1 |   700 |  1200 |
| JAMES  |  950.00 |     1 |   700 |  1200 |
| MILLER | 1300.00 |     2 |  1201 |  1400 |
+--------+---------+-------+-------+-------+
12 rows in set (0.00 sec)

```

法二

```
mysql> select ename,sal,grade, losal, hisal from emp,salgrade where sal between losal and hisal;
+--------+---------+-------+-------+-------+
| ename  | sal     | grade | losal | hisal |
+--------+---------+-------+-------+-------+
| SMITH  |  800.00 |     1 |   700 |  1200 |
| ALLEN  | 1600.00 |     3 |  1401 |  2000 |
| WARD   | 1250.00 |     2 |  1201 |  1400 |
| JONES  | 2975.00 |     4 |  2001 |  3000 |
| MARTIN | 1250.00 |     2 |  1201 |  1400 |
| BLAKE  | 2850.00 |     4 |  2001 |  3000 |
| CLARK  | 2450.00 |     4 |  2001 |  3000 |
| SCOTT  | 3000.00 |     4 |  2001 |  3000 |
| KING   | 5000.00 |     5 |  3001 |  9999 |
| TURNER | 1500.00 |     3 |  1401 |  2000 |
| ADAMS  | 1100.00 |     1 |   700 |  1200 |
| JAMES  |  950.00 |     1 |   700 |  1200 |
| FORD   | 3000.00 |     4 |  2001 |  3000 |
| MILLER | 1300.00 |     2 |  1201 |  1400 |
+--------+---------+-------+-------+-------+
14 rows in set (0.00 sec)
```



### 自连接

相同的表自己连接自己

#### 案例

- 显示员工FORD的上级领导的编号和姓名（mgr是员工领导的编号--empno）

法一:

```
mysql> select ename,empno from emp where empno=(select mgr from emp where ename='FORD');
+-------+--------+
| ename | empno  |
+-------+--------+
| JONES | 007566 |
+-------+--------+
1 row in set (0.00 sec)
```

法二:

```
mysql> select t1.ename,t1.mgr,t2.ename leader from emp t1,emp t2 where t1.ename='FORD' and t1.mgr=t2.empno;
+-------+------+--------+
| ename | mgr  | leader |
+-------+------+--------+
| FORD  | 7566 | JONES  |
+-------+------+--------+
1 row in set (0.00 sec)
```



### 内连接

上文用的笛卡尔积实际就是内连接;内连接也有特定的语法:

> 内连接实际上就是利用where子句对两种表形成的笛卡儿积进行筛选，也是在开发过程中使用的最多的连接查询。

```
select 字段 from 表1 inner join 表2 on 连接条件 and 其他条件；
```

> 使用语法能够能够提高模块化程度与可读性,同样的学习成本也提高了

### 外连接

外连接分为左外连接和右外连接

#### 左外连接

如果联合查询，左侧的表完全显示我们就说是左外连接。

> 保留左表(左表始终所有数据始终可见),右表如果不满足则填充NULL;
>
> 显然,核心就是以左表为主的思想

```
select 字段名  from 表名1 left join 表名2 on 连接条件;				## 实际就是把inner换成left或right
```



#### 右外连接

语法:

```	
select 字段 from 表名1 right join 表名2  on  连接条件；
```



