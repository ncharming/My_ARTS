# 快速排序

## 核心思想

> 冒泡 + 二分 + 递归

>1.取一个序列的第一个数作为基准数
>
>2.将第一个数下标置为i；最后一个数下标置为j分别向中间靠近
>
>3.当i==j后，递归循环两边的数

## 完整代码

```java
public class QuickSort {

   static void quickSort(int s[], int l, int r) {
        if (l < r) { //不能使用 while ，否则死循环
            /* 注意事项：
            1.* while：当语句执行完，自动返回继续执行判断
              * if：只判断当前一次
            2.* 开始判断交换位置的时候，必须从所选基准位的对面位开始
              * 比如，我这里选第一位作为基准位，那我从最右面开始查找
              * 
            */
            int i = l, j = r, x = s[l];
            while (i < j) { //必须从这里开始
                while (i < j && s[j] >= x) // 从右向左找第一个小于x的数
                    j--;
                if (i < j)
                    s[i++] = s[j];

                while (i < j && s[i] < x) // 从左向右找第一个大于等于x的数
                    i++;
                if (i < j)
                    s[j--] = s[i];
            }
            s[i] = x;
            quickSort(s, l, i - 1); // 递归调用
            quickSort(s, i + 1, r);
        }
    }
    public static void main(String[] args) {
        int[] array = {6, 2, 7, 0, 3, 5, 8, 9, 4};
        quickSort(array, 0, array.length - 1);

        for(int i=0;i<array.length;i++){
            System.out.print(array[i]);
        }
    }
```

## 效率分析

### 复杂度分析

平均时间复杂度：O(nlogn)

空间复杂度：由于递归调用：O(logn)

### 稳定性分析

**不稳定排序**，两个相同的数，在排序后相对位置可能发生改变；比如6,2,3,3,7；