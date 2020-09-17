## 1、Java 8

2014 年 3 月 18 日，Oracle 发布了 Java 8，为目前唯二的 LTS（长期支持）版本之一，另一个是 Java 11，目前最新的版本为 Java 15

- Java 8 为目前使用最多的发行版本

![20200917223048](../md.assets/Java/20200917223048.png)

*更多：[Java版本历史](https://zh.wikipedia.org/wiki/Java%E7%89%88%E6%9C%AC%E6%AD%B7%E5%8F%B2)、[Jetbrains Java编程](https://www.jetbrains.com/zh-cn/lp/devecosystem-2020/java/)*

## 2、接口默认实现与静态方法

Java 8 之后可以为接口方法提供一个默认实现，**用 default 修饰符来标记方法**，这样就可以只关心需要的方法，而不用去实现不需要的方法

- 在 JVM 中，接口的默认实现是非常高效的，并且通过字节码指令为方法调用提供了支持

```java
interface Animal {
    // 默认实现
    default void cao() {
        System.out.println("animal 艹");
    }

    void ri();
}

class Human implements Animal {
    // 默认方法可以不进行重写，非默认方法必须重写
    @Override
    public void ri() {
        System.out.println("human 日");
    }
}

public class Test {
    public static void main(String[] args) {
        Human human = new Human();
        // 不需要重写，直接调用默认方法
        human.cao();
        human.ri();
    }
}
```

### 2.1  默认方法冲突

如果先在一个接口中将一个方法定义为默认方法，然后又在超类或另一个接口中定义了同样的方法，就会产生冲突

- 超类优先：如果超类提供了一个具体方法，同名而且有相同参数类型的默认方法会被忽略
- 接口冲突：如果一个超接口提供了一个默认方法，另一个接口提供了一个同名而且参数类型（不论是否是默认参数）相同的方法，必须覆盖这个方法来解决冲突

```java
// 超类优先
interface Animal {
    default void cao() {
        System.out.println("animal 艹");
    }
}

class Mammal {
   public void cao() {
        System.out.println("mammal 艹");
    }
}

class Human extends Mammal implements Animal {}

public class Test {
    public static void main(String[] args) {
        Human human = new Human();
        human.cao();	// mammal 艹
    }
}
```

```java
// 接口冲突
interface Animal {
    default void cao() {
        System.out.println("animal 艹");
    }
}

interface Mammal {
    default void cao() {
        System.out.println("mammal 艹");
    }
}

class Human implements Mammal, Animal {
    // 必须重写默认方法
    @Override
    public void cao() {
        System.out.println("human 艹");
    }
}

public class Test {
    public static void main(String[] args) {
        Human human = new Human();
        human.cao();
    }
}
```

### 2.2  接口中的静态方法

```java
public class Test {
    public static void main(String[] args) {
        Animal.cao();
    }
}

interface Animal {
    // 必须要有方法体
    static void cao() {
        System.out.println("animal 艹");
    }
}
```

## 3、函数式接口

并不是所有的接口都可以使用 Lambda 表达式来实现，只有函数式接口才能写成 Lambda 表达式

- 函数式接口：**要求接口中定义的必须要实现的抽象方法只能有一个**

```java
// 该注解修饰函数式接口，即意味着接口中的抽象方法只能有一个，否则编译器会报错
// default方法和静态方法不会造成任何影响
interface Animal {
    void cao();
    
    default void ri() {
        System.out.println("日");
    }

    static void shit() {
        System.out.println("shit");
    }
}
```

## 4、Lambda 表达式

Lambda 表达式是 Java 8 添加的一个新特性，可以认为 Lambda 是一个匿名函数（相似于匿名内部类），作用是返回一个实现了接口的对象

- Lambda 表达式是一个匿名函数，主要关注方法的参数列表和方法体
  - `()`：描述参数列表
  - `{}`：描述方法体
  - `->`：Lambda 运算符，读作 `goes to`

```java
// 无返回值的无参接口
@FunctionalInterface
public interface LambdaNoneReturnNoneParameter {
    void test();
}

// 无返回值的单参接口
@FunctionalInterface
public interface LambdaNoneReturnSingleParameter {
    void test(int i);
}

// 无返回值的多参接口
@FunctionalInterface
public interface LambdaNoneReturnMultipleParameter {
    void test(int a, int b);
}

// 有返回值的无参接口
@FunctionalInterface
public interface LambdaSingleReturnNoneParameter {
    int test();
}

// 有返回值的单参接口
@FunctionalInterface
public interface LambdaSingleReturnSingleParameter {
    int test(int i);
}

// 有返回值的多参接口
@FunctionalInterface
public interface LambdaSingleReturnMultipleParameter {
    int test(int a, int b);
}
```

```java
// 无参无返回值
LambdaNoneReturnNoneParameter l1 = () -> {
    System.out.println("无参无返回值");
};
l1.test();

// 单参无返回值
LambdaNoneReturnSingleParameter l2 = (int i) -> {
    System.out.println("\n单参无返回值传入参数：" + i);
};
l2.test(999);

// 多参无返回值
LambdaNoneReturnMutipleParameter l3 = (int a, int b) -> {
    System.out.println("\n多参无返回值传入参数：" + a + "，" + b);
};
l3.test(333, 555);

// 无参有返回值
LambdaSingleReturnNoneParameter l4 = () -> {
    System.out.println("\n无参有返回值");
    return 100;
};
int l4Result = l4.test();
System.out.println("无参有返回值结果：" + l4Result);

// 单参有返回值
LambdaSingleReturnSingleParameter l5 = (int i) -> {
    System.out.println("\n单参有返回值传入参数：" + i);
    return a;
};
int l5Result = l5.test(200);
System.out.println("单参有返回值结果：" + l5Result);

// 多参有返回值
LambdaSingleReturnMutipleParameter l6 = (int a, int b) -> {
    System.out.println("\n多参有返回值传入参数：" + a + "，" + b);
    return a + b;
};
int l6Result = l6.test(100, 200);
System.out.println("多参有返回值结果：" + l6Result);
```

### Lambda 表达式语法精简

- 参数类型的省略：由于在接口中已经定义了参数，所以在 Lambda 表达式中参数的类型可以省略
  - 如果省略参数的类型，则所有的参数的类型都要省略

```java
LambdaNoneReturnSingleParameter l2 = (i) -> {
    System.out.println("\n单参无返回值传入参数：" + i);
};
```

- 参数小括号的省略：如果参数列表中，**参数的个数有且只有一个**，那么小括号可以省略，且仍然可以省略参数的类型

```java
LambdaNoneReturnSingleParameter l2 = i -> {
    System.out.println("\n单参无返回值传入参数：" + i);
};
```

- 方法体大括号的省略：如果方法体只有一条语句，那么此时大括号可以省略

```java
LambdaNoneReturnSingleParameter l2 = i -> 
    System.out.println("\n单参无返回值传入参数：" + i);
```

- return 的省略：如果方法体 **只有一条语句，且是返回语句**，可以省略 return，**且必须要省略大括号**

```java
LambdaSingleReturnSingleParameter l4 = () -> a;
```

### 方法引用

​		如果新建了许多接口的实现对象，其方法都是相同的，但是如果方法需要修改，那么修改的复杂度就随着对象数量的上升而上升，此时可以将一个 Lambda 表达式的实现指向一个已经写好的方法

- **返回值的类型和参数列表要与接口中定义的一致**

```java
public class Test {
    public static void main(String[] args) {
        // 每次使用都实现相同的方法，则非常冗余
        LambdaSingleReturnSingleParameter l1 = i -> i * 2;
        LambdaSingleReturnSingleParameter l2 = i -> i * 2;
		// 一般的方法调用：将Lambda表达式的实现指向change方法
        LambdaSingleReturnSingleParameter l3 = i -> change(i);
		// 方法引用：引用方法隶属者的change方法
        // 方法隶属者：静态方法隶属者为类，非静态方法的隶属者是对象
        LambdaSingleReturnSingleParameter l4 = Test::change;
        LambdaSingleReturnSingleParameter l5 = new Test()::change2;
    }

    private static int change(int i) {return i * 2;}
    
    private int change2(int i) {return i * 2;}
}
```

#### 构造方法的引用

```java
public class Person {
    public String name;
    public int age;

    public Person() {
        System.out.println("无参构造方法");
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
        System.out.println("有参构造方法");
    }
}

public class SimpleTest {
    public static void main(String[] args) {
        PersonCreator creator = () -> new Person();
        // 构造方法的引用
        PersonCreator creator = Person::new;
        // 调用getPerson方法得到person对象
        Person person = creator.getPerson();
        // 引用有参构造方法
        PersonCreator2 creator2 = Person::new;
        Person person = creator.getPerson("习近平", 18);
    }
}

interface PersonCreator{
    Person getPerson();
}

interface PersonCreator2{
    Person getPerson(String name, int age);
}
```

*更多：[Java-Lambda表达式和“方法引用”的对比和详解](https://blog.csdn.net/li_xunhuan/article/details/97930596)*