判断一个integer是否为回文字符串：即121，反着读也是121.

# java 类库实现

```java
public class PalindromeNum {
    public boolean isPalindromeNum(int x) {
        if (x <= 0 || (x % 10 == 0 && x != 0)) {
            return false;
        } else {
            // java 类库实现，和 ReverserInteger 算法一样
            String s = new StringBuilder().append(x).reverse().toString();
            if (s.equals(String.valueOf(x))) {
                return true;
            }
            ////
        }
        return false;
    }

    public static void main(String[] args) {
        PalindromeNum palindromeNum = new PalindromeNum();
        System.out.print(palindromeNum.isPalindromeNum(12321));
    }
}
```

```java
public class PalindromeNum {
    public boolean isPalindromeNum(int x) {
        String str = String.valueOf(x);
        int start = 0;
        int end = str.length() - 1;
        /**********
        while(start < end){
            if(str.charAt(start++) != str.charAt(end--)) return false;
        }
        ************/
        return true;
    }

    public static void main(String[] args) {
        PalindromeNum palindromeNum = new PalindromeNum();
        System.out.print(palindromeNum.isPalindromeNum(2321));
    }
}
```



# 直接方法实现

```java
public class PalindromeNum {
    public boolean isPalindromeNum(int x) {
        if (x <= 0 || (x % 10 == 0 && x != 0)) {
            return false;
        } else {
            int revertedNumber = 0;
            /********** 主要逻辑
            while (x > revertedNumber) {
                revertedNumber = revertedNumber * 10 + x % 10;
                x /= 10;
            }
            return x == revertedNumber || x == revertedNumber / 10;
            ********/
        }
    }

    public static void main(String[] args) {
        PalindromeNum palindromeNum = new PalindromeNum();
        System.out.print(palindromeNum.isPalindromeNum(12321));
    }
}
```

