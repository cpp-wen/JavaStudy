#### [78. 子集](https://leetcode-cn.com/problems/subsets/)

难度中等669

给定一组**不含重复元素**的整数数组 *nums*，返回该数组所有可能的子集（幂集）。

**说明：**解集不能包含重复的子集。

**示例:**

```
输入: nums = [1,2,3]
输出:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```





```JAVA
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
            //不用回溯来实现 观察题目中状态
            List<List<Integer>> res=new ArrayList<>();
            res.add(new ArrayList<Integer>());
            for(int i=0;i<nums.length;i++){
                int all=res.size();
                for(int j=0;j<all;j++){
                    List<Integer> tmp=new ArrayList<>(res.get(j));
                    // nums[i]  represent addNumber
                    tmp.add(nums[i]);
                    res.add(tmp);
                }
            }
            return res;
    }   
}
```

simple than bacldata