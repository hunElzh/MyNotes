### 题目描述

> 给你一个非空数组，返回此数组中 第三大的数 。如果不存在，则返回数组中最大的数。



### 我的题解

时间复杂度为冒泡排序的时间复杂度O(n^2)

~~~ java
public static int thirdMax(int[] array) {

    int result = array[0];

    int temp;

    int flag = 1;

    //当array的长度等于1时，直接返回这个数；当array的长度等于2时，返回两个中的最大；当array的长度大于2，将数组排序，并输出第三大的数
    if (array.length == 1) {
        return result;
    } else if (array.length < 3) {
        for (int i = 1; i < array.length; i++) {
            if (array[i] > result) {
                result = array[i];
            }
        }
        return result;
    } else {
        //当数组长度大于3时降序排序数组
        for (int i = 0; i < array.length - 1; i++) {
            for (int j = i + 1; j < array.length; j++) {
                if (array[i] < array[j]) {
                    temp = array[i];
                    array[i] = array[j];
                    array[j] = temp;
                }
            }
        }
        //输出第三大的数据
        for (int i = 0; i < array.length; i++) {
            if (i == 0) {
                result = array[i];
            } else {
                if (array[i] == array[i-1]) {
                    continue;
                } else {
                    result = array[i];
                    flag++;
                }
            }
            //如果存在第三大的数，则返回，否则返回最大的数
            if (flag == 3) {
                return result;
            }
        }
        if (flag != 3) {
            result = array[0];
        }
    }
    return result;
}
~~~



## 大神题解

时间复杂度为O(n)

知识点，巧妙运用TreeSet这个数据结构

TreeSet升序存储不重复的数据

```java
public static int thirdMax(int[] nums){
    /*
      关键点: TreeSet可以将添加的元素自动排序(由小到大),同时去除重复的元素
        思路
           将数组的元素复制到TreeSet集合中,去除重复且排好序
           将集合转换为数组,取集合倒数第三个数
    */
    Set<Integer> set = new TreeSet<>();
    if(nums.length==1){
        return nums[0];
    }else if(nums.length==2){
        return nums[0]>nums[1]?nums[0]:nums[1];
    }else{
        for (Integer x: nums) {
            set.add(x);
        }
        Object[] arr = set.toArray();
        //返回第三个元素
        if(set.size()==1){
            return (Integer) arr[0];
        }
        if(set.size()==2){
            return (Integer) arr[1];
        }
        return (Integer) arr[arr.length-3];
    }
}
```