### 题目描述

统计字符串中的单词个数，这里的单词指的是连续的不是空格的字符。

请注意，你可以假定字符串里不包括任何不可打印的字符。



### 我的题解

```java
public static int countSegments(String s) {

    int result;

    //将字符串trim然后以" "作为分隔
    String[] s1 = s.trim().split(" ");

    //特殊情况"    "
    if (s1[0].equals("")) {
        return 0;
    }

    result = s1.length;

    //遍历一遍后将空字符过滤
    for (String c : s1) {
        if (c.equals("")) {
            result--;
        }
    }

    return result;
}
```



### 官方题解

> 一重循环碰到非空字符就++


```java
public int countSegments(String s) {
	int segmentCount = 0;

    for (int i = 0; i < s.length(); i++) {
        //当当前字符前一个字符是空格，并且当前字符不是空格字符总数++
        if ((i == 0 || s.charAt(i - 1) == ' ') && s.charAt(i) != ' ') {
            segmentCount++;
        }
    }

    return segmentCount;
}
```

