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
    private int count=0;
    public int sumOfLeftLeaves(TreeNode root) {
        if(root==null){
            return 0;
        }
        //递归 时空复杂度O（n）
        if(root.left!=null&&root.left.left==null&&root.left.right==null){
            count+=root.left.val;
            sumOfLeftLeaves(root.right);
        }else{
            sumOfLeftLeaves(root.left);
            sumOfLeftLeaves(root.right);
        }
        return count;
    }
}
```

思路

- 想要遍历每棵树的左叶子节点。先利用一棵树来进行抽象

  <img src="https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200302133725.png" style="zoom:50%;" />

  要添加的节点值为2和6，如何判断这个节点是左叶子节点，从1来看，只要当前节点左节点不为0，并且左节点是叶子节点就可以。ok这时候将这个值保存下来就可以。

  为什么保存这个节点值之后需要递归右节点呢，看下面的这个图就知道了。

  <img src="https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200302134130.png" style="zoom:50%;" />

  如果当前这个节点 不是左叶子节点的上面一个根节点，只需将将左右孩子节点分别作为根节点再次递归就可以。

  