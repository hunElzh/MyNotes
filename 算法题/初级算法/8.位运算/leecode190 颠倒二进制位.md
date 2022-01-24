看题解非自解

- 逐位颠倒

~~~ java
public int reverseBits(int n) {
    int a = 0;
    for(int i=0;i<=31;i++){
        a=a+((1&(n>>i))<<(31-i));
    }
    return a;
}
~~~

- 分治

  大佬解释

  - 原数据为:12345678
  - 第一轮 奇偶位交换 21436587
  - 第二轮 每两位交换 43218765
  - 第三轮 每四位交换 87654321

~~~ java
private static final int M1 = 0x55555555; // 01010101010101010101010101010101
private static final int M2 = 0x33333333; // 00110011001100110011001100110011
private static final int M4 = 0x0f0f0f0f; // 00001111000011110000111100001111
private static final int M8 = 0x00ff00ff; // 00000000111111110000000011111111

public int reverseBits(int n) {
    n = n >>> 1 & M1 | (n & M1) << 1;
    n = n >>> 2 & M2 | (n & M2) << 2;
    n = n >>> 4 & M4 | (n & M4) << 4;
    n = n >>> 8 & M8 | (n & M8) << 8;
    return n >>> 16 | n << 16;
}
~~~

