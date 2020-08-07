# 网易实习项目——数源平台

项目需求介绍：
在开发过程中有一个需求，用户提交请求之后，后端将用户请求内容从kafka保存中hive中，这个过程需要一定时长。想要当后端hive数据保存完毕后，用户可以马上得到响应，可以创建的新日志。

实现过程版本迭代

因为考虑用户可以立马得到更新的状态码，采用websocket实现

websocket实现方式常见的只有两种

1.基于tomcat实现websocket

2.基于spring实现websocket

两者导入的包是不相同的，具体实现方式有很大区别。这边有一个坑：使用tomcat实现websocket 网络很少有关于如何获取请求头部信息 比如cookie 等，而我的业务场景需要获取token 所以使用第二种方式。 第一种方法也有解决办法 不过我未尝试[链接](https://stackoverflow.com/questions/18097334/how-can-i-get-a-cookie-value-inside-websocket-end-point)

第二个坑：使用两种方式实现都需要注意创建Spring中对象是单例的，而webscocket对象是只有连接的时候才实例化对象。并且多个连接就有多个对象，而@Autowired注解 是启动的时候就将对象注入。而不是在使用A对象的时候将A需要的B对象注入A中。所以你想注入Service是 得到的只能是null。解决方式有两种

分别是让service属于WebsocketServer这个类和运行时候动态从spring 容器中取出 Service .

第一种

```
	@Component
	@ServerEndpoint(value = "/ws/etlFlag/{flowLineId}")
    public class FlagWebsocketServer {
        @Autowired
        private void initLogSchemaService(LogSchemaServiceImpl logSchemaService) {
            FlagWebsocketServer.logSchemaService = logSchemaService;
        }
    }
```



1.0    考虑使用websocket只要用户请求过来就创建一个websocketsession，spring中实现websocket 常见有两种

