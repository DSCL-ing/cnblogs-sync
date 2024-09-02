

### systemctl命令 – 管理系统服务

[linux systemctl 指令 —— 阮一峰 - 七脉 - 博客园 (cnblogs.com)](https://www.cnblogs.com/zwcry/p/9602756.html)

systemctl命令来自英文词组system control的缩写，其功能是管理系统服务。从RHEL 7/ CentOS 7版本起，初始化进程服务init被替代为systemd服务，systemd初始化进程服务的管理是通过systemctl命令完成的，该命令涵盖了service、chkconfig、init、setup等多个命令的大部分功能。

**语法格式：**systemctl 参数 动作 服务名

**常用参数：**

| -a   | 显示所有单位           | -q        | 静默执行模式         |      |
| ---- | ---------------------- | --------- | -------------------- | ---- |
| -f   | 覆盖任何冲突的符号链接 | -r        | 显示本地容器的单位   |      |
| -H   | 设置要连接的主机名     | -s        | 设置要发送的进程信号 |      |
| -M   | 设置要连接的容器名     | -t        | 设置单元类型         |      |
| -n   | 设置要显示的日志行数   | --help    | 显示帮助信息         |      |
| -o   | 设置要显示的日志格式   | --version | 显示版本信息         |      |

**常用动作：**

| start   | 启动服务         | disable | 取消服务开机自启   |      |
| ------- | ---------------- | ------- | ------------------ | ---- |
| stop    | 停止服务         | status  | 查看服务状态       |      |
| restart | 重启服务         | list    | 显示所有已启动服务 |      |
| enable  | 设置服务开机自启 |         |                    |      |

**参考示例**

启动指定的服务：

```
[root@linuxcool ~]# systemctl start sshd
```

停止指定的服务：

```
[root@linuxcool ~]# systemctl stop sshd
```

重启指定的服务：

```
[root@linuxcool ~]# systemctl restart sshd 
```

将指定的服务加入到开机启动项中：

```
[root@linuxcool ~]# systemctl enable sshd
```

查看指定服务的运行状态：

```
[root@linuxcool ~]# systemctl status sshd 
```

将指定的服务从开机启动项中取消：

```
[root@linuxcool ~]# systemctl disable sshd
```

显示系统中所有已启动的服务列表信息：

```
[root@linuxcool ~]# systemctl list-units --type=service 
```





### rpm命令 – RPM软件包管理器

> yum安装的软件包通常是.rpm版本,通常搭配rpm一起使用

rpm命令来自英文词组redhat package manager的缩写，中文译为“红帽软件包管理器”，其功能是在Linux系统下对软件包进行安装、卸载、查询、验证、升级等工作，常见的主流系统（如RHEL、CentOS、Fedora等）都采用这种软件包管理器，推荐用固定搭配“rpm-ivh 软件包名”安装软件，而卸载软件则用固定搭配“rpm -evh 软件包名”，简单好记又好用。



**语法格式：**rpm 参数 软件包名

**常用参数：**

| -a   | 显示所有软件包               | -p          | 显示指定的软件包信息     |      |
| ---- | ---------------------------- | ----------- | ------------------------ | ---- |
| -c   | 仅显示组态配置文件           | -q或--query | 显示指定软件包是否已安装 |      |
| -d   | 仅显示文本文件               | -R          | 显示软件包的依赖关系     |      |
| -e   | 卸载软件包                   | -s          | 显示文件状态信息         |      |
| -f   | 显示文件或命令属于哪个软件包 | -U          | 升级软件包               |      |
| -h   | 安装软件包时显示标记信息     | -v          | 显示执行过程信息         |      |
| -i   | 安装软件包                   | -vv         | 显示执行过程详细信息     |      |
| -l   | 显示软件包的文件列表         |             |                          |      |



**参考示例**

显示rpm命令帮助

```
rpm --help
```

正常安装软件包：

```
[root@linuxcool ~]# rpm -ivh cockpit-185-2.el8.x86_64.rpm 
```

显示系统已安装过的全部RPM软件包：

```
[root@linuxcool ~]# rpm -qa 
```

查询某个软件的安装路径：

```
[root@linuxcool ~]# rpm -ql cockpit
```

卸载通过RPM软件包安装的某个服务：

```
[root@linuxcool ~]# rpm -evh cockpit 
```

升级某个软件包：

```
[root@linuxcool ~]# rpm -Uvh cockpit-185-2.el8.x86_64.rpm 
```



### xargs命令 – 给其他命令传参数的过滤器

xargs命令来自英文词组extended arguments的缩写，用作给其他命令传递参数的过滤器。xargs命令能够处理从标准输入或管道符输入的数据，并将其转换成命令参数，也可以将单行或多行输入的文本转换成其他格式。

xargs命令默认接收的信息中，空格是默认定界符，所以可以接收包含换行和空白的内容。

**语法格式：**xargs 参数 文件名

**常用参数：**

| -a   | 设置从文件中读取数据       | -r        | 如果输入数据为空，则不执行 |
| ---- | -------------------------- | --------- | -------------------------- |
| -d   | 设置自定义定界符           | -s        | 设置每条命令最大字符数     |
| -I   | 设置替换字符串             | -t        | 显示xargs执行的命令        |
| -n   | 设置多行输出               | --help    | 显示帮助信息               |
| -p   | 执行命令前询问用户是否确认 | --version | 显示版本信息               |