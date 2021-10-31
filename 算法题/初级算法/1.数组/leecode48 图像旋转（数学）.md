### 题目描述

给定一个 n × n 的二维矩阵 matrix 表示一个图像。请你将图像顺时针旋转 90 度。

你必须在 **原地** 旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要 使用另一个矩阵来旋转图像。

![UNT395YD0TUJZD](..\..\..\img\UNT395YD0TUJZD.png)

>提示：
>
>matrix.length == n
>matrix[i].length == n
>1 <= n <= 20
>-1000 <= matrix[i][j] <= 1000



### 我的题解

> 如果允许用数组复制，那么题目将简单很多，找到旋转前后各元素对应关系即可

~~~ java
public static void rotate(int[][] matrix) {
    if (matrix.length == 1) {
        return;
    }
    int len = matrix.length;
    int[][] res = new int[len][len];
    for (int i = 0; i < matrix.length; i++) {
        for (int j = 0; j < matrix[i].length; j++) {
            res[j][len-i-1] = matrix[i][j];
        }
    }
    System.out.println(Arrays.toString(res[0]));
    System.out.println(Arrays.toString(res[1]));
    System.out.println(Arrays.toString(res[2]));
}
~~~



### 大神题解

#### 方案一

> 先上下交换，再对角线交换

![1615651665-WFwYuH-leet0048](..\..\..\img\1615651665-WFwYuH-leet0048.png)

~~~ java
public static void rotate(int[][] matrix) {
    int len = matrix.length;
    //先上下交换，len/2是因为上下只需要交换一半
    for (int i = 0; i < len/2; i++) {
        int[] tmp = matrix[i];
        matrix[i] = matrix[len-i-1];
        matrix[len-i-1] = tmp;
    }
    //再对角交换
    for (int i = 0; i < len; i++) {
        for (int j = i + 1; j < len; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    }
}
~~~



#### 方案二

> 根据对称的规律交换

![1615652398-MpQCOV-幻灯片2](..\..\..\img\1615652398-MpQCOV-幻灯片2.png)

~~~ java
public void rotate(int[][] matrix) {
    int length = matrix.length;
    //因为是对称的，只需要计算循环前半行即可
    for (int i = 0; i < length / 2; i++)
        for (int j = i; j < length - i - 1; j++) {
            int temp = matrix[i][j];
            int m = length - j - 1;
            int n = length - i - 1;
            matrix[i][j] = matrix[m][i];
            matrix[m][i] = matrix[n][m];
            matrix[n][m] = matrix[j][n];
            matrix[j][n] = temp;
        }
}
~~~













