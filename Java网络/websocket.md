# webscocket

http是一种无状态 的使用tcp进行可靠化连接的协议  无状态指 对于每个请求和相应不保存通讯状态，是非持久化的一种连接（非持久化定义为对于一个request只有一个response ）【这边需要区分长连接 短连接，长连接对于多个request 也是有多个reponse】

ajax和long poll 存在问题：服务器只能被动推送响应给请求端 所以客户端需要不断请求服务端，造成服务端资源的消耗，如果使用long poll，用户发送信息给服务端之后进入阻塞状态。

http->websoscket  添加 请求头字段 upgrade:websocket  connection:upgrade  以及websocket连接的一些校验字段

websocket 通过一次http握手连接  学术名字为 升级握手的机制

请求头比较重要参数：

Connection: Upgrade   中间代理商收到请求之后对更换协议进行处理

Upgrade: websocket  更换的协议

Sec-WebSocket-Key: afmbhhBRQuwCLmnWDRWHxw==  类似公钥的性质， 用于服务端发送消息的一个验证

Sec-WebSocket-Protocol: chat, superchat   标识 客户端支持的子协议的列表

Sec-WebSocket-Extensions: permessage-deflate; client_max_window_bits  客户端和服务端协商拓展的一个字段 



使用netty-websocket 进行分发路由 

[连接](https://cloud.tencent.com/developer/article/1032466)

使用scheduledThreadpool来实现定时任务 这块需要了解一下

```
public void pushMsg(){
        //模拟异步发送推送消息
        ScheduledExecutorService scheduledThreadPool = Executors.newScheduledThreadPool(1);
        scheduledThreadPool.scheduleWithFixedDelay(() -> {
            TextWebSocketFrame tws = new TextWebSocketFrame("服务器主动推送消息。当前服务器时间："+System.currentTimeMillis());
            // 群发
            ChannelSupervise.send2All(tws);
        }, 0,1, TimeUnit.SECONDS);

    }
    
    public static void main(String[] args) {
        MyWebSocketServer server = new MyWebSocketServer(8080);// 8081为启动端口
        
        server.pushMsg();
        
        server.start();
    }
```





## 技术选形

主要有三种分别是基于容器tomcat的， netty sockio以及netty实现 

