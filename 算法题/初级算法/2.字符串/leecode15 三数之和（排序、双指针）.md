## 大神题解

1. 特判，如果数组为null或者数组长度小于3返回 []
2. 使数组升序排序
3. 遍历数组，声明双指针。令 `l = i+1`，`r = length - 1`。在此情况下当nums[i] > 0 时即可不用判断

```java
public List<List<Integer>> threeSum(int[] nums) {
    List<List<Integer>> listOut = new ArrayList<>();
    List<Integer> listIn = new ArrayList<>();
    int l, r, n = nums.length;
    //
    if (nums == null || n < 3) {
        return new ArrayList<>();
    }
    Arrays.sort(nums);
    for (int i = 1; i < n - 1; i++) {
        // l = nums[i] + 1
        if (nums[i] > 0) {
            break;
        }
        // 逻辑去重
        if (nums[i] == nums[i-1]) {
            continue;
        }
        l = i + 1;
        r = n - 1;
        while (l < r) {
            if (nums[i] + nums[l] + nums[r] == 0) {
                listIn.add(nums[i]);
                listIn.add(nums[l]);
                listIn.add(nums[r]);
                // 逻辑去重
                while (l < r && nums[l] == nums[l+1]) {
                    l ++;
                }
                while (l < r && nums[r] == nums[r-1]) {
                    r --;
                }
                l ++;
                r --;
            } else if (nums[i] + nums[l] + nums[r] < 0) {
                l ++;
            } else {
                r --;
            }
        }
        listOut.add(listIn);
    }
    return listOut;
}
```