```java
/*
// Definition for a Node.
class Node {
    int val;
    Node next;
    Node random;

    public Node(int val) {
        this.val = val;
        this.next = null;
        this.random = null;
    }
}
*/
class Solution {
    public Node copyRandomList(Node head) {
        //算法思想 是创建一个链表 这个链表长度是原先两倍，这个链表原先每个节点的后面带一个新节点
        if(head==null){
            return null;
        }
        Node temp=head;
        while(temp!=null){
            Node node=new Node(temp.val);
            node.next=temp.next;
            temp.next=node;
            temp=node.next;
        }
        temp=head;
        //这边成为了一条 先进行random链接
        // Node originalHead=head;
        // Node resHead=head.next;
        while(temp!=null){
            //npe
            temp.next.random=temp.random==null?null:temp.random.next;
            temp=temp.next==null?null:temp.next.next;
        }
        //拆分node链
        // head=resHead;
        // while(originalHead!=null){
        //     originalHead.next=resHead.next;
        //     resHead.next=originalHead.next;
        //     originalHead=originalHead.next;
        //     resHead=resHead.next;
           
        // }
        // return head;
        Node ptrOldList = head;
        Node ptrNewList = head.next;
        Node headOld = head.next;
        while (ptrOldList != null) {
            ptrOldList.next = ptrOldList.next.next;
            ptrNewList.next = (ptrNewList.next != null) ? ptrNewList.next.next : null;
            ptrOldList = ptrOldList.next;
            ptrNewList = ptrNewList.next;
        }
        return headOld;
    }
}
```

思路：

- 复制每个node跟在 oldnode后面，在后续中分割这条node链，成为两条node链表