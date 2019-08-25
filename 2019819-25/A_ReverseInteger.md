# Reverse Integer 

[LeetCode](https://leetcode.com/problems/reverse-integer/solution/)

## Example

Given a 32-bit signed integer,reverse digits of an integer.

123->321

-123->-321

120->21

## Note

假设我们只在一个平台上有 32 位的有符号的 int 型数字，范围是 [-2的31次方--2的31次方-1]，当超出范围返回 0。

## Code

### 直接方法

```java
public class ReverseInteger {
    public int reverse(int n) {
        int mid = 0;
        int result = 0;
        while (n != 0) {
            mid = n % 10;
            n = n / 10;
            if (result > Integer.MAX_VALUE/10 || (result == Integer.MAX_VALUE / 10 && mid > 7)) return 0;
            if (result < Integer.MIN_VALUE/10 || (result == Integer.MIN_VALUE / 10 && mid < -8)) return 0;
            result = result * 10 + mid;
        }
        return result;
    }

    public static void main(String[] args) {
        ReverseInteger reverseInteger = new ReverseInteger();
        Scanner scanner = new Scanner(System.in);
        int m = scanner.nextInt();
        int n = reverseInteger.reverse(m);
        System.out.println("reverse:" + n);
    }
}
```

>if (result > Integer.MAX_VALUE/10 || (result == Integer.MAX_VALUE / 10 && mid > 7)) return 0;
>if (result < Integer.MIN_VALUE/10 || (result == Integer.MIN_VALUE / 10 && mid < -8)) return 0;

为什么是这个判断条件：

>Integer.MAX_VALUE = 2,147,483,647
>
>Integer.MIN_VALUE = -2,147,483,647

当 result =  2,147,483,640 时并且 mid 大于 7 就超出范围了

同理，mid < -8

### java 库方法

```java
public int reverseInteger(int n) {
    int m = 0;
    // StringBuffer 也有这个方法
    String stringBuilder = new StringBuilder().append(Math.abs(n)).reverse().toString();
    try {
        m = (n < 0) ? Integer.parseInt(stringBuilder) * -1 : Integer.parseInt(stringBuilder);
    } catch (Exception e) {
        e.printStackTrace();
    }
    return m;
}
```

