```java
class Solution {
    // public int combinationSum4(int[] nums, int target) {
    //     //递归直接超时
    //     if(target==0){
    //         return 1;
    //     }
    //     int res=0;
    //     for( int i=0;i<nums.length;i++){
    //         if(target>=nums[i]){
    //           res+=combinationSum4(nums,target-nums[i]);
    //         }
    //     }
    //     return res;
    // }
    //回溯应该也可以 不用记录 不过应该会超时
    //跟半数组合问题差不多 
    public int combinationSum4(int[] nums, int target) {
      int dp[]=new int[target+1];
      dp[0]=1;
      for(int i=0;i<=target;i++){
          for(int j=0;j<nums.length;j++){
              //这边<= 一开始漏写了= 暴0 
              if(i+nums[j]<=target){
                dp[i+nums[j]]+=dp[i];
              }
          }
      }
      return dp[target];
    }
}
```



