参考链接

D:\所有学习总结\java编程思想\3-6\操作符.md（分割线下面的内容）

====================================================

在看《Thinking in java》第三章：操作符的过程中，记录两个觉得最有用的点

# java 别名现象

我总结下来，其实就是函数参数的传递本质：到底是值传递还是引用传递。

**java 中只有值传递**，当传递的是一个对象的时候，其实也是值传递，只不过值的内容是一个引用。

参见总结：

D:\所有学习总结\碰到问题的杂项\java基础知识_杂项\java中的值传递（分割线下面的内容）

# equals与==

equals：比较**两个对象的实际内容**是否相等。（**基本类型**不适用equals，直接使用==判断即可）

==：比较的是**对象的引用**。

对于equals，如果我们自己不去**覆写equals**方法，那么会直接调用Object类的equals方法，而这个方法的底层逻辑是使用 == 判断（即比较的是引用）

=====================================================

D:\所有学习总结\碰到问题的杂项\java基础知识_杂项\java中的值传递

# 为什么 Java 中只有值传递

## 实参与形参

形式参数：在定义函数名和函数体的时候使用的参数，目的是用来接收调用该函数时传入的参数。

实际参数：在调用有参函数时，主调函数和被调函数之间有数据传递关系。在主调函数中调用一个函数时，函数名后面括号中的参数称为“实际参数”。

```java
public void getName(String s){ //形参
    System.out.println("test:"+s);
}

public static void main(String[] args) {
    Test test=new Test();
    test.getName("nhy"); //实参
}
```

当我们调用一个有参函数的时候，会把实际参数传递给形式参数。但是，在程序语言中，这个传递过程中传递分两种情况，即值、引用传递

## 值传递与引用传递

值传递（pass by value）：在调用函数时将实际参数复制一份传递到函数中，这样在函数中如果对参数进行修改，将不会影响到实际参数。

引用传递（pass by reference）：在调用函数时将实际参数的地址直接传递到函数中，那么在函数中对参数所进行的修改，将影响到实际参数。

```java
public void pass(int s) {
    s = 20;
    System.out.println("print in pass:" + s);
}

public static void main(String[] args) {
    Test test = new Test();
    int s = 10;
    test.pass(10);
    System.out.println("print in main:" + s);
}
//打印结果：pass函数中的改变，没有影响到实际参数
print in pass:20
print in main:10
```

这只是一个值传递的例证。

下面传递一个 student 对象:

```java
public void pass(Student s) {
    s.setName("Ncharming");
    System.out.println("print in pass:" + s);
}

public static void main(String[] args) {
    Test test = new Test();
    Student student=new Student();
    student.setName("nhy");
    student.setAge(10);
    test.pass(student);
    System.out.println("print in main:" + student);
}

//打印结果：pass函数中的改变，影响到了实际参数
print in pass:Student{name='Ncharming', age=10}
print in main:Student{name='Ncharming', age=10}
```

和 String 对象例子的区别：就是 s = “Ncharming”；会自动 new 一个对象。

即 s = “Ncharming” 等价于 s = new String("Ncharming");

**说明 Java传递是引用传递吗？**下面再传递一个对象试试

```java
public void pass(String s) {
    s = "Ncharming";
    System.out.println("print in pass:" + s);
}

public static void main(String[] args) {
    Test test = new Test();
    String name = "nhy";
    test.pass(name);
    System.out.println("print in main:" + name);
}

//打印结果：pass函数中的改变，没有影响到实际参数
print in pass:Ncharming
print in main:nhy
```

**此时，传递的对象，但是实际参数又没有被修改。**

## 值传递VS引用传递

|          | 值传递                 | 引用传递               |
| -------- | ---------------------- | ---------------------- |
| 根本区别 | 会创建副本             | 不创建副本             |
| 所以     | 函数中无法改变原始对象 | 函数中可以改变原始对象 |

> 我们都只关注了对内容的修改，这验证方式本身就有问题，所以结论就有问题。
>
> 你复制你把钥匙给你朋友，不管他对这个钥匙做什么改变都不会影响到你的钥匙，但是他进门砸了你的电视，那就对你有影响了。
>
> 我们在改变name属性的时候，不就是在“砸电视吗”

```java
public void pass(Student s) {
	// 真正改变参数
    s = new Student();
    s.setName("Ncharming");
    System.out.println("print in pass:" + s);
}

public static void main(String[] args) {
    Test test = new Test();
    Student student = new Student();
    student.setName("nhy");
    student.setAge(10);
    test.pass(student);
    System.out.println("print in main:" + student);
}

//打印结果：没有改变实参
print in pass:Student{name='Ncharming', age=0}
print in main:Student{name='nhy', age=10}
```

当我们**真正去改变参数**的时候，即在pass函数中 s = new Student();



## 图讲解为什么只有值传递

![](D:\所有学习总结\碰到问题的杂项\图片\改变内容，而非参数.png)

在这个方法中个，**并没有对形参本身进行修改，而是修改的形参持有的地址中存储的内容。**

# 总结

值传递和引用传递的区别并不是传递的内容。而是实参到底有没有被复制一份给形参。在判断实参内容有没有受影响的时候，要看传的是什么。

**如果你传的是个地址，那么就看这个地址的变化会不会有影响，而不是看地址指向的对象的变化**。就像钥匙和房子的关系



> 所以说，**Java中还是值传递的，只不过对于对象参数，值的内容是对象的引用。**



参考文章：

[java为什么只有值传递](https://www.hollischuang.com/archives/2275)





































