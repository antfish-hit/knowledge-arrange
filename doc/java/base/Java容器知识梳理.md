# Java容器知识梳理

1. List、Set、Map的区别：

   List：存储一组不唯一对象的集合

   Set：不重复存储集合(根据元素hashCode判断)

   Map：键值对存储字典，以key的hashCode判断重复

9. HashSet底层是基于HashMap实现的，区别：

   |             HashMap              |                           HashSet                            |
   | :------------------------------: | :----------------------------------------------------------: |
   |          实现了Map接口           |                         实现Set接口                          |
   |            存储键值对            |                          仅存储对象                          |
   |   调用 `put()`向map中添加元素    |               调用 `add()`方法向Set中添加元素                |
   | HashMap使用键（Key）计算Hashcode | HashSet使用成员对象来计算hashcode值，对于两个对象来说hashcode可能相同，所以equals()方法用来判断对象的相等性， |

15. Comparable和Comparator的区别：前者用于实现让类获得可排序的性质，后者是定制化排序器。

