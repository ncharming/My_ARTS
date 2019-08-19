# 搭建 SpringBoot 

今天在 GitHub 找到一个项目，自己搭建着试试，当然就遇到了下面的这些坑。

搭建过程：就是简单的使用 spring.io initialize 模板。

[链接](https://github.com/lenve/vhr)

## 第一次启动

当写了部分代码启动项目时，报错：
Field menuMapper in com.nhy.practice.service.MenuService required a bean of type 'com.nhy.practice.mapper.MenuMapper' that could not be found.
The injection point has the following annotations:

	- @org.springframework.beans.factory.annotation.Autowired(required=true)

解决方案：在启动类中加入**@MapperScan("你的mapper所在包")**即可，

此时，项目可以成功启动

## postman 调用

当第一次用 postman 调用时：

localhost:8082/login?username=admin&password=123

因为我Maven中引用的是8.0.X版本（MySQL版本在6.0.X以上的都要加上时区，不然）的MySQL驱动，所以在 application.praperties 配置文件中的 url 配置必须加上一个时区，改变如下：

~~~
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/vhr?serverTimezone=UTC&useUnicode=true&characterEncoding=UTF-8
~~~

## 再次调用

还是报错，但是不是MySQL的报错了。报错信息如下：

~~~
org.apache.ibatis.binding.BindingException: Invalid bound statement (not found): com.nhy.practice.mapper.MenuMapper.getAllMenu
~~~

遇到这种情况：

因为在target（编译后（build后）的文件）中是源码的编译文件，但是没有 xml 文件，所以报错。

+ 检查 XML 文件中 namespace 的属性是否与包文件名一致
+ 检查Mybatis SQL写法的id与Mapper文件中的方法名是否一致

反正就是检查各种名字（细节问题）出错没有，如果这些检查完都一致的话：

最终解决方案：

~~~xml
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.xml</include>
        </includes>
    </resource>
    <resource>
        <directory>src/main/resources</directory>
    </resource>
</resources>
~~~

表明将xml文件 加到classpath中，这样编译打包的时候就会把XML也加载到target文件夹中。

至此，项目可以通过 postman 调用。

