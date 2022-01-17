### 题解

- 递归

~~~ java
public boolean isSymmetric(TreeNode root) {
    if (root == null) {
        return true;
    } else {
        return Order(root.left, root.right);
    }
}

private boolean Order(TreeNode left, TreeNode right) {
    if (left == null && right == null) {
        return true;
    }
    // 注意判断不等条件要放在if中的最后，以防空指针异常
    if (left == null || right == null || left.val != right.val) {
        return false;
    }

    // 使用 && 逻辑，只要有一边返回false，结果即为false
    return Order(left.left, right.right) && Order(left.right, right.left);
}
~~~



- 迭代

~~~ java
public boolean isSymmetric(TreeNode root) {
    return check(root, root);
}

public boolean check(TreeNode u, TreeNode v) {
    // 两个两个取
    Queue<TreeNode> q = new LinkedList<TreeNode>();
    q.offer(u);
    q.offer(v);
    while (!q.isEmpty()) {
        u = q.poll();
        v = q.poll();
        if (u == null && v == null) {
            continue;
        }
        if ((u == null || v == null) || (u.val != v.val)) {
            return false;
        }

        q.offer(u.left);
        q.offer(v.right);

        q.offer(u.right);
        q.offer(v.left);
    }
    return true;
}
~~~

