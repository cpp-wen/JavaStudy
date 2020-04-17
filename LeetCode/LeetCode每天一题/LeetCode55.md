```java
class Solution {
    public boolean canJump(int[] nums) {
        int len=nums.length;
        if(len<=1){
            return true;
        }
        int max=nums[0];
        for(int i=1;i<len-1;i++){
            if(i<=max){
                max=Math.max(max,nums[i]+i);
            }
        }
        return max>=len-1;
    }
}
```
