#### [152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

难度中等504

给你一个整数数组 `nums` ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

 

**示例 1:**

```
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```

**示例 2:**

```
输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
```



```java
class Solution {
    public int maxProduct(int[] nums) {
        //分类讨论 选择最大的乘数数组或者选择最小的负数数组 
        //应该使用dp来做
        //在在线处理算法
        int max=Integer.MIN_VALUE,imax=1,imin=1;
        for(int i=0;i<nums.length;i++){
            if(nums[i]<0){
                //交换最大值和最小值
                int temp=imax;
                imax=imin;
                imin=temp;
            }
            imax=Math.max(imax*nums[i],nums[i]);
            imin=Math.min(imin*nums[i],nums[i]);
            //这边有可能 断开 产生负数的情况发生
            max=Math.max(max,imax);
        }
        return max;
    }
}
```

