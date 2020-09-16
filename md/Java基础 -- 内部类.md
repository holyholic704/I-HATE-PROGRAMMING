## 20、内部类

内部类是定义在一个类的内部的类，内部类 **不能定义静态变量和方法**。接口内也可以使用内部类，但没必要

* 更好的实现隐藏：内部类可以被 private 与 protected 修饰
* **内部类拥有外部类的所有元素的访问权限**：可以直接调用所有外部类的成员，包括 private 修饰的变量和方法、非静态变量和方法等
* 可以间接的实现多重继承：**一个类中可以写多个内部类，分别继承不同的类**
* 可以避免实现的接口或继承的类中有同名的方法
* 当想要定义一个回调函数且不想编写大量代码时，使用匿名内部类比较便捷

### 20.1  成员内部类

定义在一个类的内部，可以被权限修饰符和 static 修饰

- 外部类想访问成员内部类的成员，必须 **先创建一个成员内部类的对象**
- 当成员内部类中拥有和外部类同名的成员变量或者方法时，**默认会调用成员内部类的成员**
- 如果要使用外部类的同名成员，可以通过 **`外部类名.this.成员变量/成员方法`** 调用，如果调用外部类的静态变量及方法，可以省略 this

```java
public class OuterClass {
    public int i1 = 1;
    private static int i2 = 2;
    public void outerMethod(){}
    private static void outerStaticMethod(){}

    class InnerClass {
        private int i = 0;
        public void innerMethod() {
            System.out.println(i1);
            System.out.println(i2);
            outerMethod();
            outerStaticMethod();
        }
    }

    public static void main(String[] args) {
        OuterClass outerClass = new OuterClass();
        OuterClass.InnerClass oci = outerClass.new InnerClass();
        innerClass.innerMethod();
    }
}
```

### 20.2  局部内部类

定义在一个方法或者代码块里面的类，可以直接访问 **方法内或者代码块内的成员**。**不能被权限修饰符或 static 修饰**

- 如果要访问外部类的成员变量，则需先创建外部类对象

```java
public class OuterClass {
    public int i1 = 1;
    private static int i2 = 2;

    public void outerMethod() {
    }

    private static void outerStaticMethod() {
    }

    public static void main(String[] args) {
        int i = 1
        class InnerClass {
            private int i = 1;

            public void method() {
                OuterClass outerClass = new OuterClass();
                System.out.println(outerClass.i1);
                System.out.println(OuterClass.i2);
                System.out.println(i);
            }
        }
        int x = new InnerClass().i;
        System.out.println(x);
    }
}
```

### 20.3  匿名内部类

没有名称的类，利用父类的构造函数和自身类体构造成一个类，其他地方不能引用，**没有构造器，不能实例化，只能使用一次**

```java
public class One {
    public void show() {}
}

public class Two {
    public void show() {}
}

public class OuterClass {
    public static void main(String[] args) {
        OuterClass outerClass = new OuterClass();
        One one = new One() {
            private String name = "one";
            @Override
            public void show() {
                System.out.println("hello " + name);
            }
        };
        one.show();
        outerClass.two.show();
    }

    Two two = new Two() {
        private String name = "two";
        @Override
        public void show() {
            System.out.println("hello " + name);
        }
    };
}
```

### 20.4  静态内部类

被 static 修饰的成员内部类，不需要依赖于外部类的，可以看做静态变量，不能直接使用外部类的非静态变量或方法

```java
public class OuterClass {
    private static int num = 9;

    public static void main(String[] args) {
        OuterClass.InnerClass.innerStaticMethod();
        InnerClass innerClass = new OuterClass.InnerClass();
        innerClass.innerMethod();
    }

    static class InnerClass {
        public static void innerStaticMethod(){ System.out.println(num); }
        public void innerMethod(){ System.out.println(num); }
    }
}
```

### 20.5  小伙汁，你见过内部内部内部内部类吗

```java
public class Test {

    class InnerClass {
        class InnerInnerClass {
            class InnerInnerInnerClass {
                class InnerInnerInnerInnerClass {
                    void show() {
                        System.out.println("内部内部内部内部类");
                    }
                }
            }
        }
    }


    public static void main(String[] args) {
        Test test = new Test();
        Test.InnerClass.InnerInnerClass.InnerInnerInnerClass.InnerInnerInnerInnerClass inner = test.new InnerClass().new InnerInnerClass().new InnerInnerInnerClass().new InnerInnerInnerInnerClass();
        inner.show();
    }
}
```