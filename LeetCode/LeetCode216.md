```java
class Solution {
    List<List<Integer>> res=new ArrayList<List<Integer>>();
    public List<List<Integer>> combinationSum3(int k, int n) {
        if(n>=55||n<1){
            return res;
        }
        dfs(1,k,n,new ArrayList<Integer>());
        return res;
    }
    private void dfs(int start,int k,int n,ArrayList<Integer> list){
        if(k==0&&n==0){
            res.add(new ArrayList<>(list));
            return;
        }
        if(n<=0||k<=0){
            return ;
        }
        for(int i=start;i<10;i++){
            list.add(i);
            dfs(i+1,k-1,n-i,list);//k--不行有坑
            list.remove(list.size()-1);
        }
    }
}
```

k-1不能替换成k--，被坑了 k++和++k，应该优先使用++k;