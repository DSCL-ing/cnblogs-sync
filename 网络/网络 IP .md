[toc]

## IP地址概念

分类:

A类：000~127，默认[子网掩码](https://so.csdn.net/so/search?q=子网掩码&spm=1001.2101.3001.7020)：255.0.0.0
B类：128~191，默认子网掩码：255.255.0.0
C类：192~223，默认子网掩码：255.255.255.0
D类：224~239
E类：240~255



A类地址以0开头，第一个字节作为网络号，地址范围为：0.0.0.0~127.255.255.255；

B类地址以10开头，前两个字节作为网络号，地址范围是：128.0.0.0~191.255.255.255;

C类地址以110开头，前三个字节作为网络号，地址范围是：192.0.0.0~223.255.255.255。

D类地址以1110开头，地址范围是224.0.0.0~239.255.255.255，D类地址作为组播地址（一对多的通信）；

E类地址以1111开头，地址范围是240.0.0.0~255.255.255.255，E类地址为保留地址，供以后使用。

注：只有A,B,C有网络号和主机号之分，D类地址和E类地址没有划分网络号和主机号。

> `0.0.0.0` 不属于传统的五种IP地址类别（A类、B类、C类、D类、E类）中的任何一种。



**1）网络地址**

IP地址由网络号/网络地址（包括子网号）和主机号组成，网络地址的主机号为全0，网络地址代表着整个网络。

**2）广播地址**

广播地址通常称为直接广播地址，是为了区分受限广播地址。

广播地址与网络地址的主机号正好相反，广播地址中，主机号为全1。当向某个网络的广播地址发送消息时，该网络内的所有主机都能收到该广播消息。

**3）组播地址**

D类地址就是组播地址。

**4）255.255.255.255**

该IP地址指的是受限的广播地址。受限广播地址与一般广播地址（直接广播地址）的区别在于，受限广播地址只能用于本地网络，路由器不会转发以受限广播地址为目的地址的分组；一般广播地址既可在本地广播，也可跨网段广播。例如：主机192.168.1.1/30上的直接广播数据包后，另外一个网段192.168.1.5/30也能收到该数据报；若发送受限广播数据报，则不能收到。

注：一般的广播地址（直接广播地址）能够通过某些路由器（当然不是所有的路由器），而受限的广播地址不能通过路由器。

**5）0.0.0.0**

常用于寻找自己的IP地址，例如在我们的RARP，BOOTP和DHCP协议中，若某个未知IP地址的无盘机想要知道自己的IP地址，它就以255.255.255.255为目的地址，向本地范围（具体而言是被各个路由器屏蔽的范围内）的服务器发送IP请求分组。

**6）回环地址**

127.0.0.0/8被用作回环地址，回环地址表示本机的地址，常用于对本机的测试，用的最多的是127.0.0.1。

**7）A、B、C类私有地址**

私有地址(private address)也叫专用地址，它们不会在全球使用，只具有本地意义。

A类私有地址：10.0.0.0/8，范围是：10.0.0.0~10.255.255.255

B类私有地址：172.16.0.0/12，范围是：172.16.0.0~172.31.255.255

C类私有地址：192.168.0.0/16，范围是：192.168.0.0~192.168.255.255





## IP地址2

在IPv4中，IP地址按照类别分为五种类型：A类、B类、C类、D类和E类。每种类型的地址都有其特定的用途和特点。下面是这五种IP地址类别的详细介绍：

### A类地址

- **地址范围**：1.0.0.1 到 126.255.255.254
- **默认子网掩码**：255.0.0.0 或 /8
- 特点
  - 网络部分占据第一个八位组（最高位为0）。
  - 主机部分占据最后三个八位组。
  - 可以容纳大量的主机（最多2^24 - 2 = 16,777,214个主机）。
  - 适合大型网络。

### B类地址

- **地址范围**：128.0.0.1 到 191.255.255.254
- **默认子网掩码**：255.255.0.0 或 /16
- 特点
  - 网络部分占据前两个八位组（最高两位为10）。
  - 主机部分占据最后两个八位组。
  - 可以容纳较多的主机（最多2^16 - 2 = 65,534个主机）。
  - 适合中型网络。

### C类地址

- **地址范围**：192.0.0.1 到 223.255.255.254
- **默认子网掩码**：255.255.255.0 或 /24
- 特点
  - 网络部分占据前三个八位组（最高三位为110）。
  - 主机部分占据最后一个八位组。
  - 可以容纳较少的主机（最多2^8 - 2 = 254个主机）。
  - 适合小型网络。

### D类地址

- **地址范围**：224.0.0.0 到 239.255.255.255
- **默认子网掩码**：无（因为这是多播地址）
- 特点
  - 用于多播通信，其中多个接收者可以同时接收来自单个发送者的数据。
  - 没有默认子网掩码，因为它们不是用于路由的单一网络地址。
  - 适用于流媒体应用、实时会议等。

### E类地址

- **地址范围**：240.0.0.0 到 255.255.255.255
- 特点
  - 保留为将来使用或实验目的。
  - 不应用于常规网络通信。
  - 通常不分配给实际的网络设备。

### 特殊IP地址

除了上述五类地址外，还有一些特殊的IP地址，它们具有特定的功能：

- 私有地址

  ：这些地址不会在全球互联网上路由，而是用于内部网络。例如：

  - 10.0.0.0/8
  - 172.16.0.0/12
  - 192.168.0.0/16

- **环回地址**：127.0.0.0/8 用于测试本地网络接口。

- **广播地址**：每个子网的最后一个地址，用于向子网内的所有主机发送消息。

这些类别和特殊地址构成了IPv4地址体系的基础。在IPv6中，地址不再按类别划分，而是使用全局唯一的地址、链路本地地址、站点本地地址等不同的类型。





## `0.0.0.0`

`0.0.0.0` 是一个特殊的 IP 地址，在 IPv4 中具有多种用途：

1. **未指定地址**:
   - 在某些情况下，`0.0.0.0` 被用作未指定地址，表示“任何”或“未知”的 IP 地址。例如，在配置网络接口时，如果一个接口还没有分配具体的 IP 地址，可能会暂时使用 `0.0.0.0`。
2. **本地主机的任意接口**:
   - 当应用程序需要监听所有网络接口上的连接时，可以绑定到 `0.0.0.0`。这意味着它将接收来自任何 IP 地址的连接请求。
3. **默认路由**:
   - 在路由表中，`0.0.0.0` 通常代表默认路由的目标地址。当路由器不知道如何将数据包发送到特定的目的地时，它可以将数据包发送到默认网关，即默认路由条目指向的下一跳。
4. **广播地址**:
   - 尽管 `0.0.0.0` 本身不是广播地址，但有时它被用来指代整个网络的广播地址。例如，如果一个子网的网络地址是 `192.168.1.0/24`，那么它的广播地址将是 `192.168.1.255`，而 `0.0.0.0` 有时会被用来表示这种广播的“概念”。

**使用场景**

- 服务器监听
  - 服务器软件（如 web 服务器、数据库服务器等）可以配置为监听 `0.0.0.0`，这样就可以接受来自任何 IP 地址的连接请求。
- 网络调试
  - 开发者或网络管理员可能使用 `0.0.0.0` 来调试网络连接，测试服务是否能够正常运行而不关心特定的 IP 地址。
- 路由配置
  - 在路由表中，`0.0.0.0` 经常被用来指定默认路由。例如，如果一台计算机的默认网关是 `192.168.1.1`，那么它的路由表中可能会有一个条目，目标地址为 `0.0.0.0`，下一跳为 `192.168.1.1`。



## 三类私网地址

私网地址分为A,B,C三类

1. A类私网地址

- **地址范围**：`10.0.0.0` 到 `10.255.255.255` (`/8`)
- **子网掩码**：默认的子网掩码为 `255.0.0.0`。
- 其中10.0.0.0是网络地址，10.255.255.255是广播地址。
- 特点
  - 一个A类私网地址可以支持多达16777214个主机地址（减去网络地址和广播地址）。
  - A类私网地址可以用于非常大的网络环境。

2. B类私网地址

- **地址范围**：`172.16.0.0` 到 `172.31.255.255` (`/12`)
- **子网掩码**：默认的子网掩码为 `255.255.0.0`。
- 其中172.16.0.0是网络地址，172.31.255.255是广播地址。
- 特点
  - 一个B类私网地址可以支持多达65534个主机地址（减去网络地址和广播地址）。
  - B类私网地址适用于中等规模的网络环境。

3. C类私网地址

- **地址范围**：`192.168.0.0` 到 `192.168.255.255` (`/16`)
- **子网掩码**：默认的子网掩码为 `255.255.255.0`。
- 其中192.168.0.0是网络地址，192.168.255.255是广播地址。
- 共有256种私网网络起始地址:`192.168.0.0`到`192.168.255.0`
- 特点
  - 每个C类私网地址可以支持多达254个主机地址（减去网络地址和广播地址）。
  - C类私网地址适用于小型网络环境。



## 默认网关

网关可以理解为一个网络节点，它负责不同 **网络地址**(aka. **网段**) 之间的通信。 一般情况下（家用网络环境 或 较小的网络环境），**默认网关** 就是我们的**路由器**设备。

- 默认网关通常是路由器的一个接口的**IP地址**，它负责将数据包路由到其他网络。



## 默认子网掩码

默认子网掩码是指在没有特别配置的情况下，特定IP地址版本所使用的标准子网掩码。子网掩码是用来定义IP地址中网络部分和个人主机部分的边界的一种方法。以下是不同IP地址版本的默认子网掩码：

### IP地址的分类





## **广播地址**

计算方法:子网掩码取反后，与 网络地址 进行逻辑或(|)运算

`广播地址 = 网络地址| ~子网掩码`

```
子网掩码     255.255.248.0     11111111.11111111.11111000.00000000
取反~       0.0.7.255         00000000.00000000.00000111.11111111

网络地址    104.160.40.0      1101000.10100000.00101000.00000000
===========================================================
逻辑或|运算                   1101000.10100000.00101111.11111111 
                   广播地址 = 104.160.47.255
```



## **主机号**

子网掩码取反后，与 IP地址 进行**逻辑与(&)**运算

`主机号 = IP地址 & ~子网掩码`

```
子网掩码     255.255.248.0     11111111.11111111.11111000.00000000
取反~       0.0.7.255         00000000.00000000.00000111.11111111

IP地址     104.160.41.50      1101000.10100000.00101001.00110010
=========================================================== 
逻辑与&运算                    00000000.00000000.00000001.00110010
                     主机号 = 0.0.1.50
```



## 网络地址

网络地址 = 网段 , 用于标识不同的网络

在网络中，网络地址是指用来标识一个特定网络的IP地址。网络地址通常用来定义一个网络的起始地址，并且用来确定哪些IP地址**属于同一个网络**。网络地址是由网络部分和子网掩码共同决定的。

#### 网络地址的确定

公式:`IP地址 & 子网掩码 = 网络地址`

- **网络地址**：是通过将IP地址与子网掩码进行按位与运算得到的结果。
- **子网掩码**：定义了IP地址中哪些位属于网络部分，哪些位属于主机部分。

#### 示例

假设我们有一个IP地址 `192.168.123.123` 和一个子网掩码 `255.255.255.0` (`/24`)。

- **IP地址**: `192.168.123.123`
- **子网掩码**: `255.255.255.0` (`/24`)



##### 计算网络地址步骤

1. 将IP地址和子网掩码转换为二进制表示。
2. 对应位进行按位与运算。



##### 计算过程

- **IP地址** (二进制): `11000000.10101000.01111011.01111011`
- **子网掩码** (二进制): `11111111.11111111.11111111.00000000`

进行按位与运算：

- `11000000.10101000.01111011.01111011` (IP地址)
- `11111111.11111111.11111111.00000000` (子网掩码)
- `--------------------------------------`
- `11000000.10101000.01111011.00000000` (结果)

将结果转换回十进制表示：

- **网络地址**: `192.168.123.0`



##### 网络地址的含义

- **网络地址** (`192.168.123.0`) 代表了这个子网的起始地址。
- **子网掩码** (`255.255.255.0`) 表示前24位是网络部分，后8位是主机部分。
- **有效IP范围** (`192.168.123.1` 至 `192.168.123.254`) 是可用于主机的地址范围。
- **广播地址** (`192.168.123.255`) 用于向同一子网内的所有主机发送广播消息。

- **CIDR表示法** (`192.168.123.0/24`) 表示前24位是网络部分，后8位是主机部分。





## CIDR表示法

CIDR (Classless Inter-Domain Routing, 无类别域间路由) 表示法是一种用于表示IP地址和子网掩码的方法，它取代了传统的A、B、C类地址分类。CIDR表示法允许更灵活地分配和路由IP地址，从而提高了地址空间的利用率。

#### **CIDR表示法的组成部分**:

CIDR表示法通常由两部分组成：

1. **IP地址**：IPv4地址或IPv6地址。
2. **前缀长度**：斜杠后面跟着一个数字，表示网络前缀的长度，即网络部分的位数。

#### 示例

- **IPv4 CIDR表示法**：
  - `192.168.1.0/24`：表示前24位是网络部分，后8位是主机部分。
  - `192.168.1.0/25`：表示前25位是网络部分，后7位是主机部分。
  - `192.168.1.0/16`：表示前16位是网络部分，后16位是主机部分。
- **IPv6 CIDR表示法**：
  - `2001:db8:abcd:ef01::/64`：表示前64位是网络部分，后64位是接口标识部分。

