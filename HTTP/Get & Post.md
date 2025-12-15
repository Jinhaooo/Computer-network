#GET和POST
## 两者的区别
跟据RFC规范,GET的语义是从服务器获得指定的资源.这个资源可以是静态的文本、页面、图片视频等。GET 请求的参数位置一般是写在 URL 中，URL 规定只能支持 ASCII，所以 GET 请求的参数只允许 ASCII 字符 ，而且浏览器会对 URL 的长度有限制（HTTP协议本身对 URL长度并没有做任何规定）。  

<img width="455" height="321" alt="image" src="https://github.com/user-attachments/assets/a10c405f-0223-4eca-9f03-647a91a2f040" />

跟据RFC规范, POST的语义是根据请求负荷(报文Body)对指定的资源做出处理.具体的处理方式视资源类型而不同。POST 请求携带数据的位置一般是写在报文 body 中，body 中的数据可以是任意格式的数据，只要客户端与服务端协商好即可，而且浏览器不会对 body 大小做限制。  

<img width="455" height="231" alt="image" src="https://github.com/user-attachments/assets/e66920a3-ba5c-4898-945a-24ab26cc07be" />  

## GET和POST的方法是安全和幂等的吗?
在 HTTP 协议里，所谓的「安全」是指请求方法不会「破坏」服务器上的资源。  
所谓的「幂等」，意思是多次执行相同的操作，结果都是「相同」的。  
如果从RFC定义上看,GET就是安全和幂等的.所以,可以对GET请求的数据做缓存,浏览器中GET请求也可以保存为书签.  
而POST不安全也不幂等,因为提交数据会修改服务器的资源,多次提交就会多次修改.因此浏览器中POST请求一般不能保存为书签.  
但是在实际开发中,开发者可以对这两种请求添加不同的操作,这时候,两种请求的安全和幂等就不一定了.  


