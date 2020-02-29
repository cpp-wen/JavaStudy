```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
    ListNode head=new ListNode(0);
    ListNode rss=head;
    int sum=0;
    while(l1!=null||l2!=null||sum!=0){
        int number1=l1==null?0:l1.val;
        int number2=l2==null?0:l2.val;
        sum=number1+number2+sum;
        ListNode listNode=new ListNode(sum%10);
        sum=sum/10;
        head.next=listNode;
        head=listNode;
        l1=l1==null?null:l1.next;
        l2=l2==null?null:l2.next;
    }
    return rss.next;
}
```

思路：链表相加  使用一个sum来保存进位，迭代创建listnode就可以，注意l1 和 l2 进行next时候要注意 l1 和l2 是否为空 。

优化：每次while循环的时候判断一下是否 存在 三个while条件中只有l1 或者l2 一个条件为空，进行链表拼接，更加节省时间。