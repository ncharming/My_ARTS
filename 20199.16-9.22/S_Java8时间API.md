在做项目中入数据库的时候，碰到的问题：

需要获取系统时间，并且获取系统24小时后的时间（作为数据的失效时间）

# Date 类

Date 类主要封装了**系统的日期和时间的信息**，表示系统的特定时间戳，可以精确到毫秒。Date 对象表示时间的默认顺序是星期、月、日、小时、分、秒、年。

## 构造方法

### Date()

使用此构造方法分配的对象可以获取本地的当前时间（精确到秒）

```java
Date date = new Date();
System.out.println(date);
//Thu Aug 01 20:54:32 CST 2019
```

#### simpleDateFormat 

```java
Date date = new Date();
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
System.out.println(df.format(date));
//2019-08-01 20:59:19
```

### Date(long date)

此种形式表示从 GMT 时间（格林尼治时间）1970年1月1日0时0分0秒开始经过参数 date 指定的毫秒数。

```java
Date date = new Date(1000);
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");//设置日期格式
System.out.println(df.format(date));
//1970-01-01 08:00:01
```

为什么小时是 08，而不是 00；因为使用 CST（China Standard Time）时区的时间，刚好比格林尼治时间多 8 个小时

## 常用方法

long getTime()：返回子1970.1.1 00:00:00 GMT 以来，此 Date 对象表示的毫秒数。

Boolean after(Date when)：判断此日期是否在指定日期之后

Boolean before(Date when)：判断此日期是否在指定日期之前

# Calendar 类

Calendar 类是一个**抽象类**，它为特定瞬间与 YEAR、MONTH、DAY_OF_MONTH、HOUR 等日历字段之间的转换提供了一些方法，并为之操作日历字段（如获得下星期日的日期）提供了一些方法。

创建 Calender 对象不能使用 new 关键字「抽象类」，但是它提供了一个 getInstance() 方法来获得 Calendar 类对象。

**getInstance() 方法(静态方法)返回一个 Calendar 对象，其日历字段已由当前日期和时间初始化。**

```java
Calendar calendar = Calendar.getInstance();
System.out.println(calendar);
System.out.println(calendar.getTime());
//
```

下面是打印 calendar 的结果：

~~~
java.util.GregorianCalendar[time=1564665858201,areFieldsSet=true,areAllFieldsSet=true,lenient=true,zone=sun.util.calendar.ZoneInfo[id="Asia/Shanghai",offset=28800000,dstSavings=0,useDaylight=false,transitions=29,lastRule=null],firstDayOfWeek=1,minimalDaysInFirstWeek=1,ERA=1,YEAR=2019,MONTH=7,WEEK_OF_YEAR=31,WEEK_OF_MONTH=1,DAY_OF_MONTH=1,DAY_OF_YEAR=213,DAY_OF_WEEK=5,DAY_OF_WEEK_IN_MONTH=1,AM_PM=1,HOUR=9,HOUR_OF_DAY=21,MINUTE=24,SECOND=18,MILLISECOND=201,ZONE_OFFSET=28800000,DST_OFFSET=0]
~~~

## 常用方法

int get(int field) ：返回指定日历字段的值

```java
Calendar calendar = Calendar.getInstance();
System.out.println(calendar.get(Calendar.YEAR));
//2019
```

Date getTime()：返回一个表示此 Calendar 时间值（从格林威治时间 1970.01.01 00:00:00 到现在的毫秒偏移量）的Date对象



# 格式化时间

SimpleDateFormat：

```java
SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//可指定任意的格式
```

# #################

# java 8 时间API

为什么新引入 LocalDate、LocalTime 接口？

+ java.util.Date：月份从 0 开始
+ java.time.LocalDate：月份和星期都改成 enum，就不可能在用错了
+ `java.util.Date`和`SimpleDateFormat`都不是线程安全
+ `LocalDate`和`LocalTime`和最基本的`String`一样，是不变类型，不但线程安全，而且不能修改。
+ `java.util.Date`是一个“万能接口”，它包含日期、时间，还有毫秒数；而 java8 中很干净（分类的）

LocalDate 无法包含时间，LocalTime 无法包含日期；当然 LocalDateTime 才能同时包含日期时间。

# JDBC 映射日期类型

SQL ——> java

-----------------------------------------------------

date——>LocalDate

time——> LocalTime

timestamp——>LocalDateTime



# 将时间加减一天

```java
String sminus=LocalDateTime.now().minusDays(2).format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
String splus=LocalDateTime.now().plusDays(1).format(DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
后面是格式化日期：方便入库
```

~~~java
//当前日期向后推迟一天
LocalDateTime.now().plusDays(1);
//当前日期向前减一天
LocalDateTime.now().minusDays(1);
~~~

# simpleDateFormat VS DateTimeFormatter

simpleDateFormat  适用于 Date 类型

DateTimeFormatter 适用于 LocalDateTime 类型

