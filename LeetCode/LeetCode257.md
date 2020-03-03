第一版 ：效率不是很高 主要是给一个思路 

每个节点如果不是叶子节点就将自己的val值添加到路径中，如果是叶子节点就将链表加入list集合中.

<img src="https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200303165512.png" style="zoom:50%;" />

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
    ArrayList<String> res=new ArrayList<>();
    public List<String> binaryTreePaths(TreeNode root) {
        if(root==null){
            return res;
        }
        else{
            String string=""+root.val;
            jisuan(root,string);
        }
         return res;
    }
    private void jisuan(TreeNode root,String string){
            if(root.left!=null){
                String nstring=string+"->"+root.left.val;
                jisuan(root.left,nstring);
            }
            if(root.right!=null){
                String nnstring=string+"->"+root.right.val;
                jisuan(root.right,nnstring);
            }
            if(root.left==null&&root.right==null){
                res.add(string);
            }
    }
}
```

树的题目很多利用递归，如何利用list返回值是使用递归的主要难点。

贴一个厉害的代码

思路是自底向上实现递归调用。上面的代码是自顶向下。这个其实跟dp 的思想有点类似。

```java
class Solution {
    public List<String> binaryTreePaths(TreeNode root) {
        List<String> list = new ArrayList<String>();
        if(root == null){
            return list;
        }
        //root为叶子结点时，是递归的出口
        if(root.left == null && root.right == null){
            list.add(""+root.val);
            return list;
        }
        if(root.left != null){
            for(String s : binaryTreePaths(root.left)){
                list.add(root.val+"->"+s);
            }
        }
        if(root.right != null){
            for(String s : binaryTreePaths(root.right)){
                list.add(root.val+"->"+s);
            }
        }
        return list;
    }
}
```

