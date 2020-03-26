# ReentrantLock重入锁 

1. 设计Lock的原因
2. 底层实现原理
3. synchronized关键字比较判断两者的实现区别
4. 重入锁使用场景




## 设计Lock的原因

java既然设计了synchronized锁为什么还要添加Lock接口来实现ReentrantLock这些锁呢？从java历史来讲lock接口诞生于jdk1.5，在jdk1.5之前的synchronized锁的性能很差，这时使用lock锁进行操作可以归结到效率问题上，但在jdk1.6中对synchronized进行了大量的优化，性能优化程度到跟lock锁差不多的地步，为什么到现在还保留着lock类呢。这种重复造轮子的行为是不是不符合常理。

让我们来回顾一下造成死锁的四种情况

1. 请求和保持
2. 不可剥夺
3. 环路等待
4. 互斥条件

想要打破死锁只要打破以上四种情况便可以。细心观察我们发现synchronized并没有解决这个问题 ，以下列举的一个情况就是常见的死锁

```java
synchronized(A.class){
	synchronized(B.class){
	
	}
}
synchronized(B.class){
	synchronized(A.class){
	
	}
}
```

lock锁修改的地方就是破坏了不可剥夺这个条件，当线程无法获得锁的时候，我们希望线程可以在等待一段时间后退出阻塞状态，并且释放当前占有的资源。想要实现有以下三种操作

1. 能够响应中断。synchronized 的问题是，持有锁 A 后，如果尝试获取锁 B 失败，那么线程就 进入阻塞状态，一旦发生死锁，就没有任何机会来唤醒阻塞的线程。但如果阻塞状态的线程能 够响应中断信号，也就是说当我们给阻塞的线程发送中断信号的时候，能够唤醒它，那它就有 机会释放曾经持有的锁 A。这样就破坏了不可抢占条件了。
2.   支持超时。如果线程在一段时间之内没有获取到锁，不是进入阻塞状态，而是返回一个错误， 那这个线程也有机会释放曾经持有的锁。这样也能破坏不可抢占条件。 
3.  非阻塞地获取锁。如果尝试获取锁失败，并不进入阻塞状态，而是直接返回，那这个线程也有 机会释放曾经持有的锁。这样也能破坏不可抢占条件。

这三种方案可以全面弥补 synchronized 的问题。到这里相信你应该也能理解了，这三个方案就 是“重复造轮子”的主要原因，体现在 API 上，就是 Lock 接口的三个方法。详情如下：

```java
// 支持中断的 API 
void lockInterruptibly()   throws InterruptedException;
// 支持超时的 API
boolean tryLock(long time, TimeUnit unit)   throws InterruptedException; 
// 支持非阻塞获取锁的 API 
boolean tryLock();

```

## lock实现原理

了解了诞生的原因，我们了解一下lock底层是如何实现的。先来看一幅图

![concurrent包实现整体示意图.png](https://user-gold-cdn.xitu.io/2018/5/3/163260cff7cb847c?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

lock的底层是AQS，说来说去AQS又是什么，AQS底层又是如何实现的。图中可以看到AQS底层是什么。

但是这又是为什么，volatile我们都了解，在单例模式DCL的时候我们会用到这个关键字来防止指令重排序来防止出现线程获取到未被初始化的对象。简单的说通过JVM为我们添加内存屏障我们在可以防止指令重排序发生的错误，这便解决了有序性的问题。同时volatile关键字通过emsi缓存一致原理实现了可见性，更加具体的说是在总线上添加一个嗅探监视数据是否被修改，同步到缓存中。volatile无法实现原子性，一条语句在计算机内部被编译为多条指令，volatile只能保证基础变量的赋值才是原子性的。

接下来看CAS全称为compare and swap ，如果我们进入java内部去找这个方法，我们会发现最后实现会是一个native方法。继续向内部探索我们发现cas底层是有硬件在代码中插入一个lock，来保证操作的原子性。

ok了解到这边我们继续来观察具体的实现，cas是如何来实现变量的修改。

CAS中有三个参数：内存值V、旧的预期值A、要更新的值B，当且仅当内存值V的值等于旧的预期值A时才会将内存值V的值修改为B，否则什么都不干。通过两方面的保证我们可以得知每次只有一个线程可以执行这个方法保证了安全性。

那么我们接下来就可以说AQS到底是怎么实现的了，首先了解AQS全称为AbstractQueuedSynchronizer，是一个抽象类，他的实现类只有两个分别为NonfairSync和fairSync，大家去源码中看看就懂了。

![图片描述](https://img.mukewang.com/5dc37dfd00017b7806860361.jpg)

可以看到lock实现类中的lock操作最后都会被转换为sync类的lock操作（比如ReentrantLock的lock方法）

```java
//fairsyn    
public void lock() {
        sync.lock();
    }
//Nonfairsyn
 final void lock() {
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
                acquire(1);
        }
```

现在可以关联起两者的关系了，为什么说lock的底层是AQS，但是我们还是没有确认AQS的底层到底是什么？我们查看sync.lock方法往下面看

```java
 	//fairSync类
	static final class FairSync extends Sync {
        private static final long serialVersionUID = -3000897897090466540L;

        final void lock() {
            acquire(1);
        }
     
     // nofairSync类
     protected final boolean compareAndSetState(int expect, int update) {
        // See below for intrinsics setup to support this
        return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
    }
```

其实到这边可以发现nofairSync类中用到了cas进行操作，他的参数也符合我们上面提到的内存值V、旧的预期值A、要更新的值B。至于fairSync类中我们可以接着往下看会看到addWaiter中会调用到cas的操作，这边就放一段代码。

```java
 private Node addWaiter(Node mode) {
        Node node = new Node(Thread.currentThread(), mode);
        // Try the fast path of enq; backup to full enq on failure
        Node pred = tail;
        if (pred != null) {
            node.prev = pred;
            if (compareAndSetTail(pred, node)) {
                pred.next = node;
                return node;
            }
        }
        enq(node);
        return node;
    }
```

这边保证了cas操作只能有一个线程进行修改，对于lock，我们知道对于一个对象可以多次加锁，那么在底层是如何实现多次加锁呢，在AQS中会维持一个 volatile修饰的int变量state，这个对象记录当前加锁状态。如果加锁就+1，释放锁就-1。当state为0时候表示没有线程持有锁。这边举个例子，底层还有很多volatile修饰的变量，大家可以看下源码。



## synchronized和ReentrantLock的比较

synchronized是和if、else、for、while一样的关键字，ReentrantLock是类，这是二者的本质区别。既然ReentrantLock是类，那么它就提供了比synchronized更多更灵活的特性，可以被继承、可以有方法、可以有各种各样的类变量，ReentrantLock比synchronized的扩展性体现在几点上：

（1）ReentrantLock可以对获取锁的等待时间进行设置，这样就避免了死锁

（2）ReentrantLock可以获取各种锁的信息

（3）ReentrantLock可以灵活地实现多路通知

另外，二者的锁机制其实也是不一样的。ReentrantLock底层调用的是CAS和volatile，synchronized操作的是对象头中mark word，以及monitor。



## 适用场景

适用场景：时间锁等候、可中断锁等候、无块结构锁、多个条件变量或者锁投票

具体就不讲解了，文章的重点是对整个框架有一个了解。lock类的具体实现源码下次有时间再写一篇文章。







