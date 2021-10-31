### 题目描述

将链表反转，下图为链表节点结构

```java
class ListNode {
    int val;
    ListNode next;
    ListNode() {}
    ListNode(int val) {
        this.val = val;
    }
    ListNode(int val, ListNode next) {
        this.val = val; this.next = next;
    }
}
```





### 官方题解

利用链表的特性，直接反转

![9ce26a709147ad9ce6152d604efc1cc19a33dc5d467ed2aae5bc68463fdd2888](E:..\..\..\img\9ce26a709147ad9ce6152d604efc1cc19a33dc5d467ed2aae5bc68463fdd2888.gif)

~~~ java
public ListNode reverseList(ListNode head) {
    ListNode pre = null;
    ListNode cur = head;
    while (cur != null) {
        ListNode temp = cur.next;
        cur.next = pre;
        pre = cur;
        cur = temp;
    }
    return pre;
}
~~~

