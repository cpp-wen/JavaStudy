```java
class Solution {
    public int rob(TreeNode root) {
       //动态规划  两种情况要么偷根节点 左右节点都不偷， 要么不偷跟节点，左右孩子节点任意选
       int []res=dfs(root);
       return Math.max(res[0],res[1]);
    }
    private int[] dfs(TreeNode root){
        if(root==null){
            return new int[2];
        }
        int []left=dfs(root.left);
        int []right=dfs(root.right);
        //偷根节点
        //不偷根节点 0代表不偷 1代表偷
        int res[]=new int[2];
        res[0]=Math.max(left[0],left[1])+Math.max(right[0],right[1]);
        res[1]=root.val+left[0]+right[0];
        return res;
    }
  
}
```



