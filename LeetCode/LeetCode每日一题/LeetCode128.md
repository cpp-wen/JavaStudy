#### [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/)

给定一个未排序的整数数组，找出最长连续序列的长度。

要求算法的时间复杂度为 *O(n)*。

**示例:**

```
输入: [100, 4, 200, 1, 3, 2]
输出: 4
解释: 最长连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        //最长连续序列 1234 这种
        //1排序 之后才能进行比较 比较
        //2hash结构进行比较
        //3建立最大堆或者最小堆
        //计数排序 压空间 然后左右指针多次走 遍历
        Set<Integer> numsSet=new HashSet<>();
        for(Integer num:nums){
            numsSet.add(num);
        }
        int longSet=0;
        for(Integer num:nums){
            if(numsSet.remove(num)){
                int currentLongest=1;
                int current=num;
                while(numsSet.remove(current-1)){
                    current--;
                }
                currentLongest+=(num-current);
                current=num;
                while(numsSet.remove(current+1))current++;
                currentLongest+=(current-num);
                longSet=Math.max(longSet,currentLongest);
            }
        }
        return longSet;

    }
}
```

