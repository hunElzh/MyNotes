- 二分、递归

  ~~~ java
  public TreeNode sortedArrayToBST(int[] nums) {
      return dfs(nums, 0, nums.length - 1);
  }
  
  private TreeNode dfs(int[] nums, int l, int r) {
      // 注意 r 为 length/mid-1 时的结束条件
      if (l > r) {
          return null;
      }
      // mid 的写法，好像是可以防止啥东西
      int mid = l + (r - l) / 2;
      // 二分，按顺序安置根节点
      TreeNode root = new TreeNode(nums[mid]);
      root.left = dfs(nums, l, mid - 1);
      root.right = dfs(nums, mid + 1, r);
      return root;
  }
  ~~~

  