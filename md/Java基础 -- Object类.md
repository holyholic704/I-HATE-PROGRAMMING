## 1、Object 类

所有类的父类，所有类都 **隐式地** 继承 Object，因此省略了 extends Object 关键字

### 1.1  `boolean equals()`

判断两个对象是否相同，Object 中的 equals 方法比较的是两个引用的内存地址。工作中，不应该比较内存地址，应该比较地址里面的内容，所以需要对 equals 方法进行重写

```java
// 1.自反性
x.equals(x)

// 2.对称性
x.equals(y) == y.equals(x)

// 3.传递性
x.equals(y);
y.equals(z);
x.equals(z)
    
// 4.一致性，多次调用equals()方法结果不变

// 5.非空性，对任何不是null的对象equals(null)，结果都为false
x.equals(null)
```

### 1.2  `Class<?> getClass()`

返回此 Object 的运行时的类型。不可重写，一般和 `getName()` 联合使用

```java
Test test = new Test();
System.out.println(test.getClass().getName());
// 输出包名.类名：com.test.Test
```

### 1.3  `String toString()`

返回 Java 对象的字符串表示形式，工作中一般对 toString 方法进行重写。如果直接打印一个引用数据类型的对象，系统会默认调用其 toString 方法。Object 中的 toString 方法返回的是：**`类名@Java对象` 的内存地址经过哈希算法得出的 int 类型值再转换成十六进制**，一般将这个看做 Java 对象在堆中的内存地址

```java
Test test = new Test();
System.out.println(test.toString());
// 输出包名.类名@内存地址：com.test.Test@1b6d3586
```

### 1.4  `void finalize()`

不需要程序员去调用，由 **系统自动调用**。一个对象如果没有引用指向它，就会成为垃圾数据，等待垃圾回收器的回收，垃圾回收器在回收这个对象之前会自动调用该对象的 finalize 方法。程序员只能 **建议** 垃圾回收器回收垃圾 **`System.gc()`**

|          |                             说明                             |
| :------: | :----------------------------------------------------------: |
|  final   | 修饰类，不能被继承；修饰方法，不能被重写；修饰变量，只能赋值一次 |
| finally  | try 语句中的一个语句体，不能单独使用，语句体中的语句一定会执行 |
| finalize | Object 中的一个方法，当没有引用指向某个对象时，对象的垃圾回收器在回收之前调用此方法 |

### 1.5  wait、notify、notifyAll

|               wait               |            notify            |          notifyAll           |
| :------------------------------: | :--------------------------: | :--------------------------: |
| 调用该方法后当前线程进入睡眠状态 | 唤醒在该对象上等待的某个线程 | 唤醒在该对象上等待的所有线程 |

### 1.6  `int hashcode()`

返回散列值，实际上是返回一个 int 整数，由对象的地址转换而来的。这个哈希码的作用是确定该对象在哈希表中的索引位置。同一个对象，如果该对象没有被修改，那么重复调用 `hashCode()` 返回的散列值都是相同的

#### 1.6.1  为什么要有 hashCode

**对底层是散列表的对象有提升性能的功能**，散列表存储的是键值对，特点是：能根据 key 快速的检索出对应的 value。这其中就利用到了哈希码，从而可以快速找到所需要的对象，`hashCode()` 的作用就是 **获取哈希码**

- 例如把对象加入 HashSet 时，HashSet 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与已加入的对象的 hashcode 值作比较，如果没有相符的 hashcode，HashSet 会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，会再调用 equals 方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样就 **大大减少了 equals 的次数，提高了执行速度**

#### 1.6.2  hashCode() 与 equals()

* 两个对象相等，则 hashcode 一定也是相同的
* 两个对象相等，对两个对象分别调用 equals 方法都返回 true
* 两个对象有相同的 hashcode，也不一定是相等的
* equals 方法被重写，则 hashCode 方法也必须被重写
* `hashCode()` 的默认行为是对堆上的对象产生独特值。如果没有重写 `hashCode()`，则该 class 的两个对象无论如何都不会相等，即使这两个对象指向相同的数据

### 1.7  Object Clone()

`clone()` 是 Object 的 protected 方法，一个类不显式去重写 `clone()`，其它类就不能直接去调用该类实例的 clone 方法。clone 方法用于对象的克隆，一般想要克隆出的对象是 **独立** 的，即与原有的对象是分开的

* 浅拷贝：拷贝对象和原始对象的引用类型 **引用同一个对象**
  * 基本数据类型进行值传递，引用数据类型进行引用传递
* 深拷贝：拷贝对象和原始对象的引用类型 **引用不同对象**
  * 基本数据类型进行值传递，引用数据类型，创建一个新的对象，并复制其内容

深拷贝指的是该对象的成员变量（如果是可变引用）都应该克隆一份，浅拷贝指的是成员变量没有被克隆一份

#### 1.7.1  clone 用法

* 克隆的对象要 **实现 Cloneable 接口**
  * `clone()` 不是 Cloneable 接口的方法，而是 Object 的一个 protected 方法。Cloneable 接口只是规定，如果一个类没有实现 Cloneable 接口又调用了 clone 方法，会抛出 `CloneNotSupportedException`
* **重写 clone 方法**，最好使用 public 修饰

```java
// 浅拷贝：只拷贝了Person对象，而name没有拷贝
public class Person implements Cloneable {
    // 可变的成员变量
    private Info info;

    @Override
    public Object clone() throws CloneNotSupportedException {
        Person person = (Person) super.clone();
        return person;
    }

    public static void main(String[] args) throws CloneNotSupportedException {
        Person person = new Person();
        person.info = new Info();
        person.info.name = "安倍晋三";

        Person personClone = (Person) person.clone();
        // 修改info的name
        person.info.name = "特朗普";
        // 原对象和clone对象都输出"特朗普"
        System.out.println(person.info.name);
        System.out.println(personClone.info.name);
    }
}

class Info {
    String name;
}
```

```java
// 深拷贝：不仅拷贝了Person对象，也拷贝了Info成员变量
public class Person implements Cloneable {
    // 可变的成员变量
    private Info info;

    @Override
    public Object clone() throws CloneNotSupportedException {
        Person person = (Person) super.clone();
        // 拷贝Info成员变量
        person.info = (Info) info.clone();
        return person;
    }

    public static void main(String[] args) throws CloneNotSupportedException {
        Person person = new Person();
        person.info = new Info();
        person.info.name = "安倍晋三";

        Person personClone = (Person) person.clone();
        // 修改info的name
        person.info.name = "特朗普";
        // 原对象输出"特朗普"，clone对象输出"安倍晋三"
        System.out.println(person.info.name);
        System.out.println(personClone.info.name);
    }
}

class Info implements Cloneable {
    String name;

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

#### 1.7.2  clone 方法的保护机制

Object 中 `clone()` 是被 protected 修饰的，这样就可以保证只有在本类里面才能进行克隆对象。一般来说，protected 修饰的类或属性，对于自己、本包和其子类可见。所以与 Person 类一个包的 Test 类，Person 类的子类 Someone 类应该是可以调用 clone 方法创建 person 对象的拷贝，然而事实上会报错

```java
// Person没有实现Cloneable接口，没有重写clone方法
public class Person{}

public class Test{
    public static void main(String[] args){
        Person person = new Person();
        person.clone();// 'clone()' has protected access in 'java.lang.Object'
    }
}

public class Someone extends Person{
    public static void main(String[] args){
        Person person = new Person();
        person.clone();// 'clone()' has protected access in 'java.lang.Object'
    }
}
```

- 事实上，对于 protected 的成员或方法，**要分子类和父类是否在同一个包中**。与父类不在同一个包中的子类，只能访问自身从父类继承而来的受保护成员，而不能访问父类实例本身的受保护成员。上面的代码的问题在于 Person 与 Object **不是在同一个包下** 的，而 Person 直接访问了 Object 的 clone 方法，显然是不行的

*更多：[Java:由 Object.clone()而引出的 protected权限问题](https://blog.csdn.net/wangyanguiyiyang/article/details/49800493)、[【JDK源码】java.lang.Object](https://blog.csdn.net/qq_42370146/article/details/96858172)*

#### 1.7.3  为什么要用 clone()

在实际编程过程中，常常要遇到这种情况：有一个对象 A，在某一时刻 A 中已经包含了一些有效值，此时可能会需要一个和 A 完全相同新对象 B，并且此后对 B 任何改动都不会影响到 A 中的值，即 A 与 B 是两个独立的对象，但 B 的初始值是由 A 对象确定的。在 Java 语言中，用简单的赋值语句是不能满足这种需求的。要满足这种需求虽然有很多途径，但实现 clone 方法是其中最简单，也是最高效的手段

- 使用 clone 方法来拷贝一个对象即复杂又有风险，它会抛出异常，并且还需要类型转换。最好不要去使用 `clone()`，可以使用拷贝构造函数或者拷贝工厂来拷贝一个对象

#### 1.7.4  clone 与 copy 的区别

```java
// 假设有一个Person对象
Person one = new Person("one",18);
// copy了一下引用，one和two都指向内存中同一个object，这样one或two的一个操作都可能影响到对方
Person two = one;
// 使用clone，就可以得到一个one的拷贝，这样one和two之间互不影响
Person two = (Person)one.clone();
```

#### 1.7.5  new 一个对象的过程和 clone 一个对象的过程区别

* new 操作符的本意是分配内存。程序执行到 new 操作符时，**首先去看 new 操作符后面的类型**，因为知道了类型，才能知道要分配多大的内存空间。分配完内存之后，再调用构造函数，填充对象的各个域，即对象的初始化，构造方法返回后，一个对象创建完毕，可以把他的引用（地址）发布到外部，在外部就可以使用这个引用操纵这个对象

* clone 在第一步是和 new 相似的，都是分配内存，调用 clone 方法时，分配的内存和原对象相同，然后再使用原对象中对应的各个域，填充新对象的域，填充完成之后，clone 方法返回，一个新的相同的对象被创建，同样可以把这个新对象的引用发布到外部

*更多：[详解 Java中的 clone方法](https://blog.csdn.net/zhangjg_blog/article/details/18369201#0-qzone-1-28144-d020d2d2a4e8d1a374a433f596ad1440)、[Object对象你真理解了吗？](https://segmentfault.com/a/1190000014710646)*