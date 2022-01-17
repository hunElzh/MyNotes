- 哈希表

~~~ java
public int majorityElement(int[] nums) {
    Map<Integer, Integer> map = new HashMap<>();
    int max = 0;
    // 记录每个元素出现的次数
    for (int i = 0; i < nums.length; i++) {
        map.put(nums[i], map.getOrDefault(nums[i], 0) + 1);
    }
    // 找到出现最多的次数
    for (int i = 0; i < nums.length; i++) {
        max = Math.max(map.get(nums[i]), max);
    }
    // 根据出现最多的次数返回对于元素
    for (int i = 0; i < nums.length; i++) {
        if (map.get(i) == max) {
            return nums[i];
        }
    }
    return 0;
}
~~~

- 排序，排序之后的中位数一定是结果

- 投票算法：遇到相同的数就投一票，遇到不同的数就减一票。

  使用用例[1,1,2,2,2]调试一遍即可理解

  ~~~ java
  public int majorityElement(int[] nums) {
      int count = 0;
      // 记录一个候选人
      Integer candidate = null;
  
      for (int num : nums) {
          // 此题众数数量一定大于 nums.length / 2，因此就算前面都不是候选人，到后面还是会归于0
          if (count == 0) {
              candidate = num;
          }
          // 当num不为候选人时，count += -1；当num为候选人时，count += 1；
          count += (num == candidate) ? 1 : -1;
      }
  
      return candidate;
  }
  ~~~

  