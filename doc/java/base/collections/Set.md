# Set

|                 | HashSet | LinkedHashSet |           TreeSet            |
| :-------------: | :-----: | :-----------: | :--------------------------: |
|     是否有序     |    N    |       Y       |              Y               |
|     排序规则     |    N    |    插入顺序    | key值自然顺序升序或指定排序规则 |
|   元素是否唯一   |    Y    |       Y       |              Y               |
| 元素是否可为null |    Y    |       Y       |              N               |
|     线程安全     |    N    |       N       |              N               |
|   底层数据结构   | HashMap | LinkedHashMap |           TreeMap            |