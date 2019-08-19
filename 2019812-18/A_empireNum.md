# 吸血鬼数字

《Thinking in Java》

四位数字可以有两个两位数相乘得到，并且这两个两位数包含于这个四位数：

如：21*60=1260

## 直接方法

将这个四位数拆分成 4 个数字进行组合；效率低下

```java
public class EmpireNum {
    /*
     * 将四位数字拆成一位一位的放到数组里面
     * */
    public int[] numToArray(int num) {
        int[] array = new int[4];
        int i = 0;
        while (num != 0) {
            array[i++] = num % 10;
            num = num / 10;
        }
        return array;
    }

    /*
     * 随机拼两位数相乘 等于 给定的四位数即满足
     * */
    public boolean equal(int[] a, int num) {
        for (int i = 0; i < 4; i++) {
            for (int j = 0; j < 4; j++) {
                if (i == j) {  //比较关键
                    continue;
                } else {
                    if (6 - i - j == 5) {//i,j=0或1
                        if ((a[i] * 10 + a[j]) * (a[3] * 10 + a[2]) == num || (a[i] + a[j] * 10) * (a[3] + a[2] * 10) == num) {
                            return true;
                        }
                    } else if (6 - i - j == 4) {//i,j=0或2；i=j=1时（最开始就有限制）
                        if ((a[i] * 10 + a[j]) * (a[3] * 10 + a[1]) == num || (a[i] + a[j] * 10) * (a[3] + a[1] * 10) == num) {
                            return true;
                        }
                    } else if (6 - i - j == 3 && (i == 3 || j == 3)) {//i,j=0或3；
                        if ((a[i] * 10 + a[j]) * (a[1] * 10 + a[2]) == num || (a[i] + a[j] * 10) * (a[1] + a[2] * 10) == num) {
                            return true;
                        }
                    } else if (6 - i - j == 3 && (i == 1 || j == 1)) {//i,j=1或2；
                        if ((a[i] * 10 + a[j]) * (a[0] * 10 + a[3]) == num || (a[i] + a[j] * 10) * (a[0] + a[3] * 10) == num) {
                            return true;
                        }
                    } else if (6 - i - j == 2) {//i,j=1或3；
                        if ((a[i] * 10 + a[j]) * (a[0] * 10 + a[2]) == num || (a[i] + a[j] * 10) * (a[0] + a[2] * 10) == num) {
                            return true;
                        }
                    } else if (6 - i - j == 1) {//i,j=2或3；
                        if ((a[i] * 10 + a[j]) * (a[1] * 10 + a[0]) == num || (a[i] + a[j] * 10) * (a[1] + a[0] * 10) == num) {
                            return true;
                        }
                    }
                }
            }
        }
        return false;
    }

    public static void main(String[] args) {

        EmpireNum empireNum = new EmpireNum();
        int count = 0;
        for (int i = 1001; i <= 9999; i++) {
            int[] result = empireNum.numToArray(i);
            if (result[2] != 0 && result[3] != 0) {
                if (empireNum.equal(result, i)) {
                    System.out.println(i);
                }
            }
        }
        System.out.println("count:" + count);
    }
}
```

## 将数字分成两个两位的字符串



```java
public class EmpireNum2 {
    public static void main(String[] args) {
        String[] str1 = null;
        String[] str2 = null;
        int count = 0;
        int sum = 0;

        for(int i=10;i<=99;i++) {
            for (int j = i+1; j <= 99; j++) {
                if (i%10==0 && j%10==0) {
                    continue;
                }//自己添加的代码，少运行几十次
                int val_1 = i * j;
                if (val_1 < 1000 || val_1 > 9999) {
                    continue;
                }
                count++;
                str1 = String.valueOf(val_1).split("");
                str2 = (String.valueOf(i) + String.valueOf(j)).split("");
                Arrays.sort(str1);//返回类型是 void
                Arrays.sort(str2);
                if (Arrays.equals(str1, str2)) {
                    /* 这个方法里面是对两个数组中的每一位进行对比
                    *（debug时，调用的是 String 的 equals方法）
                    * */
                    sum++;
                    System.out.println(i + "*" + j + "=" + val_1);
                }
            }
        }
        System.out.print("几个:"+sum+","+"循环多少次："+count);
    }
}
```

