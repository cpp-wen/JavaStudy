# JVM垃圾回收

前提引入：jvm内存区域

[内存区域前提引入1](https://github.com/leosanqing/Java-Notes/blob/master/JVM/Java内存区域/Java内存区域.md)

总结：内存区域是指jvm 线程 进程拥有的区域分别在内存中哪个位置。内存模型是指内存屏障 在多线程那块load、store那块。

内存区域分为：

- 线程私有
  - 虚拟机栈
  - 本地方法栈
  - 程序计数器
- 线程共享
  - 方法区
  - 堆

**虚拟机栈** 

中主要存放 栈帧 。栈帧中主要保存局部变量表，操作栈，动态连接，返回地址。

stackoverflow 问题

局部变量表 由局部变量空间组成（slot）构成【可以简单理解为一种数据结构】（slot简单理解为就是一块内存空间，基本数据类型中long double需要两个slot进行保存，而int 等只需要一个）

**本地方法栈** 

[本地方法栈链接](https://www.cnblogs.com/wade-luffy/p/5813747.html)

hotspot虚拟机将虚拟机栈和本地方法栈结合在一起

本地方法栈主要调用java中的native方法【native方法是c++/c编写的暴漏给java调用的方法】在线程调用native方法的时候 虚拟机栈并不会添加栈帧 而是通过动态链接的方式直接调用。（简答理解 本地方法栈就是c或者c++的一个栈）

**程序计数器**

私有的原因是，多核处理器中多个线程同时执行，每个线程都都可以执行自己的程序 所以程序计数器是线程私有。

**方法区**

- 类信息
- 常量
- 静态变量
- 即使编译后的代码

方法区其实也算是堆的一部分，但是为了与堆进行区分 又称为非堆



**java堆**

存放对象实例，相当于c中malloc的一片区域。会发生OOM异常问题

细分

- 新生代
- 老年代



永久代 hotspot当初为了偷懒  对方法区的内容进行回收的而产生的。在jdk1.7（逐步）和1.8中已经移除。开始使用元空间metaspace

[参考链接](https://www.cnblogs.com/paddix/p/5309550.html)

前提引入：永久代存放在堆中而metaspace存放在内存中

修改原因：

- 字符串存放在永久代中，容易出现性能问题和内存溢出
- 类及方法信息大小无法确定，永久代指定比较困难，太小容易造成永久代溢出，太大容易导致老年代空间不足 溢出。
- 永久代为GC带来不必要的复杂度而且回收效率比较低
- 



**垃圾回收**

jvm中垃圾回收主要发生在线程共享的区域 ——堆和元空间

[参考链接](https://www.cnblogs.com/paddix/p/5309550.html)

方法区主要回收 **废弃常量** 和 **无用的类**

废弃常量 ：无使用的常量类

无用的类：

- 所有实例已经被回收
- 该类的classload 已经被回收
- 该类对应的java.lang.Class 对象没有在任何地方被引用，且无法通过反射调用这个类的其他方法

满足这三个条件可以被回收，但不一定被回收。



回收的两种方式

1. 可达性分析JVM使用
2. 引用计数

引用计数缺点 ：循环引用 导致无法回收

可达性分析：图论的知识  GC ROOT到这个对象不可达

可作为GC ROOT的对象

1. 虚拟机栈使用的对象
2. 本地方法栈使用的对象
3. 方法区中的静态对象
4. 方法区中的常量对象

不可达的对象不会立即进行回收，他会经历两次标记的过程。

**四种引用**

- 强引用（一定不会被回收）
- 软引用（内存不够回收）
- 弱引用（发生回收就会被回收）
- 虚引用（主要作用是对象被回收时收到一个系统通知）



垃圾回收算法：

- 标记清除
- 标记复制
- 标记整理
- 分代收集



**标记清除**

缺点:标记和清除效率都不高造成不连续的一段空间 提前进行垃圾回收

**标记复制**

将内存分为两块，每次垃圾复制将存活的对象复制到内存中的另外一块

缺点：内存缺少一半。

内部实现：JVM将新生代的Eden区和其中一个survivor区域比例划分为8:1 而survivor总共有两个，每次只用其中一个，另一个用来复制 存放或者的对象。

**标记整理**

清除的时候  将所有活着的对象移到同一端。然后清理剩余的区域。

缺点：回收效率不高 用在老年代比较多。



**分代收集算法**：

根据不同代的特点选择不同的算法 新生代使用标记复制算法。老年代使用标记整理算法。



**Hotspot中算法实现**

hotspot枚举根节点的算法实现

oopmap 数据结构  安全点  安全区域 （暂时省略）











