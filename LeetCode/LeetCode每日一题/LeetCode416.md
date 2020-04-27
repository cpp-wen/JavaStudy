```java
class Solution {
     public boolean canPartition(int[] nums) {
        //01背包变化问题 dp[n][m] n表示第几个元素 m表示当前元素的价值为m
        if (nums.length == 0) {
            return false;
        }
        int sum = 0;
        for (int a : nums) {
            sum += a;
        }
        if ((sum & 1) == 1) {
            return false;
        }
        int target = sum / 2;
        boolean[][] dp = new boolean[nums.length][target + 1];
        if (nums[0] < target) {
            //表示当前取前0个元素的时候 价值为nums[0]的元素可以被取到
            dp[0][nums[0]] = true;
        }
        //接下来就是一个填表的过程了
        for (int i = 1; i < nums.length; i++) {
            //这边是<=target
            for (int j = 0; j <=target; j++) {
                //先将上一行表的状态都复制下来
                dp[i][j] = dp[i - 1][j];
                //前 i个元素只取当前元素的时候肯定是true
                if (j == nums[i]) {
                    dp[i][j] = true;
                    continue;
                }
                // 这边是 保证  j-nums[i]>0
                if (nums[i]<j) {
                    dp[i][j]=dp[i-1][j]||dp[i-1][j-nums[i]];
                }
                //简单的减枝
                if(j==target){
                    if(dp[i][j]){
                        return true;
                    }
                }

            }
        }
        return dp[nums.length-1][target];
    }
}
```

背包 变种问题





