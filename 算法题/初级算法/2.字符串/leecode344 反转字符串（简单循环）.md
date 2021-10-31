### 题目描述

编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 s 的形式给出。

不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

>编写一个函数，其作用是将输入的字符串反转过来。输入字符串以字符数组 s 的形式给出。
>
>不要给另外的数组分配额外的空间，你必须原地修改输入数组、使用 O(1) 的额外空间解决这一问题。

>**提示：**
>
>- `1 <= s.length <= 105`
>- `s[i]` 都是 [ASCII](https://baike.baidu.com/item/ASCII) 码表中的可打印字符



### 我的题解

> 这一题很简单，但是要注意内存问题
>
> 方法一内存消耗比方法二低很多，因为不用每次循环都创建一次temp

方法一

~~~ java
public static void reverseString(char[] s) {
    int len = s.length;
    char temp;
    for (int i = 0; i < s.length/2; i++) {
        temp = s[i];
        s[i] = s[len-1-i];
        s[len-1-i] = temp;
    }
}
~~~



方法二

~~~ java
public static void reverseString(char[] s) {
    int len = s.length;
    for (int i = 0; i < s.length/2; i++) {
        char temp = s[i];
        s[i] = s[len-1-i];
        s[len-1-i] = temp;
    }
}
~~~

