### 题目描述

给定一个 正整数 num ，编写一个函数，如果 num 是一个完全平方数，则返回 true ，否则返回 false 。

进阶：不要 使用任何内置的库函数，如  sqrt。



### 大神题解

> 利用 (n+1)^2 - n^2 = 2n - 1 这个规律，如果一个数是完全完全平方数，减基数一定能等于0

~~~ java
public boolean isPerfectSquare(int num) {
    int i = 1;
    while (num > 0) {
        num -= i;
        i += 2;
    }

    if (num == 0) {
        return true;
    } else {
        return false;
    }
}
~~~

