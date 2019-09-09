参考：[美团技术团队博客](https://tech.meituan.com/2014/03/06/in-depth-understanding-string-intern.html)

# 深入理解 String.intern

java 中 8 个基本数据类型和 String 类型，这些类型为了节省内存，加快运行速度，提供了一个常量池的概念。常量池类似 Java 系统级别提供的缓存。

8 种基本类型都是由系统协调调度，String 常量池比较特殊：

+ 如果由双引号声明的 String 对象，会直接存储到常量池中
+ 如果不是由双引号声明的 String 对象，可以用 String 的 intern 方法。会检查常量池中是否有这个对象，没有就会加入

## Java 中的代码

```java
public native String intern();
```

>如果常量池中存在该字符串，那么就会直接返回引用；如果没有，会先将字符串放到常量池中再返回。

java 7已经将String常量池移到 Java Heap 区域了。

实例：

```java
String s3 = new String("1") + new String("1");
s3.intern();
String s4 = "11"; //显式声明，直接去常量池中创建，发现已经有了，所以直接返回常量池中的引用。
//true

String s4 = "11";//“11”对象是s4新生成的对象
s3.intern();// 虽然执行了 intern 函数，但是没有赋值给 s3。所以不相等
//false：
System.out.println(s3 == s4);
```

# 补充解释

```java
String s0="kvill";
String s1="kvill";
String s2="kv" + "ill";
```

首先，我们要知道Java会确保一个字符串常量只有一个拷贝。 
　　因为例子中的s0和s1中的”kvill”都是字符串常量，它们在编译期就被确定了，所以s0==s1为true；而”kv”和”ill”也都是字符串常量，**当一个字符串由多个字符串常量连接而成时，它自己肯定也是字符串常量**，所以s2也同样在编译期就被解析为一个字符串常量，所以s2也是常量池中”kvill”的一个引用。 
　　所以我们得出s0==s1==s2; 

**用new String() 创建的字符串不是常量，不能在编译期就确定，所以new String() 创建的字符串不放入常量池中，它们有自己的地址空间。**

