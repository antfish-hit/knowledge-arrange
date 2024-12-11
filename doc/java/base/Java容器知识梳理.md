# Java容器知识梳理

### 集合框架底层数据结构总结

- List
    - `ArrayList`：`Object[]`数组
    - `LinkedList`：双向链表
- Set
    - `HashSet`（无序，唯一）：基于`HashMap`实现。
    - `LinkedHashSet`（有序，唯一）：基于`LinkedHashMap`实现。
    - `TreeSet`（有序，唯一）：基于`TreeMap`实现。
- Map
    - `HashMap`：当链表长度大于阈值（**默认为8**）（**将链表转换成红黑树前会判断，如果当前数组的长度小于64，那么会选择先进行数组扩容，而不是转换为红黑树**）时，将链表转化为红黑树，以减少搜索时间，每次扩容为当前大小的2倍。
    - `LinkedHashMap`：继承自 `HashMap`，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序。
    - `TreeMap`：可以定制化排序的红黑树。
- Queue
    - `PriorityQueue`：底层的数据结构是`Object[]`数组实现的**小顶堆**。
    - `BlockingQueue`
        - `ArrayBlockingQueue`：使用**数组**实现的**有界**阻塞队列，生产和消费用的**是同一把锁**（线程安全）。
        - `LinkedBlockingQueue`：使用**单向链表**实现的**可选有界**阻塞队列，生产和消费用的**不是同一把锁**（线程安全）。生产用的是`putLock`，消费是`takeLock`。

## ArrayList源码解析

- 以无参构造实例化`ArrayList`时，初始化的是一个空数组，在调用`add()`时才真正分配并扩容为10(default)。
- 扩容逻辑伪代码：
```
minCapacity = size + 1
if minCapacity > currentCapacity
    //开始扩容
newCapacity = currentCapacity * 1.5
if newCapacity <  minCapacity
    newCapacity = minCapacity
if newCapacity > MAX_ARRAY_SIZE  // MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8
   newCapacity = minCapacity > MAX_ARRAY_SIZE ? Integer.MAX_VALUE : MAX_ARRAY_SIZE
// 调用Arrays.copyOf()扩容
```
- `ArrayList`中的`ensureCapacity(int)`可以直接使容器扩容以确保能容纳指定大小，最好在添加大量元素之前调用，以减少添加元素时扩容的次数；`trimToSize()`可以将list容量缩减到实际大小。

## HashSet 如何检查重复
先根据对象的`hashcode()`判重，如果相等，再根据`equals()`判重。

## HashMap 为什么线程不安全
多线程对 `HashMap `的 `put` 操作会导致生成相同的**哈希key**，最终导致**数据覆盖**。

## ConcurrentHashMap 实现线程安全的方式

- 并发控制使用`synchronized`和`CAS`来操作。每个链表(or 红黑树根节点)一把锁。
- 没有哈希冲突直接新加`Node`的时候用`CAS`来做并发控制，有哈希冲突需要将数据添加到`Node`的数据结构里时，用`synchronized`做并发控制。

## references:
[Java集合常见面试题总结(上)](https://javaguide.cn/java/collection/java-collection-questions-01.html)
[Java集合常见面试题总结(下)](https://javaguide.cn/java/collection/java-collection-questions-02.html)