```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        if(head==null){
            return false;
        }
        HashSet<ListNode> hashSet=new HashSet<ListNode>();
        while(head.next!=null){
            if(hashSet.add(head)){
                head=head.next;
            }else{
                return true;
            }
        }
        return false;
    }
}
```

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
       if(head==null){
           return false;
       }
       ListNode first=head;
       ListNode second=head;
       while(second.next!=null){
           first=first.next;
           second=second.next==null?null:second.next.next;
           if(second==null){
               return
           }
           if(first==second){
               return true;
           }
       }
       return false;
    }
}
```

思路

- 快慢指针
- hashset重复元素判断