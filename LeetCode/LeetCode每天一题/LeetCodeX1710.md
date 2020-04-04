```java
class Solution {
    public int majorityElement(int[] nums) {
        //int n进行计数 O(2) space 
        if(nums.length==0){
            return -1;
        }
        int n=1;
        int temp=nums[0];
        for(int i=1;i<nums.length;++i){
            if(temp==nums[i]){
                n++;
            }else{
                n--;
                if(n==0){
                    temp=nums[i];
                    n=1;
                }
            }
        }
       //投票验证
       int length=nums.length/2+1;
       int count=0;
       for(int i:nums){
           if(i==temp){
               count++;
           }
           if(count==length){
               return temp;
           }
       }
        return -1;
    }
}
```

摩尔投票方法 深入理解

摩尔投票法是默认必定存在众数，但该题可能不存在众数，所以摩尔投票法得到的候选数，应该进行验证。