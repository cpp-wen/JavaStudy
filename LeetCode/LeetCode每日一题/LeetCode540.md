#### [540. 有序数组中的单一元素](https://leetcode-cn.com/problems/single-element-in-a-sorted-array/)

给定一个只包含整数的有序数组，每个元素都会出现两次，唯有一个数只会出现一次，找出这个数。

**示例 1:**

```
输入: [1,1,2,3,3,4,4,8,8]
输出: 2
```

**示例 2:**

```
输入: [3,3,7,7,10,11,11]
输出: 10
```

**注意:** 您的方案应该在 O(log n)时间复杂度和 O(1)空间复杂度中运行。



```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        //上异或  二分 速度更快
        //如果硬推需要写多个if 条件判断 将标志位每次标记为 成双的第一个 
        //设置 区间保持奇数个
        if(nums.length<=1){
            return nums[0];
        }
        int left=0;
        int right=nums.length-1;
        while(left<right){
            int mid=left+(right-left)/2;
            if(mid%2==1){
                mid=mid-1;
            }
            if(nums[mid]==nums[mid+1]){
                left=mid+2;
            }else{
                right=mid;
            }
        }
        return nums[right];
    }
}
```

