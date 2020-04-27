```java
class Solution {
    public List<List<String>> partition(String s) {
        //有点像 递归+回溯来实现 果然就是
        List<List<String>> res=new ArrayList<List<String>>();
        dfs(s,0,res,new ArrayList<String>());
        return res;
    }
    //深搜 list保存回文的string
    private void dfs(String s,int start,List<List<String>> res,List<String> list){
        if(start==s.length()){
            res.add(new ArrayList<>(list));//引用问题
            return ;
        }
        for(int i=0;i+start<s.length();i++){
            String subString=s.substring(start,start+i+1);
            if(!isPali(subString)){
                continue;
            }
            list.add(subString);//加入字符串
            dfs(s,start+i+1,res,list);
            list.remove(list.size()-1);
        }
        
    }
    //回文判断
    private boolean isPali(String s){
        if(s==null||s.length()<=1){
            return true;
        }
        int start=0;
        int last=s.length()-1;
        while(start<last){
            if(s.charAt(start)!=s.charAt(last)){
                return false;
            }
            start++;
            last--;
        }
        return true;
    }
    
}
```

