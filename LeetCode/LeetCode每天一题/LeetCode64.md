```java
class Solution {
    //time O(m*n) space(m*n) 
    public int minPathSum(int[][] grid) {
        //状态转移 dp[i][j]=Math.min(dp[i-1][j],dp[i][j-1])+grid[i][j]
        int m=grid.length;
        if(m==0){
            return 0;
        }
        int n=grid[0].length;
        int dp[][]=new int[m][n];
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(i==0&&j==0){
                    dp[0][0]=grid[0][0];
                    continue;
                }
                if(i==0){
                    dp[i][j]=dp[0][j-1]+grid[0][j];
                }else if(j==0){
                    dp[i][j]=dp[i-1][j]+grid[i][j];
                }else{
                    dp[i][j]=Math.min(dp[i-1][j],dp[i][j-1])+grid[i][j];
                }
            }
        }
        return dp[m-1][n-1];
    }
}
```

并非最优代码只是给个思路 并能通过ac
