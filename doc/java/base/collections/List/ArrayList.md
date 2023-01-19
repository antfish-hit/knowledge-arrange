# ArrayList

## 底层数据结构

- `Object`数组

```java
    transient Object[] elementData;
```

## 默认初始容量

- 以无参构造实例化`ArrayList`时，初始化的是一个空数组，在调用`add()`时才真正分配并扩容为**10**(default)。

## 扩容逻辑

- 伪代码：
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

```java
// 源代码
    private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }

    private static int hugeCapacity(int minCapacity) {
        if (minCapacity < 0) // overflow
            throw new OutOfMemoryError();
        return (minCapacity > MAX_ARRAY_SIZE) ?
            Integer.MAX_VALUE :
            MAX_ARRAY_SIZE;
    }
```

## 实用方法

- `ensureCapacity(int)`可以直接使容器扩容以确保能容纳指定大小，最好在添加大量元素之前调用，以减少添加元素时扩容的次数；
- `trimToSize()`可以将list容量缩减到实际大小。

## 优缺点

### 优点

- 因为底层数据结构是数组，所以支持快速访问元素

### 缺点

- 增加和删除元素不方便，因为涉及到扩容和集合元素重排