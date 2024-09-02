[toc]

## 前言

在安装前,为了减少相关因素影响,先进行卸载.

测试环境 centos7; 



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



