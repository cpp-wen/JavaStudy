#### [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

给你一个链表，每 k 个节点一组进行翻转，请你返回翻转后的链表。

k 是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序。

> 示例：
>
> 给你这个链表：1->2->3->4->5
>
> 当 k = 2 时，应当返回: 2->1->4->3->5
>
> 当 k = 3 时，应当返回: 3->2->1->4->5

说明：

你的算法只能使用常数的额外空间。
你不能只是单纯的改变节点内部的值，而是需要实际进行节点交换。



思路：

1. 直接使用栈的每次保存节点然后重建一个链表 若k的值比较大，空间需要开比较大
2. 使用递归的思路，先获取k个元素后 k+1元素反转链表的头节点然后将未反转链表的头节点链接到反转链表头节点，依次进行循环，进入递归
3. 非递归，每次反转前k个元素 之后将反转后k链表的最后一个节点 当前新的开始节点进入第二轮遍历



## 递归解法

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
    public ListNode reverseKGroup(ListNode head, int k) {
        //递归写法
        ListNode cur=head;
        int count=0;
        //获取到k+1个链表节点（链表从1开始）
        while(cur!=null&&count!=k){
            cur=cur.next;
            count++;
        }
        if(count==k){
            //cur为K+1为头节点的反向节点
            cur=reverseKGroup(cur,k);
            //head未反转前的头节点
            //循环开始反转当前列表
            while(count-->0){
                ListNode temp=head.next;
                head.next=cur;
                cur=head;
                head=temp;
            }
            //若直接返回cur则count<k的时候cur为遍历链表的最后一个元素 而不是不足k的链表的头节点
            head=cur;
        }
        return head;
    }
}
```



## 非递归法

```java
		int n=0;
        //计算链表长度
        for(ListNode i=head;i!=null;n++,i=i.next);
		//建立虚拟头节点
        ListNode dmy=new ListNode(0);
        dmy.next=head;
		//通过n>=k来避免最后不足k的链表的反转
        for(ListNode pre=dmy,tail=head;n>=k;n-=k){
            //反转节点
            for(int i=1;i<k;i++){
                ListNode next=tail.next.next;
                tail.next.next=pre.next;
                pre.next=tail.next;
                tail.next=next;
            }
            //pre指向反转链表的最后一个元素
            pre=tail;
            //tail指向将要开始反转链表的头节点
            //反转后的pre和tail 跟原先的dmy 以及head是相同的
            tail=tail.next;
        }
        return dmy.next;
```

