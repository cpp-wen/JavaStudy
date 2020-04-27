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
    int count=0;    
    public int pathSum(TreeNode root, int sum) {
        if (root==null) return 0;
        jisuan(root,sum);
        pathSum(root.left, sum) ;
        pathSum(root.right, sum);
        return count;
    }
    private void jisuan(TreeNode root,int sum){
        if(root==null){
            return;
        }
        if(root.left!=null){
            jisuan(root.left,sum-root.val);
        }
        if(root.right!=null){
            jisuan(root.right,sum-root.val);
        }
        if(root.val==sum){
            count++;
        }
    }
}
```

思路

- 把每一棵子树当成一棵新树进行计算。