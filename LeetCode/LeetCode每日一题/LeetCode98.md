#### [98. 验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

- 节点的左子树只包含小于当前节点的数。
- 节点的右子树只包含大于当前节点的数。
- 所有左子树和右子树自身必须也是二叉搜索树。



> 示例 1:
>
> 输入:
>     2
>    / \
>   1   3
> 输出: true
> 示例 2:
>
> 输入:
>     5
>    / \
>   1   4
>      / \
>     3   6
> 输出: false
> 解释: 输入为: [5,1,4,null,null,3,6]。
>      根节点的值为 5 ，但是其右子节点值为 4 。

思路

1. 中序遍历 写法很多种

```java
class Solution {
     public boolean isValidBST(TreeNode root) {
        return validate(root,Long.MIN_VALUE,Long.MAX_VALUE);
    }
    private boolean validate(TreeNode node,long min,long max){
        if(node==null){
            return true;
        }
        if(node.val<=min|| node.val>=max){
            return false;
        }
        return validate(node.left,min,node.val)&&validate(node.right,node.val,max);
    }
}
```

```java
class Solution {
    //cur初始化得放在函数的外面来保证 cur的值不会被递归重复初始化
    long cur=Long.MIN_VALUE;
    public boolean isValidBST(TreeNode root) {
        //递归来做 会有问题 应该使用中序遍历来实现
       if(root==null){
           return true;
       }
       boolean left=isValidBST(root.left);
       if(root.val<=cur){
           return false;
       }
       cur=root.val;
       boolean right=isValidBST(root.right);
       return left&&right;
    }
}
```

