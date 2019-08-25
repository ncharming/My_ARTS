java 中所有的 map 都实现了 Map 接口，以下方法适用于任何 map 实现（HashMap，TreeMap，LinkedHashMap，HashTable，等等）

# for-each 中使用 entries 遍历

最常见，在大多数情况下也最可取的遍历方式，在**键值都需要时使用**。

~~~java
Map<Integer, Integer> map = new HashMap<Integer, Integer>();
// 打印为{}，默认初始化不为 null
for (Map.Entry<Integer, Integer> entry : map.entrySet()) {
    System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
}
~~~

注意：如果遍历的是一个空的 map 对象，将会抛出NullPointerException。？？？

（上面的 new 不会出现空指针异常，下面的会）

```java
Map<String,String> map = null;

log.info("size:" + map.entrySet().size());
// map.entrySet()：会报空异常：
// Map.java 源码： 
// @NotNull @Contract Set<Map.Entry<K, V>> entrySet();
for(Map.Entry entry:map.entrySet()){
    log.info(entry.getKey()+","+entry.getValue());
}
```



# 在 for-each 中遍历 keys 或者 values 

当只需要 map 中的键或值，可以通过 keySet、values 来遍历。

~~~java

Map<Integer, Integer> map = new HashMap<Integer, Integer>();
 
//遍历map中的键
for (Integer key : map.keySet()) {
    System.out.println("Key = " + key);
}
 
//遍历map中的值
for (Integer value : map.values()) {
    System.out.println("Value = " + value);
 }
~~~

比entrySet在性能上稍好（10%）

# 使用 Iterator 遍历

## 使用泛型

~~~java

Map<Integer, Integer> map = new HashMap<Integer, Integer>();

Iterator<Map.Entry<Integer, Integer>> entries = map.entrySet().iterator();
while (entries.hasNext()) {
    Map.Entry<Integer, Integer> entry = entries.next();
    System.out.println("Key = " + entry.getKey() + ", Value = " + entry.getValue());
 }
~~~

## 不使用泛型

~~~java
Map map = new HashMap();
 
Iterator entries = map.entrySet().iterator();
 
while (entries.hasNext()) {
 
    Map.Entry entry = (Map.Entry) entries.next();
 
    Integer key = (Integer)entry.getKey();
 
    Integer value = (Integer)entry.getValue();
 
    System.out.println("Key = " + key + ", Value = " + value);
}
~~~

在老版本中，这是唯一遍历 map 的方式；

在遍历时调用 Iterator.remove()  来删除 entries，上面两个则不能。

# 通过键找值遍历（效率低）

~~~java

Map<Integer, Integer> map = new HashMap<Integer, Integer>();
 
for (Integer key : map.keySet()) {
 
    Integer value = map.get(key);
 
    System.out.println("Key = " + key + ", Value = " + value);
 }
~~~

# 总结

仅仅需要键或值使用方法二；

如果 java 版本低于5，或者打算在遍历的时候删除entries，必须使用方法三。否则使用方法一。
