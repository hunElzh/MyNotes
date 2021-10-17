### 题目描述

> 给定一个字符串，找到它的第一个不重复的字符，并返回它的索引。如果不存在，则返回 -1。

> 示例：
>
> s = "leetcode"
> 返回 0
>
> s = "loveleetcode"
> 返回 2



### 我的题解

> 用哈希表存储数组频率

```java
public static int firstUniqChar(String s) {
    Map<Character, Integer> map = new HashMap<>();
    char[] chars = s.toCharArray();

    //当出现一次对应字符
    for (char c : chars) {
        if (map.get(c) == null) {
            map.put(c, 1);
            Integer integer = map.get(c);
        } else {
            map.put(c, map.get(c)+1);
        }
    }

    for (int i = 0; i < chars.length; i++) {
        if (map.get(chars[i]) == 1) {
            return i;
        }
    }

    return -1;
}
```



### 官方题解

> 用哈希表存储数组频率，与我的题解不同的是 frequency.put(ch, frequency.getOrDefault(ch, 0)+1)

~~~ java
public int firstUniqChar(String s) {
    Map<Character, Integer> frequency = new HashMap<Character, Integer>();
    for (int i = 0; i < s.length(); ++i) {
        char ch = s.charAt(i);
        frequency.put(ch, frequency.getOrDefault(ch, 0) + 1);
    }
    for (int i = 0; i < s.length(); ++i) {
        if (frequency.get(s.charAt(i)) == 1) {
            return i;
        }
    }
    return -1;
}
~~~

