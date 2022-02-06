- 二重循环获取结果（超时）

~~~ java
public boolean containsNearbyDuplicate(int[] nums, int k) {
    for (int i = 0; i < nums.length-1; i++) {
        for (int j = i+1; j < nums.length; j++) {
            if (nums[i] == nums[j] && j-i <= k) {
                return true;
            }
        }
    }
    return false;
}
~~~

- 哈希表解决

~~~ java
public boolean containsNearbyDuplicate(int[] nums, int k) {
    // map记录<数组索引值, 数组索引>
    HashMap<Integer, Integer> map = new HashMap();
    for (int i = 0; i < nums.length; i++) {
        // 初始化索引值值存在与否
        if (map.get(nums[i]) == null) {
            map.put(nums[i], i);
        } else {
            // 结束情况
            if (i - map.get(nums[i]) <= k) {
                return true;
            } else { // 更新索引
                map.put(nums[i], i);
            }
        }
    }
    return false;
}
~~~

