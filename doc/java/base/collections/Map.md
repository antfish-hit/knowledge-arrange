# Map

1. HashMap和Hashtable的区别：

   |              |                           HashMap                            | Hashtable |
   | :----------: | :----------------------------------------------------------: | :-------: |
   |   线程安全   |                              N                               |     Y     |
   |     效率     |                              高                              |    低     |
   | 支持null key |                              Y                               |     N     |
   | 底层数据结构 | 1.8以前是数组+链表，1.8以后当链表长度大于8时，将链表转为红黑树 |           |

2. ConcurrentHashMap/Hashtable/LinkedHashMap/TreeMap:

   |                   |                                                              |
   | :---------------: | :----------------------------------------------------------: |
   | ConcurrentHashMap |                 线程安全的HashMap，推荐使用                  |
   |     Hashtable     |                线程安全的HashMap，不推荐使用                 |
   |   LinkedHashMap   | 记录了插入顺序的HashMap，用Iterator遍历时按照记录顺序返回元素 |
   |      TreeMap      |               按照key值默认以升序排序的HashMap               |

3. ConcurrentHashMap和Hashtable的区别：

   |                    |                      ConcurrentHashMap                       |  Hashtable   |
   | :----------------: | :----------------------------------------------------------: | :----------: |
   |    底层数据结构    |                        跟HashMap一致                         |  数组+链表   |
   | 实现线程安全的方式 | Jdk1.7分段(segment)可重入锁, Jdk 1.8 CAS和synchronized；synchronized只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲突，就不会产生并发 | synchronized |

   Hashtable:

   ![](../../../../assert/java/base/collections/HashTable全表锁.png)

   Jdk 1.7 ConcurrentHashMap:

   ![](../../../../assert/java/base/collections/ConcurrentHashMap分段锁.jpg)

   Jdk 1.8 ConcurrentHashMap:

   ![](../../../../assert/java/base/collections/JDK1.8-ConcurrentHashMap-Structure.jpg)

4. 

