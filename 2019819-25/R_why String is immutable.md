# Why String is immutable in Java ？

[英文地址](https://www.programcreek.com/2013/04/why-string-is-immutable-in-java/)

String 在 Java 中是不可变的。一个不可变类就是这个类的实例不能被修改。所有实例信息在实例被创建的时候被初始化，并且这些信息都不能被修改。不可变类有很多优点。这篇文章总结了为什么 **String 被设计成不可变的**。这篇文章从内存、同步、和数据结构不同的角度来说明不可变性的概念。

## 1.Requirement of String Pool

字符串池

String 常量池是方法区中特殊的存储区域。当一个 String 被创建并且这个 String 已经存在在常量池中，那么已经存在的 String 的引用会被返回，而不是新建一个对象。

下面的代码只会创建一个 String 对象在堆中。

~~~java
String string1 = “abcd”;

String string2 = "abcd";
~~~

![ ](.\R_stringPool.jpeg)

如果 String 是可变的，改变一个 String 引用将会导致其他引用的值改变（出错）

## 2.Caching HashCode

缓存 hashCode

hashCode 在 Java 中很常用，比如 HashSet 和 HashMap。不可变性可以保证 hashCode 保持不变，而不用担心变化。即当用它的时候不用计算每次的 hashCode。这是高效的。

在 String 类中：

~~~java
private int hash;//用于缓存 hashCode 值
~~~

## 3.Facilitating the Use of other Object

促进其他对象的使用

更便于其他对象的使用，参看如下代码：

~~~java
HashSet<String> set = new HashSet<String>();
set.add(new String("a"));
set.add(new String("b"));
set.add(new String("c"));
 
for(String a: set)
	a.value = "a";
~~~

这个例子中，如果 String 是可变的，它的值将会被改变就会违反 set 的设计原则（set 中不能有重复的元素）。当然，上面的代码只是一个 demo 说明，String 中也没有 Value 字段(直译)「我的理解：不能直接从String 中拿到 value 字段，因为是 private 修饰的」。

## 4.Security

安全性

String 被当做参数广泛应用于很多 Java 类中，例如：网络连接、文件操作等等。如果 String 是可变的，那么一个连接或文件会被改变，将会导致严重的安全威胁。这些方法认为它将会连接到一个机器，但事实并没有。可变的 String 可能同样会导致反射安全，因为他的参数是 String。

下面是代码示例：

~~~java
boolean connect(string s){
    if (!isSecure(s)) { 
throw new SecurityException(); 
}
    //here will cause problem, if s is changed before this by using other references.  
    // 如果 s 在该操作之前被其他引用改变，就会产生问题
    causeProblem(s);
}
~~~

## 5.Immutable objects are naturally thread-safe

不可变对象是天生线程安全的

因为不可变对象不能被改变，所以他们能在多个线程中自由使用。这就消除了进行同步的要求。

总之，String 被设计成不可变的是为了高效和安全。这也是为什么一般来说不可变的类是更好的在很多情况下。











