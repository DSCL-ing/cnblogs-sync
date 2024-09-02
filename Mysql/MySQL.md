[toc]

## 前言

在安装前,为了减少相关因素影响,建议先卸载干净后再安装;

整个安装过程需要root权限;

安装环境 centos 7; 

安装版本 MySQL 5.7

## MySQL卸载

### 查看是否已安装MySQL
查看正在运行的进程,如果存在可以先kill掉
```
ps axj|grep mysql
```


查看是否存在有正在运行的SQL服务
```
systemctl list-units|grep mysql
```

暂停mysql服务

```
systemctl stop mysqld
```





### 卸载mysql服务

yum安装的mysql通常是.rpm版本,因此可以通过rpm管理器查看mysql安装包

```
rpm -qa | grep mysql			#	qa 表示 'query all'
```

全部卸载,可以选择手动一条条删,也可以选择组合命令自动化删除

```
rpm -qa | grep mysql | xargs yum -y remove 		# 将rpm输出结果通过xargs转成命令参数喂给yum
```





### 查看是否卸载干净

卸载mysql后,/etc/my.cnf文件也会被删除

```
ls /etc/my.cnf
```

历史mysql数据默认会保留.需要的话可以做一个备份

```
sudo ls /var/lib/mysql/
```



## MySQL安装



### 查看linux版本

安装前先查看自己linux发行版

```
cat /etc/centos-release
```

或

```
cat /etc/redhat-release
```

![image-20240902132006151](MySQL.assets/image-20240902132006151.png)

或

```
uname -a		# el代表centos
```

![image-20240902132155884](MySQL.assets/image-20240902132155884.png)



### 选择MySQL版本

打开网页[http://repo.mysql.com/](http://repo.mysql.com/)

![image-20240902132324522](MySQL.assets/image-20240902132324522.png)

可以发现很多版本,但是版本过长,显示不全,右键选择查看网页源代码就能展开查看了

![image-20240902132812997](MySQL.assets/image-20240902132812997.png)

我的服务器是centos7,因此选择el版本;即[mysql57-community-release-el7.rpm](http://repo.mysql.com/mysql57-community-release-el7.rpm)(centos7通用版本,如果有对应的版本也可以选择)

![image-20240902133653139](MySQL.assets/image-20240902133653139.png)

> [noarch_百度百科 (baidu.com)](https://baike.baidu.com/item/noarch/5351882?fr=aladdin



### 获取mysql官方yum源

使用wget直接下载到服务器

```
wget http://repo.mysql.com/mysql57-community-release-el7.rpm
```

或者直接下载到本地,然后再使用其他方法如scp,lrzsz,ftp方式等拷贝过去



### rpm安装mysql官方yum源

进入到存放mysql的目录后,因为我们下载的mysql是.rpm版本,因此需要使用rpm管理器进行安装.

> yum类似一个商店,用于下载需要的包,实际安装也是调用rpm进行安装.如果配好了下载mysql的yum源,就可以直接通过yum进行直接安装.
>
> 可以通过ls /etc/yum.repos.d/ 查看所有yum源
>
> ![image-20240902135747741](MySQL.assets/image-20240902135747741.png)
>
> 我的liunx上没有mysql相关的yum源,因此需要安装;

安装命令:

```
sudo rpm -ivh mysql57-community-release-el7.rpm
```

![image-20240902135051895](MySQL.assets/image-20240902135051895.png)

安装后,yum也更新了mysql相关的yum源

![image-20240902135902499](MySQL.assets/image-20240902135902499.png)

之后就可以通过yum进行安装mysql及mysql相关工具.



测试新增的yum源是否正常工作:

```
yum list |grep mysql
```

如果出现mysql相关的包,就说明安装成功



### 安装mysql服务

```
sudo yum install -y mysql-community-server
```

安装大概需要1G空间,安装时间根据机器配置而定,需要一定时间



### 查看是否安装成功

查看mysql配置文件是否存在

```
ls /etc/my.cnf
```

查看服务端应用程序是否存在

```
which mysqld
```

![image-20240902184828864](MySQL.assets/image-20240902184828864.png)

> sbin:super bin # 需要超管权限的服务

查看客户端应用程序

```
which mysql
```



查看监听端口号,mysql启动后会在local Address 3306

```
netstat nltp
```



## 配置MySQL

### 简单登录mysql命令

先看能不能用,简单登录测试,注意刚安装时是不知道密码的,看下文处理

```
mysql -uroot -p ## -u:User, -p表示password
```

退出命令

```
quit
```



### 免密登录配置

在不知道密码时,网上有多种登录方式,目前只介绍免密登录配置.

在/etc/my.cnf文件中追加以下命令即可.

```
skip-grant-tables
```

配置好后重启mysqld

```
systemctl restart mysqld 	##或者分两步走,先stop再start
```

然后再次登录mysql,密码处直接回车即可



### my.cnf 其他配置项

```
## /etc/my.cnf
## ...

datadir=/var/lib/mysql                  #建表等数据存放路径
socket=/var/lib/mysql/mysql.sock        #内部数据,略

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

log-error=/var/log/mysqld.log           #错误日志
pid-file=/var/run/mysqld/mysqld.pid

# User-defind Add
skip-grant-tables                       #免密设置,重启mysqld生效
port=3306                               #mysql服务端口号
character-set-server=utf8               #设置字符集为utf8
default-storage-engine=innodb           #设置默认存储引擎为innodb

```



### 设置开机启动(可选)

```
systemctl enable mysqld		#开机自启
systemctl daemon-reload		#重新加载配置文件
```



## 常用命令与名词认识

### 登录命令

```
mysql -h127.0.0.1 -P3306 -uroot		## -h 指定服务器ip -P指定端口号 ##不指定时使用默认
```



### 认识mysql 和 mysqld

mysqld是网络服务一种,d表示daemon,守护进程;mysqld表示mysql数据库服务的服务器端

mysql就是数据库服务的客户端了



mysql是一套提供数据存取服务的网络程序;

数据库本质是,在磁盘或内存中存储的 特定结构组织的数据



一般谈数据库服务就表示mysqld ;谈数据库时,就是具体的数据组织文件
