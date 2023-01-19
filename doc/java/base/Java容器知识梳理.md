# Java容器知识梳理

## List、Set、Map的区别

- List：存储一组可重复对象的集合
- Set：不重复存储集合(先根据元素hashCode判断，若相等再根据equals()判断)
- Map：键值对存储字典，以key的hashCode判断重复，若相等再根据equals()判断

### 集合框架底层数据结构总结

- List
    - `ArrayList`：`Object[]`数组
    - `LinkedList`：双向链表
- Set
    - `HashSet`（无序，唯一）：基于`HashMap`实现的，底层采用`HashMap`来保存元素
    - `LinkedHashSet`：`LinkedHashSet`是`HashSet`的子类，并且其内部是通过`LinkedHashMap`来实现的
    - `TreeSet`（有序，唯一）：基于`TreeMap`实现的
- Map
    - `HashMap`：JDK1.8之前`HashMap`由**数组+链表**组成的，数组是`HashMap`的主体，链表则是主要**为了解决哈希冲突而存在**的（“拉链法”解决冲突）。JDK1.8以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（**默认为8**）（**将链表转换成红黑树前会判断，如果当前数组的长度小于64，那么会选择先进行数组扩容，而不是转换为红黑树**）时，将链表转化为红黑树，以减少搜索时间
    - `LinkedHashMap`：`LinkedHashMap`继承自`HashMap`，所以它的底层仍然是基于拉链式散列结构即由数组和链表或红黑树组成。另外，`LinkedHashMap`在上面结构的基础上，增加了一条双向链表，使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑。
    - `TreeMap`：红黑树

## Arraylist 与 LinkedList 区别

- 都是线程不安全的。
- `Arraylist`底层使用的是`Object`数组；`LinkedList`底层使用的是**双向链表**。
- `Arraylist`利于快速访问，`LinkedList`利于增删元素。
- `LinkedList`内存占用比`Arraylist`大。

## list 的遍历方式选择

- 实现了`RandomAccess`接口的list，优先选择普通`for`循环 ，其次`foreach`。
- 未实现`RandomAccess`接口的list，优先选择`iterator`遍历（`foreach`遍历底层也是通过`iterator`实现的,），大size的数据，千万不要使用普通for循环。

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