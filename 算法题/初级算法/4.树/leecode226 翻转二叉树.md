- 最基本的递归写法

~~~ java
public TreeNode invertTree(TreeNode root) {
    if (root == null) {
        return null;
    }

    TreeNode temp = root.left;
    root.left = invertTree(root.right);
    root.right = invertTree(temp);
	
    // 每次调用节点返回的都是入参root
    return root;
}
~~~

