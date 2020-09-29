## 1、集合

![20190901145230](../md.assets/集合/20190901145230.png)

## 2、Collection

Collection 接口是最基本的集合接口，不提供直接的实现。Collection 所代表的是一种规则，它所包含的元素都必须遵循一条或者多条规则，如是否允许重复、是否按照顺序插入

### 2.1  Collection 与 Collections 的区别

- Collection：是一个接口，是各种集合结构的父接口
- Collections：是一个工具类，包含有各种有关集合操作的静态方法

## 3、Iterator（迭代器）

Iterator 是一个接口，迭代器取代了集合框架中的 Enumeration，允许调用者在迭代过程中移除元素。它只有三个方法：`boolean hasNext()`、`T next()`、`void remove()`

- 在遍历集合的元素可以使用 Iterator，它的具体实现是 **以内部类的方式实现的**

### 3.1  Enumeration 与 Iterator 的区别

Enumeration 的速度是 Iterator 的两倍，也使用更少的内存。Enumeration 满足了基础的需要，但 Iterator 更加安全，因为当一个集合正在被遍历的时候，它会阻止其它线程去修改集合

- Iterator 允许调用者从集合中移除元素，而 Enumeration 不可以

## 4、数组与集合

数组是一种 **顺序表**，在各种高级语言中，它是组织和处理数据的一种常见方式，可以使用索引下标进行快速定位并获取指定位置的元素。在 Java 中，数组用以存储同一类型的对象，一旦分配内存后则无法扩容

### 4.1  数组和集合的区别

|                |                             数组                             |                             集合                             |
| :------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|      长度      |                           长度固定                           |                 可以根据元素的增加而自动增长                 |
| 元素的数据类型 | 可以存储基本数据类型和引用数据类型，基本数据类型存储的是值，引用数据类型存储的是地址值 | 只能存储引用数据类型，如果存储基本数据类型时，会 **自动装箱** 变成相应的包装类 |
|    存储内容    |                      存储同一类型的元素                      |               可以存储不同类型的元素（不建议）               |

### 4.2  数组转集合

可以使用 `Arrays.asList` 方法进行数组转集合。该方法适用于对象型数据的数组，不建议使用于基本数据类型的数组

- 支持使用 set 方法修改元素的值，**原有数组相应位置的值同时也会被修改**，但是 **不能进行修改元素个数的任何操作**， 如 add、remove、clear 方法，否则均会抛出 `UnsupportedOperationException` 异常
- `Arrays.asList` 体现的是适配器模式，后台的数据仍是原有数组，set 方法间接地对数组进行值的修改操作

- asList 的 **返回对象是一个 Arrays 里的内部类**，它并没有实现集合个数的相关修改方法，这也正是抛出异常的原因

```java
public class Test {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(getArray());
        // 修改元素值
        list.set(0, 999);
        // 报错
        list.add(123);
        list.forEach(System.out::println);
    }
    
    static Integer[] getArray() {
        Integer[] arr = new Integer[100];
        for (int i = 0; i < 100; i++) {
            arr[i] = i;
        }
        return arr;
    }
}

```

### 4.3  集合转数组

可以使用集合的 toArray 方法把集合转换成数组，数组长度要等于集合的长度，否则可能会输出 Null 值

```java
public class Test {
    public static void main(String[] args) {
        List<Integer> list = getList();
        Object[] arr = list.toArray();
        System.out.println(Arrays.toString(arr));
    }

    static List<Integer> getList() {
        List<Integer> list = new ArrayList<>();
        for (int i = 0; i < 100; i++) {
            list.add(i);
        }
        return list;
    }
}
```

## 5、List 接口

List 接口为 Collection 直接接口，是线性数据结构的主要实现。List 代表的是 **有序的 Collection**，里面的数据是有序可重复的

### 5.1  ArrayList

ArrayList 内部使用 **数组** 进行存储。**初始容量为 10**，该容量代表了数组的大小。当容器中的元素不断增加时，集合扩容时会创建更大的数组空间。所以 **最好指定一个初始容量值**，避免过多的进行扩容操作而浪费时间、效率

- ArrayList 擅长于随机访问，但插入和删除时速度通常很慢，因为可能需要移动其他元素

### 5.2  LinkedList

LinkedList 本质是 **双向链表**。除了有 ArrayList 的基本操作方法外，还提供了 get，remove，insert 方法操作首部或尾部。LinkedList 包含 3 个重要的成员：**size、first、last**。size 是双向链表中节点的个数，first 和 last 分别指向第一个和最后一个节点的引用。LinkedList 的优点在于 **可以将零散的内存单元通过附加引用的方式关联起来，形成按链路顺序查找的线性结构，内存利用率较高**

- LinkedList 除了继承 AbstractList 抽象类外，LinkedList 还实现了 **Deque 接口**，这个接口 **同时具有队列和栈的性质**

- LinkedList 的插入和删除速度很快，但是随机访问速度则很慢。因为所有的操作都是要按照双重链表的需要执行。**在列表中索引的操作将从开头或结尾遍历列表**（从靠近指定索引的一端）。这样做的好处就是可以通过较低的代价在 List 中进行插入和删除操作

- 与 ArrayList 一样，LinkedList 也是非同步的。如果多个线程同时访问一个 List，则必须自己实现访问同步。一种解决方法是在创建 List 时构造一个同步的 List：`List list = Collections.synchronizedList(new LinkedList(...))`

### 5.3  Vector

与 ArrayList 相似，但是 Vector 是同步的，即 Vector 是 **线程安全的动态数组**。它的操作与 ArrayList 几乎一样

### 5.4  Stack

**继承自 Vector**，实现一个 **后进先出的堆栈**，Stack 刚创建后是空栈。Stack 提供 5 个额外的方法使得 Vector 得以被当作堆栈使用。基本的 push 和 pop 方法，还有 peek 方法得到栈顶的元素，empty 方法测试堆栈是否为空，search 方法检测一个元素在堆栈中的位置

## 6、Queue 接口

Queue（队列）是一种先进先出的数据结构，队列是一种特殊的线性表，只允许在表的一端进行获取操作，在表的另一端进行插入操作，**可以存放重复的数据**。主要分为两大类

- 阻塞式队列：**队列满了以后再插入元素则会抛出异常**，主要包括 ArrayBlockQueue、PriorityBlockingQueue、LinkedBlockingQueue
- 双端队列：**支持在头、尾两端插入和移除元素**，主要包括：ArrayDeque、LinkedBlockingDeque、LinkedList

自从 BlockingQueue（阻塞队列）问世以来，队列的地位得到极大的提升，在各种高并发编程场景中，由于其本身 FIFO 的特性和阻塞操作的特点，经常被作为 Buffer（数据缓冲区）使用

## 7、Map 接口

Map 集合是以键值对作为存储元素实现的 **哈希结构**，**Key 按某种哈希函数计算后是唯一的，Value 则是可以重复的**。Map 没有继承 Collection，两个接口之间只是有依赖关系。最早用于存储键值对的 Hashtable 因为性能瓶颈已经被淘汰，而如今广泛使用的 HashMap，线程是不安全的。ConcurrentHashMap 是线程安全的，在 Java 8 中进行了锁的大幅度优化，体现出不错的性能。**在多线程并发场景中，优先推荐使用 ConcurrentHashMap**

- 可以使用 `keySet()` 查看所有的 Key，使用 `values()` 查看所有的 Value，使用 `entrySet()` 查看所有的键值对

### 7.1  HashMap

以哈希表数据结构实现，查找对象时通过哈希函数计算其位置，它是为快速查询而设计的，其内部定义了一个 hash 表数组（Entry[]），元素会通过 **哈希转换函数** 将元素的 **哈希地址转换成数组中存放的索引**，如果有冲突，则使用 **散列链表的形式将所有相同哈希地址的元素串起来**，可以通过查看 `HashMap.Entry` 的源码它是一个单链表结构

### 7.2  TreeMap

键以某种排序规则排序，内部以 **红黑树** 数据结构实现，实现了 SortedMap 接口

### 7.3  HashTable

以哈希表数据结构实现，解决冲突时与 HashMap 一样也是采用了散列链表的形式，但性能比 HashMap 要低

## 8、Set 接口

Set 是 **不允许出现重复元素** 的集合类型。Set 体系最常用的是 HashSet、TreeSet、LinkedHashSet。**它允许 Null 的存在但是仅有一个**

### 8.1  HashSet

HashSet 堪称 **查询速度最快的集合**，从源码分析是使用 **HashMap** 来实现的，只是 **Value 固定为一个静态对象，使用 Key 保证集合元素的唯一性，但它不保证集合元素的顺序**

### 8.2  TreeSet

从源码分析是使用 **TreeMap** 来实现的，**底层为树结构**，在添加新元素到集合中时，按照某种比较规则将其插入合适的位置，保证插入后的集合仍然是有序的

### 8.3  LinkedHashSet

继承自 HashSet，具有 HashSet 的优点，**内部使用链表维护了元素插入顺序**

## 9、集合间的异同

### 9.1  Vector、ArrayList 与 LinkedList

|              | ArrayList | LinkedList | Vector |
| :----------: | :-------: | :--------: | :----: |
| 底层数据结构 |   数组    |    链表    |  数组  |
|   线程安全   |  不安全   |   不安全   |  安全  |
|     查询     |    快     |     慢     |   快   |
|     增删     |    慢     |     快     |   慢   |
|     效率     |    高     |     高     |   低   |

### 9.2  HashSet、LinkedHashSet 与 TreeSet

- HashSet：**无序**，HashSet 中存放自定义类型对象时，一定要重写 hashCode 和 equals 方法
- LinkedHashSet：底层使用了链表的数据结构，特点是 **读取元素的顺序跟存入元素的顺序是一致的**
- TreeSet：特点是 **可以对存放进去的元素进行排序**

### 9.3  HashMap、HashTable 与 HashSet

- **实现接口不同**：HashMap、HashTable 是 Map 接口的实现类，而 HashSet 是 Set 接口的实现类，Set 接口是继承 Collection 接口
- **线程安全性，同步**：HashTable 中的方法加了同步锁（synchronized），所以对象是线程安全，而 HashMap 是异步的，所以存放的对象并不是线程安全的，而 HashSet 的底层是用 HashMap 实现的，所以它也不是线程安全的
- **执行效率**：HashTable 是同步的，而 HashMap 是异步的，所以 HashMap 的执行效率比 HashTable 要高，三者之间执行效率的排序是：HashMap > HashSet > HashTable
- **Key、Value 能否存放 Null**：HashMap 的 Key、Value 都可以为 Null，而 HashTable 是不能存放 Null，HashSet 存放的不是键值对，而是对象，也是可以为 Null
- **添加元素的方法不同**：HashMap 是通过 put 方法来增加元素的，而 HashSet 是通过 add 方法来增加元素的

### 9.4  HashMap 与 ConcurrentHashMap

ConcurrentHashMap 对整个桶数组进行了分割分段（Segment），然后在每一个分段上都用 lock 锁进行保护，相对于 HashTable 的 synchronized 锁的粒度更精细了一些，并发性能更好，而 HashMap 没有锁机制，不是线程安全的。JDK1.8 之后 ConcurrentHashMap 启用了一种全新的方式实现，利用 CAS 算法

- HashMap 的键值对允许有 Null，但是 ConCurrentHashMap 都不允许
