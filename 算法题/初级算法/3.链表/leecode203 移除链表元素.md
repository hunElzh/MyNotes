移除元素的两种方法：

1. 直接操作原链表，例如 head = head.next（仅方便删除头部几个）
2. 定义虚拟节点dummy，并使用cur=dummy辅助删除，cur.next = cur.next.next（此题就为这种解法）

~~~ java
public ListNode removeElements(ListNode head, int val) {
    // 定义虚拟节点指向head，这样就能成功地跳过头结点这一特殊边界情况
    ListNode dummy = new ListNode(0, head); // 定义虚拟指针，用于重构链表
    ListNode cur = dummy; // 引入一个辅助节点等于虚拟，跳过元素时会用到
    while (cur.next != null) {
        if (cur.next.val == val) {
            cur.next = cur.next.next;
        } else {
            cur = cur.next;
        }
    }
    return dummy.next;
}
~~~

