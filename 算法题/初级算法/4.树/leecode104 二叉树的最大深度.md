- 深度优先搜索

  ~~~ java
  public int maxDepth(TreeNode root) {
      return dfs(root);
  }
  
  private int dfs(TreeNode node) {
      if (node == null) {
          return 0;
      }
      //return Math.max(dfs(node.left)+1, dfs(node.right)+1);
      // 以下面这种形式更加直观
      int leftHeight = dfs(node.left) + 1;
      int rightHeight = dfs(node.right) + 1;
      return Math.max(leftHeight + rightHeight) + 1
  }
  ~~~

  

- 迭代 + 广度优先搜索

  使用BFS获取树的最大层数

  ``` java
  public int maxDepth(TreeNode root) {
      // 广度优先搜索需要特判
      if (root == null) {
          return 0;
      }
      // new一个双端队列；offer加入元素，poll删除元素，peek获取队列front元素
      Queue<TreeNode> queue = new LinkedList<TreeNode>();
      // 初始将根节点压入队列
      queue.offer(root);
      int ans = 0;
      while (!queue.isEmpty()) {
          // 这个size用于记录每一层节点的数量
          int size = queue.size();
          while (size > 0) {
              TreeNode node = queue.poll();
              if (node.left != null) {
                  queue.offer(node.left);
              }
              if (node.right != null) {
                  queue.offer(node.right);
              }
              size--;
          }
          // 经历完一层之后，ans++
          ans++;
      }
      return ans;
  }
  ```

  