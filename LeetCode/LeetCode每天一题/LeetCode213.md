```java
class Solution {
    public int rob(int[] nums) {
        if(nums==null||nums.length==0){
            return 0;
        }
        if(nums.length==1){
            return nums[0];
        }
        return Math.max(otherRob(nums,0,nums.length-2),otherRob(nums,1,nums.length-1));
    }
    private int  otherRob(int[]nums,int start,int end){
        int p1=0;
        int p2=0;
        int cur;
        for(int i=start;i<=end;i++){
            cur=Math.max(p2+nums[i],p1);
            p2=p1;
            p1=cur;
        }
        return p1;
    }
}
```

打家劫舍系列问题

