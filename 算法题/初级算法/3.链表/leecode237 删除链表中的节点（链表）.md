### 题目描述

请编写一个函数，用于 删除单链表中某个特定节点 。在设计函数时需要注意，你无法访问链表的头节点 head ，只能直接访问 要被删除的节点 。

题目数据保证需要删除的节点 不是末尾节点 。

![zzzzh7m7878o78p78rt98y4k13a5ert4](..\..\..\img\zzzzh7m7878o78p78rt98y4k13a5ert4.png)



### 我的题解

> 直接将 Given node 修改成为后一节点，然后改变指向即可

~~~ java
public void deleteNode(ListNode node) {
    node.val = node.next.val;
    node.next = node.next.next;
}
~~~

