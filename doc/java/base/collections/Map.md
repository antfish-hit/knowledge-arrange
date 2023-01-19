# Map

1. ConcurrentHashMap/HashMap/LinkedHashMap/TreeMap:

   |                   |            HashMap              |            LinkedHashMap              |           TreeMap               |        ConcurrentHashMap                  |
   | :---------------: | :---------------------: | :---------------------: | :---------------------: | :---------------------: |
   |      线程安全             |            N              |              N            |           N               |             Y             |
   |       支持null key            |             Y             |             Y             |              N            |            N             |
   |      是否有序             |             N             |             Y             |              Y            |               N           |
    |         排序规则          |             /             |              插入顺序            |       key值自然顺序升序或指定排序规则                   |         /                 |
   |         底层数据结构          |      数组+链表，当链表长度大于8且数组长度超过64时， 将链表转为红黑树                   |        双向链表+HashMap                  |          红黑树                |                    类似HashMap                |
    |      实现线程安全的方式             |             /             |             /             |              /            |           CAS和synchronized；synchronized只锁定当前链表或红黑二叉树的首节点，这样只要hash不冲突，就不会产生并发               |

   Jdk 1.8 ConcurrentHashMap:

   ![](../../../../assert/java/base/collections/JDK1.8-ConcurrentHashMap-Structure.jpg)

4. 

