#### [329. 矩阵中的最长递增路径](https://leetcode-cn.com/problems/longest-increasing-path-in-a-matrix/)

给定一个整数矩阵，找出最长递增路径的长度。

对于每个单元格，你可以往上，下，左，右四个方向移动。 你不能在对角线方向上移动或移动到边界外（即不允许环绕）。

> 示例 1:
>
> 输入: nums = 
> [
>   [9,9,4],
>   [6,6,8],
>   [2,1,1]
> ] 
> 输出: 4 
> 解释: 最长递增路径为 [1, 2, 6, 9]。
> 示例 2:
>
> 输入: nums = 
> [
>   [3,4,5],
>   [3,2,6],
>   [2,2,1]
> ] 
> 输出: 4 
> 解释: 最长递增路径是 [3, 4, 5, 6]。注意不允许在对角线方向上移动。

```java
class Solution {
    public int longestIncreasingPath(int[][] matrix) {
        //记忆化搜索目前理解  感觉有点像dp的感觉 bfs+记录
        if(matrix==null||matrix.length==0||matrix[0].length==0){
            return 0;
        }
        int max=0;
        int row=matrix.length;
        int col=matrix[0].length;
        int dp[][]=new int[row][col];
        for(int i=0;i<row;i++){
            for(int j=0;j<col;j++){
                max=Math.max(max,loop(matrix,Integer.MIN_VALUE,dp,i,j));
            }
        }
        return max;
    }
    private int loop(int [][]mat,int pre,int [][]dp,int i ,int j){
        //这边给的一个条件是递增所以是<=就跳过元素
        if(i<0||j<0||i>=mat.length||j>=mat[0].length||mat[i][j]<=pre){
            return 0;
        }
        //使用记忆数据避免重复计算
        if(dp[i][j]!=0){
            return dp[i][j];
        }
        int max=0;
        max=Math.max(max,loop(mat,mat[i][j],dp,i-1,j));
        max=Math.max(max,loop(mat,mat[i][j],dp,i+1,j));
        max=Math.max(max,loop(mat,mat[i][j],dp,i,j+1));
        max=Math.max(max,loop(mat,mat[i][j],dp,i,j-1));
        dp[i][j]=1+max;
        return dp[i][j];
    }
}
```




