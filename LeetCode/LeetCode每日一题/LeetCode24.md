#### [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

难度中等493

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

 

**示例:**

```
给定 1->2->3->4, 你应该返回 2->1->4->3.
```



```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode swapPairs(ListNode head) {
        //迭代来写
        ListNode tmep=new ListNode(0);
        ListNode res=tmep;
        tmep.next=head;
        ListNode cur=head;
        ListNode next;
        while(cur!=null){
            next=cur.next;
            if(next!=null){
                tmep.next=next;
                cur.next=next.next;
                next.next=cur;
                tmep=cur;
                cur=tmep.next;
            }else{
                return res.next;
            }
        }
        return res.next;
    }
}
```

