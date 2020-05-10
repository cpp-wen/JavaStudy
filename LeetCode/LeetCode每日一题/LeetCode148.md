#### [148. 排序链表](https://leetcode-cn.com/problems/sort-list/)

在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序。

> 示例 1:
>
> 输入: 4->2->1->3
> 输出: 1->2->3->4
> 示例 2:
>
> 输入: -1->5->3->4->0
> 输出: -1->0->3->4->5

思路：根据时间复杂度 可以得出使用归并的主体思想 

```java

class Solution {
    public ListNode sortList(ListNode head) {
        //链表排序 应该是归并排序吧
        //主要的几个步骤1. 找到链表中间的元素 进行拆分链表 2. 开始链表的合并
        if(head==null){
            return null;
        }       
        return mergeSort(head);
    }
    public ListNode mergeSort(ListNode head){
        if(head.next==null){
            return head;
        }
        //快慢指针找到中间元素
        ListNode tempHead=head;
        ListNode mid=head;
        //要记录中间元素的值 然后进行置null操作不然会出现重复mergeSort问题
        ListNode pre=null;
        while(head!=null&&head.next!=null){
            pre=mid;
            head=head.next.next;
            mid=mid.next;
        }
        pre.next=null;
        ListNode left = mergeSort(tempHead);
        ListNode right= mergeSort(mid);
        return merge(left,right);
    }
    //merge两个链表
    public ListNode merge(ListNode left,ListNode right){
        ListNode temp=new ListNode(0);
        ListNode res=temp;
        while(left!=null&&right!=null){
            if(left.val<right.val){
                temp.next=left;
                temp=temp.next;
                left=left.next;
            }else{
                temp.next=right;
                temp=temp.next;
                right=right.next;
            }
        }
        if(left!=null){
            temp.next=left;
        }
        if(right!=null){
            temp.next=right;
        }
        return res.next;
    }
}
```

