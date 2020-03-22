```java
class Solution {
    List<List<Integer>> res=new ArrayList<List<Integer>>();
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        dfs(nums,0,new ArrayList<Integer>());
        return res;
    }
    private void dfs(int[] nums,int start,ArrayList<Integer> list){
        res.add(new ArrayList<>(list));
        for(int i=start;i<nums.length;i++){
            //主要是减枝 几天前减枝条件相同
            if(i>start&&nums[i-1]==nums[i]){
                continue;
            }
            list.add(nums[i]);
            dfs(nums,i+1,list);
            list.remove(list.size()-1);
        }
    }
}
```

思路

减枝的条件跟前面的一题一样