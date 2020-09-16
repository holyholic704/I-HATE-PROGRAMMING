## 1、单一职责原则（Single-Responsibility Principle）

**一个类应该只有一个能引起变化的原因**，一个类的功能要单一，只做与它相关的事情

- 职责过多，可能引起它变化的原因就越多，这将导致职责依赖，相互之间就产生影响，从而大大损伤其内聚性和耦合度

### 1.1  优点

- 类的复杂性降低，实现什么职责都有清晰明确的定义
- 可读性提高
- 可维护性提高
- 变更引起的风险降低，变更是必不可少的，如果接口的单一职责做得好，一个接口修改只对相应的实现类有影响，对其他的接口无影响，这对系统的扩展性、维护性都有非常大的帮助

### 1.2  示例

```java
interface Human {
    void cry();
    void laugh();
    
    void punch();
    void kick();
}
```

- cry 与 laugh 属于情绪一类的，punch 与 kick 属于动作一类，虽然都同属于 Human 接口，但两个分类之间关联并不大，还导致 Human 接口承担的过多的职责

```java
interface Mood{
    void cry();
    void laugh();
}

interface Act{
    void punch();
    void kick();
}
```

## 2、开放封闭原则（Open-Closed principle）

**软件实体（类，模块，函数等）应该是可扩展的，而不可修改的**。实现该原则的核心思想就是对抽象编程，而不对具体编程，因为抽象相对稳定

- 对扩展开放：有新的需求或变化时，可以对现有代码进行扩展，以适应新的情况
  - 通过面向对象的继承和多态，使用重写来改变固有行为，实现新的拓展方法，所以扩展是开放的
- 对修改封闭：类一旦设计完成，就可以独立完成其工作，而不要对其进行任何的修改
  - 让类依赖于固定的抽象，所以修改就是封闭的

### 2.1  优点

- 有利于进行单元测试
- 复用性提高
- 代码可读性高，可维护性提高
- 面向对象开发的要求
- 保持软件内部的封装体系稳定，不被需求的变化影响

### 2.2  示例

```java
interface Phone {
    void call();
}

class Nokia implements Phone {
    @Override
    public void call() {}
}
```

- 早期的诺基亚只有打电话的功能，随着科技发展，又多了拍照、上网等功能

```java
interface Phone {
    void call();
}

class Nokia implements Phone {
    @Override
    public void call() {}
}

class NokiaWP8 extends Nokia {
    @Override
    public void call() {}

    public void photograph() {}
    
    public void surfing() {}
}
```

## 3、里氏替换原则（Liskov-Substitution Principle）

**子类应当可以替换父类并出现在父类能够出现的任何地方**。这一思想体现为对继承机制的约束规范，只有子类能够替换父类时，才能保证系统在运行期内识别子类，这是保证继承复用的基础

- 这一约束反过来则是不成立的，子类可以替换基类，但是基类不一定能替换子类
- 子类可以扩展父类的功能，可以实现父类的抽象的方法，但不能覆盖改变父类的原有功能

该原则主要着眼于对抽象和多态建立在继承的基础上，因此只有遵循了里氏替换原则，才能保证继承复用是可靠的。实现的方法是面向接口编程：将公共部分抽象为接口或抽象类，在子类中通过覆写父类的方法实现新的方式支持同样的职责

- 里氏替换原则是关于继承机制的设计原则，违反了该原则就必然导致违反开放封闭原则。 里氏替换原则能够保证系统具有良好的拓展性，同时实现基于多态的抽象机制，能够减少代码冗余，避免运行期的类型判断

### 3.1  优点

- 代码共享，减少创建类的工作量，每个子类都拥有父类的方法和属性
- 提高代码的重用性
- 提高代码的可扩展性
- 提高项目的开放性
- 子类可以形似父类，但是又异于父类

### 3.2  示例

```java
public class Demo {
    public static void main(String[] args) {
        Animal animal = new Animal();
        animal.say();	// 嗷~
        Animal human = new Human();
        human.say();	// 艹
    }
}

class Animal{
    void say() {
        System.out.println("嗷~");
    }
}

class Human extends Animal {
    @Override
    void say() {
        System.out.println("艹");
    }
}
```

- 这个示例违反了里氏置换原则，子类方法覆盖了父类的方法

```java
public class Demo {
    public static void main(String[] args) {
        Animal animal = new Animal();
        animal.say();	// 嗷~
        Animal human = new Human();
        human.say();	// 嗷~
    }
}

class Animal{
    void say() {
        System.out.println("嗷~");
    }
}

class Human extends Animal {
    @Override
    void say() {
        super.say();
    }
}
```

## 4、依赖倒置原则（Dependecy-Inversion Principle）

**高层模块不依赖于底层模块，二者都同依赖于抽象；抽象不依赖于具体，具体依赖于抽象**

- 低层模块：不可分割的原子逻辑，可能会根据业务逻辑经常变化
- 高层模块：低层模块的再组合，对低层模块的抽象

依赖一定会存在于类与类、模块与模块之间。当两个模块之间存在紧密的耦合关系时，最好的方法就是分离接口和实现：在依赖之间定义一个抽象的接口使得高层模块调用接口，而底层模块实现接口的定义，以此来有效控制耦合关系，达到依赖于抽象的设计目标

- 抽象的稳定性决定了系统的稳定性，因为抽象是不变的，依赖于抽象是面向对象设计的精髓，也是依赖倒置原则的核心

### 4.1  优点

- 通过依赖于接口，隔离了具体实现类
- 低一层的变动并不会导致高一层的变动
- 提高了代码的容错性、扩展性和易于维护

###  4.2  示例

```java
public class Demo {
    public static void main(String[] args) {
        User user = new User();
        Phone phone = new Iphone();
        user.usePhone(phone);
    }
}

interface Phone {
    void call();
}

interface Human {
    void usePhone(Phone phone);
}

class Iphone implements Phone {
    @Override
    public void call() {
        System.out.println("calling...");
    }
}

class User extends Iphone implements Human {
    @Override
    public void usePhone(Phone phone) {
        phone.call();
    }
}
```

## 5、接口隔离原则（Interface-Segregation Principle）

**客户端不应该依赖它不需要的接口，一个类对另一个类的依赖应该建立在最小的接口上**。接口应该是内聚的，应该避免大接口。接口有效地将细节和抽象隔离，强调了接口的单一性

- 单一职责原则要求是类和接口的职责单一，注重的职责，接口隔离原则是业务逻辑上的划分，要求接口的方法尽量少

大接口存在明显的弊端，必须得完全实现接口的所有方法、属性等；而某些时候，并不需要所有的接口定义，而且对大接口的修改将导致一连串的程序需要修改。将大接口分解为多个特点的定制化方法，使得客户端仅仅依赖于它们的实际调用的方法

分离的手段主要有两种

- 委托分离，通过增加一个新的类型来委托客户的请求，隔离客户和接口的直接依赖，但是会增加系统的开销
- **多重继承分离**，通过接口多继承来实现客户的需求，推荐

### 5.1  优点

- 避免接口污染
- 灵活性提高
- 提供定制服务
- 实现高内聚

### 5.2  示例

```java
interface Behavior {
    void eat();
    void fly();
    void speak();
}

class Bird implements Behavior {
    @Override
    public void eat() {}
    
    @Override
    public void fly() {}

    @Override
    public void speak() {}
}

class Human implements Behavior {
    @Override
    public void eat() {}
    
    @Override
    public void fly() {}

    @Override
    public void speak() {}
}
```

- 人类不需要 fly 方法，鸟类也不需要 speak 方法，但有一个共有的 eat 方法，可以将行为接口拆分为共有的行为接口、人类特定行为接口、鸟类特定行为接口

```java
interface Beahvior {
    void eat();
}

interface Behavior1 extends Beahvior {
    void fly();
}

interface Behavior2 extends Beahvior {
    void speak();
}

class Bird implements Behavior1 {
    @Override
    public void fly() {}

    @Override
    public void eat() {}
}

class Human implements Behavior2 {
    @Override
    public void eat() {}

    @Override
    public void speak() {}
}
```

## 6、最少知道原则 / 迪米特法则（Least Knowledge Principle）

**一个对象应当对其他对象有尽可能少的了解**，每个类尽量减少对其他类的依赖

该原则不希望类之间建立直接的接触，如果真的有需要建立联系，也希望能通过中间类来转达。因此，迪米特原则有可能会造成系统中存在大量的中介类，这些类都是为了传递类之间的相互调用关系，在一定程度上增加了系统的复杂度

### 6.1  优点

- 减少对象之间的耦合性

### 6.2  示例

```java
public class Demo {
    public static void main(String[] args) {
        Human human = new Human("Trump");
        Phone phone = new Phone();
        phone.call(new User(human));
    }
}

class Phone {
    void call(User user) {
        user.callSomeone();
    }
}

class Human {
    String name;

    public Human(String name) {
        this.name = name;
    }
}

class User {
    private Human human;

    public User(Human human) {
        this.human = human;
    }

    void callSomeone() {
        System.out.println("call: " + human.name);
    }
}
```

## 7 、组合 / 聚合复用原则（Composite / Aggregate Reuse Principle）

**在一个新的对象里面使用一些已有的对象，使之成为新对象的一部分**，新对象通过向这些对象的委派达到复用已有功能的目的。尽量的使用合成和聚合，而不是继承关系达到复用的目的

- 组合（Composition）表示一种强的拥有关系，体现了严格的部分和整体关系，部分和整体的生命周期一样

- 聚合（Aggregation）表示一种弱的拥有关系，体现的是 A 对象可以包含 B 对象但 B 对象不是 A 对象的一部分

### 7.1  优点

- 新的实现较为容易，因为超类的大部分功能可通过继承关系自动进入子类
- 修改或扩展继承而来的实现较为容易

### 7.2  组合与继承

|                          组合关系                          |                           继承关系                           |
| :--------------------------------------------------------: | :----------------------------------------------------------: |
|     不破坏封装，整体类与局部类之间松耦合，彼此相对独立     | 破坏封装，子类与父类之间紧密耦合，子类依赖于父类的实现，子类缺乏独立性 |
|                     具有较好的可扩展性                     |        支持扩展，但是往往以增加系统结构的复杂度为代价        |
| 支持动态组合。在运行时，整体对象可以选择不同类型的局部对象 |       不支持动态继承。在运行时，子类无法选择不同的父类       |
| 整体类可以对局部类进行包装，封装局部类的接口，提供新的接口 |                    子类不能改变父类的接口                    |
|            整体类不能自动获得和局部类同样的接口            |                   子类能自动继承父类的接口                   |
|        创建整体类的对象时，需要创建所有局部类的对象        |             创建子类的对象时，无须创建父类的对象             |

- 建议在同样可行的情况下，优先使用组合而不是继承。因为组合更安全，更简单，更灵活，更高效
  - 只有当子类真正是超类的子类型时，才适合用继承

### 7.3  示例

- 组合：Human 与 Air 是强拥有关系，人类不能离开空气

```java
class Human {
}

class Air {
    private Human human;

    public Air() {
        human = new Human();
    }
}
```

- 聚合：Phone 与 Human 是弱拥有关系，人类可以不用手机

```java
class Phone {
}

class Human {
    private Phone phone;

    public Human() {
        phone = new Phone();
    }
}
```