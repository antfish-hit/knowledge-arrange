# List

1. ArrayList与LinkedList的区别：

   |                          |  ArrayList   | Linkedlist |
   | :----------------------: | :----------: | :--------: |
   |         线程安全         |      N       |     N      |
   |       底层数据结构       |  Object数组  |  双向链表  |
   | 插入与删除受元素位置影响 |      N       |     Y      |
   |     支持快速随机访问     |      Y       |     N      |
   |        内存占用点        | 预留容量空间 |  每个元素  |

2. `RandomAccess`只是用来作为证明支持快速随机访问的标识，关键还是**底层数组天然支持随机访问**，实现了`RandomAccess`接口的list优先选择普通`for`循环，其次`foreach`；未实现的list优先选择`iterator`，大size的数据不要用普通`for`循环。

3. ArrayList与Vector的区别：Vector是线程安全的。

4. ArrayList的扩容机制：以无参数构造方法创建ArrayList时，实际上初始化赋值的是一个空数组。当真正对数组进行添加元素操作时，才真正分配容量，首次扩容为10。扩容逻辑是在`ensureCapacityInternal(int)`中完成的：先根据当前存储数组`length`和`minCapacity`判断是否需要扩容，当`minCapacity > length`时，调用`grow(int)`执行扩容。首先将`newCapacity`扩容至`oldCapacity`的**1.5**倍，然后检查`(newCapacity < minCapacity == true)`, `true`则以`minCapacity`为待扩容量赋值给`newCapacity`，接着检查`(newCapacity > MAX_ARRAY_SIZE) == true`，`true`则以`Integer.MAX_VALUE`赋值给`newCapacity`。最后调用`Arrays.copyOf(array, int)`返回`length`为`newCapacity`的新数组。

5. 最好在`add(Object)`大量元素之前调用`ensureCapacity(int)`方法以减少元素重排的次数。

6. `trimToSize()`可以缩短List底层数组`length`至当前元素个数以减少内存占用。