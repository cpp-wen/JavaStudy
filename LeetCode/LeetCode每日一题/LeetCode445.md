#### [445. 两数相加 II](https://leetcode-cn.com/problems/add-two-numbers-ii/)

给你两个 非空 链表来代表两个非负整数。数字最高位位于链表开始位置。它们的每个节点只存储一位数字。将这两数相加会返回一个新的链表。

你可以假设除了数字 0 之外，这两个数字都不会以零开头。

 

进阶：

如果输入链表不能修改该如何处理？换句话说，你不能对列表中的节点进行翻转。

 

> 示例：
>
> 输入：(7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)
> 输出：7 -> 8 -> 0 -> 7

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
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        //建立两个栈用来保存两个listnode里面中的元素 再利用一个add 用来保存进位元素
        //或者使用递归来做，但是两者的效果是一样的 
        Stack<Integer> ll1=new Stack<Integer>();
        Stack<Integer> ll2=new Stack<Integer>();
        while(l1!=null){
            ll1.add(l1.val);
            l1=l1.next;
        }
        while(l2!=null){
            ll2.add(l2.val);
            l2=l2.next;
        }
        int add=0;
        ListNode temp=null;
        while(!ll1.isEmpty()||!ll2.isEmpty()||add!=0){
             int sum=(ll1.isEmpty()?0:ll1.pop())+(ll2.isEmpty()?0:ll2.pop())+add;
             add=sum/10;
             ListNode tt = new ListNode(sum%10);
             tt.next=temp;
             temp=tt;
        }
        return temp;
    }
}
```

