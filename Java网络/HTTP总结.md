HTTP的总结 个人感觉cyc 总结的蛮好的 看完了专栏后感觉cyc专栏http很全面适合先看完[HTTP最全解析](https://juejin.im/post/5ea4f293f265da47f4070533)后再看。下面是自己复习HTTP进行的内容总结。有部分图片来自参考的博客。

# url和uri

URI和URL   identity和location  统一资源定位符和统一资源标志符。



# 请求方式

**请求方式的变化**：不同版本对应请求方式的变化

| 版本        | 支持请求方式                                 |
| ----------- | -------------------------------------------- |
| **HTTP0.9** | GET                                          |
| **HTTP1.0** | HEAD,POST（新增）                            |
| **HTTP1.1** | OPTIONS，PATCH，DELETE，PUT，CONNECT（新增） |

目前请求方式总共 **八种**

| 请求方式 | 作用                                                         |
| -------- | ------------------------------------------------------------ |
| get      | 获取资源                                                     |
| post     | 传输数据                                                     |
| put      | 修改资源                                                     |
| delete   | 删除资源                                                     |
| head     | 简易版get请求，无响应体内容，主要用于获取资源缓存有效时间等头部字段等内容 |
| patch    | 部分修改资源 与put区别是patch修改仅需要提交后端需要更新的字段，而put需要提供完整的实体对象 |
| options  | 获取支持的方法（即返回以上8种请求方法中支持的方法）          |
| connect  | 在与代理服务器通信时建立通道                                 |



# get和post的区别

**安全性** ：多次请求不会修改计算机资源

**幂等性** ：调用多次对资源是否有影响

1. 从安全性和幂等性来看：get请求符合安全性和幂等性，而post两者都不符合。
2. 从请求的方式来看：get请求字段放在url中是不安全的，post请求字段放在请求体中相对来说比get更安全。但是仍然可以使用抓包工具获取请求内容。（因为url仅支持ascii码，所以在url中 的中文会转换为ascii码，同时url有长度限制，所以get请求字段长度有一定的限制）。
3. 在使用 XMLHttpRequest （ajax原生方法）的 POST 方法时，浏览器会先发送 Header 再发送 Data。但并不是所有浏览器会这么做，例如火狐就不会。而 GET 方法 Header 和 Data 会一起发送。即 post会根据浏览器的不同选择发送两个tcp数据包或者一个，而get只有一个。



# post和put区别

单单从restful角度说post和put的区别是不正确的，应该从幂等性作为两者的主要区别。

1. 都可以用来增加资源，主要区别是put是幂等的，而post不是。
2. 在restful中默认post是新增资源，put是进行资源的修改。



# http报文格式

| 请求行 | 响应行 |
| ------ | ------ |
| 请求头 | 响应头 |
| 空行   | 空行   |
| 请求体 | 响应体 |

![image-20200425092339944](https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200425092341.png)

**请求行**包括

- 请求方法
- 请求目标
- HTTP协议版本号

这三个部分通常用空格来进行隔开，最后用CRLF来表示结束



**状态行**包括

- HTTP协议版本
- 状态码
- 原因 （如ok等）



# :hot_pepper: 头部字段

**头部字段分别**

- 请求字段
- 响应字段
- 通用字段

以下是比较重要的几个头部信息

**通用字段**

- cache-control ：控制缓存的行为
- connection：控制不再转发给代理的首部字段
- Transfor-Encoding：指定报文主体的传输编码方式

**请求字段**

- Accept：用户可处理的媒体类型
- if-Match：比较实体标记
- if-noMatch：比较实体标记（与if-match相反）
- If-Modified-Since:比较资源的更新时间
- If-Unmodified-Since:比较资源的更新时间(与if-Modified相反)
- Range：实体的字节范围请求
- host：指定特定主机处理

**响应字段**

- Location：重定向指定url
- ETag：资源的匹配信息
- Server ：服务器信息



# 状态码

状态码的分类为

- 1××：提示信息，表示目前是协议处理的中间状态，还需要后续的操作；
- 2××：成功，报文已经收到并被正确处理；
- 3××：重定向，资源位置发生变动，需要客户端重新发送请求；
- 4××：客户端错误，请求报文有误，服务器无法处理；
- 5××：服务器错误，服务器在处理请求时内部发生了错误。



**常用状态码**

**2××**

2××类状态码表示服务器收到并成功处理了客户端的请求，这也是客户端最愿意看到的状态码。

“**200 OK**”是最常见的成功状态码，表示一切正常，服务器如客户端所期望的那样返回了处理结果，如果是非 HEAD 请求，通常在响应头后都会有 body 数据。

“**204 No Content**”是另一个很常见的成功状态码，它的含义与“200 OK”基本相同，但响应头后没有 body 数据。所以对于 Web 服务器来说，正确地区分 200 和 204 是很必要的。

“**206 Partial Content**”是 HTTP 分块下载或断点续传的基础，在客户端发送“范围请求”、要求获取资源的部分数据时出现，它与 200 一样，也是服务器成功处理了请求，但 body 里的数据不是资源的全部，而是其中的一部分。



**3××**

3××类状态码表示客户端请求的资源发生了变动，客户端必须用新的 URI 重新发送请求获取资源，也就是通常所说的“重定向”，包括著名的 301、302 跳转。

“**301 Moved Permanently**”俗称“永久重定向”，含义是此次请求的资源已经不存在了，需要改用改用新的 URI 再次访问。

与它类似的是“**302 Found**”，曾经的描述短语是“**Moved Temporarily**”，俗称“临时重定向”，意思是请求的资源还在，但需要暂时用另一个 URI 来访问。



**4××**

4××类状态码表示客户端发送的请求报文有误，服务器无法处理，它就是真正的“错误码”含义了。

“**400 Bad Request**”是一个通用的错误码，表示请求报文有错误，但具体是数据格式错误、缺少请求头还是 URI 超长它没有明确说，只是一个笼统的错误，

“**403 Forbidden**”实际上不是客户端的请求出错，而是表示服务器禁止访问资源。原因可能多种多样，例如信息敏感、法律禁止等，如果服务器友好一点

“**404 Not Found**”可能是我们最常看见也是最不愿意看到的一个状态码，它的原意是资源在本服务器上未找到



**5××**

5××类状态码表示客户端请求报文正确，但服务器在处理时内部发生了错误，无法返回应有的响应数据，是服务器端的“错误码”。

“**500 Internal Server Error**”与 400 类似，也是一个通用的错误码

“**501 Not Implemented**”表示客户端请求的功能还不支持

“**502 Bad Gateway**”通常是服务器作为网关或者代理时返回的错误码

“**503 Service Unavailable**”表示服务器当前很忙，暂时无法响应服务，我们上网时有时候遇到的“网络服务正忙，请稍后重试”的提示信息就是状态码 503





# https

HTTPS相对与HTTP实现了加密（防窃听）、认证（防伪装）和完整性保护（防篡改）。

**加密**

对称加密:指加密和解密使用的秘钥是同一个。只要秘钥是安全的，双方的通信就是安全的。

主流对称加密方法：AES和Chacha20。

非对称加密:指加密和解密使用的秘钥不是同一个，分别私钥和公钥，公钥是公开的主要用来解密，而私钥为加密方私有主要用来加密，除此之外私钥还能用来进行数字签名。

主流非对称加密方法：RSA和ECC

组合加密:对称加密 加密解密速度快，无法实现秘钥的安全传输。非对称加密可以更安全的实现加密和解密，加密解密速度慢。结合两者优点HTTPS采用 **混合加密** ，第一次通信使用非对称加密传输对称加密秘钥，之后使用对称加密进行通讯，从而提升效率。

加密分组模式（科普）

对称算法还有一个“**分组模式**”的概念，它可以让算法用固定长度的密钥加密任意长度的明文，把小秘密（即密钥）转化为大秘密（即密文）。



**完整性保护** 

摘要算法：一般使用哈希函数或者散列函数实现保证内容一旦改变，通过算法加密的结果也会改变来确保内容的完整性。

主流摘要算法：SHA2(MD5以及SHA1由于算法安全性不足被TLS禁用)



**认证（难点）**

如何来确保第一个获取到的公钥是请求网站公钥？为解决这个问题引入CA，数字证书和数字签名。

CA指证书认证机构，起权威角色，否则为了传输公钥而不断引入加密进入死循环问题。

数字证书包括数字签名以及当前网站的公开信息。

数字签名是对网站公开信息使用单向HASH算法生成摘要，再使用CA的私钥进行再次加密。用户获取到证书，使用CA公钥进行解密，获得摘要，再对公开信息使用单向HASH算法进行加密后与摘要进行比较。相同则安全。 以上称为 **签名**和 **验签**

![img](https://upload-images.jianshu.io/upload_images/292372-60fd73c3f79e8641.png?imageMogr2/auto-orient/strip|imageView2/2/w/433/format/webp)

![img](https://upload-images.jianshu.io/upload_images/292372-4dbf41053fb6fb25.png?imageMogr2/auto-orient/strip|imageView2/2/w/485/format/webp)



![image-20200429142137466](C:\Users\caopeng\AppData\Roaming\Typora\typora-user-images\image-20200429142137466.png)





# http版本对比

HTTP/0.9最初版本 到HTTP/1.0过度版本 到HTTP1.1主要版本 到HTTP/2升级版本以及未普及的HTTP/3,以介绍主流的两个版本为例。

短连接->长连接->虚拟流传递

纯文本格式->二进制格式

重复请求头传递->头部压缩算法HPACK

请求响应形式->服务器主动推送

### **HTTP0.9** 

1. 采用纯文本形式
2. 只允许使用GET获取资源，获得后就断开连接

### **HTTP1.0**

1. 增加了 HEAD、POST 等新方法；
2. 增加了响应状态码，标记可能的错误原因；
3. 引入了协议版本号概念；
4. 引入了 HTTP Header（头部）的概念，让 HTTP 处理请求和响应更加灵活；
5. 传输的数据不再仅限于文本。

### **HTTP1.1** 

1. 增加了 PUT、DELETE 等新的方法；
2. 增加了缓存管理和控制；
3. 明确了连接管理，允许持久连接；
4. 允许响应数据分块（chunked），利于传输大文件；
5. 强制要求 Host 头，让互联网主机托管成为可能。

> 缓存管理和控制：当浏览器获得资源后，服务器回传资源有效时间，浏览器将参数放在Cache-Control中下次请求时判断缓存是否过期再次请求	

### **HTTP2**

1. 二进制协议，不再是纯文本；
2. 可发起多个请求，废弃了 1.1 里的管道；
3. 使用专用算法压缩头部，减少数据传输量；
4. 允许服务器主动向客户端推送数据；
5. 增强了安全性，“事实上”要求加密通信。





> 参考资料 
>
> [掘金博客](https://juejin.im/post/5d032b77e51d45777a12618)
>
> [Cyc2018笔记]([https://github.com/CyC2018/CS-Notes/blob/master/notes/HTTP.md#%E5%85%ADhttps](https://github.com/CyC2018/CS-Notes/blob/master/notes/HTTP.md#六https))
>
> 极客时间 详解http专栏
>
> [参考博客](https://juejin.im/post/5caab0bff265da24cf311d5b )
>
> [参考博客](https://www.jianshu.com/p/4932cb1499bf) 
>
> [参考博客](https://www.jianshu.com/p/705dcd60c264)