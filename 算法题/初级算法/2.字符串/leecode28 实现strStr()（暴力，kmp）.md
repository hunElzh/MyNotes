### 题目描述

>实现 strStr() 函数。
>
>给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串出现的第一个位置（下标从 0 开始）。如果不存在，则返回  -1 。

>说明：
>
>当 needle 是空字符串时，我们应当返回什么值呢？这是一个在面试中很好的问题。
>
>对于本题而言，当 needle 是空字符串时我们应当返回 0 。这与 C 语言的 strstr() 以及 Java 的 indexOf() 定义相符。
>



### 我的题解（本地可行，LC上不通过）

> 暴力解法，双重循环

```java
public static int strStr(String haystack, String needle) {
    //result记录字符串中第一个索引坐标
    int result = -1;

    //特殊情况返回
    if (haystack == "" && needle == "") {
        return 0;
    }

    for (int i = 0; i < haystack.length(); i++) {
        for (int j = 0; j < needle.length(); j++) {
            if (haystack.charAt(i) == needle.charAt(0)) {
                result = i;
            }
            //当子串与母串存在不等，并且子串长度超过剩余母串时，重置索引，跳过循环
            if (haystack.charAt(i+j) != needle.charAt(j) || (i+needle.length()) > haystack.length()) {
                result = -1;
                break;
            }
        }
        //当与子串匹配上后，返回结果
        if (result != -1) {
            return result;
        }
    }


    return result;
}
```



### 官方题解

> 暴力解法

~~~ java
public int strStr(String haystack, String needle) {
    int n = haystack.length(), m = needle.length();
    //i+m<=n，字串长度长于剩余母串时出现的情况
    for (int i = 0; i + m <= n; i++) {
        boolean flag = true;
        for (int j = 0; j < m; j++) {
            if (haystack.charAt(i + j) != needle.charAt(j)) {
                flag = false;
                break;
            }
        }
        if (flag) {
            return i;
        }
    }
    return -1;
}
~~~



