ioc发展简介

ioc主要实现策略

ioc容器的职责

ioc容器的实现

传统ioc容器的实现

轻量级ioc容器

依赖查找和依赖注入

构造器注入和setter注入



ioc发展简介

好莱坞原则

Martin Fowler提出ico对di的理解



ioc主要的实现策略

依赖查找

依赖注入



ioc容器的职责

- 通用职责
- -依赖处理
  - 依赖查找
  - 依赖注入
- 生命周期管理
  - 容器
  - 托管的资源（Java Beans或其他资源）
- 配置
  - 容器
  - 外部化配置
  - 托管的资源





ioc 的主要实现

- 主要实现
- java se
  - java beans
  - java serviceloader spi
  - jndi
- javaee
  - ejb 
  - servlet
- 开源框架



传统ioc容器的实现

- java Beans作为IOC容器
  - 特性
    - 依赖查找
    - 生命周期管理
    - 配置元信息
    - 时间
    - 自定义
    - 资源管理
    - 持久化
  - 规范
    - java Beans
    - BeanContext





