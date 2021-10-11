### 题目描述

给定一个整数数组，判断是否存在重复元素。

如果存在一值在数组中出现至少两次，函数返回 `true` 。如果数组中每个元素都不相同，则返回 `false` 。

>示例 1:
>
>输入: [1,2,3,1]
>输出: true
>示例 2:
>
>输入: [1,2,3,4]
>输出: false
>示例 3:
>
>输入: [1,1,1,3,3,4,3,2,4,2]
>输出: true



### 我的题解

> 利用 HashSet 这一数据结构，当重复元素加入 HashSet 时返回 false，说明出现重复元素

~~~ java
public static boolean containsDuplicate(int[] nums) {
    Set<Integer> set = new HashSet<>();
    for (int curNum : nums) {
        if (set.add(curNum) == false) {
            return true;
        }
    }
    return false;
}
~~~

