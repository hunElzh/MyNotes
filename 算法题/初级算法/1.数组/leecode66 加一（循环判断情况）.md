###  题目描述

给定一个由 整数 组成的 非空 数组所表示的非负整数，在该数的基础上加一。

最高位数字存放在数组的首位， 数组中每个元素只存储单个数字。

你可以假设除了整数 0 之外，这个整数不会以零开头。

>示例 1：
>
>输入：digits = [1,2,3]
>输出：[1,2,4]
>解释：输入数组表示数字 123。
>
>示例 2：
>
>输入：digits = [4,3,2,1]
>输出：[4,3,2,2]
>解释：输入数组表示数字 4321。
>
>示例 3：
>
>输入：digits = [0]
>输出：[1]



### 我的题解

~~~ java
public static final int MAX_LEN = 101;
public static int[] plusOne(int[] digits) {
    int cur = MAX_LEN;

    //令cur等于9后缀中的第一位
    for (int i = digits.length-1; i >= 0; i--) {
        if (digits[i] == 9) {
            cur = i;
        } else {
            break;
        }
    }
	
    //判断情况返回对应数组
    if (cur == MAX_LEN) {
        digits[digits.length-1] += 1;
        return digits;
    } else if (cur == 0) {
        int[] newArr = new int[digits.length+1];
        newArr[0] = 1;
        return newArr;
    } else {
        digits[cur-1] += 1;
        for (int i = cur; i < digits.length; i++) {
            digits[i] = 0;
        }
        return digits;
    }
}
~~~

