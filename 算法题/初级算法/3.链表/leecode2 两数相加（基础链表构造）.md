### 题目描述

![zzzzzg86n7qe16](..\..\..\img\zzzzzg86n7qe16.png)

### 官方题解

> 一步步地构造链表，很基础的一道题

```java
// 链表的构造原来是这样，用一个节点指向头，然后用另一个节点堆链表进行扩展
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    // head指向头结点，tail就是链表移动的节点，此题中为尾节点。head用户返回，tail用户扩展
    ListNode head = null, tail = null;
    // 记录是否进位
    int carry = 0;
    // 相当于一步步构造，最终构造出一个head；
    while (l1 != null || l2 != null) {
        // 当前节点若为 null 则将当前节点的值标记为 0
        int n1 = l1 != null ? l1.val : 0;
        int n2 = l2 != null ? l2.val : 0;
        int sum = n1 + n2 + carry;
        // 当前节点与下一节点
        if (head == null) {
            head = tail = new ListNode(sum % 10);
        } else {
            tail.next = new ListNode(sum % 10);
            tail = tail.next;
        }
        carry = sum / 10;
        if (l1 != null) {
            l1 = l1.next;
        }
        if (l2 != null) {
            l2 = l2.next;
        }
    }
    // 当 carry=1，最后一位进位
    if (carry > 0) {
        tail.next = new ListNode(carry);
    }
    return head;
}
```

