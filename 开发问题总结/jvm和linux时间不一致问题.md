# jvm时间和系统时间不一致问题

## 前提引入：

在项目开发过程中发现一个更新其他字段的sql 竟然在数据库中把updateTime字段修改了，而且修改的时间与当前系统北京时间相差了接近八个小时。

mybatis 中update更新语句为

```
  <update id="updateLogSend" >
    update xxxx set xxxx = #{xxxx}
    where xxxxid = #{xxxxxId}
  </update>
```

通过排查发现在数据库中 对于update_time字段 定义如下

```java
`update_time` timestamp NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP 
```

同时只有使用upate 语句进行更新的时候（有值改变）才会对update_time进行更新

使得在进行更新的时候数据库时间自动更新，以前未发现这个bug原因是对于update更新语句传入的参数都是使用了java Date类生成时间，进行重新赋值，所以没有这个问题。

因为刚好相差 八个小时 ，估计是时区的问题所以进行排查。发现linux系统时间确实跟更新数据库时间一样。

使用的Date —R 指令查看指令时间 当前北京时间为19:58 linux服务器时间为11:58

找到问题之后产生疑问为什么使用java类重新生成的时间确实是北京时间，难道这边跟系统时间不一样吗？

方式1：

```csharp
jinfo 1728 |grep user.timezone
```

查看jvm 运行pid 设置时间

方式2：

查看JVM虚拟机参数 通过jps  或者ps -ef |grep java 指令查看 当前运行的java项目

获取到pid之后使用jinfo  （可以实时查看和调整jvm配置参数）

![image-20200804204612823](https://raw.githubusercontent.com/cpp-wen/picGo/master/imgTest/image-20200804204612823.png)

可以看到服务器这边jvm设置的时间为上海时间。

方式3：

通过`jvisualvm`即可得到、

![img](https://upload-images.jianshu.io/upload_images/6580486-6fc968800e5b6531.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

## 解决方法

1. 在程序中使用java的函数设定时区。

2. 在启动java程序时加参数-Duser.timezone=GMT+8
3. 修改etc/sysconfig/clock 文件 因为jre是从/etc/sysconfig/clock这个文件中 获取时区信息的

```java
附clock文件内容：

ZONE="Asia/Shanghai"
UTC=false
ARC=false

ZONE -- 时区
UTC  -- 表明时钟设置为UTC。 
ARC  -- 仅用于alpha表明使用ARC。　
```



