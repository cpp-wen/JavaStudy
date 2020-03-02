![c9126832e39c155029b5894ca49e8672](C:\Users\caopeng\Pictures\Saved Pictures\c9126832e39c155029b5894ca49e8672.jpg)

# List集合看完就忘，不如跟我Debug过一遍

> 你知道的越多，你不知道的越多
>
> 本文收录 **GitHub** [JavaStudy](https://github.com/cpp-wen/JavaStudy) 欢迎Star和完善，里面放了学习的一些资料，希望我们一起学习冲进大厂。

阅读list集合观察它们底层是如何实现的，以及集合面试中提出的问题进行实践。

 list集合中常用的类为Arraylist、LinkedLIst。

两者的区别

| 区别       | Arraylist                     | LinkedList                                |
| ---------- | ----------------------------- | ----------------------------------------- |
| 底层实现   | 数组                          | 双向链表                                  |
| 适用场景   | 增删操作较少，查找较多        | 增删效率较高，查找效率较低                |
| 容量大小   | 数组大小不能超过Integer最大值 | 理论无限增加，实际size范围为Integer最大值 |
| 线程安全   | 线程不安全                    | 线程不安全                                |
| NUll值处理 | 允许添加NUll值                | 允许添加NUll值                            |



下面分别对着源码来进行逐条比较

### **底层实现**  

###### Arraylist

```java
....
    private static final long serialVersionUID = 8683452581122892189L;
    private static final int DEFAULT_CAPACITY = 10;
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

好了以上就是在阅读Linkedlist和Arraylist中遇到的问题以及使用Debug观察后的感受。

>  文章每周持续更新，你的关注就是我的动力，本文 **GitHub** [JavaStudy](https://github.com/cpp-wen/JavaStudy) 欢迎Star和完善，里面放了学习的一些资料，希望我们一起学习冲进大厂。争取一个礼拜两篇博客，每日一道算法题！

