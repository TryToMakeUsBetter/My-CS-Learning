# 网络概述
## 不同网络模型
### OSI网络七层模型
应用层:最终呈现

表示层:转换数据格式

会话层:建立和断开链接

传输层:数据报文（TCP和UDP）

网络层:数据分组（IP和子网掩码）

数据链路层:数据帧

物理层:比特

### TCP/IP体系结构
<a id="networkModule"></a>
应用层:[FTP](#FTP)、[SMTP](#SMTP)、[HTTP](#HTTP)

传输层:定义端口，表示应用程序，实现端到端通信 

网络层:IP层，负责IP数据包的路由和传输

数据链路层:网络接口层，包含了OSI的物理层和数据链路层
## 不同层级的协议和介绍
### 应用层（TCP/IP模型下）
#### FTP<a id="FTP"></a>

[返回网络模型章节](#networkModule)
#### SMTP<a id="SMTP"></a>

[返回网络模型章节](#networkModule)
#### HTTP<a id="HTTP"></a>

[返回网络模型章节](#networkModule)

### 传输层
#### TCP和UDP
##### 端口
适用源IP、源端口、目的IP、目的端口、协议号来标识一个通信
0～1023:应用层协议端口，ssh 22；ftp 21；Telnet 23；HTTP 80；
1024～65535
##### 报文结构
TCP报文
|参数名称|长度|说明|
|:-:|:-:|:-:|
|源端口|16|发送方端口|
|目的端口|16|接收方端口|
|SEQ|32|字节流编号|
|ACK Number|32|应答顺序号|
|偏移|4|TCP包头长度|
|保留位|6||
|URG|1|紧急标志位|
|ACK|1|确认标志位|
|PSH|1|推送标志位|
|RST|1|复位标志位:1代表需要重连|
|SYN|1|同步标志位:1代表连接请求报文|
|FIN|1|终止标志位:1代表断开连接|
|窗口|16|从本报文段首部中的确认号开始，接收方目前允许对方一次发送的数据量|
|CheckSum|16|包括首部和数据两部分的CRC16|
|紧急指针|16||
|可选部分|0-40||
|数据部分|N||

UDP报文

|参数名称|长度|说明|
|:-:|:-:|:-:|
|源端口|16|发送方的端口号|
|目的端口|16|接收方的端口号|
|总长度|16|首部+数据内容|
|校验和|16|数据校验和|
|数据部分|N||
##### 异同
|特性|TCP|UDP|
|:---:|:---:|:---:|
|连接|有连接的|无连接的|
|数据格式|流模式|数据报模式|
|数据正确性|确保|不确保|
|数据顺序|保证顺序|不保证|

编码上的区别
TCP编码一般流程:
    Server:
        创建一个socket，用函数socket()；

        设置socket属性，用函数setsockopt(); * 可选

        绑定IP地址、端口等信息到socket上，用函数bind();

        开启监听，用函数**listen()**；

        接收客户端上来的连接，用函数**accept()**；

        收发数据，用函数send()和recv()，或者read()和write();

        关闭网络连接；

        关闭监听；
    Client:
        创建一个socket，用函数socket()；

        设置socket属性，用函数setsockopt();* 可选

        绑定IP地址、端口等信息到socket上，用函数bind();* 可选

        设置要连接的对方的IP地址和端口等属性；

        连接服务器，用函数**connect()**；

        收发数据，用函数send()和recv()，或者read()和write();

        关闭网络连接；

UDP编码一般流程:
    Server:
        创建一个socket，用函数socket()；

        设置socket属性，用函数setsockopt();* 可选

        绑定IP地址、端口等信息到socket上，用函数bind();

        循环接收数据，用函数**recvfrom()**;

        关闭网络连接；
    Client:
        创建一个socket，用函数socket()；

        设置socket属性，用函数setsockopt();* 可选

        绑定IP地址、端口等信息到socket上，用函数bind();* 可选

        设置对方的IP地址和端口等属性;

        发送数据，用函数**sendto()**;

        关闭网络连接；
具体编程差别
    socket()的参数不同

    UDP Server不需要调用listen和accept

    UDP收发数据用sendto/recvfrom函数

    TCP：地址信息在connect/accept时确定

    UDP：在sendto/recvfrom函数中每次均 需指定地址信息

    UDP：shutdown函数无效
##### TCP建立连接和断开连接

### 网络层

#### IP