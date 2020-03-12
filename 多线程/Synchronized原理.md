Synchronized原理讲解 

多线程的一些知识设计很广，最好先有一个全局的总览然后再进行逐个突破。今天记录一下Synchronized底层是如何实现。

## 1.Syn是如何使用的

使用Syn关键字从不同方面可以分为不同的使用方式，这边分为类锁和对象锁

| 类型   | 锁对象               | 锁的数量                                           | 表示形式                                        | 应用场景               |
| ------ | -------------------- | -------------------------------------------------- | ----------------------------------------------- | ---------------------- |
| 对象锁 | 实例对象             | 多个（一个对象有多个实例，每个实例内部保存一个锁） | 具备Syn修饰的方法或者代码块                     | 控制同步方法之间的同步 |
| 类锁   | 静态变量或者静态方法 | 一个（一个类只能有一个类对象）                     | 具备Syn修饰的静态方法或者静态代码块或者静态变量 | 控制静态方法之间的同步 |

对象锁分为 

- 方法锁
- 代码块锁

```java
//方法锁
public synchronized void method1() {
        System.out.println("执行方法1");
    }
```

```java
//代码块锁	
public void method3(){
        synchronized (this){
            System.out.println("代码块锁");
        }
    }
```

类锁分为

- 静态方法锁
- 静态代码块锁

```java
	public static synchronized void method4(){
        System.out.println("静态方法锁");
    }
    
    public void method5(){
        synchronized (SynchronizedTest.class){
            System.out.println("静态代码块锁");
        }
    }
```

在静态代码块锁中，其实可以等价于在method5之前添加关键字Sychronized。

## 2.底层原理

为什么将它分为对象锁或者类锁其实跟Synchronized底层的原理有关。

被Synchronized修饰的方法或者内部拥有被修饰的代码块的时候，通过javap 指令可以看到相对于普通方法flags中多出一个同步标记`ACC_SYNCHRONIZED`,这个标记表明执行这个方法的时候会获取monitor，并在代码块前后添加两个指令	monitorenter和monitorexit表示开始执行这个方法和结束方法执行。那monitor又是如何来避免多个线程执行这个代码块呢？

1. 其实每个monitor中都有一个计数器，计数器的初始值为0，当一个线程想要执行这段代码，如果 monitor 的 entry count 为0，则该线程可以进入 monitor，并将 monitor 的 entry count 的值设为 1，该线程成为 monitor 的 owner（拥有者）；

2. 如果当前线程已经拥有该 monitor，只是重新进入（reenter），则将 monitor 的 entry count 的值加 1；（类似于偏向锁后面介绍）

3. 如果 monitor 的 owner 是其他线程，则当前线程进入阻塞状态，直到 monitor 的 entry count 为 0 之后再次尝试获取 monitor。

monitor底层是通过操作系统的互斥锁来进行实现，所以实现线程之间上锁的过程要经过操作系统用户态要内核态的转换，这也是造成Synchronized操作效率低的主要原因。

这边通过几张图来直观的了解  图片取自豪横 的小耳朵

<img src="https://user-gold-cdn.xitu.io/2020/3/11/170c7aab9cbcff3b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1" alt="img" style="zoom: 80%;" />

​	我们知道在java中类是唯一的，对象是可以有多个的。那么就不难理解为什么分为类锁和对象锁了，那如何解释方法锁呢，其实方法锁的jvm内部实现为执行这个方法前判断这个方法flags中是否有`ACC_SYNCHRONIZED`，如果有表示该方法是同步方法并使用调用该方法的对象或该方法所属的Class在JVM的内部对象表示Klass做为锁对象。

## 3.如何实现原子性 有序性 可见性

![img](https://imgconvert.csdnimg.cn/aHR0cDovL3VwbG9hZC1pbWFnZXMuamlhbnNodS5pby91cGxvYWRfaW1hZ2VzLzk0NDM2NS01Mjg1ZTlhNDBlODNhMDQxLnBuZz9pbWFnZU1vZ3IyL2F1dG8tb3JpZW50L3N0cmlwJTdDaW1hZ2VWaWV3Mi8yL3cvMTI0MA)

1. 原子性是指一个操作是不可中断的，要全部执行完成，要不就都不执行。

   > 线程1在执行`monitorenter`指令的时候，会对Monitor进行加锁，加锁后其他线程无法获得锁，除非线程1主动解锁。即使在执行过程中，由于某种原因，比如CPU时间片用完，线程1放弃了CPU，但是，他并没有进行解锁。而由于`synchronized`的锁是可重入的，下一个时间片还是只能被他自己获取到，还是会继续执行代码。直到所有代码执行完。这就保证了原子性。

2. 可见性是指当多个线程访问同一个变量时，一个线程修改了这个变量的值，其他线程能够立即看得到修改的值。

   > 被`synchronized`修饰的代码，在开始执行时会加锁，执行完成后会进行解锁。而为了保证可见性，有一条规则是这样的：对一个变量解锁之前，必须先把此变量同步回主存中。这样解锁后，后续线程就可以访问到被修改后的值。

3. 有序性即程序执行的顺序按照代码的先后顺序执行。

   > 如果在本线程内观察，所有操作都是天然有序的。如果在一个线程中观察另一个线程，所有操作都是无序的。
   >
   > 简单说就是，`as-if-serial语义`保证了单线程中，指令重排是有一定的限制的，而只要编译器和处理器都遵守了这个语义，那么就可以认为单线程程序是按照顺序执行的。当然，实际上还是有重排的，只不过我们无须关心这种重排的干扰。



## 4.jdk1.6对Synchronized的优化

单纯使用Synchronized，多个线程执行方法岂不是变成了管道型，这个对java的效率有很大的影响。那么jdk1.6中是如何进行优化的。

主要通过引入四种状态，对锁的不同状态进行划分，提高获取锁和释放锁的效率。按照锁的轻重顺序分别为无状态锁，偏向锁，轻量级锁和重量级锁。注意 锁状态可以升级，但是无法降级。

图片来自于豪横的小耳朵，其中乐观锁就是轻量级锁，悲观锁就是重量级锁。

![img](https://user-gold-cdn.xitu.io/2020/3/11/170c7ad2e01f4774?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

**一开始是无锁的状态，一上来会先去判断一下有没有锁，有锁的话最开始的时候锁是支持偏向锁的。偏向锁当前获取到锁资源的这个线程，我会优先让他再去获取这个锁，如果它没获取到这个锁，就升级为一个轻量级的，一个cas锁，即乐观锁，乐观锁的时候它是一个比较和交换的过程，如果没有设置成功的话，它会进行一个自旋，然后自旋到一定次数之后才会升级成一个synchronized的这样一个重量级的锁，这样的话他就保证了性能的问题**。



先来了解偏向锁和轻量级锁和重量级锁使用场景有一个直观的认识。

1. 偏向锁是 JDK 1.6 加入的，其目的是消除数据在无竞争情况下的同步原语，进一步提高程序的性能。
2. 轻量级锁也是 JDK 1.6 加入的，其目的是在线程交替执行的情况下，减少传统的重量级锁使用操作系统互斥量产生的性能消耗。
3. 重量级锁是通过对象内部的监视器（monitor）实现，锁对象的 Mark Word 指向一个类型为 ObjectMonitor 的对象指针；monitor 的本质是依赖于底层操作系统的 Mutex Lock 实现，操作系统实现线程同步时，需要进行系统调用，需要从用户态（User Mode）到内核态（Kernel Mode）的切换，代价相对较高。



接下来我们来了解底层是如何实现锁升级的

首先需要了解一下cas（compare and swap）思想。

当一个线程A正在执行的时候，另一个线程B想要获取这个线程A执行方法的锁。本来B应该进入阻塞状态，但是这时cas操作会让B进入一个自旋的状态，不断尝试去获取这个锁，而不是进入阻塞。通过这个思想引出自旋锁，适应自旋锁等一系列优化。

其次需要了解java对象头 ，以及java对象头中Mark word数据

![img](https://gitee.com/duchaochen/ycbbs.vip-1/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/002-images/sijava/201812081003.png)

Mark word是java对象头中的一部分数据，那么他是如何跟当前线程持有这个锁联系起来的呢？主要通过以下几个步骤

1. 如果同步对象锁状态为无锁状态，虚拟机将首先在当前线程的栈帧中建立一个名为锁记录（Lock Record）的空间，用于存储锁对象目前的 Mark Word 的拷贝，然后将对象头中的 Mark Word 复制到锁记录中。

2. 拷贝成功后，虚拟机将使用 CAS 操作尝试将对象的 Mark Word 更新为指向 Lock Record 的指针，并将 Lock Record 里的 owner 指针指向对象的 Mark Word。

3. 如果这个更新动作成功了，那么这个线程就拥有了该对象的锁，并且对象 Mark Word 的锁标志位设置为“00”，表示此对象处于轻量级锁定状态。

4. 如果轻量级锁的更新操作失败了，虚拟机首先会检查对象的 Mark Word 是否指向当前线程的栈帧，如果是就说明当前线程已经拥有了这个对象的锁，那就可以直接进入同步块继续执行，否则说明多个线程竞争锁。

   

   ![img](https://gitee.com/duchaochen/ycbbs.vip-1/raw/master/%E4%B8%AA%E4%BA%BA%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0/002-images/sijava/201812081005.png)

   





