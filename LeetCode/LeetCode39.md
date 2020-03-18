```java
class Solution {
    //space time O(n*n!)
    List<List<Integer>> res=new ArrayList<List<Integer>>();
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        //这个回溯的时间复杂度太高了吧
        //排序方便减枝
        Arrays.sort(candidates);
        dfs(candidates,target,new ArrayList<Integer>());
        return res;
    }
    //回溯 正整数避免0问题 添加减枝操作避免重复
    private void dfs(int[]candidates,int target,ArrayList<Integer> list){
        if(target==0){
            res.add(new ArrayList(list));
        }
        for(int i=0;i<candidates.length;i++){
            if(target-candidates[i]<0||(list.size()-1>=0&&candidates[i]<list.get(list.size()-1))){
                continue;
            }else{
                list.add(candidates[i]);
                dfs(candidates,target-candidates[i],list);
                list.remove(list.size()-1);
            }
        }
    }
}
```

