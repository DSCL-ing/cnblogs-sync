# ProtoBuf





## ProtoBuf 安装

下载地址：https://github.com/protocolbuffers/protobuf/releases

如果要在 C++ 下使⽤ ProtoBuf，可以选择cpp.zip ；

如果要在 JAVA 下使⽤ ProtoBuf，可以选择 java.zip； 

其他语⾔选择对应的链接即可。

希望⽀持全部语⾔，选择 all.zip 。



```
⽀持全部语⾔，所以选择 protobuf-all-21.11.zip，右键将下载链接复制出来。
下载命令：
wget https://github.com/protocolbuffers/protobuf/releases/download/v21.11/protobuf-all-21.11.zip
```



可以不⽤下载最新版本，我当前使用v21.11



Win下需要把解压后⽂件中的bin⽬录配置到系统环境变量的Path中去



Linux下载 ProtoBuf 前需要安装依赖库：autoconf automake libtool curl make g++ unzip

```
# - Ubuntu
sudo apt-get install autoconf automake libtool curl make g++ unzip -y
# - CentOS
sudo yum install autoconf automake libtool curl make gcc-c++ unzip
```

进⼊解压好的⽂件，执⾏以下命令：

```
# 第⼀步执⾏autogen.sh，但如果下载的是具体的某⼀⻔语⾔，不需要执⾏这⼀步。
./autogen.sh 
 
# 第⼆步执⾏configure，有两种执⾏⽅式，任选其⼀即可，如下：
# 1、protobuf默认安装在 /usr/local ⽬录，lib、bin都是分散的
./configure 
# 2、修改安装⽬录，统⼀安装在/usr/local/protobuf下
./configure --prefix=/usr/local/protobuf

# 第三步
make // 执⾏15分钟左右
make check // 执⾏15分钟左右
# check没有问题后,才可以执行. (test例外,云服务器内存小)
sudo make install

```



如果当时选择了第⼀种执⾏⽅式，也就是 ./configure ，那么到这就可以正常使⽤protobuf了。如果选择了第⼆种执⾏⽅式，即修改了安装 ⽬录，那么还需要在/etc/profile 中添加⼀些内容：

```
sudo vim /etc/profile

# 添加内容如下：
#(动态库搜索路径) 程序加载运⾏期间查找动态链接库时指定除了系统默认路径之外的其他路径
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/protobuf/lib/
#(静态库搜索路径) 程序编译期间查找动态链接库时指定查找共享库的路径
export LIBRARY_PATH=$LIBRARY_PATH:/usr/local/protobuf/lib/
#执⾏程序搜索路径
export PATH=$PATH:/usr/local/protobuf/bin/
#c程序头⽂件搜索路径
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/usr/local/protobuf/include/
#c++程序头⽂件搜索路径
export CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH:/usr/local/protobuf/include/
#pkg-config 路径
export PKG_CONFIG_PATH=/usr/local/protobuf/lib/pkgconfig/
```



最后⼀步，重新执⾏ /etc/profile ⽂件:

```
source /etc/profile
```



检查是否配置成功\

- Win打开cmd,输⼊: protoc --version 查看版本，有显⽰说明成功
- linux命令提示符输入protoc --version 查看版本，有显⽰说明成功





## 快速上⼿

在快速上⼿中，会编写第⼀版本的通讯录 1.0。在通讯录 1.0 版本中，将实现： 
• 对⼀个联系⼈的信息使⽤ PB 进⾏序列化，并将结果打印出来。 
• 对序列化后的内容使⽤ PB 进⾏反序列，解析出联系⼈信息并打印出来。 
• 联系⼈包含以下信息: 姓名、年龄。 
通过通讯录 1.0，我们便能了解使⽤ ProtoBuf 初步要掌握的内容，以及体验到 ProtoBuf 的完整使⽤流
程。





### 步骤1：创建 .proto ⽂件 

⽂件规范 
• 创建 .proto ⽂件时，⽂件命名应该使⽤全⼩写字⺟命名，多个字⺟之间⽤ _ 连接。 例如：
lower_snake_case.proto 。 
• 书写 .proto ⽂件代码时，应使⽤ 2 个空格的缩进。 
我们为通讯录 1.0 新建⽂件： contacts.proto 
添加注释 
向⽂件添加注释，可使⽤// 或者 /* ... */ 

指定 proto3 语法 

步骤2：编译 contacts.proto ⽂件，⽣成 C++ ⽂件 
编译命令 
编译命令⾏格式为：

```
protoc  [--proto_path=IMPORT_PATH]  --cpp_out=DST_DIR  path/to/file.proto

protoc               是 Protocol Buffer 提供的命令⾏编译⼯具。

--proto_path         指定 被编译的.proto⽂件所在⽬录，可多次指定。可简写成 -I IMPORT_PATH 。如不指定										 该参数，则在当前⽬录进⾏搜索。当某个.proto ⽂件 import 其他 .proto ⽂件时，或需										 要编译的 .proto ⽂件不在当前⽬录下，这时就要⽤-I来指定搜索⽬录。

--cpp_out=           指编译后的⽂件为 C++ ⽂件。 

OUT_DIR              编译后⽣成⽂件的⽬标路径。

path/to/file.proto   要编译的.proto⽂件。
```

编译 contacts.proto ⽂件命令如下： 

```
protoc --cpp_out=. contacts.proto		##生成C++的类
```

或

```
protoc -I fast_start --cpp_out=fast_start contacts.proto
```

