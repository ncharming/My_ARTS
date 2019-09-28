# Tip

写在前面：在对集合对象进行的遍历的时候，不建议进行增减操作



# 如何在遍历的同时删除ArrayList元素

## 增强循环

```java
public static void main(String[] args) {
    List<String> list=new ArrayList<String>();
    list.add("1");
    list.add("2");
    list.add("3");
    list.add("4");
    for (String s:list) {
        if(s=="2"){       // tips：当 s=="3" ，会成功删除元素
            			  // 当等于listzhong 其他三个元素时都会报错：ConcurrentModificationException
            list.remove("2");
        }
    }
    log.info("before:"+list);
}
```

~~~java
java.util.ConcurrentModificationException
~~~

### 总结

在遍历到 list 中倒数第二个数的时候不会报错，否则报错

## 增强循环+break

```java
for (String s:list) {
    if(s=="2"){
        list.remove("2");
        break;
    }
}
```

在操作后，马上跳出循环不会报错

## 普通的 for 循环

**不会报错，但是取得值可能会出现异常**

**这主要是因为删除元素后，被删除元素后的元素索引发生了变化。假设被遍历list中共有10个元素，当  删除了第3个元素后，第4个元素就变成了第3个元素了，第5个就变成 了第4个了，但是程序下一步循环到的索引是第4个， 这时候取到的就是原本的第5个元素了**。  比如下面的程序：

```java
public static void main(String[] args) {
        List<String> list=new ArrayList<String>();
        list.add("1");
        list.add("2");
        list.add("3");
        list.add("4");
        list.add("5");
        list.add("6");
        list.add("7");
        list.add("8");

        for(int i=0;i<list.size();i++){
            if(i%3==0){
                list.remove(i);
            }
        }

    	//逆向的 for 循环可以消除元素移动的影响，打印正常
        //for(int i=list.size()-1;i>=0;i--){
        //    if(i%3==0){
        //        list.remove(i);
        //    }
        //}
        log.info("after:"+list);
    }
```

打印结果如下：

~~~java
- after:[2, 3, 4, 6, 7, 8]
~~~

按照代码逻辑：本来正确的应该是：- after:[2, 3, 5, 6, 8]

## *Iterator 方式

这种方式可以正常删除，建议使用

```java
public static void main(String[] args) {
    List<String> list = new ArrayList<String>();
    list.add("1");  //
    list.add("2");
    list.add("3");
    list.add("4");  //
    list.add("5");
    list.add("6");
    list.add("7");  //
    list.add("8");

   Iterator<String> iterator=list.iterator();
   while (iterator.hasNext()){
       String s=iterator.next();
       if((Integer.parseInt(s)-1)%3==0){
           iterator.remove();  //***
           //list.remove(s);  这里要使用Iterator的remove方法移除当前对象，
           // 如果使用List的remove方法，则同样会出现 ConcurrentModificationException
       }
   }
    log.info("after:"+list);
}
```

暂时没有深入源码研究！！！只是从功能性上去实现。

# 总结

1. 增强 for 循环：用 break，可以防止报错
2. for 循环：用反向删除 可以正常运行
3. 用 Iterator 迭代器自带的 remove 函数。

# 源码

## ArrayList.remove

### 根据下标删除元素

```java
public E remove(int index) {
    rangeCheck(index);   //检查是否越界

    modCount ++;          //保存修改的次数
    E oldValue = elementData(index);

    int numMoved = size - index - 1;
    if (numMoved > 0)
        //将数组中的某一段数据取出来放到另一个数组中
        System.arraycopy(elementData, index + 1, elementData, index,numMoved);
    	
    elementData[--size] = null; // clear to let GC do its work

    return oldValue;
}
```

```java
src      the source array.
srcPos   starting position in the source array.
dest     the destination array.
destPos  starting position in the destination data.
length   the number of array elements to be copied.
public static native void arraycopy(Object src,  int  srcPos,Object dest, int destPos, 			int length);
```

删除一个元素后，会重新改变 ArrayList 中的 elementData，而这个 elementData 就代表 ArrayList 的值

### 根据元素删除元素

//也是相当于通过 下标删除元素

```java
public boolean remove(Object o) {
    if (o == null) {
        for (int index = 0; index < size; index++)
            if (elementData[index] == null) {
                fastRemove(index);
                return true;
            }
    } else {
        for (int index = 0; index < size; index++)
            if (o.equals(elementData[index])) {
                fastRemove(index);
                return true;
            }
    }
    return false;
}


private void fastRemove(int index) {
        modCount++;
        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work
    }
```

### 普通 for 循环

就是按照上面的 remove 方法的源码运行，不会报错！

### 增强 for 循环

```java
public Iterator<E> iterator() {
    return new Itr();
}

Itr()：是 ArrayList 中实现 Iterator 的内部类

 private class Itr implements Iterator<E> {
        int cursor;       // index of next element to return
        int lastRet = -1; // index of last element returned; -1 if no such
        int expectedModCount = modCount;

        Itr() {}
        
        // 这个 hasNext 很关键，当遍历到倒数第二个的时候，cursor刚好等于size
        //所以当 if 条件是倒数第二个的时候，不会报错。
        
        /*
        	返回的是：增强的 for（）括号中的值
        */
        public boolean hasNext() {
            return cursor != size;
        }
        
        // 每运行一次 next() 就会 cursor + 1
         @SuppressWarnings("unchecked")
        public E next() {
            checkForComodification();
            int i = cursor;
            if (i >= size)
                throw new NoSuchElementException();
            Object[] elementData = ArrayList.this.elementData;
            if (i >= elementData.length)
                throw new ConcurrentModificationException();
            cursor = i + 1;
            return (E) elementData[lastRet = i];
        }
        
        //当两者不等时，
        final void checkForComodification() {
            if (modCount != expectedModCount)
                throw new ConcurrentModificationException();
        }

        
		......

/*在第一次初始化的时候会把 modCount 赋值给 expectedModCount；进行增加或者删除，modCount（记录改变次数的）都会加一，所以两者不相等时，会报错异常 ConcurrentModificationException
*/
```

### 迭代器

```java
//在 ArrayList 内部实现类中
/*
调用 ArrayList Itr 内部类的的 remove 方法
*/
public void remove() {
    if (lastRet < 0)
        throw new IllegalStateException();
    checkForComodification();

    try {
        ArrayList.this.remove(lastRet);
        cursor = lastRet;
        lastRet = -1;
        expectedModCount = modCount;
    } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
    }
}
```

