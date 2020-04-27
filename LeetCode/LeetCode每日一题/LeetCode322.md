```java
class Solution {
     public int coinChange(int[] coins, int amount) {
        //首先的想法是贪心，每次都拿最大的银币 好像不可行
        //dp动态规划  好像没有找到合适的状态转移方程
        //bfs好像可以 自己写的bfs超过时间限制了
        //dfs+贪心好像也可以
        int []f =new int[amount+1];
        f[0]=0;
        for(int i=1;i<=amount;i++){
            int cost=Integer.MAX_VALUE;
            for(int j=0;j<coins.length;j++){
                 if(i-coins[j]>=0){
                if(f[i-coins[j]]!=Integer.MAX_VALUE){
                    cost=Math.min(cost,1+f[i-coins[j]]);
                }
            }
            }
            f[i]=cost;
        }
        return f[amount]==Integer.MAX_VALUE?-1:f[amount];
    }
}
```

