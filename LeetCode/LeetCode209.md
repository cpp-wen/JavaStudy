```java
class Solution {
    public int minSubArrayLen(int s, int[] nums) {
       //滑动窗口的思想
       int sum=0;
       int left=0;
       int right=0;
       int count=nums.length+1;
       while(right<nums.length){
           sum+=nums[right];
           if(sum>=s){
               while(sum>=s){
                   sum-=nums[left++];
               }
               count=Math.min(count,right-left+2);
           }
           right++;
       }
       return count==nums.length+1?0:count;
    }
}
```

思想

- 滑动窗口 right-left+2原因是因为while条件的影响
- 滑动窗口l r基本都是从0开始算的