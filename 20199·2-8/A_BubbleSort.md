[参考博文](https://blog.csdn.net/guoweimelon/article/details/50902597)

冒泡排序（Bubble Sort）是一种典型的交换排序算法，通过交换数据元素的位置进行排序。

# 核心思想

从一个序列的一边进行两两比较，直到最后将最大（小）的数据元素交换到了无序队列的队尾，从而成为有序的一部分；下次继续进这个过程，直到所有数据元素都排好序。

![](D:\所有学习总结\ARTS\My_ARTS\20199·2-8\bubbleSort.jpg)

# 代码

```java
public class BubbleSort {
    public static int[] bubbleSort(int[] arr) {
        int temp;
        /*
        * 为什么用length-1：后面再比较的时候是用j+1
        * 第二个循环从 0 开始，length-1-i 结束
        * j 和 j+1 比较：相邻的比较，每一轮把最大的数放到最后
        */
        
        for (int i = 0; i < arr.length-1; i++) {
            for (int j = 0; j < arr.length-i-1; j++) {
                // 交换
                if (arr[j] > arr[j+1]) {
                    temp = arr[j];
                    arr[j] = arr[j+1];
                    arr[j+1] = temp;
                }
            }
        }
        return arr;
    }

    public static void main(String[] args) {
        int[] array = {2, 7, 0, 4, };
        int[] swapArray = bubbleSort(array);
        for (int a = 0; a < swapArray.length; a++) {
            System.out.print(swapArray[a]);
        }
    }
}
```

~~~java

  void bubble_sort ( int arr[], int len){
            int i, j, change = 1;
			//优化：设置哨兵，假如 change=0，说明已经有序，不必在排序
            for (i = 0; i < len - 1 && change != 0; i++) {
                change = 0;
                for (j = 0; j < len - 1 - i; j++)
                    if (arr[j] > arr[j + 1]) {
                        swap(arr[j], arr[j + 1]);
                        change = 1;
                    }
            }
        }
~~~

# 性能分析

算法h时间、空间复杂度、稳定性分析

+ 时间复杂度

在设置哨兵后，最好情况是O(n)：比较次数为 n-1，移动 0 次；最坏情况O(n次方)。

+ 空间复杂度

冒泡排序中需要一个临时变量进行两两交换，所需额外空间是 1 。因此空间复杂度为O(1)。

+ 稳定性

在排序过程中，元素两两交换，**相同的元素**前后顺序并没有改变，所以是**稳定排序算法**。

## 稳定性

比如冒泡排序：比较的是相邻的两个元素，交换也在这两个元素之间。所以，如果两个元素相等，我们是不会把他们交换一下的；如果两个相等的元素没有相邻，那么即使通过前面的两两交换把两个元素相邻起来，这个时候也不会交换。所以相同元素的前后顺序并没有改变，所以冒泡排序是一种稳定的排序算法。



























































































































































































