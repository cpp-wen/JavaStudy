

 list集合中常用的类为Arraylist、LinkedLIst。

两者的区别

| 区别       | Arraylist                     | LinkedList                                | vector                  | copyonWriteArrayList        |
| ---------- | ----------------------------- | ----------------------------------------- | ----------------------- | --------------------------- |
| 底层实现   | 数组                          | 双向链表                                  | 数组跟ArrayList想相同   | 数组                        |
| 适用场景   | 增删操作较少，查找较多        | 增删效率较高，查找效率较低                | 要求线程安全场景        | 读多写少场景                |
| 容量大小   | 数组大小不能超过Integer最大值 | 理论无限增加，实际size范围为Integer最大值 | 初始值为10，扩容为1.5倍 | 初始化为0，扩容是不断的复制 |
| 线程安全   | 线程不安全                    | 线程不安全                                | 线程安全                | 线程安全                    |
| NUll值处理 | 允许添加NUll值                | 允许添加NUll值                            | 允许添加null值          | 允许添加null值              |



下面分别对着源码来进行逐条比较

### **底层实现**  

###### Arraylist

```java
....
    private static final long serialVersionUID = 8683452581122892189L;
    private static final int DEFAULT_CAPACITY = 10;//默认初始值大小为10
    private static final Object[] EMPTY_ELEMENTDATA = new Object[0];
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = new Object[0];
    transient Object[] elementData;
    private int size;
    private static final int MAX_ARRAY_SIZE = 2147483639;
...
    //构造方法1
public ArrayList(int var1) {
    if (var1 > 0) {
        this.elementData = new Object[var1];
    } else {
        if (var1 != 0) {
            throw new IllegalArgumentException("Illegal Capacity: " + var1);
        }

        this.elementData = EMPTY_ELEMENTDATA;
    }

}
	//构造方法2
public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
	//构造方法3
public ArrayList(Collection<? extends E> var1) {
        this.elementData = var1.toArray();
        if ((this.size = this.elementData.length) != 0) {
            if (this.elementData.getClass() != Object[].class) {
                this.elementData = Arrays.copyOf(this.elementData, this.size, Object[].class);
            }
        } else {
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

ArrayList的三个构造方法分别初始化了ElementData变量不同的值。若传入参数<=0或者使用无参构造函数会赋值数组element对象一个长度为0的Object数组。

ArrayList的原先初始容量为10 ，扩容为1.5倍



###### LinkedList

```java
	//linkedList集合元素
	transient int size = 0;
    transient Node<E> first;
    transient Node<E> last;
    
     private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
	//构造方法1
 	public LinkedList() {
    }
	//构造方法2
	public LinkedList(Collection<? extends E> c) {
        this();
        addAll(c);
    }
```

LinkedList内部实现很简单只有一个头节点，一个尾节点，以及一个size变量来记录集合中的元素。Ctrl点入Node<E>。发现这是一个泛型类，内部保存两个指针，指向前后两个节点，E 类型的变量。如果链表数据为空的话，头尾节点是同一个节点，本身是 null，指向前后节点的值也是 null。





###### Vector

vector的实现跟ArrayList是相同的但是底层的实现是使用Synchronized进行同步来保证线程的安全，vector的扩容大小是原来的两倍。

目前使用场景不是很多，如果想要获得一个线程安全的list 除了使用这个方法外还可以使用Collections.synchronizedList()方法对ArrayList进行包装生成一个线程安全的ArrayList对象。



###### copyonWriteArrayList  有重复 下面更加详细

copyonWriteArrayList类是多线程包下的一个类底层使用ReentrantLock 和volatile关键字来保证线程的安全

```java
public class CopyOnWriteArrayList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    private static final long serialVersionUID = 8673264195747942595L;

    /** The lock protecting all mutators */
    final transient ReentrantLock lock = new ReentrantLock();

    /** The array, accessed only via getArray/setArray. */
    private transient volatile Object[] array;
    .......
```

这边可以看到内部数据结构为数组，并且使用volatile关键字进行修饰，以及使用有一个ReentrantLock对象。volatile关键字保证了有序性和可见性，需要强调的是有序性和可见性是指当被volatile修饰的变量引用改变时，对其其它线程来说是可见的。如果只是修改数组中的元素如`array[1]=1`是无法保证可见性的。

```java
public boolean add(E e) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            Object[] newElements = Arrays.copyOf(elements, len + 1);
            newElements[len] = e;
            setArray(newElements);
            return true;
        } finally {
            lock.unlock();
        }
    }
    
```

 setArray(newElements);修改数组引用地址。来保证可见性。

```java
 public E remove(int index) {
        final ReentrantLock lock = this.lock;
        lock.lock();
        try {
            Object[] elements = getArray();
            int len = elements.length;
            E oldValue = get(elements, index);
            int numMoved = len - index - 1;
            if (numMoved == 0)
                setArray(Arrays.copyOf(elements, len - 1));
            else {
                Object[] newElements = new Object[len - 1];
                System.arraycopy(elements, 0, newElements, 0, index);
                System.arraycopy(elements, index + 1, newElements, index,
                                 numMoved);
                setArray(newElements);
            }
            return oldValue;
        } finally {
            lock.unlock();
        }
    }
```

copyOnwriteArrayList 两个问题

1. 频繁的创建新数组 以及销毁老数组会产生频繁的GC对cpu有一定消耗。
2. 多线程场景下读数据时可能获取到数组元素为未更新的元素，不适合实时性要求较高的场景。

###### 总结

Arraylist和Linkedlist实现底层都跟数据结构有关系，可以转换为链表以及数组的优缺点进行两者的回答，链表的扩容更加方便，而数组的查找更加便捷。具体如何可以给面试官讲你看到的细节。





### 适用场景

###### Arraylist

Arraylist底层是使用数组来进行实现，对于数组的查找只要通过下标便可以获得，查找时间复杂度为O(1);数组的删除操作为删除该下标节点，并将后续节点前移动，具体实现为使用System.arraycopy()方法时间复杂度为O(n)。添加方法分为尾部添加以及指定位置添加，尾部添加时间复杂度为O(n),指定位置添加与删除类似。

```java
 	//查找操作
 	public E get(int var1) {
 		//查找范围判断 下标是否越界
        this.rangeCheck(var1);
        return this.elementData(var1);
    }
    //添加操作
    public boolean add(E var1) {
    	//检查数组是否需要扩容
        this.ensureCapacityInternal(this.size + 1);
        this.elementData[this.size++] = var1;
        return true;
    }
    //指定位置添加元素
    public void add(int var1, E var2) {
    	//指定位置 越界判断
        this.rangeCheckForAdd(var1);
        this.ensureCapacityInternal(this.size + 1);
        //底层native方法 
        System.arraycopy(this.elementData, var1, this.elementData, var1 + 1, this.size - var1);
        this.elementData[var1] = var2;
        ++this.size;
    }
    //删除方法
    public E remove(int var1) {
        this.rangeCheck(var1);
        //modCount 记录列表结构修改次数
        ++this.modCount;
        Object var2 = this.elementData(var1);
        int var3 = this.size - var1 - 1;
        if (var3 > 0) {
            System.arraycopy(this.elementData, var1 + 1, this.elementData, var1, var3);
        }

        this.elementData[--this.size] = null;
        return var2;
    }
```

###### Linkedlist

Linkedlist底层是使用双向链表来进行实现，所以对于元素的添加更加灵活，对于添加操作，有表首添加表尾添加，以及制定位置添加。对于链表的遍历时间复杂度都为O(n),表首添加和表尾添加时间复杂度为O(1),查找操作时间复杂度为O(n)但是底层有利用二分思想进行细微的优化。删除操作为O(n);

```java
//表首添加 与表尾添加 方法类似 以addfirst为例
public void addFirst(E e) {
        linkFirst(e);
    }

private void linkFirst(E e) {
        final Node<E> f = first;
        final Node<E> newNode = new Node<>(null, e, f);
        first = newNode;
        if (f == null)//表为空，设置表尾节点也是这个元素
            last = newNode;
        else
            f.prev = newNode;
        size++;
        modCount++;
    }

//查找操作  node方法中使用一次二分判断进行优化
public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
Node<E> node(int index) {
        if (index < (size >> 1)) {
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else {
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
//移除操作分为表首移除和表尾移除，以及指定元素删除 以指定元素删除为例
public boolean remove(Object o) {
    //添加时未对空进行判断所以移动时进行判断
        if (o == null) {
            for (Node<E> x = first; x != null; x = x.next) {
                if (x.item == null) {
                    unlink(x);
                    return true;
                }
            }
        } else {
            for (Node<E> x = first; x != null; x = x.next) {
                if (o.equals(x.item)) {
                    unlink(x);
                    return true;
                }
            }
        }
        return false;
    }
```



### 容量大小

Arraylist 可以从size获取到列表中当元素的个数，所以通过Arraylist类中size的大小就可以判断数组的大小，其次通过Arraylist的扩容函数也可以发现这一点，之后会介绍。 同时创建一个数组数组的容量大小也是跟虚拟机heap大小相互关联。

```java
public int size() {
    return this.size; //返回的元素为int型 所以数组大小被限制为最大2147483647
}
```

```java
//main代码
public static void main(String[] args) {
    ArrayList<Integer> a=new ArrayList<>(100000);
}
//结果
Error occurred during initialization of VM
GC triggered before VM initialization completed. Try increasing NewSize, current value 1536K.
```

Linkedlist 底层是双向链表，理论上可以无限大。但源码中，LinkedList 实际大小用的是 int 类型，这也说明了 LinkedList 不能超过 Integer 的最大值，不然会溢出。

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable
{
    transient int size = 0;
    ......
    ......
```



### 线程安全

Arraylist和Linkedlist都不是线程安全的。当链表对象作为一个共享变量，多个线程在任何时刻下都可以对链表进行操作导致数值覆盖等问题。只有当链表对象作为局部变量的时候是没有线程安全问题的。

若想创建一个线程安全的链表可以使用Collections.synchronizedList()方法，底层实现是将链表转换为`SynchronizedList`类该类的所有方法都带有Synchronized方法。

```java
 Collections.synchronizedList(a);
 //底层实现
 public static <T> List<T> synchronizedList(List<T> list) {
        return (list instanceof RandomAccess ?
                new SynchronizedRandomAccessList<>(list) :
                new SynchronizedList<>(list));
    }
```



### NUll值处理

Arraylist和Linkedlist类中对于添加NUll值时并没有特殊判断所以在删除时要对null进行判断。

```java
//Arraylist类 删除方法
public boolean remove(Object var1) {
        int var2;
        if (var1 == null) {
            for(var2 = 0; var2 < this.size; ++var2) {
                if (this.elementData[var2] == null) {
                    this.fastRemove(var2);
                    return true;
                }
            }
        } else {
            for(var2 = 0; var2 < this.size; ++var2) {
                if (var1.equals(this.elementData[var2])) {
                    this.fastRemove(var2);
                    return true;
                }
            }
        }

        return false;
    }
//linkedlist类的null值remove方法在适用场景处已经举出，不在重复
```



### 难点 ：Arraylist扩容操作

了解Arraylist扩容的最好办法就是一路debug下去了解扩容机制整体流程。

```java
 public static void main(String[] args) {
        ArrayList<Integer> a=new ArrayList<>();
        a.add(1);
        a.add(2);
    }
```

Debug分析可得首先进行`ensureCapacityInternal()`方法进行size判断,若数组为第一次添加元素则初始化数组大小为10，若数组size+1后小于数组容量，就直接添加否则调用`grow()`扩容函数。

```flow
st=>start: add()
e=>end: 结束
op=>operation: ensureCapacityInternal() 容量判断
cond=>condition: Elements数组是否为空
io=>operation: minCapacity设置为10
three=>operation: ensureExplicitCapacity(minCapacity)
panduan=>condition: minCapacity>Elements.length
grow=>operation: grow()

st(right)->op(right)->cond
cond(yes)->io(right)->e
cond(no)->panduan
io->three->panduan
panduan(yes,right)->grow->e
panduan(no)->e
```

```java
//扩容函数
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
1           newCapacity = minCapacity; 
        if (newCapacity - MAX_ARRAY_SIZE > 0)
2           newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```

扩容内部使用1.5倍扩容来实现 其中使用右移运算符来进行0.5倍长度计算，使用右移而不是除法，因为计算机底层右移操作速度更快。如果扩容后的数组大小仍小于要添加元素大小，会将大小设置为要添加元素大小

> 举例：先前我们已经计算出来数组在加入一个值后，实际大小是 1，最大可用大小是 10 ，现
> 在需要一下子加入 15 个值，那我们期望数组的大小值就是 16，此时数组最大可用大小只有
> 10，明显不够，需要扩容，扩容后的大小是：10 + 10 ／2 = 15，这时候发现扩容后的大小仍
> 然不到我们期望的值 16，这时候源码使用上述策略如下：所以最终数组扩容后的大小为 16。



如果遇到大数组的情况：最好一次性添加元素容量使用`addAll(Collection<? extends E> c)`方法，而不要使用for循环add，因为循环add，会造成多次扩容，性能降低。



源码扩容过程有什么值得借鉴的地方？

- 是扩容的思想值得学习，通过自动扩容的方式，让使用者不用关心底层数据结构的变化，封
  装得很好，1.5 倍的扩容速度，可以让扩容速度在前期缓慢上升，在后期增速较快，大部分
  工作中要求数组的值并不是很大，所以前期增长缓慢有利于节省资源，在后期增速较快时，
  也可快速扩容。
- 扩容过程中，有数组大小溢出的意识，比如要求扩容后的数组大小，不能小于 0，不能大于
  Integer 的最大值。



### **难点：Arraylist remove方法**  

先看代码 猜测结果是多少？

```java
public static void main(String[] args) {
        ArrayList<Integer> a=new ArrayList<>();
        a.add(1);
        a.add(1);
        a.add(1);
        a.add(1);
        for (int i = 0; i < a.size(); i++) {
            if(a.get(i)==1){
                a.remove(i);
            }
        }
        System.out.println(a.size());
    }
```

输出结果为2，而不是0，原因是底层在remove后调用System.copyOf()方法进行复制，将删除Element[0]后，放在Element[1]的值放在Element[0]处，使得原先Element[1]元素未被删除。



如果使用`foreach()`方法结果如何？

```java
for (int i:a) {
            if(i==1){
                a.remove(i);
            }
        }
```

输出结果为Exception in thread "main" java.util.ConcurrentModificationException

再次Debug进去走一遍流程，哪里出了问题。

foreach的内部实现是使用Iterator迭代器来实现。因为增强 for 循环过程其实调用的就是迭代器的 next () 方法，当你调用list#remove () 方 法 进 行 删 除 时 ， modCount 的 值 会 +1 ， 而 这 时 候 迭 代 器 中 的expectedModCount 的 值 却 没 有 变 ， 导 致 在 迭 代 器 下 次 执 行 next () 方 法 时 ，expectedModCount != modCount 就会报 ConcurrentModificationException 的错误。



那如果使用Iterator.remove () 方法可以删除么，为什么？

可以的，因为 Iterator.remove () 方法在执行的过程中，会把最新的 modCount 赋值给expectedModCount，这样在下次循环过程中，modCount 和 expectedModCount 两者就会相等。

```
//Iterator内部remove方法
public void remove() {
            if (lastRet < 0)
                throw new IllegalStateException();
            checkForComodification();

            try {
                ArrayList.this.remove(lastRet);
                cursor = lastRet;
                lastRet = -1;
                expectedModCount = modCount;
            } catch (IndexOutOfBoundsException ex) {
                throw new ConcurrentModificationException();
            }
        }
```





---

### CopyOnWriteArraylist介绍

Arraylist和Linkedlist都是线程不安全的，那么如何实现一个线程安全的list

根据读写分离的思想来，java提供了CopyOnWriteArraylist和CopyOnWriteSet来保证线程的安全

先说一个CopyOnWrite底层是如何实现的：当线程对容器进行修改的时候，会复制出一个新数组将修改以及原数组复制到一个新数组中。为防止多个线程进行同时修改所以对修改的过程进行了加锁的操作。

CopyOnWrite容器的使用场景 ：适合读多写少的情况。

CopyOnWrite容器使用问题：频繁的创建新数组会导致GC频繁，对于内存来说是不利的，其次因为只有在对数组进行修改的时候才会进行复制并改写，所以当数组进行get方法获取数组元素时，可能获取到的还是原先数组中值，导致数据不一致问题。

> 既然对数组使用了volatile关键字，为什么不在原来的数组上进行修改呢？反而要新创建一个数组，原因是是因为线 程工作内存中只拷贝了数组的引用，如果只改变数组中的值，那么线程工作内存中的值（数组 引用）没有改变，所以不会触发可见性。

重点可以看一下批量删除操作的源码

```java
// 批量删除包含在 c 中的元素
public boolean removeAll(Collection<?> c) {
if (c == null) throw new NullPointerException();
final ReentrantLock lock = this.lock;
lock.lock();
try {
Object[] elements = getArray();
int len = elements.length;
// 说明数组有值，数组无值直接返回 false
if (len != 0) {
// newlen 表示新数组的索引位置，新数组中存在不包含在 c 中的元素
int newlen = 0;
Object[] temp = new Object[len];
// 循环，把不包含在 c 里面的元素，放到新数组中
for (int i = 0; i < len; ++i) {
Object element = elements[i];
// 不包含在 c 中的元素，从 0 开始放到新数组中
if (!c.contains(element))
temp[newlen++] = element;
}
// 拷贝新数组，变相的删除了不包含在 c 中的元素
if (newlen != len) {
setArray(Arrays.copyOf(temp, newlen));
return true;
}
}
return false;
} finally {
lock.unlock();

```

批量删除操作是将要未删除的元素放入到一个临时数组中，最后在一次性复制到整个数组中，避免多个创建新的数组造成内存浪费。









### 问题解答：（全部有所了解在看）

> 关于List相关的问题主要有：
>
> （1）ArrayList和LinkedList有什么区别？  fail-fast  cache缓存不友好 ArraayList删除问题

> （2）ArrayList是怎么扩容的？  扩容为原先的1.5倍
>
> （3）ArrayList插入、删除、查询元素的时间复杂度各是多少？ 查找O(1) 增删看位置一般为O(n)
>
> （4）怎么求两个集合的并集、交集、差集？ addAll()求并集 retainAll求交集  removeAll()单向差集
>
> （5）ArrayList是怎么实现序列化和反序列化的？  transient关键字保证不初始化整个数组，通过writeObject和readObject进行size个数元素输入。
>
> （6）集合的方法toArray()有什么问题？
>
> （7）什么是fail-fast？ 快速失败机制在线程不安全的时候使用
>
> （8）LinkedList是单链表还是双链表实现的？ 双链表实现，有head 以及tail两个指针
>
> （9）LinkedList除了作为List还有什么用处？ 双向链表还可以作为栈使用
>
> （10）LinkedList插入、删除、查询元素的时间复杂度各是多少？ 插入删除O(1)  查找O(n)
>
> （11）什么是随机访问？ 实现RandomAccess接口 表明可以O(1)复杂度获取下标元素
>
> （12）哪些集合支持随机访问？他们都有哪些共性？ 底层使用数组实现便可以随机访问
>
> （13）CopyOnWriteArrayList是怎么保证并发安全的？ 添加ReetLock锁进行限制
>
> （14）CopyOnWriteArrayList的实现采用了什么思想？读写分离
>
> （15）CopyOnWriteArrayList是不是强一致性的？不是强一致，只能保证最终一致，无法保证实时一致
>
> （16）CopyOnWriteArrayList适用于什么样的场景？ 读多写少
>
> （17）CopyOnWriteArrayList插入、删除、查询元素的时间复杂度各是多少？ 插入删除O(n)查询O(1)
>
> （18）CopyOnWriteArrayList为什么没有size属性？ 因为元素个数始终等于数组长度无需size属性
>
> （19）比较古老的集合Vector和Stack有什么缺陷？ 线程安全但是性能较差
>
> 

