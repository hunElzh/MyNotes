### 题目描述

给定一个整数数组 `nums`，其中恰好有两个元素只出现一次，其余所有元素均出现两次。 找出只出现一次的那两个元素。你可以按 **任意顺序** 返回答案。

>示例 1：
>
>输入：nums = [1,2,1,3,2,5]
>输出：[3,5]
>解释：[5, 3] 也是有效的答案。
>示例 2：
>
>输入：nums = [-1,0]
>输出：[-1,0]
>示例 3：
>
>输入：nums = [0,1]
>输出：[1,0]
>提示：



>2 <= nums.length <= 3 * 104
>-231 <= nums[i] <= 231 - 1
>除两个只出现一次的整数外，nums 中的其他数字都出现两次



### 我的题解

~~~ java
public int[] singleNumber(int[] nums) {
    int i;
    int j = 0;
    Set<Integer> set = new HashSet<>();
    for (i = 0; i < nums.length; i++) {
        if (set.add(nums[i]) == false) {
            set.remove(nums[i]);
        }
    }

    int[] result = new int[set.size()];

    for (int s : set) {
        result[j++] = s;
    }

    return result;

}
~~~



### 进阶

你的算法应该具有线性时间复杂度。你能否仅使用常数空间复杂度来实现？