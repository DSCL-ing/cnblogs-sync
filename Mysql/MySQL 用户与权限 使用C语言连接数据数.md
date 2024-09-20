[toc]

## 用户

### 用户管理

mysql用户管理位于数据库mysql中的user表中

```
mysql> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| engine_cost               |
| event                     |
| func                      |
| general_log               |
| gtid_executed             |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| innodb_index_stats        |
| innodb_table_stats        |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| server_cost               |
| servers                   |
| slave_master_info         |
| slave_relay_log_info      |
| slave_worker_info         |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
31 rows in set (0.00 sec)
```



```
mysql> select * from user\G;
*************************** 1. row ***************************
                  Host: localhost							## 允许的登录方式,localhost表示只允许本机登录
                  User: root
           Select_priv: Y
           Insert_priv: Y
           Update_priv: Y
           Delete_priv: Y
           Create_priv: Y
             Drop_priv: Y
           Reload_priv: Y
         Shutdown_priv: Y
          Process_priv: Y
             File_priv: Y
            Grant_priv: Y
       References_priv: Y
            Index_priv: Y
            Alter_priv: Y
          Show_db_priv: Y
            Super_priv: Y
 Create_tmp_table_priv: Y
      Lock_tables_priv: Y
          Execute_priv: Y
       Repl_slave_priv: Y
      Repl_client_priv: Y
      Create_view_priv: Y
        Show_view_priv: Y
   Create_routine_priv: Y
    Alter_routine_priv: Y
      Create_user_priv: Y
            Event_priv: Y
          Trigger_priv: Y
Create_tablespace_priv: Y
              ssl_type:
            ssl_cipher:
           x509_issuer:
          x509_subject:
         max_questions: 0
           max_updates: 0
       max_connections: 0
  max_user_connections: 0
                plugin: mysql_native_password
 authentication_string: *********************************2E128811		## 用户密码(经过password函数加密)
      password_expired: Y
 password_last_changed: 2024-02-28 22:08:19
     password_lifetime: NULL
        account_locked: N
        
### ....
```

因此有一种简单粗暴的用户管理方法就是像USER表中插入用户数据,不过需要插入很多字段如权限那些,太过于麻烦;一般都是使用MySQL提供的方法进行用户管理;



#### 查询所有用户

```
mysql> select user,host from user;
+---------------+-----------+
| user          | host      |
+---------------+-----------+
| mysql.session | localhost |
| mysql.sys     | localhost |
| root          | localhost |
+---------------+-----------+
3 rows in set (0.00 sec)
```



#### 查看当前用户

```
mysql> select user();
+--------+
| user() |
+--------+
| root@  |
+--------+
1 row in set (0.00 sec)
```



#### 查看当前连接数

```
mysql> show processlist;
+----+------+----------------------+------+---------+------+----------+------------------+
| Id | User | Host                 | db   | Command | Time | State    | Info             |
+----+------+----------------------+------+---------+------+----------+------------------+
|  3 | root | localhost            | NULL | Query   |    0 | starting | show processlist |
|  4 | chj  | *********            | NULL | Sleep   |    4 |          | NULL             |
+----+------+----------------------+------+---------+------+----------+------------------+
2 rows in set (0.00 sec)
```





### 创建用户

语法

```
create user '用户名'@'ip' identified by '密码'; 
flush privileges;			## 刷新权限
```

> 涉及密码的操作mysql都不会记录下来

> 涉及权限的操作都需要刷新 ## flush privileges;

- 仅限本地登录用户

```
create user '用户名'@'localhost' identified by '密码'; 
```

- 仅限特定ip登录(一般都是内网,除了学习使用外一般不建议公网)

```
create user '用户名'@'ip' identified by '密码'; 
```

- 所有用户可登录(危险,仅学习用,一般用户没有固定ip)

```
create user '用户名'@'%' identified by '密码'; 
```



> 如果遇到SSL不匹配问题,可以尝试在my.cnf中加入skip_ssl,注意位置不对可能不生效



### 删除用户

语法:

```
drop user '用户名'@'主机名'
```





### 修改密码规则

[文章引用](https://blog.csdn.net/qq_39390545/article/details/112135841)

#### 查看规则/策略

```
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+--------+
| Variable_name                        | Value  |
+--------------------------------------+--------+
| validate_password_check_user_name    | OFF    |
| validate_password_dictionary_file    |        |
| validate_password_length             | 8      |
| validate_password_mixed_case_count   | 1      |
| validate_password_number_count       | 1      |
| validate_password_policy             | MEDIUM |
| validate_password_special_char_count | 1      |
+--------------------------------------+--------+
7 rows in set (0.00 sec)
```



#### 规则说明

| 属性                                 | 默认值 | 属性描述                                                     |
| ------------------------------------ | ------ | ------------------------------------------------------------ |
| validate_password_check_user_name    | OFF    | 设置为ON的时候表示能将密码设置成当前用户名。                 |
| validate_password_dictionary_file    |        | 用于检查密码的字典文件的路径名，默认为空                     |
| validate_password_length             | 8      | 密码的最小长度，也就是说密码长度必须大于或等于8              |
| validate_password_mixed_case_count   | 1      | 如果密码策略是中等或更强的，validate_password要求密码具有的小写和大写字符的最小数量。对于给定的这个值密码必须有那么多小写字符和那么多大写字符。 |
| validate_password_number_count       | 1      | 密码必须包含的数字个数                                       |
| validate_password_policy             | MEDIUM | right-aligned 密码强度检验等级，可以使用数值0、1、2或相应的符号值LOW、MEDIUM、STRONG来指定。 `0/LOW：只检查长度。` `1/MEDIUM：检查长度、数字、大小写、特殊字符。` `2/STRONG：检查长度、数字、大小写、特殊字符、字典文件。` |
| validate_password_special_char_count | 1      | 密码必须包含的特殊字符个数                                   |



#### 临时设置

修改密码前可以修改密码规则

```
#安全强度，默认为中，即1，要求必须包含 数字、符号、⼤⼩写字⺟，⻓度⾄少为8位 
set global validate_password_policy=0;		## 设置为弱密码强度

set global validate_password_length=1;	## 设置密码最小长度
```

#### 持久设置

将以下内容添加到`\etc\my.cnf`中

```
validate_password_policy=LOW
validate_password_length=6
validate_password_number_count=0
validate_password_special_char_count=0
validate_password_mixed_case_count=0
```





### 修改密码

- 自己改自己

```
set password=password('新的密码');
```

- root改指定用户

```
set password for '用户名'@'主机名'=password('新的密码')；
```

- root直接对user表操作

```
update user set authentication_string=password('111') where user='chj';
flush privileges;
```

或

```
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456';

flush privileges;	## 刷新权限
```



## 权限

### 数据库提供的 权限列表

![image-20240911123237917](MySQL%20%E7%94%A8%E6%88%B7%E4%B8%8E%E6%9D%83%E9%99%90%20%E4%BD%BF%E7%94%A8C%E8%AF%AD%E8%A8%80%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E6%95%B0.assets/image-20240911123237917.png)





### 查看权限

```
mysql> show grants for 'chj'@'%';
+--------------------------------------------------+
| Grants for chj@%                                 |
+--------------------------------------------------+
| GRANT USAGE ON *.* TO 'chj'@'%'                  |
| GRANT ALL PRIVILEGES ON `test_db`.* TO 'chj'@'%' |
+--------------------------------------------------+
2 rows in set (0.00 sec)
```





### 给用户授权

语法:

```
grant 权限列表 on 库.对象名 to '用户名'@'登陆位置' [identified by '密码'] ;
## identified by: 可选。 如果用户存在，赋予权限的同时修改密码,如果该用户不存在，就是创建用户
## 密码一般忽略,因为一般都是先创建用户再授限,创建用户时密码已经设置好了;
```

案例:

```
grant select on ...

grant select, delete, create on ....

grant all  on ... -- 表示赋予该用户在该对象上的所有权限

grant [privileges] on 库.*  to '用户名'@'登陆位置'
## 库.* : 表示某个数据库中的所有数据对象(表，视图，存储过程等) 

grant [privileges] on *.*   to '用户名'@'登陆位置'
## *.*代表本系统中的所有数据库的所有对象（表，视图，存储过程等） 
```



````
### 查看权限

```
mysql> show grants for 'chj'@'%';
+--------------------------------------------------+
| Grants for chj@%                                 |
+--------------------------------------------------+
| GRANT USAGE ON *.* TO 'chj'@'%'                  |
| GRANT ALL PRIVILEGES ON `test_db`.* TO 'chj'@'%' |
+--------------------------------------------------+
2 rows in set (0.00 sec)
```
````





### 回收用户权限



案例:

```
mysql> revoke delete on test_db.* from 'chj'@'%';
Query OK, 0 rows affected (0.00 sec)

mysql> show grants for 'chj'@'%'\G;
*************************** 1. row ***************************
Grants for chj@%: GRANT USAGE ON *.* TO 'chj'@'%'
*************************** 2. row ***************************
Grants for chj@%: GRANT SELECT, INSERT, UPDATE, CREATE, DROP, REFERENCES, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, EVENT, TRIGGER ON `test_db`.* TO 'chj'@'%'
2 rows in set (0.00 sec)
```



权限列表:

```
SELECT, INSERT, UPDATE, CREATE, DROP, REFERENCES, INDEX, ALTER, CREATE TEMPORARY TABLES, LOCK TABLES, EXECUTE, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, EVENT, TRIGGER, DELETE
```









## 使用C语言连接

> mysql操作是线程安全的(事务)

以API路线认识mysql API的使用.

### 库的安装

如果是通过yum等安装方式安装的mysql,则在安装过程mysql开发包一般也安装好了,因此推荐这种方式安装mysql;

安装好后开发头文件目录位于`/usr/include/mysql`,库位于`/usr/lib64/mysql`

如果没有找到,说明可能没有安装,可以手动命令安装:

````
yum install -y mysql-community-devel			## 开发包
````



也可以单独下载mysql连接工具,[MySQL :: Download MySQL Connector/C++ (Archived Versions)](https://downloads.mysql.com/archives/c-cpp/);注意,如果能找到对应的版本是最好,不过现在旧版本官网可能不显示了,这个也无妨,mysql对新版本是能做到**前后兼容**的,因此可以下载推荐的8.x版本.这种方法缺点就是比较繁琐,还可能出现兼容性问题等,因此不是很推荐.

对下载下来的包中,目前最重要的是include文件夹与lib文件夹,有这两个目录就能C/C++连接数据库与开发.(需要动静态库系列知识)

> linux generic: 通用版







### C API

[MySQL官方C接口声明](https://dev.mysql.com/doc/c-api/5.7/en/c-api-function-reference.html)

[MySQL C 结构体](https://dev.mysql.com/doc/c-api/5.7/en/c-api-data-structures.html)







#### mysql_init

[MySQL :: MySQL 5.7 C API Developer Guide :: 5.4.54 mysql_real_connect()](https://dev.mysql.com/doc/c-api/5.7/en/mysql-real-connect.html)

```
MYSQL* mysql_init(MYSQL *mysql);
```



> 顾名思义,对 `MYSQL*` 对象进行初始化.



#### mysql_real_connect

[MySQL :: MySQL 5.7 C API Developer Guide :: 5.4.54 mysql_real_connect()](https://dev.mysql.com/doc/c-api/5.7/en/mysql-real-connect.html)

```
 MYSQL *mysql_real_connect(
         MYSQL *mysql,               // 输出型参数:成功则返回mysql访问句柄,错误返回NULL;
         const char *host,           // mysqld的ip
         const char *user,           // 登录的用户名
         const char *passwd,         // 登录密码
         const char *db,             // 连接的数据库
         unsigned int port,          // mysqld端口号
         const char *unix_socket,    // 域间套接字(类似进程间管道通信)
         unsigned long clientflag);  // 位图,启用一些特性;一般使用默认就好,即0
```



注意:

> 需要先使用mysql_init初始化`MYSQL*`对象.



返回值

> 如果连接成功，则使用MYSQL*连接处理程序，如果连接不成功，则为NULL。对于成功的连接，返回值与第一个参数的值相同。



#### mysql_close

[MySQL :: MySQL 5.7 C API Developer Guide :: 5.4.5 mysql_close()](https://dev.mysql.com/doc/c-api/5.7/en/mysql-close.html)

```
void mysql_close(MYSQL *mysql);
```

> 关闭



#### mysql_query

[MySQL :: MySQL 5.7 C API Developer Guide :: 5.4.53 mysql_query()](https://dev.mysql.com/doc/c-api/5.7/en/mysql-query.html)

```
int mysql_query(
		 	MYSQL *mysql,
      const char *stmt_str);
```

注意:

> 官方文档中描述说到,不用在sql语句后面加上\';\'和\'\G\'



返回值:

>  成功返回0,失败返回错误码



模拟实现一个简单的MySQL客户端:

```
#include<iostream>
#include<mysql/mysql.h>
#include<string>
#include<cassert>
#include<chrono>
#include<thread>

/*
MYSQL *mysql_real_connect(
        MYSQL *mysql,               // 输出型参数:成功则返回mysql访问句柄,错误返回NULL
        const char *host,           // mysqld的ip
        const char *user,           // 登录的用户名
        const char *passwd,         // 登录密码
        const char *db,             // 连接的数据库
        unsigned int port,          // mysqld端口号
        const char *unix_socket,    // 域间套接字(类似进程间管道通信)
        unsigned long clientflag);  // 位图,启用一些特性;一般使用默认就好,即0
 */

const std::string host = "127.0.0.1";
const std::string user = "chj";
const std::string passwd = "123456";
const std::string db = "test_db";
const unsigned int port = 3306;
//const std::string socket = ;
const unsigned long clientflag = 0;


int main(){
  std::cout<<"mysql client version :" << mysql_get_client_info()<<std::endl;
  MYSQL* mysql = mysql_init(nullptr);
  if(mysql == nullptr){
    std::cerr<<" mysql init error "<<std::endl;
    return 1;
  }
  if(mysql_real_connect(mysql,host.c_str(),user.c_str(),passwd.c_str(),db.c_str(),port,nullptr,clientflag)){
    std::cout<<"conect success" <<std::endl;
  }
  else{
    std::cerr<<"mysql connect error"<<std::endl;
    return 2;
  }

  std::string sql;
  while(true){
    std::cout<<"mysql >>> ";
    if(!std::getline(std::cin,sql)) break;
    if(sql == "quit" || sql == "exit") break;
    int n = mysql_query(mysql,sql.c_str());
    if(n == 0){
      std::cout<<sql<<" sucess"<<std::endl;
    }
    else{
      std::cerr<<sql<<" failed, "<<n<<std::endl;
    }

  }

  mysql_close(mysql);

  return 0;
}
```



#### mysql_set_character_set

[MySQL :: MySQL 5.7 C API Developer Guide :: 5.4.69 mysql_set_character_set()](https://dev.mysql.com/doc/c-api/5.7/en/mysql-set-character-set.html)

```
int mysql_set_character_set(
					MYSQL *mysql,
          const char *csname);	//cs == character set
```



问题: 一般来说,mysqld也是设置成utf8的,C/C++代码也是utf8的,为什么中文可能会出现乱码呢?

解析: 两端都没问题,那问题只能出自于链接,因此需要设置链接字符集

>建立好链接之后，获取英文没有问题，如果获取中文是乱码： 
>设置**链接**的默认字符集是utf8，原始默认是latin1 
>mysql_set_character_set(myfd, "utf8");



#### mysql_store_result

[MySQL :: MySQL 5.7 C API Developer Guide :: 5.4.77 mysql_store_result()](https://dev.mysql.com/doc/c-api/5.7/en/mysql-store-result.html)

```
MYSQL_RES *mysql_store_result(MYSQL *mysql);
```



