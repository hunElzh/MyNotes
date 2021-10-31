### 题目描述

给你一个字符串数组 `words` ，只返回可以使用在 **美式键盘** 同一行的字母打印出来的单词（qwertyuiop，asdfghjkl，zxcvbnm）。



>示例 1：
>
>输入：words = ["Hello","Alaska","Dad","Peace"]
>输出：["Alaska","Dad"]
>示例 2：
>
>输入：words = ["omk"]
>输出：[]
>示例 3：
>
>输入：words = ["adsdf","sfd"]
>输出：["adsdf","sfd"]



### 我的题解

存储字符和键盘行之间的映射，最终符合条件的加入集合

~~~ java
public String[] findWords(String[] words) {

        int i;

        Map<Character, Integer> map = new HashMap();
        List<String> list = new ArrayList<>();

        String s1 = "qwertyuiopQWERTYUIOP";
        String s2 = "asdfghjklASDFGHJKL";
        String s3 = "zxcvbnmZXCVBNM";

        //存储字符与键盘行数的映射
        for (i = 0; i < s1.length(); i++) {
            map.put(s1.charAt(i), 1);
        }
        for (i = 0; i < s2.length(); i++) {
            map.put(s2.charAt(i), 2);
        }
        for (i = 0; i < s3.length(); i++) {
            map.put(s3.charAt(i), 3);
        }

        for (String str : words) {
            int exp = map.get(str.charAt(0)) * str.length();
            int fact = 0;
            for (i = 0; i < str.length(); i++) {
                fact += map.get(str.charAt(i));
            }
            if (exp == fact) {
                list.add(str);
            }
        }

        return list.toArray(new String[list.size()]);
    }
~~~



