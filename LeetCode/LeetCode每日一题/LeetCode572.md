#### [572. 另一个树的子树](https://leetcode-cn.com/problems/subtree-of-another-tree/)

给定两个非空二叉树 s 和 t，检验 s 中是否包含和 t 具有相同结构和节点值的子树。s 的一个子树包括 s 的一个节点和这个节点的所有子孙。s 也可以看做它自身的一棵子树。

示例 1:
给定的树 s:

```
     3
    / \
   4   5
  / \
 1   2
```

给定的树 t：

```
   4 
  / \
 1   2
```

返回 **true**，因为 t 与 s 的一个子树拥有相同的结构和节点值。

**示例 2:**
给定的树 s：

```
     3
    / \
   4   5
  / \
 1   2
    /
   0
```

给定的树 t：

```
   4
  / \
 1   2
```

思路: 

​	一个树是另一个树的子树 则

- 要么这两个树相等
- 要么这个树是左树的子树
- 要么这个树hi右树的子树

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
    public boolean isSubtree(TreeNode s, TreeNode t) {
        //这边有两个递归 主函数一个递归 子函数一个递归 两个递归相互嵌套 
        //主函数递归 是将每个节点作为根节点
        //子函数递归 是将相等的两个递归节点继续进行比较
        if(s==null){
            return false;
        }
        return isSubtree(s.left,t)||isSubtree(s.right,t)||isSubtree2(s,t);
    }
    private boolean isSubtree2(TreeNode s,TreeNode t){
        if(s==null&&t==null){
            return true;
        }
        if(s==null||t==null){
            return false;
        }
        return s.val==t.val&&isSubtree2(s.left,t.left)&&isSubtree2(s.right,t.right);
    }
    
}
```

