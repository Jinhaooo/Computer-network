# HTTP缓存机制  
为了避免重复发送HTTP请求，可以把这对请求-响应的数据缓存到本地。HTTP有两种缓存方式，分别是强制缓存和协商缓存。  

## 强制缓存
强缓存指的是只要浏览器判断缓存没有过期，就直接使用浏览器的本地缓存，决定是否使用缓存的是浏览器。  
强缓存是利用以下的两个HTTP字段来实现的，它们都用来表示资源在客户端缓存的有效时间。  
Cache-Control： 是一个相对时间；  
EXPIRES：是一个绝对时间。  
Cache-control的优先级更高，而且选项更多，设置更加精细，一般最好用Cache-control.具体流程如下：  
当浏览器第一次请求访问服务器资源时，服务器会在返回这个资源的同时，在 Response 头部加上 Cache-Control，Cache-Control 中设置了过期时间大小；  
浏览器再次请求访问服务器中的该资源时，会先通过请求资源的时间与 Cache-Control 中设置的过期时间大小，来计算出该资源是否过期，如果没有，则使用该缓存，否则重新请求服务器；  
如果没有，则使用该缓存，否则重新请求服务器。（计算的逻辑很简单，当前时间 - 资源首次获取时间 < Cache-Control 中的 max-age）  

## 协商缓存  
当我们在浏览器使用开发者工具的时候，你可能会看到过某些请求的响应码是 304，这个是告诉浏览器可以使用本地缓存的资源，这种通过服务器告诉客户端能否使用本地缓存的方式称为协商缓存。  
<img width="1017" height="1127" alt="image" src="https://github.com/user-attachments/assets/fb863efd-47ee-4963-950e-cd2bc4d834d4" />  
<img width="1017" height="1127" alt="image" src="https://github.com/user-attachments/assets/2c9b7b4b-9ce5-4f71-b9fa-d985a99d4f6b" />  
协商缓存就是与服务器协商之后，通过协商结果来判断是否使用本地缓存。  
协商缓存可以基于两种头部来实现。  
第一种：请求头部中的 If-Modified-Since 字段与响应头部中的 Last-Modified 字段实现，这两个字段的意思是：  
响应头部中的 Last-Modified：标示这个响应资源的最后修改时间；  
请求头部中的 If-Modified-Since：当资源过期了，发现响应头中具有 Last-Modified 声明，则再次发起请求的时候带上 Last-Modified 的时间，  
服务器收到请求后发现有 If-Modified-Since 则与被请求资源的最后修改时间进行对比（Last-Modified），如果最后修改时间较新（大），说明资源又被改过，则返回最新资源，HTTP 200 OK；  
如果最后修改时间较旧（小），说明资源无新修改，响应 HTTP 304 走缓存。  

第二种：请求头部中的If-None-Match 字段与响应头部中的 ETag 字段，这两个字段的意思是：  
响应头部中 Etag：唯一标识响应资源；  
请求头部中的 If-None-Match：当资源过期时，浏览器发现响应头里有 Etag，则再次向服务器发起请求时，会将请求头 If-None-Match 值设置为 Etag 的值。服务器收到请求后进行比对，如果资源没有变化返回 304，如果资源变化了返回 200。

Etag的优先级要比Last-Modified的更高，这时因为Etag能够解决一些Last-Modified不能解决的问题：  
在没有修改文件内容情况下文件的最后修改时间可能也会改变，这会导致客户端认为这文件被改动了，从而重新请求；（情况较多，可以举例，时区问题，还有一些自动保存情况）  
可能有些文件是在秒级以内修改的，If-Modified-Since 能检查到的粒度是秒级的，使用 Etag就能够保证这种需求下客户端在 1 秒内能刷新多次；  
有些服务器不能精确获取文件的最后修改时间。  

注意，协商缓存的这两个字段也需要配合强缓存Cache-control来使用，也就是说只有未命中强制缓存的情况下，才会启用协商缓存。  

<img width="1348" height="1122" alt="image" src="https://github.com/user-attachments/assets/4da86f30-2076-49e9-83f4-a68bc5bb0c66" />





