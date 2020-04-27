```java
class Solution {
    public boolean exist(char[][] board, String word) {
       //单词搜索 dfs+回溯进行实现
       boolean result=false;
       for(int i=0;i<board.length;i++){
           for(int j=0;j<board[0].length;j++){
               //提前在这边判断首字母相等可以提高很多效率
               if(word.charAt(0)!=board[i][j]){
                   continue;
               }
               result=dfs(board,word,new boolean[board.length][board[0].length],0,i,j);
               if(result){
                   return true;
               }
           }
       }
       return false;
    }
    // ij 下标
    private boolean  dfs(char [][]board,String word,boolean [][]isSearch,int index,int i,int j){
        if(index==word.length()){
            return true;
        }
        if(i<0||i>=board.length||j<0||j>=board[0].length||isSearch[i][j]||board[i][j]!=word.charAt(index)){
            return false;
        }
        isSearch[i][j]=true;
         if(dfs(board,word,isSearch,index+1,i+1,j)||dfs(board,word,isSearch,index+1,i-1,j)||dfs(board,word,isSearch,index+1,i,j+1)||dfs(board,word,isSearch,index+1,i,j-1)){
            return true;
        }
        isSearch[i][j]=false;
        return false;
    }
}
```

思路是回溯+遍历 