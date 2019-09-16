# SelectSort

思想：每次在未排序序列中找到最大（小）值与当前未排序序列第一个数进行交换，直到排序完成（最后一个数都被比较完）

## 代码

```java
public class SelectSort {
    public static void selecctSort(int[] arr) {
        int len = arr.length;
        int temp, index;
        for (int i = 0; i < len - 1; i++) {
            index = i;
            for (int j = i + 1; j < len; j++) {
                if (arr[j] < arr[index]) {
                    // 不用每次都交换比它小的数，只需要设置一个下标，比完这个排序序列后再进行交换。
                    index = j;
                }
            }
            temp = arr[i];
            arr[i] = arr[index];
            arr[index] = temp;
        }
    }

    public static void main(String[] args) {
        int[] array = {1, 9, 0, 4, 6, 5, 1};
        selecctSort(array);
        for (int i = 0; i < array.length; i++) {
            System.out.print(array[i]);
        }
    }
}
```

## 性能分析

稳定性：数组作为被排序列是不稳定的；比如：{5,5,2}；

时间复杂度：O(n次方)

空间复杂度：O(1)，我们用了额外的一个空间作为临时变量









