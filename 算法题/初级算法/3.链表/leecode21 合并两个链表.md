## 题解

- 递推法：使用虚拟头dummy，一次次改变list1和list2头结点的指向

~~~ java
public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
    ListNode dummy = new ListNode(0, list1);
    ListNode pre = dummy;
    while (list1!=null && list2!=null) {
        if (list1.val <= list1.val) {
            pre.next = list1;
            list1 = list1.next;
        } else {
            pre.next = list2;
            list2 = list2.next;
        }
        pre = pre.next;
    }
    // pre.next最终指向若为list1，说明list2被插入list1中
    pre.next = list1 == null ? list2 : list1;
    return dummy.next;
}
~~~



- 递归法

~~~ java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
    // l1先等于null，说明最大的在l2；反之。最终返回结果
    if (l1 == null) {
        return l2;
    }
    else if (l2 == null) {
        return l1;
    }
    else if (l1.val < l2.val) {
        // (l1.next, l2)为l1.next之后的剩余排序结果
        l1.next = mergeTwoLists(l1.next, l2);
        // 返回排序过后的l1
        return l1;
    }
    else {
        l2.next = mergeTwoLists(l1, l2.next);
        return l2;
    }
}
~~~

