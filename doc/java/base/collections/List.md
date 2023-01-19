# List

1. ArrayList与LinkedList的区别：

   |                          |  ArrayList   | LinkedList |
   | :----------------------: | :----------: | :--------: |
   |         线程安全         |      N       |     N      |
   |       底层数据结构       |  Object数组  |  双向链表  |
   | 插入与删除受元素位置影响 |      N       |     Y      |
   |     支持快速随机访问     |      Y       |     N      |
   |        内存占用点        | 预留容量空间 |  每个元素  |

2. `RandomAccess`只是用来作为证明支持快速随机访问的标识，关键还是**底层数组天然支持随机访问**，实现了`RandomAccess`接口的list优先选择普通`for`循环，其次`foreach`；未实现的list优先选择`iterator`，大size的数据不要用普通`for`循环。