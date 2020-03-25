```java
class Solution {
    public int lengthOfLIS(int[] nums) {
        //最长上升子序列
        //暴力两倍for循环 类似冒泡
        //冒牌不成功 使用dp dp是对的，但是写的时候出问题了 dp是从前往后进行
        if(nums.length<2){
            return nums.length;
        }
        int []dp=new int [nums.length];
        Arrays.fill(dp,1);
        for(int i=1;i<nums.length;i++){
            for(int j=0;j<i;j++){
                if(nums[i]>nums[j]){
                    dp[i]=Math.max(dp[i],dp[j]+1);
                }
            }
        }
        Arrays.sort(dp);
        return dp[nums.length-1];
    }
}
```

