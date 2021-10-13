### 题目描述

 给定两个数组，编写一个函数来计算它们的交集。 

>示例 1：
>
>输入：nums1 = [1,2,2,1], nums2 = [2,2]
>输出：[2,2]
>示例 2:
>
>输入：nums1 = [4,9,5], nums2 = [9,4,9,8,4]
>输出：[4,9]



>说明：
>
>输出结果中每个元素出现的次数，应与元素在两个数组中出现次数的最小值一致。
>我们可以不考虑输出结果的顺序。
>进阶：
>
>如果给定的数组已经排好序呢？你将如何优化你的算法？
>如果 nums1 的大小比 nums2 小很多，哪种方法更优？
>如果 nums2 的元素存储在磁盘上，内存是有限的，并且你不能一次加载所有的元素到内存中，你该怎么办？



### 我的题解（理解错题目意思）

> 我以为是要得到两个数组之中相同且连续的集合

~~~ java
public static int[] intersect(int[] nums1, int[] nums2) {

    int i1 = 0, i2 = 0;

    int start = 0, len = 0;

    //获取第一次相同时数组下标索引
    for (int i = 0; i < nums1.length; i++) {
        for (int j = 0; j < nums2.length; j++) {
            if (nums1[i] == nums2[j]) {
                i1 = i;
                i2 = j;
                break;
            }
        }
        if (nums1[i1] == nums2[i2]) break;
    }

    start = i1;

    while (i1 < nums1.length) {
        if (i2 >= nums2.length) {
            break;
        }
        if (nums1[i1++] == nums2[i2++]) {
            len++;
            continue;
        } else {
            break;
        }
    }

    int[] result = getNewArr(nums1, start, len);

    return result;
}

public static int[] getNewArr(int[] nums, int start, int len) {
    int[] newArr = new int[len];
    for (int i = 0; i < len; i++) {
        newArr[i] = nums[i+start];
    }
    return newArr;
}
~~~





### 官方题解

#### 双指针

> 先对两个数组进行排序，然后使用两个指针，分别指向两个数组开始的位置。
>
> 如果两个指针指向的值相同，说明这个值是他们的交集，就把这个值加入到集合list中，然后两个指针在分别往后移一步。
> 如果两个指针指向的值不同，那么指向的值相对小的往后移一步，相对大的先不动，然后再比较

~~~ java
public int[] intersect(int[] nums1, int[] nums2) {
    List<Integer> list = new ArrayList<>();

    Arrays.sort(nums1);
    Arrays.sort(nums2);

    int i = 0;
    int j = 0;

    while (i < nums1.length && j < nums2.length) {
        if (nums1[i] < nums2[j]) {
            // 如果i指向的值小于j指向的值，，说明i指向
            // 的值小了，i往后移一步
            i++;
        } else if (nums1[i] > nums2[j]) {
            // 如果i指向的值大于j指向的值，说明j指向的值
            // 小了，j往后移一步
            j++;
        } else {
            // 如果i和j指向的值相同，说明这两个值是重复的，
            // 把他加入到集合list中，然后i和j同时都往后移一步
            list.add(nums1[i]);
            i++;
            j++;
        }
    }

    int[] res = new int[list.size()];
    for (int k = 0; k < res.length; k++) {
        res[k] = list.get(k);
    }
    return res;
}
~~~

### 还能使用哈希表，只是累了，不想写了

