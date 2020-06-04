#### [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

根据一棵树的前序遍历与中序遍历构造二叉树。

**注意:**
你可以假设树中没有重复的元素。

例如，给出

```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
```

返回如下的二叉树：

```
    3
   / \
  9  20
    /  \
   15   7
```

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
    public TreeNode buildTree(int[] preorder, int[] inorder) {
            // 添加一个 boolean 数组使用 递归来做 
            //递归来做 不用添加boolean数组 前序和中序i下标的时候 两边的长度都是一样的
            if(preorder.length==0||inorder.length==0){
                return null;
            }
            TreeNode tree=new TreeNode(preorder[0]);
            for(int i=0;i<preorder.length;i++){
                if(tree.val==inorder[i]){
                    //构建左子树
                    tree.left=buildTree(Arrays.copyOfRange(preorder,1,i+1),Arrays.copyOfRange(inorder,0,i));
                    //构建右子树
                    tree.right=buildTree(Arrays.copyOfRange(preorder,i+1,preorder.length),Arrays.copyOfRange(inorder,i+1,inorder.length));
                }
            }
            return tree;
    }
}
```

