- 位运算

利用 a^a = 0的特性去解此题

~~~ java
public int singleNumber(int[] nums) {
    int ans = nums[0];
    for (int i = 1; i < nums.length; i++) {
        ans ^= nums[i];
    }
    return ans;
}
~~~

