### 题目描述

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。



>示例 1：
>
>输入：nums = [2,7,11,15], target = 9
>输出：[0,1]
>解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
>示例 2：
>
>输入：nums = [3,2,4], target = 6
>输出：[1,2]
>示例 3：
>
>输入：nums = [3,3], target = 6
>输出：[0,1]



>提示：
>
>2 <= nums.length <= 104
>-109 <= nums[i] <= 109
>-109 <= target <= 109
>只会存在一个有效答案
>进阶：你可以想出一个时间复杂度小于 O(n2) 的算法吗？



### 我的题解

> 循环遍历数组
>
> 利用二重循环拿到指定索引，并且为两数之和，返回的数组长度为2即可
>
> 时间复杂度0(n^2)

~~~ java
public static int[] twoSum(int[] nums, int target) {
    if (nums.length == 0) {
        return null;
    }

    for (int i = 0; i < nums.length-1; i++) {
        for (int j = i+1; j < nums.length; j++) {
            if (nums[i] + nums[j] == target) {
                return  new int[]{i,j};
            }
        }
    }

    return null;
}
~~~



### 大神题解

>双指针
>
>本质上也是二重循环的思想，指针i相当于外循环，指针j相当于内循环。
>
>相比于二重循环，双指针排除了很多二重循环中冗余的匹配

~~~java
public static int[] twoSum(int[] nums, int target) {
    int i = 0;
    int j = 1;
    int max_Area = nums.length - 1;
    //相当于一个内循环
    while(nums[i] + nums[j] != target) {
        //这样做i始终递增，不会一直从0开始增加
        if (j == max_Area) {
            i++;
            j = i;
        }
        j++;
    }

    return null;
}
~~~



>哈希表
>
>target - 结果 = 另一个结果
>
>哈希表key存索引值，value存索引，当能获取到target-num[i]的值的话，说明结果曾被存储到过哈希表中

~~~ java
public static int[] twoSum(int[] nums, int target) {
    //  索引值  , 索引
    Map<Integer, Integer> m = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        // m.get(target - nums[i]) != null
        // target - nums[i] = 结果，如果获取到这个结果的key说明结果曾被加入至哈希表中，获取到的结果的索引和当前的索引既是最终答案
        if (m.get(target - nums[i]) != null) {
            return new int[]{m.get(target - nums[i]), i};
        }
        m.put(nums[i], i);
    }
    //没有结果的情况
    return new int[]{0, 0};
}
~~~



