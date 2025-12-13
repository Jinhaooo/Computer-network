# TCP/IP 网络模型
## 为什么要有TCP/IP模型？
从通信层面来讲，信息的传递是物理信号的传递，比如电信号，光信号...如何从物理信号中提取信息，又如何知道信息的传递对象等更多信息，其实是一个复杂的问题。  
而TCP/IP模型，就是建立了一套标准的规则，也就是一套通用的的网络协议，来把这些复杂的问题分为诺干个子问题进行解决。
<img width="602" height="782" alt="image" src="https://github.com/user-attachments/assets/abecb9d9-5bf2-4126-915a-cfde8f72a1de" />
## 应用层
应用层只专注于为用户层提供应用功能。可以直观理解，应用层将人类想要的应用功能翻译为计算机能够理解的语言，而不同的功能基于不同的协议，如http，smtp...
## 传输层
传输层不关心应用层信息，只关注于传输的逻辑控制，也就是说传输层不管你的信息是什么，只负责用我的传输协议（TCP或者UDP）来把你送到目标应用程序。  
传输层封装的是如何在两个应用程序之间传递信息，包括：  
从谁传到谁：由端口号标识  
怎么传：控制信息  
## 网络层
网络层负责消息的逻辑传输：  
网络层负责在全局范围内规划数据包的传输路径，并通过IP协议封装了源设备和目标设备的IP地址，使得数据包能够跨越多个网络最终到达目的地。
## 网络接口层
实际中消息是在以太网中传递的，电脑上的以太网接口，WIFI-接口，以太网交换机，路由器上的千兆网口，还有网线，都是以太网的组成部分。  
以太网就是一种在局域网内，使得附件的设备能够进行通讯的技术。  
MAC地址就是在以太网中传输的地址，所以网络接口层，又给消息加上了MAC头部。  
<img width="905" height="501" alt="image" src="https://github.com/user-attachments/assets/bd9fe98f-fbda-4149-aea7-5d2ca8bff80b" />
# 从浏览器键入网页，到显示网页，到底发生了什么？
## HTTP
浏览器首先做的是把键入的URL，解析成为发给web服务器的请求消息。  
首先我们要搞清楚URL的组成：
<img width="1503" height="1879" alt="image" src="https://github.com/user-attachments/assets/9d8be048-adaf-4a34-8798-d0e8678607cb" />
对URL解析之后，浏览器就确定了Web服务器和文件名，接着就根据这些信息生成HTTP请求消息。

## DNS
浏览器生成HTTP请求信息后，会让操作系统发给Web服务器，这时候需要知道Web服务器的IP地址。  
就像电话通讯录一样，Web服务器的域名相当于人名，而IP地址就是电话号码了。而DNS服务器，就是通讯录本身，他存储了这种对应关系。  
域名的层次关系如图所示：  
<img width="621" height="420" alt="image" src="https://github.com/user-attachments/assets/395c8e77-75b7-4623-bbcb-560702935784" />
根DNS服务器 (.) 全球只有13组，分布在世界各地  
顶级域名DNS服务器 (.com, .org, .cn)  由各个域名注册机构管理  
权威DNS服务器 (example.com) 由域名所有者或托管商管理  
递归DNS服务器（你实际查询的） 由ISP、Google、Cloudflare等提供  
具体的查询方式呢，首先会查本地的缓存，如果有就直接调用，没有的话，就像在政府机关办事一样，只不过一开始就找到了一把手，一把手再推给下面，最后得到答案：  
步骤1：查询根DNS服务器  
递归DNS → 根DNS服务器  
问："www.example.com 在哪？"  
根DNS回答："我不知道具体地址，但负责 .com 的服务器在这里：[.com DNS服务器IP列表]"  

步骤2：查询顶级域名DNS服务器  
递归DNS → .com DNS服务器  
问："www.example.com 在哪？"  
.com DNS回答："我不知道具体地址，但负责 example.com 的权威服务器在这里：[example.com DNS服务器IP列表]"

步骤3：查询权威DNS服务器  
递归DNS → example.com 权威DNS服务器  
问："www.example.com 在哪？"  
权威DNS回答："www.example.com 的IP地址是 93.184.216.34"  

步骤4：返回结果并缓存  
递归DNS → 你的电脑  
回答："www.example.com 的IP地址是 93.184.216.34"  
（同时递归DNS会缓存这个结果，下次直接返回）  
<img width="1505" height="1095" alt="image" src="https://github.com/user-attachments/assets/d53e21c2-db42-4ba6-b0ed-7c502eab317b" />

## 协议栈
当获得IP地址后，HTTP的传输工作就可以交给操作系统中的协议栈了。  
<img width="903" height="917" alt="image" src="https://github.com/user-attachments/assets/a799f262-cd51-4121-af62-80371f6d66a4" />
IP 中还包括 ICMP 协议和 ARP 协议。  
ICMP：用于告知网络包传送过程中产生的错误以及各种控制信息。  
ARP：用于根据 IP 地址查询相应的以太网 MAC 地址。  
可以说，协议栈就是把我们之前讨论的TCP/IP分层模型，在操作系统中实际落地的软件实现。

## TCP
HTTP是基于TCP协议传输的。  
<img width="978" height="749" alt="image" src="https://github.com/user-attachments/assets/f12041ba-ad20-46d7-8da0-fef4f7ea3578" />
源端口号和目标端口号不用多言，接下来是包的序号，是为了解决包乱序的问题。  
还有确认号，这是为了确定发出去后对方有没有收到，如果没有收到就应该重新发送，直到送达，这个是为了解决丢包的问题。
接下来是状态位，例如SYN是为了发起一个连接，ACK是回复，RST是重新连接，FIN是结束连接等。TCP 是面向连接的，因而双方要维护连接的状态，这些带状态位的包的发送，会引起双方的状态变更。  
接下来是窗口大小，TCP是会做流量控制的，通信双方各声明一个窗口（缓存大小），标识自己当前能够的处理能力。
TCP还会做拥塞控制，通信双方各声明一个窗口（缓存大小），标识自己当前能够的处理能力。
在HTTP传输数据之前，首先TCP要建立连接，TCP连接建立，称为三次握手。
<img width="1221" height="1019" alt="image" src="https://github.com/user-attachments/assets/1aa7f93e-ef9d-486f-962a-0415422d878d" />
一开始，客户端和服务器都处于closed状态，先是服务端主动监听某个端口，处于Listen状态。  
然后客户端发起连接SYN，之后处于SYN-Sent状态。  
接着服务端收到连接，返回SYN，并且ACK客户端的SYN，之后处于SYN_RCVD状态。  
客户端收到服务端的SYN和ACK后，发送对SYN确定的ACK，之后处于ESTABLISHED状态。  
所以三次握手目的是保证双方都有接收和发送的能力。  
### TCP分割数据
如果HTTP的请求信息过长，超过了MSS的长度，TCP是会将数据分割成一块块的数据包来分别发送的。
<img width="1067" height="422" alt="image" src="https://github.com/user-attachments/assets/6d68cb50-fe01-40cf-879f-e91261ff0a98" />
MTU：一个网络包的最大长度，在以太网中，这个长度为1500字节。  
MSS: 除去IP和TCP头部后，一个网络包的最大长度。  
数据会被以MSS的长度为单位拆分，拆分出来的每一块数据都会被放进单独的数据包中，然后加上TCP和IP头部。  
<img width="1142" height="702" alt="image" src="https://github.com/user-attachments/assets/6fb5e8f9-4379-4337-bb81-bd4f28ed6359" />

TCP协议中有两个端口，一个是浏览器监听的端口，通常是随机生成的，一个是Web服务器监听的端口，通常是80(HTTP)或443(HTTPS)。至此，网络包的报文如下：  
<img width="1233" height="1038" alt="image" src="https://github.com/user-attachments/assets/d755ac8d-bee7-443b-965e-fc0063ff584b" />

## IP
TCP 模块在执行连接、收发、断开等各阶段操作时，都需要委托 IP 模块将数据封装成网络包发给通信对象。  
### IP包头形式
<img width="906" height="1806" alt="image" src="https://github.com/user-attachments/assets/c5b17545-7450-4b44-97c5-458f51e474eb" />
在IP协议里面需要有源地址IP和目标地址IP。  
因为HTTP是经过TCP传输的，所以在IP包头的协议号，要写成06(十六进制)。  
假设客户端有多个网卡，在填写源地址IP时，就需要判断到底应该填写哪个地址。这个判断相当于在多块网卡中判断应该使用哪个一块网卡来发送包。这个时候就需要用路由表规则。  
至此，网络包的报文如下图：
<img width="1851" height="2729" alt="image" src="https://github.com/user-attachments/assets/1b683f2b-7791-4127-a4c8-81f676defbfe" />

## MAC
生成了IP头部之后，接下来网络包还需要加上MAC头部。MAC 头部是以太网使用的头部，它包含了接收方和发送方的 MAC 地址等信息。
<img width="558" height="558" alt="image" src="https://github.com/user-attachments/assets/60096e53-1da1-45a2-8547-f57fd044da15" />
一般在TCP/IP通信里，协议类型只有：  
0800：IP协议  
0806：ARP协议  
MAC的发送方和接收方的地址如何确认呢？发送方的地址确认很简单，是在网卡生产时写入到 ROM 里的，只要将这个值读取出来写入到 MAC 头部就可以了。  
接收方的地址确认比较复杂，首先需要查找路由表来搞清楚把包发给哪个IP，知道后如何得到MAC地址呢？这时需要用ARP协议。ARP协议会在以太网中，以广播的形式查找MAC地址：
<img width="594" height="498" alt="image" src="https://github.com/user-attachments/assets/d5237ac2-bad3-4e9b-992c-10817d6c5a54" />
需要理解的关键一点是，协议栈不需要知道，也不能知道目标服务器的MAC地址，因为MAC地址是用在同一个以太网中的，所以实际上得到是下一跳的目标设备的MAC地址。

## 网卡
网络包只是一串网络信息，需要转换成电信号来通过网线传输，这时就需要网卡，而控制网卡的是网卡的驱动程序。  
网卡驱动获取网络包后，会将其复制到网卡内的缓存区域，然后会在开头添加上报头和起始帧分界符，在末尾加上用来检验错误的帧校验序列。  
<img width="968" height="392" alt="image" src="https://github.com/user-attachments/assets/8c5ceb14-f474-40f3-ab59-b2aac59c228a" />
起始帧分界符是一个用来表示包起始位置的标记  
末尾的FCS（帧校验序列）用来检查包传输过程是否有损坏  

## 交换机
首先，电信号到达网线接口，交换机里的模块进行接收，接下来交换机里的模块将电信号转换为数字信号。然后通过包末尾的FCS校验错误，如果没问题则放到缓冲区。这部分操作基本和计算机的网卡相同，但交换机的工作方式和网卡不同。  
计算机的网卡本身具有MAC地址，并通过核对收到的包的接收方 MAC 地址判断是不是发给自己的，如果不是发给自己的则丢弃；相对地，交换机的端口不核对接收方 MAC 地址，而是直接接收所有的包并存放到缓冲区中。因此，和网卡不同，交换机的端口不具有MAC地址。















