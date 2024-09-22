[toc]



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



