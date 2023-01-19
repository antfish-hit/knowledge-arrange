# LinkedList

## 概括

记录头尾双端节点，底层数据结构为双向链表的集合

## 底层数据结构

- `Node`双向链表

```java
    // 源码
    /**
     * Pointer to first node.
     * Invariant: (first == null && last == null) ||
     *            (first.prev == null && first.item != null)
     */
    transient Node<E> first;

    /**
     * Pointer to last node.
     * Invariant: (first == null && last == null) ||
     *            (last.next == null && last.item != null)
     */
    transient Node<E> last;

    /**
     * Constructs an empty list.
     */
    public LinkedList() {
    }
    
    private static class Node<E> {
        E item;
        Node<E> next;
        Node<E> prev;

        Node(Node<E> prev, E element, Node<E> next) {
            this.item = element;
            this.next = next;
            this.prev = prev;
        }
    }
```

- `add(E)`新增元素默认追加到链表尾部

```java
    //源码
    public boolean add(E e) {
        linkLast(e);
        return true;
    }
    /**
     * Links e as last element.
     */
    void linkLast(E e) {
        final Node<E> l = last;
        final Node<E> newNode = new Node<>(l, e, null);
        last = newNode;
        if (l == null)
            first = newNode;
        else
            l.next = newNode;
        size++;
        modCount++;
    }
```

## 优缺点

### 优点

- 增加和删除元素方便

### 缺点

- 查询元素不方便
- 因为底层数据结构是双向链表，所以占更多空间
