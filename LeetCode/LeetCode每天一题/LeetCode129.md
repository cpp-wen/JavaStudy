```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    int sum=0;
     public int sumNumbers(TreeNode root) {
     Jisuan(root);
     return sum;
    }
    private void Jisuan(TreeNode node){
        if(node==null){
            return;
        }
        if(node.left==null&&node.right==null){
            sum+=node.val;
        }
        if(node.left!=null){
            node.left.val=node.val*10+node.left.val;
        }
        if(node.right!=null){
            node.right.val=node.val*10+node.right.val;
        }
        Jisuan(node.left);
        Jisuan(node.right);
    }
   
}
```

思路 

- 子节点的值等于父节点的值*10+自身的值 然后判断这个节点是否为根节点就可以了。Jisuan 方法可以简化，但是没有很必要。