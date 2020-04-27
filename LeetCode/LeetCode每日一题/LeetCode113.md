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
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        //思路是 dfs +回溯 
        List<List<Integer>> res=new  ArrayList<List<Integer>>();
        if(root==null){
            return res;
        }
        dfs(root,sum,res,new ArrayList<>());
        return res;
    }
    private void dfs(TreeNode node,int sum,List<List<Integer>> res,ArrayList<Integer> path){
        if(node==null){
            return;
        }
        // if(sum-node.val<0){
        //     return;
        // }  题目出了个负数的案例
        path.add(node.val);
        if(node.left==null&&node.right==null&&sum==node.val){
            res.add(new ArrayList<>(path));//这边要注意是要new一个list 因为java传值引用 所以回溯的时候这边会被删掉
        }else{
            dfs(node.left,sum-node.val,res,path);
            dfs(node.right,sum-node.val,res,path);
        }
        //回溯删除最后那个节点
        path.remove(path.size()-1);
        
    }
}
```



思路：

- 主要思想是深度遍历，如何保存这个深度遍历的值就是关键。这边加了一个回溯 删除那些计算过的值。