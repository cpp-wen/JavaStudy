# ConcurrentHashMap源码分析

## 预备知识

安全失败和快速失败



分析思路

1. hashmap实现线程安全的方式介绍

2. 并发容器是如何实现线程安全的（底层的介绍）

3. hashmap和concurrenthashmap进行对比

   

如何实现实现线程安全

1. hashtable
2. colletions.synchronizedMap()方法
3. ConcurrentHashMap

其中上面两种是效率比较低，原因是使用粗粒度锁，所以在高并发环境下效率没有第三种高

其中hashTable中使用了大量对Synchronized关键字保证线程安全。而Collections的方法则是将传入的形参作为一个参数，内部拥有一个mutex对象作为加锁对象。进行synchronized关键字的加锁。



ConcurrentHashmap实现线程安全的方法

主要看put和get  扩容以及size方法

put方法流程

1. 如果数组为空，初始化，初始化完成之后，走 2；

2. 计算当前槽点有没有值，没有值的话，cas 创建，失败继续自旋（for 死循环），直到成功，
   槽点有值的话，走 3；
3. 如果槽点是转移节点(正在扩容)，就会一直自旋等待扩容完成之后再新增，不是转移节点走
   4；
4. 槽点有值的，先锁定当前槽点，保证其余线程不能操作，如果是链表，新增值到链表的尾
   部，如果是红黑树，使用红黑树新增的方法新增；
5. 新增完成之后 check 需不需要扩容，需要的话去扩容。

使用的流程主要是自旋+cas+锁来保证put方法为安全的。

其中方法1中初始化方法保证多个线程中只有一个线程进行初始化的主要操作是通过一个cas操作来保证只有一个线程能够初始化 ,当一个线程获取到这个sizeCtl后就将sizeCtl值设置为-1。在不断的while循环中若读取要sizeCtl的值为-1则调用yield()方法后，释放cpu再次进入自旋的过程。

```java
 private final Node<K,V>[] initTable() {
        Node<K,V>[] tab; int sc;
        while ((tab = table) == null || tab.length == 0) {
            if ((sc = sizeCtl) < 0)
                Thread.yield(); // lost initialization race; just spin
            else if (U.compareAndSwapInt(this, SIZECTL, sc, -1)) {
                try {
                    if ((tab = table) == null || tab.length == 0) {
                        int n = (sc > 0) ? sc : DEFAULT_CAPACITY;
                        @SuppressWarnings("unchecked")
                        Node<K,V>[] nt = (Node<K,V>[])new Node<?,?>[n];
                        table = tab = nt;
                        sc = n - (n >>> 2);
                    }
                } finally {
                    sizeCtl = sc;
                }
                break;
            }
        }
        return tab;
    }
```

> todo cas 底层还是调用unsage类

第三步：若当前的槽点为空的时候为了安全会进行cas进行添加元素，而不是直接添加安全性能更好。

第四步：若当前数组正在扩容就进行获取到当前最新的数组列表然后再次进入自旋。

第五步：对当前发生hash冲突的首节点进行上锁操作，这边的锁的粒度比jdk1.7中更加细，所以并发效果更加好





# ConcurrentHashMap 1.7和1.8进行比较



| 区别             | 1.7                           | 1.8                  |
| ---------------- | ----------------------------- | -------------------- |
| 底层实现         | Segment数组+hashEntry数组实现 | 数组+链表+红黑树实现 |
| 如何实现线程安全 | Segment保证                   | cas+Synchronized     |
| 重要方法         | put size                      | put size             |

## 底层数据结构

jdk1.7

![](C:\Users\caopeng\Pictures\学校课程\20200322224405.png)

其中Segment数组容量初始化为16，hashEntry数组的大小为2，两者扩容大小都为2的幂次。

put方法

1.7中put方法主要流程为

1、线程A执行`tryLock()`方法成功获取锁，则把`HashEntry`对象插入到相应的位置；
 2、线程B获取锁失败，则执行`scanAndLockForPut()`方法，在`scanAndLockForPut`方法中，会通过重复执行`tryLock()`方法尝试获取锁，在多处理器环境下，重复次数为64，单处理器重复次数为1，当执行`tryLock()`方法的次数超过上限时，则执行`lock()`方法挂起线程B；
 3、当线程A执行完插入操作时，会通过`unlock()`方法释放锁，接着唤醒线程B继续执行；

多次获取锁的过程有点类似自旋。



size方法

先采用不加锁的方式，连续计算元素的个数，最多计算3次：
 1、如果前后两次计算结果相同，则说明计算出来的元素个数是准确的；
 2、如果前后两次计算结果都不同，则给每个`Segment`进行加锁，再计算一次元素的个数；







jdk1.8

![img](https://upload-images.jianshu.io/upload_images/2184951-d9933a0302f72d47.png?imageMogr2/auto-orient/strip|imageView2/2/w/768/format/webp)

put方法 

1. 若插入位置元素为null 则通过cas进行插入元素
2. 若插入元素不为空则对头节点元素进行加锁操作，进行插入元素操作
3. 第二步需要判断头结点类型若为链表使用链表方式进行插入否则使用树节点形式进行插入

size方法

实现原理是通过baseCount和cell数组来计数保证数组元素的大小不会改变

1. 初始化时`counterCells`为空，在并发量很高时，如果存在两个线程同时执行`CAS`修改`baseCount`值，则失败的线程会继续执行方法体中的逻辑，使用`CounterCell`记录元素个数的变化；
2. 如果`CounterCell`数组`counterCells`为空，调用`fullAddCount()`方法进行初始化，并插入对应的记录数，通过`CAS`设置cellsBusy字段，只有设置成功的线程才能初始化`CounterCell`数组。
3. 如果通过`CAS`设置cellsBusy字段失败的话，则继续尝试通过`CAS`修改`baseCount`字段，如果修改`baseCount`字段成功的话，就退出循环，否则继续循环插入`CounterCell`对象；

简单总结就是如果多个线程如果同时要修改元素的数量，一个元素成功修改basecount的同时，另一个元素会进行修改countCell数组内元素的大小，若多个线程同时修改则启动cas。



## 总结

在1.7中多线程的并发修改容量跟segment数组的容量大小有关，而在1.8中进行修改后，锁的粒度更加小，使得多线程环境下效率更高。 



