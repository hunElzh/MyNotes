### 题目描述

你总共有 n 枚硬币，并计划将它们按阶梯状排列。对于一个由 k 行组成的阶梯，其第 i 行必须正好有 i 枚硬币。阶梯的最后一行 可能 是不完整的。

给你一个数字 n ，计算并返回可形成 完整阶梯行 的总行数。



将其想象成为阶梯形状

```
输入：n = 5
输出：2
解释：因为第三行不完整，所以返回 2 。
```

```
输入：n = 8
输出：3
解释：因为第四行不完整，所以返回 3 。
```



### 我的题解

> 我所用的是最基本的迭代方式，时间复杂度为O(n)，并且不够简洁（这里使用的是long，因为最后一个测试用例用int类型会溢出）

```java
public static int arrangeCoins(int n) {
    //当前硬币总数
    long fullLayerCoin = 0;
    //当前层数
    long layer = 0L;
    //当前层硬币数
    long curLayerCoin = 0;

    while (fullLayerCoin <= n) {
        if (fullLayerCoin == n) {
            return (int) layer;
        } else {
            curLayerCoin++;
            layer++;
            fullLayerCoin += curLayerCoin;
        }
    }
    return (int) (layer - 1);
}
```



### 官方、大神题解

1. 二分查找

   > 根据等差数列求和公式，前 k 个完整接替所需的硬币数量为  
   >
   > *total*=*k*×(*k*+1)/2，
   >
   > 因此可以通过二分查找计算n枚硬币可形成的完整阶梯行的总行数，接下来就只需要判断最后一行是否被硬币填满
   >
   > 时间复杂度为 O(log(n / 2 + 1))

   ```java
   public static int arrangeCoins(int n) {
       int left = 1;
       int right = n;
   
       while (left < right) {
           int mid = (right - left + 1)/2 + left;
           if ((long)mid*(mid+1) <= (long)2*n) {
               left = mid;
           } else {
               right = mid - 1;
           }
       }
       return left;
   }
   ```

2. 迭代

   > 即累加层数，到达一定条件时返回结果
   >
   > 时间复杂度为 O(n)

   ```java
    public static int arrangeCoins2(int n) {
        int i = 1;
        while (n >= i) {
            n -= i;
            i++;
        }
        return i - 1;
    }
   ```

3. 数学公式

   > 这是啥公式？可恶啊，数学公式作弊！属于旁门左道
   >
   > 时间复杂度为 O(1)

   ```java
   return (int) (-1 + Math.sqrt(1 + 8 * (long) n)) / 2;
   ```