### 题目描述

所有 DNA 都由一系列缩写为 'A'，'C'，'G' 和 'T' 的核苷酸组成，例如："ACGAATTCCG"。在研究 DNA 时，识别 DNA 中的重复序列有时会对研究非常有帮助。

编写一个函数来找出所有目标子串，目标子串的长度为 10，且在 DNA 字符串 s 中出现次数超过一次。



### 我的题解

> 关键，利用set.add()方法的特性，返回true说明重复，并将该元素加入返回的列表中；否则说明不重复，不加入

~~~ java
public List<String> findRepeatedDnaSequences(String s) {
    int i,j;
    int start = 0;
    List<String> allSubString = new ArrayList<>();
    TreeSet<String> treeSet = new TreeSet<>();
    Set<String> set = new HashSet<>();

    //将长度为10的字符串存储至allSubString中
    while (start + 10 <= s.length()) {
        allSubString.add(s.substring(start, start + 10));
        start ++;
    }
	
    //将所有allSubString中的元素加入set中，如果set.add()返回false
    for (i = 0; i < allSubString.size(); i++) {
        if (!set.add(allSubString.get(i))) {
            treeSet.add(allSubString.get(i));
        }
    }

    return new ArrayList<>(treeSet);
}
~~~



### 官方题解

方法一：哈希表

~~~ java
static final int L = 10;
public List<String> findRepeatedDnaSequences(String s) {
    //存储返回的结果
    List<String> ans = new ArrayList<String>();

    //存储当字符，和其对应出现的次数
    Map<String, Integer> cnt = new HashMap<String, Integer>();
    int n = s.length();
    for (int i = 0; i <= n - L; ++i) {
        String sub = s.substring(i, i + L);
        //cnt.getOrDefault(key,value)，当key存在时，返回value；否则返回设置的value
        cnt.put(sub, cnt.getOrDefault(sub, 0) + 1);
        if (cnt.get(sub) == 2) {
            ans.add(sub);
        }
    }
    return ans;
}
~~~



方法二：哈希表+滑动窗口+位运算

~~~ java
static final int L = 10;
Map<Character, Integer> bin = new HashMap<Character, Integer>() {{
    put('A', 0);
    put('C', 1);
    put('G', 2);
    put('T', 3);
}};

public List<String> findRepeatedDnaSequences(String s) {
    List<String> ans = new ArrayList<String>();
    int n = s.length();
    if (n <= L) {
        return ans;
    }
    int x = 0;
    for (int i = 0; i < L - 1; ++i) {
        x = (x << 2) | bin.get(s.charAt(i));
    }
    Map<Integer, Integer> cnt = new HashMap<Integer, Integer>();
    for (int i = 0; i <= n - L; ++i) {
        x = ((x << 2) | bin.get(s.charAt(i + L - 1))) & ((1 << (L * 2)) - 1);
        cnt.put(x, cnt.getOrDefault(x, 0) + 1);
        if (cnt.get(x) == 2) {
            ans.add(s.substring(i, i + L));
        }
    }
    return ans;
}

~~~





