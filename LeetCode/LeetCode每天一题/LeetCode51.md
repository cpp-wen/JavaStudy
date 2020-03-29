```java
class Solution {
    //time space O(n*n!)
    public List<List<String>> solveNQueens(int n) {
        //n皇后问题 
        //感觉是回溯+bfs问题
        List<List<String>> res=new ArrayList<>();
        List<String> temp=new ArrayList<>();
        for(int i=0;i<n;i++){
            char[] c=new char[n];
            Arrays.fill(c,'.');
            temp.add(String.valueOf(c));
        }
        //回溯进行dfs
        dfs(res,temp,0);
        return res;
    }
    private void dfs(List<List<String>>res,List<String> temp,int row){
            if(row==temp.size()){
                res.add(new ArrayList<>(temp));
                return ;
            }
            //col每列进行遍历
            for(int i=0;i<temp.size();i++){
                if(!isValid(temp,row,i)){
                    continue;
                }
                char[]ch =temp.get(row).toCharArray();
                ch[i]='Q';
                temp.set(row,String.valueOf(ch));
                //进行下一行的回溯
                dfs(res,temp,row+1);
                char[] char1=temp.get(row).toCharArray();
                char1[i]='.';
                temp.set(row,String.valueOf(char1));
            }
    }
    private boolean isValid(List<String> temp,int row ,int col){
        //判断这一列是否有Q
        for(String s:temp){
            if(s.charAt(col)=='Q'){
                return false;
            }
        }
        //题目中没有明确说不能在同一条斜线上
        //判断右上是否有皇后
        for(int i=row-1,j=col+1;i>=0&&j<temp.size();i--,j++){
            if(temp.get(i).charAt(j)=='Q'){
                return false;
            }
        }
        //判断左上是否有Q
        for(int i=row-1,j=col-1;i>=0&&j>=0;i--,j--){
            if(temp.get(i).charAt(j)=='Q'){
                return false;
            }
        }
        return true;
    
    }
}
```

