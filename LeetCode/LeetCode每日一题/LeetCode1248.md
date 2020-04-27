```java
class Solution {
    public int numberOfSubarrays(int[] nums, int k) {
        //双指针的题目 
        int left=0;
        int right=0;
        int sum=0;
        int res=0;
        int temp=0;
        //一开始没想到的是负数的问题 还有就是应该left指针滑动的时候 右边指针还可以添加的值为temp
        while(right<nums.length){
            //如果相同就不断减少个数
           if(sum<k){
               //使用& 不用%避免负数问题
               sum+=nums[right]&1;
               right++;
           }
           if(sum==k){
               temp=0;
               while(sum==k){
                   ++res;
                   ++temp;
                   sum-=nums[left]&1;
                   ++left;
               }
           }else{
               res+=temp;
           }
        }
        return res;
    }
}
```



