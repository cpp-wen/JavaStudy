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
    ArrayList<Integer> res=new ArrayList<>();
    public int kthSmallest(TreeNode root, int k) {
        //中序遍历然后存数组进行o(1)复杂度提取
        midRoot(root);
        return res.get(k-1);
    }
    private void midRoot(TreeNode root){
        if(root==null){
            return;
        }
        midRoot(root.left);
        res.add(root.val);
        midRoot(root.right);
    }
}
```

思路 

- 中序遍历 底层使用数组 来实现，ArrayList扩容可能对时间效率有一点影响 优化是记录一下数组中个数如果个数相等就可以退出，差不多是O(n/2)时间复杂度