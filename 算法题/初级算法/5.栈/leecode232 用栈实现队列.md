~~~ java
class MyQueue {
    private Deque<Integer> mainStack;
    private Deque<Integer> supStack;

    public MyQueue() {
        mainStack = new LinkedList();
        supStack = new LinkedList();
    }

    // 主要工作就集中在这一步，每次push的时候都会用辅助栈去更新子栈
    public void push(int x) {
        if (mainStack.isEmpty()) {
            mainStack.push(x);
        } else {
            while (!mainStack.isEmpty()) {
                supStack.push(mainStack.pop());
            }
            supStack.push(x);
            while (!supStack.isEmpty()) {
                mainStack.push(supStack.pop());
            }
        }
    }

    public int pop() {
        return mainStack.pop();
    }

    public int peek() {
        return mainStack.getFirst();
    }

    public boolean empty() {
        return mainStack.isEmpty();
    }
}
~~~

