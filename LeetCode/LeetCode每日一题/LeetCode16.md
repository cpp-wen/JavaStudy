```java
class Solution {
   public int threeSumClosest(int[] nums, int target) {
        Arrays.sort(nums);
        //思路 左右指针
        int res=nums[0]+nums[1]+nums[2];
        for(int i=0;i<nums.length-2;i++){//避免最后一个数组元素重复取到两次
            int l=i+1;
            int r=nums.length-1;
            int count;
            while(l<r){
                count=nums[i]+nums[l]+nums[r];
                if(Math.abs(count-target)<Math.abs(res-target)){
                    res=count;
                }
                if(count<target){
                    l++;
                }
                else if(count>target){
                    r--;
                }else{
                    return count;
                }
            }
        }
        return res;
    }
}
```

思路

- 类比第十五题使用技巧也是双指针。