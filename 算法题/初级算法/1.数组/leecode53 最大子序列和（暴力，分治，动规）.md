### 题解

1. 暴力+前缀和

时间：O(n)

空间：O(1)

~~~ java
public int maxSubArray(int[] nums) {
    int maxSum = Integer.MIN_VALUE;
    int sum;
    for (int i = 0; i < nums.length; i++) {
        sum = 0;
        for (int j = i; j < nums.length; j++) {
            sum += nums[j];
            // 每次相加后都进行一次比较，巧妙啊
            maxSum = Math.max(sum, maxSum);
        }
    }
    return maxSum;
}
~~~



2. 优化前缀和

时间：O(n)

空间：O(1)

使用一个变量minSum来记录  `Min（之前最小前缀和，当前最小前缀和）`，使用变量maxSum记录  `Max（之前最大前缀和，（当前前缀和 — 最小前缀和））`

太tm巧妙了

~~~ java
public int maxSubArray3(int[] nums) {
    int maxSum = nums[0];
    int sum = 0;
    int minSum = 0;
    for (int num : nums) {
        // prefix Sum
        sum += num;
        // update maxSum
        maxSum = Math.max(maxSum, sum - minSum);
        // update minSum
        minSum = Math.min(minSum, sum);
    }
    return maxSum;
}
~~~



3. 动态规划
   - dp[i]：当前位置 i 的最大子序列和
   - 状态转移方程：`dp[i] = max(dp[i-1] + nums[i], nums[i]);`
   - 初始化dp[0] = nums[0]
   - 输出 `max(sumMax, dp[i])`：sumMax 记录状态转移过程中的最大值，在本体中即为输出结果

~~~ java
public int maxSubArray(int[] nums) {
    if (nums.length == 0) {
        return nums[0];
    }
    int curMaxSum = nums[0];  // 最大前缀和
    int maxSum = nums[0];   // 用maxSum记录curMaxSum动态过程中的最大值
    for (int i = 1; i < nums.length; i++) {
        curMaxSum = Math.max(curMaxSum + nums[i], nums[i]);
        maxSum = Math.max(curMaxSum, maxSum);
    }
    return maxSum;
}
~~~

