# spring   RestTemplate 

什么是 RestTemplate？ 

RestTemplate是一个http请求的客户端工具，它不是类似HttpClient的东东，也不是类似Jackson，jaxb等工具，但它封装了这些工具．

RestTemplate能做什么？ 

RestTemplate是Spring 调用http的client端核心类．顾名思义，与其它template类如 `JdbcTemplate`一样，它封装了与http server的通信的细节，使调用端无须关心http接口响应的消息是什么格式，统统使用Java pojo来接收请求结果．它主要做了以下事情：

- 封装并屏蔽了http通信细节
- 组装Http请求参数
- 抽取结果并转成调用端指定的Java pojo对象
- 屏蔽了不同消息格式的差异化处理

在http通信的底层，它默认采用的是Jdk的标准设施，当然你也可以切换成其它的http类库 例如 `Apache` `HttpComponents`, `Netty`, and `OkHttp`等.

> RestTemplate这个类是用在同步调用上的，异步调用请移步 `AsyncRestTemplate`