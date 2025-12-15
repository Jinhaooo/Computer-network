# HTTP 和 HTTPS
## HTTP和HTTPS的区别  
1.HTTPS在 TCP 和 HTTP 网络层之间加入了 SSL/TLS 安全协议，使得报文能够加密传输。  
2.HTTPS 在 TCP 三次握手之后，还需进行 SSL/TLS 的握手过程，才可进入加密报文传输。  
3.两者的默认端口不一样，HTTP 默认端口号是 80，HTTPS 默认端口号是 443。  
4.HTTPS 协议需要向 CA（证书权威机构）申请数字证书，来保证服务器的身份是可信的。  

## HTTPS是如何解决HTTP的风险的？
### 1.混合加密
通过混合加密的方式可以保证信息的机密性，解决了窃听的风险。  
<img width="613" height="471" alt="image" src="https://github.com/user-attachments/assets/3a1a45d5-00eb-418d-a88e-8ab08f47f1f3" />  

