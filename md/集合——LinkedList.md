## 1、LinkedList

LinkedList 本质是 **双向链表**。除了有 ArrayList 的基本操作方法外，还提供了一些操作头部或尾部的方法。LinkedList 的优点在于 **可以将零散的内存单元通过附加引用的方式关联起来，形成按链路顺序查找的线性结构，内存利用率较高**

- LinkedList 的插入和删除速度很快，但是随机访问速度则很慢。因为所有的操作都是要按照双重链表的需要执行。**在列表中索引的操作将从开头或结尾遍历列表**（从靠近指定索引的一端）。这样做的好处就是可以通过较低的代价在 List 中进行插入和删除操作


### 1.1  实现接口与父类

![20201002164808](../md.assets/集合/20201002164808.png)

- LinkedList 实现了两个表示接口 `Serializable`、`Cloneable`
  - `Serializable`：支持序列化
  - `Cloneable`：支持克隆
- LinkedList 继承了  `AbstractSequentiaList` 抽象类，支持顺序访问，并且实现了 `List` 接口，提供了添加、删除、修改、遍历等功能
- LinkedList 还实现了 `Deque` 接口，这个接口 **同时具有队列和栈的性质**

## 2、LinkedList 中的属性

```java
// LinkedList实际大小
transient int size = 0;

// 头节点
transient Node<E> first;

// 尾节点
transient Node<E> last;
```

```java
private static class Node<E> {
    // 节点值
    E item;
    // 前节点
    Node<E> next;
    // 后节点
    Node<E> prev;

    Node(Node<E> prev, E element, Node<E> next) {
        this.item = element;
        this.next = next;
        this.prev = prev;
    }
}
```

![node](../md.assets/集合/node.png)

## 3、构造方法

```java
// 无参构造方法
public LinkedList() {
}

// 构造一个包含指定集合的LinkedList
public LinkedList(Collection<? extends E> c) {
    this();
    // 调用addAll方法
    addAll(c);
}
```

## 4、常用方法

### 4.1  添加元素

#### 4.1.1  添加元素到头部或尾部

![add](../md.assets/集合/add-linkedlist.png)

```java
// 添加元素到尾部
public boolean add(E e) {
    linkLast(e);
    return true;
}

// 添加元素到头部
public void addFirst(E e) {
    linkFirst(e);
}

// 添加元素到尾部
public void addLast(E e) {
    linkLast(e);
}
```

```java
// 添加元素到头部
public void push(E e) {
    addFirst(e);
}
```

```java
// 添加元素到尾部
public boolean offer(E e) {
    return add(e);
}

// 添加元素到头部
public boolean offerFirst(E e) {
    addFirst(e);
    return true;
}

// 添加元素到尾部
public boolean offerLast(E e) {
    addLast(e);
    return true;
}
```

- `linkFirst` 与 `linkLast`

![linkfirst](../md.assets/集合/linkfirst.png)

```java
// 链接元素到头部
private void linkFirst(E e) {
    // 获取头节点
    final Node<E> f = first;
    // 新建元素节点，因为添加到头部，将前节点指向null
    final Node<E> newNode = new Node<>(null, e, f);
    // 将头节点指向新建的节点
    first = newNode;
    if (f == null) {
        // 如果是空链表，则将尾节点也指向新建的节点，头尾都指向同一节点
        last = newNode;
    } else {
        // 将原来的头结点的前节点指向新建的节点
        f.prev = newNode;
    }
    size++;
    // 继承于AbstractList，记录着集合的修改次数
    modCount++;
}

// 链接元素到尾部
void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null) {
        first = newNode;
    } else {
        l.next = newNode;
    }
    size++;
    modCount++;
}
```

#### 4.1.2  添加元素到指定位置

![add-index-linkedlist](../md.assets/集合/add-index-linkedlist.png)

```java
// 添加元素到指定位置
public void add(int index, E element) {
    // 检查传入的下标是否越界
    checkPositionIndex(index);

    // 如果传入的下标等于实际元素大小，则将传入的元素添加到尾部，否则添加到传入的下标位置
    if (index == size) {
        linkLast(element);
    } else {
        linkBefore(element, node(index));
    }
}

// 界限检查
private void checkPositionIndex(int index) {
    if (!isPositionIndex(index)) {
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
}

// 界限检查
private boolean isPositionIndex(int index) {
    return index >= 0 && index <= size;
}

// 错误信息
private String outOfBoundsMsg(int index) {
    return "Index: "+index+", Size: "+size;
}

// 获取指定下标位置的元素
Node<E> node(int index) {
    // assert isElementIndex(index);
	// 判断从头部还是从尾部遍历
    // 将实际容量大小除以2，下标大于该值则从后向前遍历，否则从前向后遍历，最多遍历size/2个元素
    if (index < (size >> 1)) {
        Node<E> x = first;
        // 遍历到下标位置
        for (int i = 0; i < index; i++) {
            x = x.next;
        }
        return x;
    } else {
        Node<E> x = last;
        for (int i = size - 1; i > index; i--) {
            x = x.prev;
        }
        return x;
    }
}
```

- `linkBefore`

![linkbefore](../md.assets/集合/linkbefore.png)

```java
// 将传入的元素e添加到元素succ前
void linkBefore(E e, Node<E> succ) {
    // assert succ != null;
    // 获取succ的前节点
    final Node<E> pred = succ.prev;
    // 新建元素节点，前节点指向succ的前节点，后节点指向succ
    final Node<E> newNode = new Node<>(pred, e, succ);
    // 将succ的前节点指向新建的节点
    succ.prev = newNode;
    if (pred == null) {
        // 如果succ旧的前节点为null，则说明succ是一个头节点，所以修改后应将头节点指向新建的节点
        first = newNode;
    } else {
        // 如果succ之前不是头节点，则将修改后的succ的后节点指向新建的节点
        pred.next = newNode;
    }
    size++;
    modCount++;
}
```

### 4.2  获得元素

```java
// 根据下标获取元素
public E get(int index) {
    // 检查传入的下标是否越界
    checkElementIndex(index);
    // 调用node方法
    return node(index).item;
}

// 界限检查
private void checkElementIndex(int index) {
    if (!isElementIndex(index)) {
        throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
}

// 界限检查
private boolean isElementIndex(int index) {
    return index >= 0 && index < size;
}
```

```java
// 获得头元素
public E element() {
    return getFirst();
}
```

```java
// 获得头元素
public E peek() {
    final Node<E> f = first;
    return (f == null) ? null : f.item;
}

// 获得头元素
public E peekFirst() {
    final Node<E> f = first;
    return (f == null) ? null : f.item;
}

// 获得尾元素
public E peekLast() {
    final Node<E> l = last;
    return (l == null) ? null : l.item;
}
```

```java
// 获得头元素
public E getFirst() {
    final Node<E> f = first;
    if (f == null) {
        throw new NoSuchElementException();
    }
    return f.item;
}

// 获得尾元素
public E getLast() {
    final Node<E> l = last;
    if (l == null) {
        throw new NoSuchElementException();
    }
    return l.item;
}
```

### 4.3  修改元素

```java
public E set(int index, E element) {
    // 检查传入的下标是否越界
    checkElementIndex(index);
    // 获得下标位置的元素
    Node<E> x = node(index);
    // 获得旧值，用于返回
    E oldVal = x.item;
    // 修改值
    x.item = element;
    return oldVal;
}
```

### 4.4  删除元素

```java
// 删除传入的元素
public boolean remove(Object o) {
    // 因为LinkedList可以保存null，所以传入的元素为null也可以进行操作
    if (o == null) {
        // 从头元素开始遍历，如果元素等于null，调用unlink方法，只删除查到的第一个元素
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null) {
                unlink(x);
                return true;
            }
        }
    } else {
        // 从头元素开始遍历，如果元素等于传入的元素，调用unlink方法，只删除查到的第一个元素
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item)) {
                unlink(x);
                return true;
            }
        }
    }
    return false;
}

// 删除头元素
public E removeFirst() {
    final Node<E> f = first;
    if (f == null) {
        throw new NoSuchElementException();
    }
    return unlinkFirst(f);
}

// 删除尾元素
public E removeLast() {
    final Node<E> l = last;
    if (l == null) {
        throw new NoSuchElementException();
    }
    return unlinkLast(l);
}
```

```java
// 删除头元素
public E pop() {
    return removeFirst();
}
```

```java
// 删除头元素
public E poll() {
    final Node<E> f = first;
    return (f == null) ? null : unlinkFirst(f);
}

// 删除头元素
public E pollFirst() {
    final Node<E> f = first;
    return (f == null) ? null : unlinkFirst(f);
}

// 删除尾元素
public E pollLast() {
    final Node<E> l = last;
    return (l == null) ? null : unlinkLast(l);
}
```

- `unlink`

![unlink](../md.assets/集合/unlink.png)

```java
// 移除传入元素的链接
E unlink(Node<E> x) {
    // assert x != null;
    // 传入元素的值，用于返回
    final E element = x.item;
    // 传入元素的后节点
    final Node<E> next = x.next;
    // 传入元素的前节点
    final Node<E> prev = x.prev;

    // 如果前节点为null，则说明该节点是头节点，直接将头节点指向后节点
    if (prev == null) {
        first = next;
    } else {
        // 将前节点的后节点，指向该节点的后节点
        prev.next = next;
        // 将元素的前节点指向null
        x.prev = null;
    }

    // 如果后节点为null，则说明该节点是尾节点，直接将尾节点指向前节点
    if (next == null) {
        last = prev;
    } else {
        // 将后节点的前节点，指向该节点的前节点
        next.prev = prev;
        // 将元素的后节点指向null
        x.next = null;
    }

    // 将元素值指向null
    x.item = null;
    size--;
    modCount++;
    // 返回删除的值
    return element;
}

// 移除头元素的链接
private E unlinkFirst(Node<E> f) {
    // assert f == first && f != null;
    // 传入元素的值，用于返回
    final E element = f.item;
    // 传入元素的后节点
    final Node<E> next = f.next;
    // 将元素值指向null
    f.item = null;
    // 将元素的后节点指向null
    f.next = null; // help GC
    // 将头节点指向后节点
    first = next;
    if (next == null) {
        // 如果后节点为null，则表示是一个空链表，则尾节点也指向null
        last = null;
    } else {
        // 如果后节点不为null，则将后节点的前节点指向null，即声明为头节点
        next.prev = null;
    }
    size--;
    modCount++;
    return element;
}

// 移除尾元素的链接
private E unlinkLast(Node<E> l) {
    // assert l == last && l != null;
    final E element = l.item;
    final Node<E> prev = l.prev;
    l.item = null;
    l.prev = null; // help GC
    last = prev;
    if (prev == null) {
        first = null;
    } else {
        prev.next = null;
    }
    size--;
    modCount++;
    return element;
}
```

### 4.5  其他方法

```java
public boolean contains(Object o) {
    return indexOf(o) != -1;
}

public int size() {
    return size;
}

public boolean addAll(Collection<? extends E> c) {
    return addAll(size, c);
}

public boolean addAll(int index, Collection<? extends E> c) {
    checkPositionIndex(index);

    Object[] a = c.toArray();
    int numNew = a.length;
    if (numNew == 0)
        return false;

    Node<E> pred, succ;
    if (index == size) {
        succ = null;
        pred = last;
    } else {
        succ = node(index);
        pred = succ.prev;
    }

    for (Object o : a) {
        @SuppressWarnings("unchecked") E e = (E) o;
        Node<E> newNode = new Node<>(pred, e, null);
        if (pred == null)
            first = newNode;
        else
            pred.next = newNode;
        pred = newNode;
    }

    if (succ == null) {
        last = pred;
    } else {
        pred.next = succ;
        succ.prev = pred;
    }

    size += numNew;
    modCount++;
    return true;
}

public void clear() {
    for (Node<E> x = first; x != null; ) {
        Node<E> next = x.next;
        x.item = null;
        x.next = null;
        x.prev = null;
        x = next;
    }
    first = last = null;
    size = 0;
    modCount++;
}

public int indexOf(Object o) {
    int index = 0;
    if (o == null) {
        for (Node<E> x = first; x != null; x = x.next) {
            if (x.item == null)
                return index;
            index++;
        }
    } else {
        for (Node<E> x = first; x != null; x = x.next) {
            if (o.equals(x.item))
                return index;
            index++;
        }
    }
    return -1;
}

public int lastIndexOf(Object o) {
    int index = size;
    if (o == null) {
        for (Node<E> x = last; x != null; x = x.prev) {
            index--;
            if (x.item == null)
                return index;
        }
    } else {
        for (Node<E> x = last; x != null; x = x.prev) {
            index--;
            if (o.equals(x.item))
                return index;
        }
    }
    return -1;
}

public boolean removeFirstOccurrence(Object o) {
    return remove(o);
}

public boolean removeLastOccurrence(Object o) {
    if (o == null) {
        for (Node<E> x = last; x != null; x = x.prev) {
            if (x.item == null) {
                unlink(x);
                return true;
            }
        }
    } else {
        for (Node<E> x = last; x != null; x = x.prev) {
            if (o.equals(x.item)) {
                unlink(x);
                return true;
            }
        }
    }
    return false;
}

public Object[] toArray() {
    Object[] result = new Object[size];
    int i = 0;
    for (Node<E> x = first; x != null; x = x.next)
        result[i++] = x.item;
    return result;
}


@SuppressWarnings("unchecked")
public <T> T[] toArray(T[] a) {
    if (a.length < size)
        a = (T[])java.lang.reflect.Array.newInstance(
        a.getClass().getComponentType(), size);
    int i = 0;
    Object[] result = a;
    for (Node<E> x = first; x != null; x = x.next)
        result[i++] = x.item;

    if (a.length > size)
        a[size] = null;

    return a;
}
```

## 5、内部类

![20201002165409](../md.assets/集合/20201002165409.png)



