# Java容器知识梳理

1. List、Set、Map的区别：

   List：存储一组不唯一对象的集合

   Set：不重复存储集合(根据元素hashCode判断)

   Map：键值对存储字典，以key的hashCode判断重复

2. ArrayList与LinkedList的区别：

   |                          |  ArrayList   | Linkedlist |
   | :----------------------: | :----------: | :--------: |
   |         线程安全         |      N       |     N      |
   |       底层数据结构       |  Object数组  |  双向链表  |
   | 插入与删除受元素位置影响 |      N       |     Y      |
|     支持快速随机访问     |      Y       |     N      |
   |        内存占用点        | 预留容量空间 |  每个元素  |
   
3. `RandomAccess`只是用来作为证明支持快速随机访问的标识，关键还是**底层数组天然支持随机访问**，实现了`RandomAccess`接口的list优先选择普通`for`循环，其次`foreach`；未实现的list优先选择`iterator`，大size的数据不要用普通`for`循环。

4. ArrayList与Vector的区别：Vector是线程安全的。

5. ArrayList的扩容机制：以无参数构造方法创建ArrayList时，实际上初始化赋值的是一个空数组。当真正对数组进行添加元素操作时，才真正分配容量，首次扩容为10。扩容逻辑是在`ensureCapacityInternal(int)`中完成的：先根据当前存储数组`length`和`minCapacity`判断是否需要扩容，当`minCapacity > length`时，调用`grow(int)`执行扩容。首先将`newCapacity`扩容至`oldCapacity`的**1.5**倍，然后检查`(newCapacity < minCapacity == true)`, `true`则以`minCapacity`为待扩容量赋值给`newCapacity`，接着检查`(newCapacity > MAX_ARRAY_SIZE) == true`，`true`则以`Integer.MAX_VALUE`赋值给`newCapacity`。最后调用`Arrays.copyOf(array, int)`返回`length`为`newCapacity`的新数组。

6. 最好在`add(Object)`大量元素之前调用`ensureCapacity(int)`方法以减少元素重排的次数。

7. `trimToSize()`可以缩短List底层数组`length`至当前元素个数以减少内存占用。

8. HashMap和Hashtable的区别：

   |              |                           HashMap                            | Hashtable |
   | :----------: | :----------------------------------------------------------: | :-------: |
   |   线程安全   |                              N                               |     Y     |
   |     效率     |                              高                              |    低     |
   | 支持null key |                              Y                               |     N     |
   | 底层数据结构 | 1.8以前是数组+链表，1.8以后当链表长度大于8时，将链表转为红黑树 |           |

   

9. HashSet底层是基于HashMap实现的，区别：

   |             HashMap              |                           HashSet                            |
   | :------------------------------: | :----------------------------------------------------------: |
   |          实现了Map接口           |                         实现Set接口                          |
   |            存储键值对            |                          仅存储对象                          |
   |   调用 `put()`向map中添加元素    |               调用 `add()`方法向Set中添加元素                |
   | HashMap使用键（Key）计算Hashcode | HashSet使用成员对象来计算hashcode值，对于两个对象来说hashcode可能相同，所以equals()方法用来判断对象的相等性， |

10. Jdk1.8之前`HashMap` 底层是 **数组和链表** 结合在一起使用也就是 **链表散列**。**HashMap 通过 key 的 hashCode 经过扰动函数处理过后得到 hash 值，然后通过 (n - 1) & hash 判断当前元素存放的位置（这里的 n 指的是数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。**

11. 所谓**扰动函数**指的就是 **HashMap 的 hash 方法。使用 hash 方法也就是扰动函数是为了防止一些实现比较差的 hashCode() 方法 换句话说使用扰动函数之后可以减少碰撞**。

12. 所谓 **“拉链法”** 就是：将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可。

13. ConcurrentHashMap/Hashtable/LinkedHashMap/TreeMap:

    |                   |                                                              |
    | :---------------: | :----------------------------------------------------------: |
    | ConcurrentHashMap |                 线程安全的HashMap，推荐使用                  |
    |     Hashtable     |                线程安全的HashMap，不推荐使用                 |
    |   LinkedHashMap   | 记录了插入顺序的HashMap，用Iterator遍历时按照记录顺序返回元素 |
    |      TreeMap      |               按照key值默认以升序排序的HashMap               |

14. 在HashMap中，哈希桶数组table的长度length大小必须为2的n次方(一定是合数)，这是一种非常规的设计，常规的设计是把桶的大小设计为素数。相对来说素数导致冲突的概率要小于合数。HashMap采用这种非常规设计，主要是为了在取模和扩容时做优化，同时为了减少冲突，HashMap定位哈希桶索引位置时，也加入了高位参与运算的过程。

15. 