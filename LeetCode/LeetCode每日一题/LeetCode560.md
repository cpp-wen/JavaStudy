#### [560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。

示例 1 :

输入:nums = [1,1,1], k = 2
输出: 2 , [1,1] 与 [1,1] 为两种不同的情况。
说明 :

数组的长度为 [1, 20,000]。
数组中元素的范围是 [-1000, 1000] ，且整数 k 的范围是 [-1e7, 1e7]。



```java
class Solution {
    public int subarraySum(int[] nums, int k) {
            //滑动窗口的模板
            //排序然后再用滑动窗口？
            //有负数 不能使用滑动窗口 改为前缀和的方法 套用公式 num[i,j]=num[0,j]-num[0,i];
            HashMap<Integer,Integer> hash=new HashMap<Integer,Integer>();
            //放入前置条件
            hash.put(0,1);
            int presum=0;
            int count=0;
            for(int a:nums){
                presum+=a;
                if(hash.containsKey(presum-k)){
                    count+=hash.get(presum-k);
                }
                hash.put(presum,hash.getOrDefault(presum,0)+1);
            }
            return count;
    }
}
```

