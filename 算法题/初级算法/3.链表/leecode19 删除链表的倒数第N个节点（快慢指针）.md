## 我的题解

链表节点的删除，只能通过前一个node.next = node.next.next

我想着用指向当前节点的指针删除想了好久tmd，原来这样删不掉

~~~ java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode dummy = new ListNode(0, head);
    // 为了保证slow能在被删除节点之前，因此令fast初始就在slow之前
    ListNode fast = head;
    ListNode slow = dummy;
    for (int i = 0; i < n; i++) {
        fast = fast.next;
    }
    while (fast != null) {
        fast = fast.next;
        slow = slow.next;
    }
    slow.next = slow.next.next;
    ListNode ans = dummy.next;
    return ans;
}
~~~

