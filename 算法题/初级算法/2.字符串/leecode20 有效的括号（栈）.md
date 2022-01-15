## 我的题解

~~~ java
public boolean isValid(String s) {
    Map<Character, Character> map = new HashMap<>();
    map.put(')', '(');
    map.put(']', '[');
    map.put('}', '{');
    Deque<Character> stack = new LinkedList();
    for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        // 等同于 (c == ')' || c == ']' || c == '}')
        if (map.containsKey(c)) {
            // 特判，栈为空，说明字符串一开始就是)]}；后面则是[}括号不匹配的情况
            if (stack.isEmpty() || map.get(c) != stack.getFirst()) {  // 当前char对于的value是否与栈头相等，不相等说明直接gg
                return false;
            }
            stack.pop();
        } else {
            stack.push(c);
        }
    }
    return stack.isEmpty();
}
~~~

