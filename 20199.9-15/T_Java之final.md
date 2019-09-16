参考《Java 编程思想》之 final 关键字

# final 关键字

由于语境（应用环境）不同，final 关键字的含义可能会稍微产生一些差异。但它一般就是“这个东西不能改变”。之所以禁止改变，可能是考虑到两方面的因素：设计或效率。以下分三种应用场合：数据、方法以及类

## final 数据

许多程序设计语言都有自己的方法告诉编译器某个数据是“常数”：

+ **编译器**常数，它永远不会改变
+ 在**运行期**初始化的一个值，我们不希望它发生变化

```java
public class FInalData {

    final int i4 = (int) (Math.random() * 20);
    static final int i5 = (int) (Math.random() * 20);

    public void print(String id) {
        System.out.println(id + ":" + "i4=" + i4 + ",i5=" + i5);
    }

    public static void main(String[] args) {
        FInalData fd1 = new FInalData();
        fd1.print("fd1");
        System.out.println("Creating new FinalData");

        FInalData fd2 = new FInalData();
        fd1.print("fd1");
        fd2.print("fd2");
    }
    
}
// 运行结果：
fd1:i4=1,i5=3
Creating new FinalData
fd1:i4=1,i5=3
fd2:i4=2,i5=3
/*
* i4 只用 final 修饰，只能保证在同一个对象内不被改变
* i5 final+static ,在载入时初始化，而不是在创建对象时初始化
*/
```

### 空白 final

我们要求对 final 进行赋值处理——要么在定义时赋值，要么在构造函数中复制，这样就可以确保 final 字段在使用前获得正确的初始化。

### final 自变量

```java
class Gizmo {
    public void spin() {}
}
public class FInalData {
    void with(final Gizmo g) {
//! g = new Gizmo(); // Illegal -- g is final
        g.spin();
    }
    void without(Gizmo g) {
        g = new Gizmo(); // OK -- g not final
        g.spin();
    }
// void f(final int i) { i++; } // Can't change  直接改变了final 修饰的值。
// You can only read from a final primitive:
    int g(final int i) { return i + 1; }    // i 的值没有被改变
    public static void main(String[] args) {
        FInalData bf = new FInalData();
        bf.without(null);
        bf.with(null);
    }
}
```

## final 方法

+ 为方法“上锁”，防止任何继承类改变它的本来的含义。
+ 执行效率，**只要编译器发现一个final方法调用，就会（根据它自己的判断）忽略为执行方法调用机制而采取常规代码插入方法（将自变量压入堆栈；跳至方法代码并执行它；跳回来；清除堆栈自变量；最后对返回值进行处理。）**

通常只有想明确禁止方法被覆盖时或方法的代码量非常少时，才应考虑将一个方法设为final.

**类内所有 private 方法都自动成为 final，可为一个private方法添加final指示符，但却不能为那个方法提供任何额外的含义。**



## final 类 

如果整个类都是 final，就表明自己从不希望从这个类继承，或者不允许其他任何人采取这种操作。

```java
class SmallBrain{}

final class Dinosaur {
    int i = 7;
    int j = 1;
    SmallBrain x = new SmallBrain();
    void f() {
        System.out.print("..");
    }
}
//! class Further extends Dinosaur {}
// error: Cannot extend final class 'Dinosaur'
public class FInalData {
    public static void main(String[] args) {
        Dinosaur n = new Dinosaur();
        n.f();
        n.i = 40;
        n.j++;
    }
}
```

>当一个类被 final 修饰，结果只是禁止进行继承--没有更多的限制。其所有方法默认为 final。而数据成员取决于我们的选择。
>
>可为 final 类内的一个方法添加 final 指示符，但这样做没有任何意义 





















