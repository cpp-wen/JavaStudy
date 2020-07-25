#### [410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)

给定一个非负整数数组和一个整数 *m*，你需要将这个数组分成 *m* 个非空的连续子数组。设计一个算法使得这 *m* 个子数组各自和的最大值最小。

**注意:**
数组长度 *n* 满足以下条件:

- 1 ≤ *n* ≤ 1000
- 1 ≤ *m* ≤ min(50, *n*)

**示例:**

```
输入:
nums = [7,2,5,10,8]
m = 2

输出:
18

解释:
一共有四种方法将nums分割为2个子数组。
其中最好的方式是将其分为[7,2,5] 和 [10,8]，
因为此时这两个子数组各自的和的最大值为18，在所有情况中最小。
```

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        //二分的思想 这个题也很妙使用了二分逼近一边边界的方式 最后数组元素的和一定在数组中
        int left=0;
        int right=0;
        for(int num:nums){
            left=Math.max(left,num);
            right+=num;
        }
        while(left<right){
            int cnt=1;
            int mid=(left+right)>>>1;
            int sum=0;
            for(int num:nums){
                if(sum+num>mid){
                    sum=0;
                    cnt++;
                }
                sum+=num;
            }
            if(cnt>m){
                left=mid+1;
            }
            else{
                right=mid;
            }
        }
        return left;
    }
}
```

二分的思想 dp也可以做 效率比较低

二分主要确定left 和 right 两者的边界 以及意义