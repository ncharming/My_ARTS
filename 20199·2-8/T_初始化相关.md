# 《Java 编程思想》第五章

对象以及成员变量的初始化

## 常见的初始化顺序

1.基类静态代码块/静态成员变量

2.子类静态代码块/静态成员变量

3.基类普通代码块/普通成员变量

4.基类构造函数

5.子类普通代码块/普通成员变量

6.子类构造函数

成员变量要先于所有方法(包括构造方法)初始化

## Super()

```java
class Super {
    public int i = 0;
    public Super(String text) {
        i = 1;
    }
}
public class Sub extends Super {

    public Sub(String text) { 
        // 运行时报错
        // There is no default constructor available in 'testAll.ThinkingInJava.Super
        i = 2;
    }

    public static void main(String args[]) {
        Super sui = new Sub("Hello");
        System.out.println(sui.i);
    }

}
```

解决方案：

+ 基类不变，在子类的构造函数中显示的调用父类构造函数
  + super(text)；

+ 子类不变，在父类中添加默认构造函数
  + public Super(){}

=================== 总结

子类实例化的时候会调用父类构造函数；如果父类没有默认构造函数，而子类没有**显示**调用父类的非默认构造函数，则会报错。

