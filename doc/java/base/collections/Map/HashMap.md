# HashMap

## 底层数据结构

- 数组+链表，当链表长度大于8且数组长度超过64时， 将链表转为红黑树

## 几个重要的指标

```java
    // 默认数组初始容量为16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
    // 最大数组容量
    static final int MAXIMUM_CAPACITY = 1 << 30;
    // 默认的加载因子，用于计算扩容阈值的
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    // 链表长度阈值，用于判断链表是否要转红黑树的条件之一
    static final int TREEIFY_THRESHOLD = 8;
    // 链表长度阈值，用于判断红黑树是否要退回链表
    static final int UNTREEIFY_THRESHOLD = 6;
    // 数组长度阈值，用于判断链表是否要转红黑树的条件之二
    static final int MIN_TREEIFY_CAPACITY = 64;
```

## 定位索引位置的逻辑

1. HashMap 通过 **key 的 hashCode **经过**扰动函数**处理过后得到 hash 值，然后通过 **(n - 1) & hash 判断当前元素存放的位置（这里的 n 指的是数组的长度）**，如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同就通过拉链法解决冲突。当**链表长度大于阈值（默认为8）**，且**数组长度大于64**，会将链表转成红黑树。

2. 所谓**扰动函数**指的就是 **HashMap 的 hash 方法。使用 hash 方法也就是扰动函数是为了防止一些实现比较差的 hashCode() 方法 换句话说使用扰动函数之后可以减少碰撞**。

3. 在HashMap中，**哈希桶数组table的长度length大小必须为2的n次方(一定是合数)**，这是一种非常规的设计，常规的设计是把桶的大小设计为素数。相对来说素数导致冲突的概率要小于合数。HashMap采用这种非常规设计，**主要是为了在取模和扩容时做优化**，同时为了减少冲突，HashMap定位哈希桶索引位置时，也加入了高位参与运算的过程。

4. HashMap定位哈希桶索引位置的算法本质：**取key的hashCode值、高位运算、取模运算**。

   ![](../../../../../assert/java/base/collections/HashMap-hash.png)

## HashMap的put方法逻辑

   ![](../../../../../assert/java/base/collections/HashMap-put.png)

   1. **第一次存储键值对时先初始化数组**：判断键值对数组`table[i]`是否为空或为`null`，否则执行`resize()`进行扩容；
   2. **根据key计算hash值并定位**：
       1. 根据键值`key`计算`hash`值得到插入的数组索引i，如果`table[i] == null`，直接新建节点添加，转向f，如果`table[i]`不为空，转向c；
       2. 判断`table[i]`的首个元素是否和`key`一样，如果相同直接覆盖`value`，否则转向d，这里的相同指的是`hashCode`以及`equals`；
       3. 判断`table[i]`是否为`treeNode`，即`table[i]`是否是红黑树，如果是红黑树，则直接在树中插入键值对，否则转向e；
       4. 遍历`table[i]`，判断链表长度是否大于8，大于8的话把链表转换为红黑树，在红黑树中执行插入操作，否则进行链表的插入操作；遍历过程中若发现`key`已经存在直接覆盖`value`即可；
   3. **比较size和扩容阈值以确认是否要扩容**：插入成功后，判断实际存在的键值对数量`size`是否超多了最大容量的`threshold`，如果超过，进行扩容。
