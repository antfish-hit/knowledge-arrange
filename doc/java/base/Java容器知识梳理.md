# Java容器知识梳理

## List、Set、Map的区别

- List：存储一组可重复对象的集合
- Set：不重复存储集合(根据元素hashCode判断)
- Map：键值对存储字典，以key的hashCode判断重复

## Arraylist 与 LinkedList 区别

- 都是线程不安全的。
- `Arraylist`底层使用的是`Object`数组；`LinkedList`底层使用的是**双向链表**。
- `Arraylist`利于快速访问，`LinkedList`利于增删元素。
- `LinkedList`内存占用比`Arraylist`大。

## list 的遍历方式选择

- 实现了`RandomAccess`接口的list，优先选择普通`for`循环 ，其次`foreach`。
- 未实现`RandomAccess`接口的list，优先选择`iterator`遍历（`foreach`遍历底层也是通过`iterator`实现的,），大size的数据，千万不要使用普通for循环。

## ArrayList与Vector的区别
Vector的所有方法都是同步的。

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

## HashMap和HashTable的区别

- `HashMap`非线程安全，`HashTable`线程安全。
- `HashMap`支持key为`null`，`HashTable`不支持key or value为`null`。
- 不指定`initCapacity`时，`initCapacity` = `defaultCapacity`，`HashTable`的`defaultCapacity`为`11`，之后每次扩容都是`newCapacity = currentCapacity * 2 + 1`；`HashMap`的`defaultCapacity`为`16`，之后每次扩容都是`newCapacity = currentCapacity * 2`。
- 指定`initCapacity`时，`HashTable`直接用`initCapacity`，`HashMap`则将其扩充为`2`的幂次方大小。
- JDK1.8前，`HashMap`底层实现是**链表数组**，JDK1.8后，当链表长度大于阈值(默认为`8`)时,将链表转为**红黑树**。
- `HashMap`中的参数`loadFactor`默认值为`0.75`，是控制数组存放数据的疏密程度，越接近1越密，元素查找效率越低；越接近0越疏，数组利用率越低。

## ConcurrentHashMap实现线程安全的方式

- 在JDK1.7时，将底层链表数组均分为多个`segment`，每个`segment`有一把锁，多线程环境下以**分段锁**改善性能。
- 在JDK1.8时，并发控制使用`synchronized`和`CAS`来操作。每个链表(or 红黑树根节点)一把锁。

## HashSet与HashMap的区别

   |             HashMap              |                           HashSet                            |
   | :------------------------------: | :----------------------------------------------------------: |
   |          实现了Map接口           |                         实现Set接口                          |
   |            存储键值对            |                          仅存储对象                          |
   |   调用 `put()`向map中添加元素    |               调用 `add()`方法向Set中添加元素                |
   | HashMap使用键（Key）计算Hashcode | HashSet使用成员对象来计算hashcode值，对于两个对象来说hashcode可能相同，所以equals()方法用来判断对象的相等性， |

## Comparable和Comparator的区别
前者用于实现让类获得可排序的性质，后者是定制化排序器。

references:
[1] [ArrayList扩容机制](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/ArrayList-Grow.md)