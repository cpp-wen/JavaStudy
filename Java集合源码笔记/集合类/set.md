我们在使用 HashSet 时，比如 add 方法，只有一个入参，但组合的 Map 的 add 方法却有
key，value 两个入参，相对应上 Map 的 key 就是我们 add 的入参，value 就是第二行代
码中的 PRESENT，此处设计非常巧妙，用一个默认值 PRESENT 来代替 Map 的 Value



set实现其实复用了map中的很多内容。面试问到可能性不是很大。

1. 当集合的元素是自定义类时，自定义类强制实现 equals 和 hashCode 方法，并且两个都要
实现。
在集合中，除了 TreeMap 和 TreeSet 是通过比较器比较元素大小外，其余的集合类在判断索
引位置和相等时，都会使用到 equals 和 hashCode 方法，这个在之前的源码解析中，我们有
说到，所以当集合的元素是自定义类时，我们强烈建议覆写 equals 和 hashCode 方法，我们
可以直接使用 IDEA 工具覆写这两个方法，非常方便

2. 所有集合类，在 for 循环进行删除时，如果直接使用集合类的 remove 方法进行删除，都会
快速失败，报 ConcurrentModificationException 的错误，所以在任意循环删除的场景下，
都建议使用迭代器进行删除；







