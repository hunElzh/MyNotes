### 题目描述

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。 

>**示例:**
>
>```
>输入: [0,1,0,3,12]
>输出: [1,3,12,0,0]
>```
>
>
>
>**说明**:
>
>1. 必须在原数组上操作，不能拷贝额外的数组。
>2. 尽量减少操作次数。



### 我的题解

> 思路：firstZeroIndex永远指向第一个0，当前元素在第一个0索引之后且当前元素不为零，与firstZeroIndex索引处元素进行交换

~~~ java
public static void moveZeroes(int[] nums) {
    //标记数组中第一个0的位置
    int firstZeroIndex = 0;
    int cur = 0;
    while (cur < nums.length) {
        while (nums[firstZeroIndex] != 0) {
            firstZeroIndex++;
            //没有0的情况，firstZeroIndex会一直加1，如果不加条件就会超出限制
            if (firstZeroIndex == nums.length) return;
        }

        //当前数组索引下的数不为0，并且当前数组下标大于firstZeroIndex时，与数组中firstZeroIndex交换元素
        if (nums[cur] != 0 && cur >= firstZeroIndex) {
            int temp = nums[cur];
            nums[cur] = nums[firstZeroIndex];
            nums[firstZeroIndex] = temp;
        }

        cur++;
    }

}
~~~



### 大神题解

> 遍历数组，将所有非0元素前移，每移动一次index++

~~~ java
public static void moveZeroes(int[] nums) {
    if (nums == null || nums.length == 0)
        return;
    //index最终记录排序后的数组中非零元素的后一个索引
    int index = 0;
    //一次遍历，把非零的都往前挪
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != 0)
            nums[index++] = nums[i];
    }
    //后面的都是0,
    while (index < nums.length) {
        nums[index++] = 0;
    }
}
~~~

