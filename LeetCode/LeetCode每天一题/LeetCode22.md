```java
class Solution {
    ArrayList res=new ArrayList<String>();
     public List<String> generateParenthesis(int n) {
        dfs("",0,0,n);
        return res;
    }
    //dfs进行回溯
    private  void dfs(String s,int left,int right,int n){
        //提前抛出这个异常
        if(right>left||left>n||right>n){
            return;
        }
        if(s.length()==2*n){
            res.add(s);
        }
        else{
            dfs(s+'(',left+1,right,n);
            dfs(s+')',left,right+1,n);
        }
    }
}
```

