```java
 public ListNode reverseList(ListNode head) {
       ListNode Vhead=null;
       ListNode temp;
       while(head!=null){
           temp=head.next;
           head.next=Vhead;
           Vhead=head;
           head=temp;
       }
       return Vhead;
    }
```

思路 

- 迭代写法，用一个头指针来保存null节点
- stack实现 先进后出
- 递归写法