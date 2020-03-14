

# Map其实真的有点难

> 你知道的越多，你不知道的越多
>
> 本文收录 **GitHub** [JavaStudy](https://github.com/cpp-wen/JavaStudy) 欢迎Star和完善，里面放了学习的一些资料，希望我们一起学习冲进大厂。

hashMap源码阅读 笔记记录以及Debug流程记录

hashmap源码大概看了一天半 其中有很多细节，本次抽出面试经常问的一些细节以及谈谈自己的理解 。

# HashMap问题详解

## 数据结构

HashMap1.8中

- 底层数据结构：使用的是数组+链表 +红黑树

HashMap1.7中

- 底层数据结构：使用的是数组+链表

数组+链表 好处是 平衡数组添加删除时间复杂度高的影响以及减少链表查询时间复杂度高的办法

链表+红黑树 好处是：平衡空间和时间，红黑树查找效率优化可以达到O(logn)级别，但是每个节点需要存储左右指针，在hashmap中树节点还要存储next指针，以便在链表长度小于6的时候转变为链表结构。



## 数组初始容量设置为16原因？

hashMap中数组的初始化容量设置为16，原因是为了在通过节点的hash值计算数组（也称哈希桶）下标的时候使得hash结点分布均匀。

通过一个例子来说明

在获取哈希桶下标的时使用哈希值对数组长度取模操作，保证下标在数组长度之中。源码中使用逻辑操作减少取模的时间开销。

```java
first = tab[(n - 1) & hash]
```

首先需要了解hash值为int型，为32位。假设我们求出hash为 1010100111 前面取0 那么来看看对于哈希桶为10以及16计算的不同结果。

十进制转二进制    10->1010B        16->10000B

1010B-1=1001B    进行与操作

| 1010100111 | 1010100111 |
| ---------: | ---------: |
|       1001 |       1111 |
|       0001 |       0111 |

上面计算出来的下标是不同的没有问题，但是如果另外一个元素要put进hashMap中，他的hash码为1010100011

| 1010100011 | 1010100011 |
| ---------: | ---------: |
|       1001 |       1111 |
|       0001 |       0011 |

我们可以直观看出两个hash最后四位的码不同却被放入相同的下标中，这样的缺点是，在特定下标中数组的链表长度会变得很长。搜索的效率变低，使得数组长度为16却可以保证每个链表中，每个节点的hash值是相同的。这是典型的利用空间换取时间思想。同理32,64 128也是如此。为此我们可以看到数组初始化大小上面的注释 。

```java
	/**
     * The default initial capacity - MUST be a power of two.
     */
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
```

从初始化大小为16我们了解了hashmap数组容量大小的奥秘。

如果你想了解一下HashMap中是如何保证数组容量大小为2的N次方你可以看下tableSizeFor()函数

```java
static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```



## java8中 hashmap 的优化  -> 扰动函数的优化 

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}
```

在hashMap中获取数组元素 底层会调用getNode()方法里面多添加了一个hash()函数进入这个hash()函数可以看到内部代码也很简单

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

这边调用key本身的hashCode()方法后，与他自身进行高十六位进行一个异或操作。这个操作的目的使得hashCode码不仅仅与低十六位有关，而且有高十六位也有关系，增加了hash的复杂性避免低效hashcode函数导致哈希字的碰撞仅在低位有关，使得计算出来的hash码更加均匀。

与JDK1.7中进行比较 发现两者的思想相同，但是实现方式不同。

```java
h ^= (h >>> 20) ^ (h >>> 12);
return h ^ (h >>> 7) ^ (h >>> 4);
```



## HashMap中解决碰撞的方法 

简单的说往数组中put值，数组为空就直接放入，有元素则放入元素后面。具体是链表的后面还是红黑树的后面，不是本篇的重点。

这边需要注意的是只有当数组的大小超过64并且单链表长度>=8的时候才会调用函数将链表转换为红黑树。

这里不仅仅判断链表个数大于等于 8，还判断了数组大小，数组容量小于 64 没有立即转化的原因，猜测主要是因为红黑树占用的空间比链表大很多，转化也比较耗时，所以数组容量小的情况下冲突严重，我们可以先尝试扩容，看看能否通过扩容来解决冲突的问题。

链表长度大于8的原因是正常情况下，链表个数出现 8 的概念不到千万分之一，所以说正常情况下，链表都不会转化成红黑树，转换为红黑树，可能是特殊的hash算法导致。

## String类中hashcode()函数计算为什么选择31作为乘数因子？

```java
 int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
```

先是使用key自带的hashcode()方法然后 添加扰动函数 h>>>16 位然后进行一个操作

String 类的hashcode方法使用31作为优质乘数因子的两个原因

- 31\*i =（2>>5）\*i -i jvm可以对他进行一定的优化
- 使用31进行hash运算的时候 不会出现2质数的hash冲突率过高的以及101 质数hash值计算溢出的问题。相对与其他质数17 29等来说，利用hash进行hashcode计算的时候，分布更加均与。

基于以上两个原因选择31作为String类hashcode的计算结果。



## equals()和hashCode()的应用

> 在effective java中 有提到这样的一句话。任何一个类，他在覆盖这些方法的时候，都有责任遵守这些通用的约定；如果不能做到这一点，其他依赖于这些约定的类（类如HashMap和Hashset）就无法结合该类一起正常运作。

这些方法中也包括 equals和hashcode方法，查看底层源码可以看到hashMap中get方法中也使用了equals来判断两个对象是否为同一个hashmap 键。

```java
if (first.hash == hash && // always check first node
    ((k = first.key) == key || (key != null && key.equals(k))))
```

Effective java中第十一条：覆盖equals时总要覆盖hashcode方法 ，没有覆盖hashcode方法违反的了第二条，相等的对象必须拥有相同的hashcode。

```
HashMap.put(new class(1,2,3),"abc");
HashMap.get(new class(1,2,3));
```

class类重写了equals方法后上面两个对象应该是相同的，但是在get操作后，第二个方法却会返回一个null。

所以如果你要重写equals()方法请也重写hashcode()方法，同时在hashcode()方法中选取一种优质的hashcode算法。



## 重新调整HashMap的大小  rehash的使用以及rehash中判断下标

重新调整HashMap中的大小后会调用resize()方法对数组进行扩容，扩容大小为原来的两倍（源码中有考虑特殊情况如数组未初始化以及超过最大值怎么样）细节不详细介绍，重点讲一下是如何在resize后进行rehash的。

因为扩容所以hash&(n-1)取的下标也会扩大一倍。这个时候需要对当前每个节点下的链表进行重新hash操作。若hash结果与原来一样，则下标不变，若结果变大则将下标改为原下标+原来数组长度。

贴一小段代码  

```java
if (loTail != null) {//loTail表示低位链表
    loTail.next = null;
    newTab[j] = loHead;
}
if (hiTail != null) {//表示高位链表
    hiTail.next = null;
    newTab[j + oldCap] = hiHead;
}
```



## 头插法和尾插法 来避免死锁问题

Jdk1.7中 HashMap rehash时候使用的头插法，这样导致的结果是会引发死循环，而JDK1.8中修改这个问题使用尾插入来实现。具体的实现可以看下[这篇博客](https://juejin.im/post/5a66a08d5188253dc3321da0) 其实很简单没有很烧脑。面试官问你的话，语言是很难表达清楚的，最好使用画图给他演示一遍就可以了。

进行内容的添加 头插法是在jkd1.7版本下使用，以下是单线程的头插入，具体调用方法是在rehash过程中调用transfer方法。

![](https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200313090017.png)





![](https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200313090306.png)



![](https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200313090736.png)



![](https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200313091312.png)



![](https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200313091335.png)



------

# TreeMap 问题详解

## 数据结构

TreeMap底层使用的数据结构是红黑树，不同的是TreeMap 利用了红黑树左节点小，右节点大的性质，根据 key 进行排序，使每个元素能够插入到红黑树大小适当的位置，维护了 key 的大小关系，适用于 key 需要排序的场景。 所以TreeMap的 containsKey、get、put、remove 等方法的时间复杂度都是 log(n)。

同时因为使用红黑树来进行存储所以TreeMap是禁止key为null的



 Comporator比较器以及Comparable接口

为什么推荐 TreeMap 的元素最好都实现 Comparable 接口？但 key 是 String 的时候，我们却没有额外的工作呢？



----

# Linkedhashmap 问题详解

## 数据结构

LinkedHashMap底层是队列，与Linkedlist数据结构相同。

两个常用特性为

- 按照插入顺序进行访问；
- 实现了访问最少最先删除功能，其目的是把很久都没有访问的 key 自动删除;

同时LinkedHashMap继承了HashMap，所以通过get方法可以获取到当前队列中是否有这个元素。建议Debug一遍LinkedHashMap中的get方法。

附上一个LeetCode  Lru算法的题目加深大家去LinkedHashMap的理解   [LeetCode146题目](https://leetcode-cn.com/problems/lru-cache/)

```java
class LRUCache {

    private int cap;
    private Map<Integer,Integer> map=new LinkedHashMap<>();

    public LRUCache(int capacity) {
        this.cap = capacity;
    }
    
    public int get(int key) {
        if(map.keySet().contains(key)){
            int value=map.get(key);
            map.remove(key);
            map.put(key,value);
            return value;
        }
        return -1;
    }
    
    public void put(int key, int value) {
        if(map.keySet().contains(key)){
            map.remove(key);
        }else if(map.size()==cap){
            Iterator<Map.Entry<Integer,Integer>> iterator =map.entrySet().iterator();
            iterator.next();
            iterator.remove();
        }
        map.put(key,value);
    }
}
```

LinkedHashMap 提供了两个很有意思的功能：按照插入顺序访问和删除最少访问元素策略，简单地通过链表的结构就实现了，设计得非常巧妙。其中删除最少访问元素的实现，使用的是钩子函数。感兴趣的小伙伴可以去看下下面在LInkedhashMap中的代码，以及在hashmap中的的这个方法。

```java
void afterNodeInsertion(boolean evict) { // possibly remove eldest
    LinkedHashMap.Entry<K,V> first;
    if (evict && (first = head) != null && removeEldestEntry(first)) {
        K key = first.key;
        removeNode(hash(key), key, null, false, true);
    }
}
```

好了以上就是在阅读Map中遇到的问题以及使用Debug观察后的感受。

>  文章每周持续更新，你的关注就是我的动力，本文 **GitHub** [JavaStudy](https://github.com/cpp-wen/JavaStudy) 欢迎Star和完善，里面放了学习的一些资料，希望我们一起学习冲进大厂。争取一个礼拜两篇博客，每日一道算法题！

