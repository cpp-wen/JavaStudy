在京东到家商家中心系统中，商家提出在 Web 端实现自动打印的需求，不需要人工盯守点击打印，直接打印小票，以节约人工成本。

## **解决思路**

关于问题的思考逻辑：

第一种：想到的是可以用ajax来轮询服务端获取最新订单，也就是**pull**。

第二种：我们是否可以用类似推送的设计来实现，也就是**push**。

两种思路我们评估其优缺点：

**ajax**方式实现简单，只需要定时从服务端pull数据即可，但也增加了很多次无效的轮询， 无形中增加服务端无效查询。

**push**方式实现稍复杂，需要服务端与PC端保持连接，这就需要建立长连接，最终通过长连接的方式来实现push效果。

经过讨论，我们选择了第二种，订单中心生产出的新订单，通过MQ的方式推送给web端，最终获得一个比较好的用户体验。

## **方案介绍**

关于长连接方案的选择，我们参考了不少帖子，最终选择使用websocket协议来实现长连接，类似场景如IM，服务端即时推送等都使用了这个协议。

接下来我们比较一下websocket的框架，比较主流的有**netty、tomcat、socketIO** 三个框架。

基于支持**websocket的容器**，开发简单，例如tomcat，但在高并发的支持不是很好，连接的时候容易连接断开，还有就是依赖容器。

**netty-socketIO**是在netty4基础之上做了一层封装，效率如同netty一样，是一个全平台方案，友好的API，京东的logbook也是用了socketIO来传递日志，也是我们的一个备选方案。

**netty**是业内主流的NIO框架，netty对javaNIO做了封装，让开发者更多关注业务，降低开发成本，很多著名的RPC框架都采用了netty作为传输层，友好的API，功能强大，内置了很多编解码协议，实现websocket协议也是十分方便。

那我们横向比较一下这些框架。

![img](https://pic2.zhimg.com/80/v2-b2b2a1b42d2190d32de7a7b188ed17f5_720w.jpg)

所以在选型方面我们还是定位在socketIO 与 netty 上面，在兼顾扩展性与灵活性的同时，我们也考虑到netty可以提供http的功能，最终我们选择了使用netty，当然socketIO封装了很多功能，也是十分强大，相比较来说netty更适合我们，比较轻量。

## netty的特性

**netty具有异步非阻塞的特性，传统IO是面向流的，NIO是面向缓冲区的，这也是它的非阻塞原因所在。**

netty的线程模型如图所示：

![img](https://picb.zhimg.com/80/v2-910548ff107f5f7213343e716ea5e8ed_720w.jpg)

这种模型就是我们常说的**Reactor**模型，**boss线程其实是一个独立的NIO线程池，用于接收client请求，默认线程池大小为1，worker线程池用于处理具体的读写操作，默认线程池大小为2\*cpu个数。**

在上述模型中要特别注意ExecutionHandler，ExecutionHandler是运行在worker线程中的，所以**耗时的操作最好在线程池中运行， 比如IO或者计算，不然会影响整个netty的吞吐。**

了解了这些，我们根据自己的业务设计出流程如下图所示：

步骤(1) web端请求服务端进行注册，注册成功保持长连接。

步骤(2)服务端发送MQ。

步骤(3)netty将收到的消息推送给web端。

步骤(4)web端调用打印控件进行打印，打印控件需提前安装好（打印控件是pc上安装的一个驱动程序，用过JS方式来调用）。

如果调用JS成功，控件将把打印信息放入打印队列，如果不成功，重复步骤(4)

![img](https://pic4.zhimg.com/80/v2-733777370a301abf32409e7261509063_720w.jpg)

当然现在的结构只是单机版，不满足生产条件，那将来的结构可能会演变成如下图所示:

![img](https://pic1.zhimg.com/80/v2-5044910795ed9165ca9a88d9dc35b12b_720w.jpg)

我们会在服务端与netty之间建立路由层，路由层的主要职责：

**第一：收集集群存活信息。**

**第二：记录落点，就是落在哪一台机器上面。**

**第三：接收消息与分发消息**。

有了这三种能力，我们就可以轻松的指定信息分发策略。这里我们希望使用http协议来路由，所以就需要netty有http短连接接收的能力 ，所以netty整体上需要长短连接两种能力。

讲了这么多，还是来点干货，下面是部分代码。

netty启动类，我们通过spring来启动netty，**因为netty启动会阻塞主线程，所以需要在子线程中来启动netty**，下面是启动参数。

![img](https://pic4.zhimg.com/80/v2-5117853961c1c94d8a2b782208445446_720w.jpg)

接着来写我们的ChannelInitializer，HttpServerCodec为编解码器，WSServerProtocolHandler为websocket协议握手，其中我们更关注业务层面自定义的两个**hander,httpRequestHandler,authorizeHandler。**

![img](https://pic3.zhimg.com/80/v2-9278360270b9e039c82c7c8bc4b40ebd_720w.jpg)

httpRequestHandler的作用是处理url是否合法，接收参数，httpRequestHandler此方法中也可以根据URI来过滤，自定义自己的短连接请求。

![img](https://picb.zhimg.com/80/v2-fd6afd5deaff195a346c2045514e2f35_720w.jpg)

authorizeHandler的作用是校验数据是否正确，如果正确会将channel保存到map中，通过map建立起业务ID与通道之间的关系。

校验的过程我们在authorizeHandler中的channelRead展开，如果未通过，直接关闭当前channel，如果通过校验，则通过ctx.fireChannelRead(msg);方法将信息传入下一个handler去处理。

在项目里主要是以传递参数来进行数据校验的，也就是通过URL传参来实现。在httpRequestHandler中我们将URL参数set到channel的attr中，并传递给了下一个handler，也就是authorizeHandler，**所以在authorize方法中我们可以利用get()方法得到参数值，u是经过加密的数据，我们需要在这里进行解密，解密失败，可认为校验失败。**

当然如果有跨应用的服务，也可以通过Cookie的方式来进行加密串的读写，通过request.getHeader 是可以获取Cookie中的信息，这就看具体业务了,示例代码如下：

![img](https://picb.zhimg.com/80/v2-84d4ff952f892a110f16c52f79ef56ad_720w.jpg)

这个map 可以理解为servlet中的session，当有信息需要传送给某个客户端时，我们调用map.get(key)方式的到当前该客户端的channel，调用writeAndFlush方法将信息发送出去，下面举例通过接收MQ消息后的处理逻辑。

接下来有人可能想到，那如果通道关闭了怎么办？map中的channel是不是就失效了呢？那其实我们还需要有一个类似心跳的机制去维护channel，间接的去维护这个map，如果是通道正常关闭，可以通过**channelInactive**方法来监听，如果是长时间空闲:在项目中我们使用了增加的**IdleStateHandler**来处理，通过覆盖**userEventTriggered**方法来监听空闲channel，当某个channel到达我们设置的超时时间时，netty会回调此方法。

至此，核心部分已经处理完成，剩下的就是通过保存的channel来发送信息给客户端了。

最后在web端，我们采用了 **reconnecting-websocket**，它是一个小型的 JavaScript 库，封装了 WebSocket API， 提供了在连接断开时自动重连的机制，很能够帮助我们完成断开重连的操作。

## 遇到的问题

经过测试，在ws的uri后面不能传递参数，不然在netty实现websocket协议握手的时候会出现断开连接的情况，针对这种情况在websocketHandler之前做了一层httpHander过滤，将传递参数放入channel的attr中，然后**重写request的uri**，并传入下一个管道中，基本上解决这个问题。

在读写空闲的时候尽量以发心跳包的方式维护连接，但在客户端由于网络不稳定或者是服务端重启，连接会断开，瞬间有可能接收不到订单消息，为此在客户端需要实现断开重连机制，此问题我们采用 **reconnecting-websocket**的js框架，此框架扩展了原生websocket的实现，做了断开重连机制，有效的防止断开后不能及时连接。

在测试过程中由于控件与小票机的问题，可能会出现打印异常或者小票机没纸的情况，Lodop控件其实是将打印信息放入电脑的打印队列，如果没纸了，小票机会报警，再次放入小票纸，打印机会自动打印队列中的数据。

出现调用控件异常偶尔发生，现在处理办法是在js中进行了的try catch 如果失败 进行重试，重试次数自定义，超过重试次数暂不做处理，此处还不太严谨，需要在进行优化。

## 总结

通过上面的实践，我们基本已经实现了web端的自动打印，经过长时间的内部测试，服务端与客户端通信稳定，我们将灰度商家做用户体验。

在特定的场景下，选择适当的技术会提高我们的效率，否则会适得其反。选择长连接，大家可以把握三个大原则：

服务端是否需要主动推送数据到客户端以实现控制的效果。

对于实时性的要求是否苛刻。

对于客户端是否需要关注其在线状态的实时变化。