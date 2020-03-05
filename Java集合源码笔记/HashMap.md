hashMap源码阅读 笔记记录以及Debug流程记录

底层数据结构：使用的是数组+链表 +红黑树

好处是 平衡数组添加删除时间复杂度高的影响以及减少链表查询时间复杂度高的办法

头插法和尾插法

hashmap高并发环境下为什么会出现死锁

为什么初始容量为16   -> 2的幂次的时候，hash函数进行分布更加均匀

java8中 hashmap 的优化  -> 扰动函数的优化 

hashing的概念 

HashMap中解决碰撞的方法 

equals()和hashCode()的应用

以及它们在HashMap中的重要性 

不可变对象的好处 

HashMap多线程的条件竞争 

重新调整HashMap的大小  rehash的使用以及rehash中判断下标

hash 扰动函数





先是使用key自带的hashcode()方法然后 添加扰动函数 h>>>16 位然后进行一个操作

String 类的hashcode方法使用31作为优质乘数因子的两个原因

- 31\*i =（2>>5）\*i -i jvm可以对他进行一定的优化
- 使用31进行hash运算的时候 不会出现2质数的hash冲突率过高的以及101 质数hash值计算溢出的问题。相对与其他质数17 29等来说，利用hash进行hashcode计算的时候，分布更加均与

基于以上两个原因选择31作为String类hashcode的计算结果。