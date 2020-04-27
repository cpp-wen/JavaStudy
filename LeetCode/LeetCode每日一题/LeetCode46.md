```java
class Solution {
    //time O(n*n!)  space O(n!)
    public List<List<Integer>> permute(int[] nums) {
        boolean []isSearch=new boolean[nums.length];//false
        ArrayList<List<Integer>> res=new ArrayList<List<Integer>>();
        dfs(nums,isSearch,new ArrayList<Integer>(),res);
        return res;
    }
    //深搜
    private void dfs(int []nums,boolean [] isSearch,List<Integer> list, ArrayList<List<Integer>> res){
        if(list.size()==nums.length){
            res.add(new ArrayList<Integer>(list));
            return ;
        }
        for(int i=0;i<nums.length;i++){
            if(isSearch[i]){
                continue;
            }
            isSearch[i]=true;
            list.add(nums[i]);
            dfs(nums,isSearch,list,res);
            isSearch[i]=false;
            list.remove(list.size()-1);
        }
    }
}
```

思路 

回溯套固定模板就好了 

回溯思想总结 ：递归+标志位