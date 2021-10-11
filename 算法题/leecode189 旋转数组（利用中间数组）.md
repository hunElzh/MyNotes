### 题目描述

给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。

 

进阶：

- 尽可能想出更多的解决方案，至少有三种不同的方法可以解决这个问题。
- 你可以使用空间复杂度为 O(1) 的 原地 算法解决这个问题吗？



### 我的题解 

1. 利用一个中间数组，将顺序弄正

   ```java
   public static void rotate(int[] nums, int k) {
       //midArray[] 用来存储所有旋转后的字符串
       int[] midArray = new int[20005];
       int l = nums.length;
       k = k % l;
       //将nums[]后k位拷贝至midArray中
       for (int i = 0; i < k; i++) {
           midArray[i] = nums[l-k+i];
       }
       //将nums[]前l-k位拷贝至midArray中
       for (int i = 0; i < l-k; i++) {
           midArray[i+k] = nums[i];
       }
       //将midArray[] 复制 至nums[]
       for (int i = 0; i < l; i++) {
           nums[i] = midArray[i];
       }
   }
   ```

2. 同样是利用中间数组，只不过这次只存之后改动的k位（时间复杂度降低，空间复杂度为啥增多？）

   ```java
   public static void rotate(int[] nums, int k) {
   	int l = nums.length;
   	k = k % l;
   	int[] midArray = new int[k+5];
       for (int i = 0; i < k; i++) {
      		midArray[i] = nums[l-k+i];
       }
       for (int i = 0; i < l-k; i++) {
       	nums[l-1-i] = nums[l-k-1-i];
       }
       for (int i = 0; i < k; i++) {
       	nums[i] = midArray[i];
       }
   }
   ```

### 算法思想

1. 利用中间数组对需要更改的数据进行存储