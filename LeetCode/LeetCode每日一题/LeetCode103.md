#### [103. 二叉树的锯齿形层次遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

给定一个二叉树，返回其节点值的锯齿形层次遍历。（即先从左往右，再从右往左进行下一层遍历，以此类推，层与层之间交替进行）。

例如：
给定二叉树 `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

返回锯齿形层次遍历如下：

```
[
  [3],
  [20,9],
  [15,7]
]
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
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        //层次遍历添加count 进行头尾添加判断
        //用两个栈实现 注意left right注意内容
        List<List<Integer>> res=new ArrayList<List<Integer>>();
        if(root==null){
            return res;
        }
        Stack<TreeNode> s1=new Stack<TreeNode>();
        Stack<TreeNode> s2=new Stack<TreeNode>();
        s1.add(root);
        while(!s1.isEmpty()||!s2.isEmpty()){
            ArrayList<Integer> list=new ArrayList<>();
            while(!s1.isEmpty()){
                TreeNode temp=s1.pop();
                if(temp.left!=null){
                    s2.push(temp.left);
                }
                if(temp.right!=null){
                    s2.push(temp.right);
                }
                list.add(temp.val);
            }
            if(list.size()>0){
                res.add(list);
            }
            ArrayList<Integer> list1=new ArrayList<>();
            while(!s2.isEmpty()){
                TreeNode temp=s2.pop();
                if(temp.right!=null){
                    s1.push(temp.right);
                }
                if(temp.left!=null){
                    s1.push(temp.left);
                }
                list1.add(temp.val);
            }
            if(list1.size()>0){
                res.add(list1);
            }
        }
        return res;
    }
}
```

