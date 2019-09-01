[参考链接](<https://www.geeksforgeeks.org/enum-in-java/>)

# enum in Java

枚举在编程语言中是以一组常量命名表示的。比如一副扑克牌的4个套装可以命名为这 4 个Club、Diamond、Heart、Space枚举值，属于 Suit 枚举类型。其他例子包含更自然的枚举类型（比如行星、星期的每天、颜色、方向等等）。

当我们在编译时知道所有枚举值时使用枚举，比如菜单上的选择、舍入模式、命令行标志等，枚举类型中的常量值不必保持固定。

在 Java 中，我们可以添加变量、方法、构造器到枚举中。枚举的主要目标是定义我们自己的数据类型（枚举数据类型）。

## Declaration of enum in Java 

+ 枚举能够在类里面或外面定义，但不能定义在方法中

```java
enum Color{
    Red,Green;
}

public class EnumTest {
    enum Color{
        Red,Greenn;
    }
	
    /*
    	类里面的 enum 会覆盖掉类外面的；
    */
    public static void main(String[] args) {
        Color color=Color.Greenn;
        System.out.println(color); //Greenn
    }

}
```

>enum 类中的**第一行必须是一组常量**，再然后是方法、变量和构造器（后面的无先后顺序）

+ 根据 Java 命名约定，我们建议所有的 Java 常量名大写

## Important points of enum

+ 每个枚举都是通过使用 Class 实现的

~~~java
/*
	上面的内部枚举可以转换成下面的 class

class Color{
    public static final Color RED=new Color();
    public static final Color BLUE = new Color();
    public static final Color GREEN = new Color();
}
*/
~~~

+ 每个枚举常量表示枚举类型的对象
+ 能用 switch 操作枚举类型的值

~~~java
enum Day {
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY,
    THURSDAY, FRIDAY, SATURDAY;
}

public class EnumTest {

    Day day;

    public EnumTest(Day day) {
        this.day = day;
    }

    public void dayIsLike() {
        switch (day) {
            case FRIDAY:
                System.out.println("better");
                break;
            case SUNDAY:
                System.out.println("good");
                break;
            case SATURDAY:
                System.out.println("good too");
                break;
            default:
                System.out.println("just-so-so");

        }
    }

    public static void main(String[] args) {
        String s = "SATURDAY";
        EnumTest enumTest = new EnumTest(Day.valueOf(s));
        enumTest.dayIsLike();
        // good too
    }
}
~~~

+ 每个枚举常量都隐式为  **public static final**。因为是 **static**，我们能用枚举类名字使用它。因为是 **final**，我们不能改变它。
+ 我们能在枚举类中声明 **main()** 方法，因此，我们可以直接从命令提示符调用枚举。

```java
enum Day {
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY,
    THURSDAY, FRIDAY, SATURDAY;

    public static void main(String[] args) {
        System.out.print(Day.FRIDAY);//FRIDAY
    }
}
```

## Enum and Inheritance:

+ 所有枚举类隐式的继承 **java.lang.Enum class**，在 Java 中类智能继承一个父类，所以枚举类不能再继承其他类
+ **toString()** 方法在枚举类中被重写，返回常量名字
+ 枚举类可以实现多个接口

## values(),ordinal() and valueOf() methods：

+ 这些方法在 java.lang.Enum 中就有
+ values() 方法可以返回枚举类中所有的值
+ 排序在枚举类中是很重要的，通过使用 ordinal() 方法，每个枚举值的序列都能被找到，就像数组序列一样（从0开始的）
+ valueOf() 方法返回指定的枚举值，如果存在的话。

```java
enum Day {
    SUNDAY, MONDAY, TUESDAY, WEDNESDAY,
    THURSDAY, FRIDAY, SATURDAY;
}

public class EnumTest {

    Day day;

    public EnumTest(Day day) {
        this.day = day;
    }

    public static void main(String[] args) {
        String s = "SATURDAY";
        EnumTest enumTest = new EnumTest(Day.valueOf(s));
       Day[] str=Day.values();
       for(Day day:str){
           System.out.println(day+",index:"+day.ordinal());
           //SUNDAY,index:0   ;一行行的打印出来
       }
    }
}
```

## Enum and constructor

+ 枚举可以包含构造函数，**并且在枚举类加载时为每个枚举常量单独执行**
+ 我们不能显示的创建枚举对象，因此我们不能直接调用枚举构造函数

## enum and methods

**枚举能包含具体的方法，不能有抽象的方法**

















