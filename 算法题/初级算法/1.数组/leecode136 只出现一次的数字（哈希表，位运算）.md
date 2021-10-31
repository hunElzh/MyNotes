### 题目描述

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

说明：

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？



### 我的题解（同解决剑指Ⅱ004，题名相同，重复元素由2变3）

> 运用哈希表解决问题，消耗时间空间都太多了

~~~ java
public static int singleNumber(int[] nums) {
    Map<Integer, Integer> map = new HashMap();
    for (int i = 0; i < nums.length; i++) {
        if (map.put(nums[i], 1) != null) {
            int curKey = nums[i];
            int curValue = map.get(curKey);
            curValue++;
            map.put(curKey, curValue);
        }
    }
    Set<Integer> integers = map.keySet();
    for (int i : integers) {
        if (map.get(i) == 1) {
            return i;
        }
    }
    return 0;
}
~~~



### 大神题解

> 位运算思路，利用异或解决当前问题

异或运算特性

- 相异为真，相同为假
  - a ^ a = 0
  - 0 ^ a = a
- 异或运算满足交换律
  - a ^ b ^ a = b ^ a ^ a = b

至此思路就很明确了，只需要定义一个初始的0，然后异或遍历数组即可得出题解

~~~ java
public static int singleNumber(int[] nums) {
    int reduce = 0;
    for (int num : nums) {
        reduce = reduce ^ num;
    }
    return reduce;
}
~~~

