## 异常

* Java 号称安全性，异常考虑到可能出现的错误，使用异常处理后把错误处理和真正的工作分开来
* 代码更易组织，更清晰，复杂的工作任务更容易实现
* 更安全，不至于由于一些小的疏忽而使程序意外崩溃了
* 在 Java 中，所有的异常都有一个共同的祖先 java.lang 包中的 **Throwable 类**
  * Throwable 有两个重要的子类：**Exception**、**Error**

### Error 与 Exception

- Error：描述了 Java 运行时系统的内部错误和资源耗尽错误。应用程序不应该抛出这种类型的对象。如果出现了这样的内部错误 ， 除了通告给用户，并尽力使程序安全地终止之外，再也无能为力了。这种情况很少出现

- Exception：程序本身可以处理的异常。又分为两个分支：一个分支派生于 RuntimeException，另一个分支包含其他异常。划分的依据是由程序错误导致的异常属于 RuntimeException；而程序本身没有问题，但由于像 I/O 错误这类问题导致的异常属于其他异常

### 可查异常与不可查异常

- 可查异常（checked exceptions）：编译器要求 **必须处置的异常**，即当程序中可能出现这类异常，要么用 try-catch 语句捕获它，要么用 throws 子句声明抛出它，否则编译不会通过。在正确的程序在运行中，很容易出现的、情理可容的异常状况。可查异常虽然是异常状况，但在一定程度上它的发生是可以预计的，而且一旦发生这种异常状况，就必须采取某种方式进行处理
  - 除了 RuntimeException 及其子类以外，其他的都属于可查异常

- 不可查的异常（unchecked exceptions）：编译器 **不要求强制处置的异常**，一般是由程序逻辑错误引起的，在程序中可以选择捕获处理，也可以不处理
  - 包括 RuntimeException 与其子类 和 Error

### 运行时异常与非运行时异常

- 运行时异常：RuntimeException 类及其子类都是不可查异常，Java 编译器不会检查它，当程序中可能出现这类异常，即使没有用 try-catch 语句捕获它，也没有用 throws 子句声明抛出它，也可以编译通过。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生

|              异常              |       说明       |
| :----------------------------: | :--------------: |
|      ArithmeticException       |     算数异常     |
| ArrayIndexOutOfBoundsException | 数组下标越界异常 |
|       ClassCastException       |   类型转换异常   |
|     ClassNotFoundException     |   找不到类异常   |
|    IllegalArgumentException    |   非法参数异常   |
|     IllegalStateException      |   非法状态异常   |
|   IndexOutOfBoundsException    |   下标越界异常   |
|      NullPointerException      |    空指针异常    |
|     NumberFormatException      |   数字格式异常   |

- 非运行时异常 （编译异常）：除 RuntimeException 类及其子类以外的异常。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过

### Throwable 类常用方法

- `String getMessage()`：返回异常发生时的详细信息
- `String toString()`：返回异常发生时的简要描述
- `String getLocalizedMessage()`：返回异常对象的本地化信息。使用 Throwable 的子类覆盖这个方法，可以声称本地化信息
  - 如果子类没有覆盖该方法，则该方法返回的信息与 `getMessage()` 返回的结果相同
- `void printStackTrace()`：在控制台上打印 Throwable 对象封装的异常信息

```java
try {
    System.out.println(1 / 0);
} catch (Exception e) {
    // / by zero
    System.out.println(e.getMessage());
    // java.lang.ArithmeticException: / by zero
    System.out.println(e.toString());
    // / by zero
    System.out.println(e.getLocalizedMessage());
    // java.lang.ArithmeticException: / by zero
    // at com.test.Test.main(Test.java:10)
    e.printStackTrace();
}
```

### 异常处理

在 Java 应用程序中，异常处理机制为：**抛出异常，捕获异常**

- 抛出异常：任何 Java 代码都可以抛出异常，当一个方法出现错误引发异常时，方法创建异常对象并交付运行时系统，异常对象中包含了异常类型和异常出现时的程序状态等异常信息，运行时系统负责寻找处置异常的代码并执行

- 捕获异常：一个方法所能捕捉的异常，一定是 Java 代码在某处所抛出的异常，即异常总是 **先被抛出，后被捕捉**。在方法抛出异常之后，运行时系统将转为寻找合适的异常处理器。潜在的异常处理器是异常发生时依次存留在调用栈中的方法的集合
  - 当异常处理器所能处理的异常类型与方法抛出的异常类型相符时，即为合适的异常处理器。运行时系统从发生异常的方法开始，依次回查调用栈中的方法，直至找到含有合适异常处理器的方法并执行。当运行时系统遍历调用栈而未找到合适的异常处理器，则运行时系统终止，同时，意味着 Java 程序的终止

### try-catch 与 finally

* try 块：用于捕获异常。其后可接 0 个或多个 catch 块，如果没有 catch 块，则必须跟一个 finally 块
* catch 块：用于处理 try 捕获到的异常
* finally 块：无论是否捕获或处理异常，finally 块里的语句都会被执行，除非以下四种特殊情况
  * 在 finally 语句块中发生了异常
  * 在前面的代码中用了 `System.exit()` 退出程序
  * 程序所在的线程死亡
  * 关闭 CPU

try 块里面的内容称为监控区域。Java 方法在运行过程中出现异常，则 **创建异常对象**。将异常抛出监控区域之外，由运行时系统寻找匹配的 catch 子句以捕获异常。若有匹配的 catch 子句，则运行其异常处理代码，try-catch 语句结束。如果抛出的异常对象属于 catch 子句的异常类或子类 ，则认为生成的异常对象与 catch 块捕获的异常类型相匹配

```java
try {
    System.out.println();
} catch (NullPointerException e1) {
    e1.printStackTrace();
} catch (ClassCastException e2) {
    e2.printStackTrace();
}
// Java7之后，同一个catch子句中可以捕获多个异常类型
try {
    System.out.println();
} catch (NullPointerException | ClassCastException e) {
    e.printStackTrace();
}

// 没有catch块，则必须写上finally块
try {
    System.out.println();
} finally {
    System.out.println("nothing");
}

try {
    System.out.println();
} catch (NullPointerException | ClassCastException e) {
    e.printStackTrace();
} finally {
    System.out.println("nothing");
}
```

#### finally 和 return 的执行顺序

* try 和 catch 中有 return
  * finally 中的代码总会执行，而且 finally 语句在 return 语句执行之后，在 return 返回之前执行


```java
// 输出try、finally、5（1+3+1）
// 如果出现异常，视出现位置而定，当前出错位置输出try、catch、finally、41（1+30+10）
public class Test {
    public static void main(String[] args) {
        Test test = new Test();
        // 传入1
        System.out.println(test.testReturn(1));
    }

    public int testReturn(int i) {
        try {
            System.out.println("try");
            // System.out.println(1 / 0);
            i += 3;
            return i + 1;
        } catch (Exception e) {
            System.out.println("catch");
            i += 30;
            return i + 10;
        } finally {
            System.out.println("finally");
            i += 300;
        }
    }
}
```

* finally 中有 return
  * 会直接返回，不会再去返回 try 或者 catch 中的返回值


```java
// 没有出现异常，则输出try、finally、404
// 如果出现异常，视出现位置而定，当前出错位置输出try、catch、finally、431（1+30+300+100）
public int testReturn(int i) {
    try {
        System.out.println("try");
        // System.out.println(1 / 0);
        i += 3;
        return i + 1;
    } catch (Exception e) {
        System.out.println("catch");
        i += 30;
        return i + 10;
    } finally {
        System.out.println("finally");
        i += 300;
        return i + 100;
    }
}
```

* finally 中对于返回变量做的改变是否会影响最终的返回结果
  * 如果 try 和 catch 的 return 是一个变量时且函数是从其中一个返回时，后面 finally 中语句即使有对返回的变量进行赋值的操作时，也不会影响返回的值

*更多：[finally 和 return的执行顺序](https://blog.csdn.net/jdfk423/article/details/80406297)*

### throw 与 throws

- throw：用在方法体中，用来抛出一个 Throwable 类型的异常。 程序会在 throw 语句后立即终止，它后面的语句不执行，然后在包含它的所有 try 块中，从里向外寻找含有与其匹配的 catch 子句的 try 块
  - 如果抛出了检查异常 ，还应该在 **方法头部声明方法可能抛出的异常类型**，该方法的调用者也必须检查处理抛出的异常。如果所有方法都层层向上抛获取的异常，最终 JVM 会进行处理，打印异常消息和堆栈信息

```java
public class Test {
    public static void main(String[] args) {
        boolean flag = true;
        System.out.println("我会执行");
        if (flag) {
            throw new RuntimeException("test");
        }
        System.out.println("我不会被执行");
    }
}
```

- throws：如果一个方法可能会出现异常，但没有能力处理这种异常，可以在方法声明处用 throws 子句来声明抛出异常。throws 语句用在方法定义时声明该方法要抛出的异常类型，多个异常可使用逗号分割。当方法抛出异常列表的异常时，方法将不对这些类型及其子类类型的异常作处理，而抛向调用该方法的方法或对象，由他去处理。还可以层层向上抛获取的异常，最终必须要有能够处理该异常的调用者

```java
public class Test {
    public static void main(String[] args) throws NullPointerException {
        Test test = null;
        System.out.println(test.toString());
    }
}
```

|                  throw                  |                  throws                   |
| :-------------------------------------: | :---------------------------------------: |
|   用在方法体内，跟的是 **异常对象名**   |   用在方法声明后面，跟的是 **异常类名**   |
|         只能抛出一个异常对象名          |      可以跟多个异常类名，用逗号隔开       |
| 表示抛出异常，由方法体内的 **语句处理** | 表示抛出异常，由该方法的 **调用者来处理** |

### 自定义异常类

```java
// 继承Exception类
public class MyException extends Exception{
    // 创建构造方法，调用父类构造方法
    public MyException(String str){
        super(str);
    }
}
```

### 异常链

将异常发生的原因一个传一个串起来，即把底层的异常信息传给上层，这样逐层抛出。当程序捕获到了一个底层异常，在处理部分选择了继续抛出一个更高级别的新异常给此方法的调用者。 这样异常的原因就会逐层传递。这样，位于高层的异常递归调用 getCause 方法，就可以遍历各层的异常原因

- 异常链的实际应用很少，发生异常时候逐层上抛不是个好注意，上层拿到这些异常又能怎么如何处理，而且异常逐层上抛会消耗大量资源，因为要保存一个完整的异常链信息

*更多：[Java异常](https://blog.csdn.net/qq_35101189/article/details/52938657)、[深入理解 Java中异常体系](https://blog.csdn.net/zhanaolu4821/article/details/81012382)*