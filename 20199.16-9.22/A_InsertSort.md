插入排序

# 核心思想

构造一个无序的序列，将第一个元素默认是有序的，将第二个元素与第一个元素进行比较，小的放在前面。

将第三个元素与前两个元素进行比较，插入在合适的位置。依次下去，直到排好序为止。

# 代码

```java
public class InsertSort {
    public static void insertSort(int[] arr) {
        int len = arr.length, temp;
        for (int i = 1; i < len; i++) {
            for (int j = i; j > 0; j--) {
                if (arr[j] < arr[j - 1]) {//从后往前对比，只要满足一次条件就交换一次
                    temp = arr[j];
                    arr[j] = arr[j - 1];
                    arr[j - 1] = temp;
                }else{
                    break;
                }
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {2, 5, 0, 4, 7, 8, 1};
        insertSort(arr);
        for (int i = 0; i < arr.length; i++) {
            System.out.print(arr[i]);
        }
    }
}
```

# 性能分析

## 稳定性

是稳定的，相同元素在排序后相对位置不发生改变

## 时间复杂度

平均时间复杂度是O(n的次方)

## 空间复杂度

O(1)：只用了额外的一个临时变量

















