When to use LinkedList over ArrayList in Java?

何时在Java中通过ArrayList使用LinkedList？

[参考StackOverflow](https://stackoverflow.com/questions/322715/when-to-use-linkedlist-over-arraylist-in-java)

翻译自文章中回答赞数第一内容

LinkedList 和 ArrayList 是List接口的两种不同的实现。LinkedList使用双向链表来实现它，ArrayList使用动态增加的数组来实现。

和标准的链表和数组操作一样，各种方法有不同的运行时。

例如 LinkedList<E>

>+ get(int index) O(n)
>+ add(E element) O(1)
>+ add(int index,E element) O(n)
>+ remove(int index) O(n)
>+ Iterator.remove() O(1)
>+ ListIterator.add(E element) O(1)

ArrrayList<E>

>- get(int index) O(1)
>- add(E element) O(1)
>- add(int index,E element) O(n)
>- remove(int index) O(n)
>- Iterator.remove() O(n)
>- ListIterator.add(E element) O(n)

**LinkedList<E>**允许你以常量的时间用iterators插入或者删除，但是仅限于顺序访问元素。换句话说，你可以遍历这个列表向前向后，但是寻找元素在列表中位置是和列表的大小成正比的。

**ArrayList<E>**允许快速随机访问，所以你可以找到任何一个元素在常量时间内。但是添加或删除需要移动此元素后面的所有元素，进行放开或者填充。如果你添加过多的元素超过了数组大小，会自动扩容1.5倍，并且旧的数组会被复制到新的数组中。



















