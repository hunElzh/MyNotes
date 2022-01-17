- 栈

~~~ java
class MinStack {
    Deque<Integer> minStack = new LinkedList<>();
    Deque<Integer> stack = new LinkedList<>();

    public MinStack() {

    }

    public void push(int val) {
        if (stack.isEmpty()) {
            minStack.push(val);
        } else {
            // <= 防止空指针
            if (val <= minStack.peek()) {
                minStack.push(val);
            }
        }
        stack.push(val);
    }

    public void pop() {
        if (!stack.isEmpty()) {
            Integer pop = stack.pop();
            if (getMin() == pop) {
                minStack.pop();
            }
        }
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        return minStack.peek();
    }
}

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack obj = new MinStack();
 * obj.push(val);
 * obj.pop();
 * int param_3 = obj.top();
 * int param_4 = obj.getMin();
 */

~~~

