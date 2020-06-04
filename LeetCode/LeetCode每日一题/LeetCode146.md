#### [146. LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/)

运用你所掌握的数据结构，设计和实现一个 [LRU (最近最少使用) 缓存机制](https://baike.baidu.com/item/LRU)。它应该支持以下操作： 获取数据 `get` 和 写入数据 `put` 。

获取数据 `get(key)` - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。
写入数据 `put(key, value)` - 如果密钥已经存在，则变更其数据值；如果密钥不存在，则插入该组「密钥/数据值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。

 

**进阶:**

你是否可以在 **O(1)** 时间复杂度内完成这两种操作？

 

**示例:**

```
LRUCache cache = new LRUCache( 2 /* 缓存容量 */ );

cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // 返回  1
cache.put(3, 3);    // 该操作会使得密钥 2 作废
cache.get(2);       // 返回 -1 (未找到)
cache.put(4, 4);    // 该操作会使得密钥 1 作废
cache.get(1);       // 返回 -1 (未找到)
cache.get(3);       // 返回  3
cache.get(4);       // 返回  4
```

先放一个效率最低的做法 后期添加算法

```java
class LRUCache {
    //如果不限制条件的话 可以使用list直接实现就可以了 arraylist和linkedlist时间复杂度都有点高
    //先用复杂度高的实现一下吧
    Queue<Integer> queue;
    Map<Integer,Integer> map;
    int capacity;
    public LRUCache(int capacity) {
        this.map=new HashMap<>();
        this.queue=new LinkedList<>();
        this.capacity=capacity;
    }
    
    public int get(int key) {
        if(queue.contains(key)){
            queue.remove(key);
            queue.add(key);
            return map.get(key);
        }else{
            return -1;
        }
    }
    
    public void put(int key, int value) {
        if(queue.contains(key)){
            queue.remove(key);
            queue.add(key);
            map.put(key,value);
        }
        else if(capacity==0){
            map.remove(queue.poll());
            queue.add(key);
            map.put(key,value);
        }else{
            queue.add(key);
            map.put(key,value);
            capacity--;
        }
    }
}

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

