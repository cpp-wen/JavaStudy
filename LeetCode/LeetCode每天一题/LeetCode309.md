```java
class Solution {
    public int maxProfit(int[] prices) {
        //贪心不行 
        //应该还是dp
        //看完题解 理解了线性dp 和状态机dp
        if(prices==null||prices.length==0){
            return 0;
        }
        int dp[][]=new int[prices.length][2];
        dp[0][0]=0;
        dp[0][1]=-prices[0];
        for(int i=1;i<prices.length;i++){
            dp[i][0]=Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);//卖出股票
            dp[i][1]=Math.max(dp[i-1][1],dp[i-2<0?0:i-2][0]-prices[i]);//持有股票
        }
        return dp[prices.length-1][0];
    }
}
```

建议看英文版题解很详细