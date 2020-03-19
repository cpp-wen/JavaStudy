```java
class Solution {
    List<List<Integer>> res=new ArrayList<List<Integer>>();
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        dfs(candidates,target,0,new ArrayList<Integer>());
        return res;
    }
    //dfs 回溯 要把重复的删除
    private void dfs(int []candidates,int target,int start,ArrayList<Integer> list){
        if(target<0){
            return ;
        }
        if(target==0){
            res.add(new ArrayList(list));
            return;
        } 
        for(int i=start;i<candidates.length;i++){
            //Jianzhi 难点 1 1的时候第二个1被减掉了
            if(i>start&&candidates[i]==candidates[i-1]){
                continue;
            }
            list.add(candidates[i]);
            dfs(candidates,target-candidates[i],i+1,list);
            list.remove(list.size()-1);
        }
    }
}
```

难点在于减枝