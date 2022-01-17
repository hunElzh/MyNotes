1. 直接排序后合并

~~~ java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    for (int i = m; i < m + n; i++) {
        nums1[i] = nums2[i-m];
    }
    Arrays.sort(nums1);
}
~~~

2. 双指针

~~~ java
public void merge(int[] nums1, int m, int[] nums2, int n) {
    int[] ints = new int[m+n];
    int i = 0, j = 0;
    for (int index = 0; index < m + n; index++) {
        // i==m; j==n 为边界条件
        if (i == m) {
            ints[index] = nums2[j];
            j++;
        } else if (j == n) {
            ints[index] = nums1[i];
            i++;
        } else if (nums1[i] <= nums2[j]) {
            ints[index] = nums1[i];
            i++;
        } else {
            ints[index] = nums2[j];
            j++;
        }
    }
}
~~~

