# HTTP1.1, HTTP2.0, HTTP3.0演变
## HTTP 1.1 相对于 HTTP 1.0的优化
### HTTP1.1 相比 HTTP/1.0 性能上的改进：  
使用长连接的方式改善了 HTTP/1.0 短连接造成的性能开销。  
支持管道（pipeline）网络传输，只要第一个请求发出去了，不必等其回来，就可以发第二个请求出去，可以减少整体的响应时间。  
### HTTP1.1的性能瓶颈：  
请求 / 响应头部（Header）未经压缩就发送，首部信息越多延迟越大。只能压缩 Body 的部分；  
发送冗长的首部。每次互相发送相同的首部造成的浪费较多；  
服务器是按请求的顺序响应的，如果服务器响应慢，会招致客户端一直请求不到数据，也就是队头阻塞；  
没有请求优先级控制；  
请求只能从客户端开始，服务器只能被动响应。  

## HTTP2的优化
HTTP2协议是基于HTTPS的，所以安全性有所保障。  
<img width="587" height="366" alt="image" src="https://github.com/user-attachments/assets/85893621-eb6a-4da1-a146-84114ceab06f" />  
那 HTTP/2 相比 HTTP/1.1 性能上的改进：  
### 头部压缩  
HTTP/2 会压缩头（Header）如果你同时发出多个请求，他们的头是一样的或是相似的，那么，协议会帮你消除重复的部分。  
这就是所谓的 HPACK 算法：在客户端和服务器同时维护一张头信息表，所有字段都会存入这个表，生成一个索引号，以后就不发送同样字段了，只发送索引号，这样就提高速度了。  
详细知识参看小林coding：https://xiaolincoding.com/network/2_http/http2.html

### 二进制格式  
HTTP/2 不再像 HTTP/1.1 里的纯文本形式的报文，而是全面采用了二进制格式，头信息和数据体都是二进制，并且统称为帧（frame）：头信息帧（Headers Frame）和数据帧（Data Frame）。  
<img width="1111" height="564" alt="image" src="https://github.com/user-attachments/assets/ecc95e68-1962-490b-bcb0-ab268f1176af" />  
计算机收到报文后，无需把明文报文转换为二进制，而是可以直接解析，提高了传输的速度。  

### 并发传输  
HTTP1.1的实现是基于请求响应模型的，如果响应迟迟不来，请求是无法发送的，这就造成了对头堵塞。而HTTP2引出了引出了 Stream 概念，多个 Stream 复用在一条 TCP 连接。  
<img width="464" height="535" alt="image" src="https://github.com/user-attachments/assets/75de6469-6788-45b3-b772-3f1cc8bf3f10" />   
从上图可以看到，1 个 TCP 连接包含多个 Stream，Stream 里可以包含 1 个或多个 Message，Message 对应 HTTP/1 中的请求或响应，由 HTTP 头部和包体构成。Message 里包含一条或者多个 Frame，Frame 是 HTTP/2 最小单位，以二进制压缩格式存放 HTTP/1 中的内容（头部和包体）。  
针对不同的 HTTP 请求用独一无二的 Stream ID 来区分，接收端可以通过 Stream ID 有序组装成 HTTP 消息，不同 Stream 的帧是可以乱序发送的，因此可以并发不同的 Stream ，也就是 HTTP/2 可以并行交错地发送请求和响应。  
<img width="787" height="228" alt="image" src="https://github.com/user-attachments/assets/0035d339-8a01-4a79-be1b-ef78b91d906f" />  
总结来说，HTTP1.1没有完全利用TCP连接的空间，好比四车道的路，它却强制车辆单列行驶，而HTTP2解决了这个问题。

### 服务器主动推送资源  
HTTP/2 还在一定程度上改善了传统的「请求 - 应答」工作模式，服务端不再是被动地响应，可以主动向客户端发送消息。  
客户端和服务器双方都可以建立 Stream，  Stream ID 也是有区别的，客户端建立的 Stream 必须是奇数号，而服务器建立的 Stream 必须是偶数号。  
<img width="1482" height="598" alt="image" src="https://github.com/user-attachments/assets/eecd094a-c913-491d-affc-49b57e83e89a" />  

### HTTP2的缺陷
HTTP2仍然存在队头堵塞的问题，但是这个问题是TCP层面的：
HTTP/2 是基于 TCP 协议来传输数据的，TCP 是字节流协议，TCP 层必须保证收到的字节数据是完整且连续的，这样内核才会将缓冲区里的数据返回给 HTTP 应用，那么当「前 1 个字节数据」没有到达时，后收到的字节数据只能存放在内核缓冲区里，只有等到这 1 个字节数据到达时，HTTP/2 应用层才能从内核中拿到数据，这就是 HTTP/2 队头阻塞问题。  
<img width="1011" height="377" alt="image" src="https://github.com/user-attachments/assets/200fa534-1b18-4727-8317-e665ec1d20b7" />  
<img width="521" height="502" alt="image" src="https://github.com/user-attachments/assets/f1affa64-a547-4ad0-801b-072a9fb814ab" />  
所以，一旦发生了丢包现象，就会触发 TCP 的重传机制，这样在一个 TCP 连接中的所有的 HTTP 请求都必须等待这个丢了的包被重传回来。  

## HTTP3的优化
HTTP3把HTTP下层的协议改成了UDP，解决了HTTP2的TCP造成的对头堵塞问题。  
<img width="782" height="366" alt="image" src="https://github.com/user-attachments/assets/fdd82dd1-e459-4fd1-bbf8-5797ee538977" />  
UDP 发送是不管顺序，也不管丢包的，所以不会出现像 HTTP/2 队头阻塞的问题。大家都知道 UDP 是不可靠传输的，但基于 UDP 的 QUIC 协议 可以实现类似 TCP 的可靠性传输。  

QUIC有以下三个特点：  
### 1.无队头堵塞
QUIC 协议也有类似 HTTP/2 Stream 与多路复用的概念，也是可以在同一条连接上并发传输多个 Stream，Stream 可以认为就是一条 HTTP 请求。  
QUIC 有自己的一套机制可以保证传输的可靠性的。当某个流发生丢包时，只会阻塞这个流，其他流不会受到影响，，因此不存在队头阻塞问题。这与 HTTP/2 不同，HTTP/2 只要某个流中的数据包丢失了，其他流也会因此受影响。  
<img width="1011" height="377" alt="image" src="https://github.com/user-attachments/assets/f338fcdf-364b-4978-8bce-efcb7d5cb800" />  

### 2.更快的连接建立
对于 HTTP/1 和 HTTP/2 协议，TCP 和 TLS 是分层的，分别属于内核实现的传输层、openssl 库实现的表示层，因此它们难以合并在一起，需要分批次来握手，先 TCP 握手，再 TLS 握手。  
HTTP/3 在传输数据前虽然需要 QUIC 协议握手，但是这个握手过程只需要 1 RTT，握手的目的是为确认双方的「连接 ID」，连接迁移就是基于连接 ID 实现的。  
但是 HTTP/3 的 QUIC 协议并不是与 TLS 分层，而是 QUIC 内部包含了 TLS，它在自己的帧会携带 TLS 里的“记录”，再加上 QUIC 使用的是 TLS/1.3，因此仅需 1 个 RTT 就可以「同时」完成建立连接与密钥协商，如下图：  
<img width="742" height="492" alt="image" src="https://github.com/user-attachments/assets/5873a3b2-d15e-4c5d-9bca-a1a57a1c3f3b" />  
甚至，在第二次连接的时候，应用数据包可以和 QUIC 握手信息（连接信息 + TLS 信息）一起发送，达到 0-RTT 的效果。  
<img width="1019" height="681" alt="image" src="https://github.com/user-attachments/assets/b8aa85ce-39cb-4f4a-8b8a-ea0a42997f53" />  

### 3.连接迁移
基于 TCP 传输协议的 HTTP 协议，由于是通过四元组（源 IP、源端口、目的 IP、目的端口）确定一条 TCP 连接。  
<img width="821" height="228" alt="image" src="https://github.com/user-attachments/assets/e960d057-2033-4aef-aebb-2a0ee242ef37" />  
那么当移动设备的网络从 4G 切换到 WIFI 时，意味着 IP 地址变化了，那么就必须要断开连接，然后重新建立连接。而建立连接的过程包含 TCP 三次握手和 TLS 四次握手的时延，以及 TCP 慢启动的减速过程，给用户的感觉就是网络突然卡顿了一下，因此连接的迁移成本是很高的。  
而 QUIC 协议没有用四元组的方式来“绑定”连接，而是通过连接 ID 来标记通信的两个端点，客户端和服务器可以各自选择一组 ID 来标记自己，导致 IP 地址变化了，只要仍保有上下文信息（比如连接 ID、TLS 密钥等），就可以“无缝”地复用原连接，消除重连的成本，没有丝毫卡顿感，达到了连接迁移的功能。  
所以， QUIC 是一个在 UDP 之上的伪 TCP + TLS + HTTP/2 的多路复用的协议。










