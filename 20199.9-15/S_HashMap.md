**基于 JDK8**

[参考链接](https://tech.meituan.com/2016/06/24/java-hashmap.html)

与 List、Set 集合隶属于 Collection 不同，Map 是一个独立的接口，与Collection 相同级别的接口。

Map 集合提供了一个不一样的元素存储方法，**利用“Key-Value”的形式进行存储。每个键映射一个值**。而 Set 集合中，元素存储就是利用 Map 这一特性来实现。

# Map 常用的方法

先了解下 Map 接口本身，以便了解所有实现的共同点。

~~~java
// 返回 map 中 key-value 键值对的数目
int size();

// 如果不存在 key-value 键值对，则返回 true
boolean isEmpty();

// 如果存在指定的 key 返回true
boolean containsKey(Object key);

// 如果存在指定的 Value 返回true
boolean containsValue(Object value);

// 返回 key 值对应的 value 值
V get(Object key);

// 插入一个键值对，将指定的值与键相关联
// 当 key 值已存在，新的Value值会覆盖旧的Value值
V put(K key, V value);
// 将另外一个 map 复制到这个 map 中
void putAll(Map<? extends K, ? extends V> m);

// 删除键和关联的值
V remove(Object key);
  
// 删除此 Map 中所有的映射
void clear();

// 返回包含键的 set 集合
Set<K> keySet();

// 返回包含所有值的 Collection 集合
Collection<V> values();

// 返回 Map 中所包含映射的 Set 视图。Set 中的每个元素都是一个 Map.Entry 对象
Set<Map.Entry<K, V>> entrySet();

// Map 集合中存储 key-valu 的对象 Entry,在 Map 集合内形成数据结构
interface Entry<K,V> {
    K getKey();
    V getValue();
    V setValue(V value);
    boolean equals(Object o);
    int hashCode();
    ...
}
~~~

# HashMap

![](.\picture\HashMap.png)

说说 HashMap 的实现：

HashMap 基于哈希表，底层结构由数组来实现，添加到集合中的元素以“key-value”形式保存到数组中，在数组中key-value 被包装成一个实体类来处理---也就是上面 Map 接口中的 Entry（每一个键值对也叫做 Entry）。Java8中HashMap是Node

HashMap 作为一个数据结构，像数组和链表一样用于常规的 CRUD，在 put 的时候会先进行一次分配索引的操作再存储，一旦分配索引存储之后，下次 get 就可以大大缩短查找时间。并且 HashMap 将数组和链表两者结合起来。

HashMap 使用了「数组 + 链表 + 红黑树」(JDK8 新增了红黑树)三种数据结构：

![HashMap的存储结构](.\picture\HashMap内部结构.png)

## 数据底层具体存储什么

>transient Node<K,V>[] table;
>
>这个重要字段就是哈希桶数组，它是一个Node的数组。
>
>下面是 java8 中 HashMap 的一段源代码：
>
>Node 是 HashMap 的一个内部类，实现了 Map.Entry 接口，本质就是一个映射（键值对）。上图中的每个黑色圆点就是一个 Node 对象。

~~~java
 static class Node<K,V> implements Map.Entry<K,V> {
        final int hash;
        final K key;
        V value;
        Node<K,V> next;

        Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }

        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }

        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }

        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
    }
~~~

## 这样的存储有什么优点

HashMap就是使用哈希表来存储的。哈希表为解决冲突，可以采用开放地址法和链地址法等，HashMap中采用了**链地址法**：数组+链表的组合。**在每个数组元素上都有一个链表结构，当数据被 Hash 后，得到数组下标，把数据放在对应下标元素的链表上。**

~~~java
map.put("nhy","ncharming");
~~~

系统将会调用“nhy”这个key的hashCode()方法得到其hashCode值（该方法适用于Java对象），然后再通过Hash算法的后两步运算（高位运算和取模运算）来定位该键值对的存储位置，有时两个key会定位到相同的位置，表示发生了Hash碰撞。当然Hash算法计算结果越分散均匀，Hash碰撞的概率就越小，map的存取效率就会越高。

如果哈希桶数组很大，即使较差的Hash算法也会比较分散，如果哈希桶数组很小，即使好的Hash算法也会出现较多碰撞。那么通过什么方式来控制map使得Hash碰撞的概率又小，哈希桶数组（Node[] table）占用空间又少呢？**答案就是好的Hash算法和扩容机制。**

在了解Hash和扩容流程之前，先了解下面这个几个字段：

~~~java
int threshold;     // 所能容纳的 key-value 对极限
final float loadFactor; //负载因子
int modCount;
int size;
~~~

>首先，Node[] table的初始化长度length是默认值是16，Loadfactor负载因子默认值是0.75，threshold是HashMap所能容纳的最大数据量的Node（键值对）个数，threshold=length*LoadFactor；即在数组定义好长度后，负载因子越大，所能容纳的键值对个数越多。

假如超过threshold允许的最大数目，就会扩容。扩容后HashMap的容量是之前的两倍。负载因子0.75是对空间和时间效率的一个平衡选择，建议不要修改（除非特定情况，且这个值可以大于1）。

>size：HashMap中实际存储的键值对个数
>
>table的长度length：就是table的总长度
>
>threshold：length*loadfactor

modCount主要用来记录HashMap内部结构发生变化的次数，，主要用于迭代的快速失败。内部结构变化指的是结构发生变化，例如put新键值对，但是某个key对应的value值被覆盖不属于结构变化。

在HashMap中，哈希桶数组table的长度length大小必须为2的n次方（一定是合数）：主要是为了在取模和扩容时做优化，同时为了减少冲突，HashMap定位哈希桶索引位置时，也加入了高位参与运算。

这里有个问题是：如果拉链（每个数组位是链表的头节点）过长，会严重影响性能，于是在JDK1.8版本中，引入了红黑树。当链表过长（默认超过8）时，链表就转换为红黑树，利用**红黑树快速增删改查**的特点来提高HashMap的性能。

[红黑树参考](https://blog.csdn.net/v_july_v/article/details/6105630)



## 主要方法分析

```java
// table 在第一次使用时被初始化；当分配时，长度始终是2的幂。
// 此 table 就是存储数据的数组。
transient Node<K,V>[] table;
// 上面图中的一个黑色的球就是一个Node；

// HashMap 的存储大小
transient int size;

// 临界值：HashMap大小达到临界值，需要重新分配大小。
int threshold;

// 统一修改：HashMap 被修改或者删除的次数总数
transient int modCount;

/**
The default initial capacity - MUST be a power of two.
默认初始化容量是：16，这个值是哈希表中桶（Node数组）的数量。
相当于：1*2的4次方
*/
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; 

// 加载因子
// 当hash表中的条目数超出了加载因子与当前容量的乘积时，通过rehash方法将容量翻倍
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```

以下主要从根据key获取哈希桶数组索引位置、put方法的详细执行、扩容过程：

### 确定哈希桶数组索引位置

将操作定位到哈希桶数组的位置是很关键的第一步。HashMap的数据结构是数组+链表，所以我们更希望这个HashMap里面元素位置尽量分布均匀些，使得每个位置上的元素数量只有一个，那么当我们用hash算法求得这个位置的时候，马上就可以知道对应位置的元素就是我们想要的，不用遍历链表。大大优化了查询的效率。

```java
static final int hash(Object key) {
    int h;
    // h = key.hashCode() 为第一步 取 hashCode 值
    // h^(h>>>16) 第二步，高位参与运算
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
// 在上面的值计算好后，在 putVal 中，有如下的代码：
  if ((p = tab[i = (n - 1) & hash]) == null)
      //取模运算：得到存储下标
  (n - 1) & hash：
  
  //这个运算就是取模，为了效率问题，使用位运算(&)来代替取模运算(%)。&运算直接对内存数据进行操作，不需要转成十进制，因此处理速度非常快。
```

上面代码的计算示例：n 为 table 长度

![](.\picture\HashMap索引运算.png)

#### tip：&取代%原理

>X % 2^n = X & (2^n-1)
>
>2^n：表示2的n次方；即，**一个数对2^n取模 等于 一个数和(2^n-1)做按位与运算。**

### 分析HashMap的 put 方法

![](.\picture\HashMap的put方法.png)

1. 判断键值对数组table[i]是否为空或null，否则执行resize()进行扩容

2. 根据键值key计算hash值得到插入的数组索引i，如果table[i]==null，直接新建节点添加，转向6，如果table[i]不为空，转向3

3. 判断table[i]的首个元素是否和key一样，如果相同直接覆盖value，否则转向4，这里的相同指的是hashCode以及equals

4. 判断table[i] 是否为treeNode，即table[i] 是否是红黑树，如果是红黑树，则直接在树中插入键值对，否则转向5；

5. 遍历table[i]，判断链表长度是否大于8，大于8的话把链表转换为红黑树，在红黑树中执行插入操作，否则进行链表的插入操作；遍历过程中若发现key已经存在直接覆盖value即可；

6. 插入成功后，判断实际存在的键值对数量size是否超过了最大容量threshold，如果超过，进行扩容。

   > JDK1.8 HashMap的源码如下：

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            e = p;
        else if (p instanceof TreeNode)
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    p.next = newNode(hash, key, value, null);
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        if (e != null) { // existing mapping for key
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    return null;
}
```

### 扩容机制

扩容(resize)就是重新计算容量，向HashMap对象里不停的添加元素，而HashMap对象内部的数组无法装载更多的元素时，对象就需要扩大数组的长度，以便能装入更多的元素。当然Java里的数组是无法自动扩容的，方法是使用一个新的数组代替已有的容量小的数组，就像我们用一个小桶装水，如果想装更多的水，就得换大水桶。

以下是JDK1.7的resize源码，JDK1.8加入了红黑树，较复杂，而且本质上区别不大，所以用JDK1.7：

~~~java
  void resize(int newCapacity) {   //传入新的容量
     Entry[] oldTable = table;    //引用扩容前的Entry数组
     int oldCapacity = oldTable.length;         
     if (oldCapacity == MAXIMUM_CAPACITY) {  //扩容前的数组大小如果已经达到最大(2^30)了
          threshold = Integer.MAX_VALUE; //修改阈值为int的最大值(2^31-1)，这样以后就不会扩容了
         return;
     }
       Entry[] newTable = new Entry[newCapacity];  //初始化一个新的Entry数组
     transfer(newTable);                         //！！将数据转移到新的Entry数组里
     table = newTable;                           //HashMap的table属性引用新的Entry数组
     threshold = (int)(newCapacity * loadFactor);//修改阈值
 }
~~~

这里就是使用一个容量更大的数组来代替已有的容量小的数组，transfer()方法将原有Entry数组的元素拷贝到新的Entry数组里。

~~~java
  void transfer(Entry[] newTable) {
      Entry[] src = table;                   //src引用了旧的Entry数组
      int newCapacity = newTable.length;
      for (int j = 0; j < src.length; j++) { //遍历旧的Entry数组
          Entry<K,V> e = src[j];             //取得旧Entry数组的每个元素
          if (e != null) {
              src[j] = null;//释放旧Entry数组的对象引用（for循环后，旧的Entry数组不再引用任何对象）
              do {
                  Entry<K,V> next = e.next;
                 int i = indexFor(e.hash, newCapacity); //！！重新计算每个元素在数组中的位置
                 e.next = newTable[i]; //标记[1]
                 newTable[i] = e;      //将元素放在数组上
                 e = next;             //访问下一个Entry链上的元素
             } while (e != null);
         }
     }
 } 
~~~

下面看看JDK1.8做了哪些优化。经过观测可以发现，我们使用的是2次幂的扩展(指长度扩为原来2倍)，所以，元素的位置要么是在原位置，要么是在原位置再移动2次幂的位置。看下图可以明白这句话的意思，n为table的长度。

图a表示扩容前的key1和key2两种可以确定索引位置的示例；图b表示扩容key1和key2两种key确定索引位置的示例，其中hash1是key1对应的哈希与高位运算结果。

![](.\picture\扩容前确定索引.png)

元素在重新计算hash之后，因为n变为2倍，那么n-1的mask范围在高位多1bit(红色)，因此新的index就会发生这样的变化：

![](.\picture\扩容后确定索引.png)

因此，我们在扩充HashMap的时候，不需要像JDK1.7的实现那样重新计算hash，只需要看看原来的hash值新增的那个bit是1还是0就好了，是0的话索引没变，是1的话索引变成“原索引+oldCap”,下图是16扩充为32的resize示意图：

![](.\picture\16扩充为32的图.png)

这个设计确实非常的巧妙，既省去了重新计算hash值的时间，而且同时，由于新增的1bit是0还是1可以认为是随机的，因此resize的过程，均匀的把之前的冲突的节点分散到新的bucket了。这一块就是JDK1.8新增的优化点。有一点注意区别，JDK1.7中rehash的时候，旧链表迁移新链表的时候，如果在新表的数组索引位置相同，则链表元素会倒置，但是从上图可以看出，JDK1.8不会倒置。**有兴趣的同学可以研究下JDK1.8的resize源码**



在多线程使用场景中，应使用ConcurrentHashMap而不是HashMap。下面的例子说明在并发的多线程使用场景中使用HashMap可能造成死循环。示例代码如下：（JDK1.7）

~~~java
public class HashMapInfiniteLoop {  
    private static HashMap<Integer,String> map = new HashMap<Integer,String>(2，0.75f);  
    public static void main(String[] args) {  
        map.put(5， "C");  

        new Thread("Thread1") {  
            public void run() {  
                map.put(7, "B");  
                System.out.println(map);  
            };  
        }.start();  
        new Thread("Thread2") {  
            public void run() {  
                map.put(3, "A);  
                System.out.println(map);  
            };  
        }.start();        
    }  
} 
~~~

其中，map初始化为一个长度为2的数组，loadFactor=0.75，threshold=2*0.75=1，也就是说当put第二个key的时候，map就需要进行resize。

通过设置断点让线程1和线程2同时debug到transfer方法(3.3小节代码块)的首行。注意此时两个线程已经成功添加数据。放开thread1的断点至transfer方法的“Entry next = e.next;” 这一行；然后放开线程2的的断点，让线程2进行resize。结果如下图。

![](.\picture\多线程示例1.png)

注意，Thread1的 e 指向了key(3)，而next指向了key(7)，其在线程二rehash后，指向了线程二重组后的链表。

线程一被调度回来执行，先是执行 newTalbe[i] = e， 然后是e = next，导致了e指向了key(7)，而下一次循环的next = e.next导致了next指向了key(3)。

![](.\picture\多线程示例2.png)

![](.\picture\多线程示例3.png)

e.next = newTable[i] 导致 key(3).next 指向了 key(7)。注意：此时的key(7).next 已经指向了key(3)， 环形链表就这样出现了。

![](.\picture\多线程示例4.png)

于是，当我们用线程一调用map.get(11)时，悲剧就出现了——Infinite Loop。

# JDK1.8较于JDK1.7性能优化

## Hash较均匀的情况

~~~java
class Key implements Comparable<Key> {

    private final int value;

    Key(int value) {
        this.value = value;
    }

    @Override
    public int compareTo(Key o) {
        return Integer.compare(this.value, o.value);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass())
            return false;
        Key key = (Key) o;
        return value == key.value;
    }

    @Override
    public int hashCode() {
        return value;
    }
}

~~~

这个类复写了equals方法，并且提供了相当好的hashCode函数，任何一个值的hashCode都不会相同，因为直接使用value当做hashcode。为了避免频繁的GC，我将不变的Key实例缓存了起来，而不是一遍一遍的创建它们。代码如下：

~~~java
public class Keys {

    public static final int MAX_KEY = 10_000_000;
    private static final Key[] KEYS_CACHE = new Key[MAX_KEY];

    static {
        for (int i = 0; i < MAX_KEY; ++i) {
            KEYS_CACHE[i] = new Key(i);
        }
    }

    public static Key of(int value) {
        return KEYS_CACHE[value];
    }
}
~~~

现在开始我们的试验，测试需要做的仅仅是，创建不同size的HashMap（1、10、100、……10000000），屏蔽了扩容的情况，代码如下：

~~~java
static void test(int mapSize) {

        HashMap<Key, Integer> map = new HashMap<Key,Integer>(mapSize);
        for (int i = 0; i < mapSize; ++i) {
            map.put(Keys.of(i), i);
        }

        long beginTime = System.nanoTime(); //获取纳秒
        for (int i = 0; i < mapSize; i++) {
            map.get(Keys.of(i));
        }
        long endTime = System.nanoTime();
        System.out.println(endTime - beginTime);
    }

    public static void main(String[] args) {
        for(int i=10;i<= 1000 0000;i*= 10){
            test(i);
        }
    }

~~~

![](.\picture\性能比较.png)

通过观测测试结果可知，JDK1.8的性能要高于JDK1.7 15%以上，在某些size的区域上，甚至高于100%。由于Hash算法较均匀，JDK1.8引入的红黑树效果不明显，下面我们看看Hash不均匀的的情况。

## Hash极不均匀的情况

假设我们有一个非常差的Key，它们所有的实例都返回相同的hashCode值。这是使用HashMap最坏的情况。代码修改如下：

```java
class Key implements Comparable<Key> {

    //...

    @Override
    public int hashCode() {
        return 1;
    }
}
```

仍然执行main方法，得出的结果如下表所示：

![](.\picture\性能比较1.png)

从表中结果中可知，随着size的变大，JDK1.7的花费时间是增长的趋势，而JDK1.8是明显的降低趋势，并且呈现对数增长稳定。当一个链表太长的时候，HashMap会动态的将它替换成一个红黑树，这话的话会将时间复杂度从O(n)降为O(logn)。hash算法均匀和不均匀所花费的时间明显也不相同，这两种情况的相对比较，可以说明一个好的hash算法的重要性。

两者都是JDK1.8较优

# 总结

(1) 扩容是一个特别耗性能的操作，所以当程序员在使用HashMap的时候，估算map的大小，初始化的时候给一个大致的数值，避免map进行频繁的扩容。

(2) 负载因子是可以修改的，也可以大于1，但是建议不要轻易修改，除非情况非常特殊。

(3) HashMap是线程不安全的，不要在并发的环境中同时操作HashMap，建议使用ConcurrentHashMap。

(4) JDK1.8引入红黑树大程度优化了HashMap的性能。

(5) 还没升级JDK1.8的，现在开始升级吧。HashMap的性能提升仅仅是JDK1.8的冰山一角。









