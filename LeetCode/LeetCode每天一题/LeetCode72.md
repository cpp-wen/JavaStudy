```java
class Solution {
    public int minDistance(String word1, String word2) {
        //动态规划题目 演变还是一个二维的dp表进行元素的保存来减少重复的计算
        int m=word1.length();
        int n=word2.length();
        int dp[][]=new int[m+1][n+1];
        for(int i=1;i<=m;i++){
            dp[i][0]=i;
        }
        for(int i=1;i<=n;i++){
            dp[0][i]=i;
        }
        for(int i=1;i<=m;i++){
            for(int j=1;j<=n;j++){
                if(word1.charAt(i-1)==word2.charAt(j-1)){
                    dp[i][j]=dp[i-1][j-1];
                    //这边是跳过
                }else{
                    //这边是修改或者替换
                    dp[i][j]=Math.min(Math.min(dp[i-1][j],dp[i][j-1]),dp[i-1][j-1])+1;
                }
            }
        }
        return dp[m][n];
    }
}
```

并非最优代码只是给个思路 并能通过ac 下一次刷题会进行优化

