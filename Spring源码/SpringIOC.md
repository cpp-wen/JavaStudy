spring面试经常问到两个问题IOC和AOP，之前对面试都没有一个很好的总结，这回突击一下学学IOC和AOP内部是如何实现的。



`Spring` 是分模块开发的，`Spring` 包含了很多模块，其中最为核心的是 `bean` 容器相关模块。像 `AOP`、`MVC`、`Data` 等模块都要依赖 `bean` 容器。这里先看一下 `Spring` 框架的结构图：

![img](https://gitee.com/duchaochen/gongzhonghao/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/Spring%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90/images/01-2.jpg)

从上图中可以看出`Core Container`处于整个框架的最底层（忽略 `Test` 模块），在其之上有 `AOP`、`Data`、`Web` 等模块。既然 `Spring` 容器是最核心的部分，那么大家如果要读 `Spring` 的源码，容器部分必须先弄懂。





alias bean别名

autowired bean注入 非注解形式

`FactoryBean`？看起来是不是很像 `BeanFactory` 孪生兄弟。不错，他们看起来很像，但是他们是不一样的。`FactoryBean` 是一种工厂 `bean，`与普通的 `bean` 不一样，FactoryBean 是一种可以产生 `bean` 的 `bean`，好吧说起来很绕嘴。`FactoryBean` 是一个接口，

depends-on

如果大家觉得看 `Spring` 源码比较困难，可以先学习一下 `tiny-spring` 的源码，先对 `Spring` 代码结构有个大概的了解。

Spring的源码太复杂 ioc这块面试基本回答不出来。





## 第二部分 ：获取单例bean

