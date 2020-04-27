```java
class Solution {
    public int numIslands(char[][] grid) {
        if(grid==null||grid.length==0){
            return 0;
        }
        //思路暴力+递归
        int res=0;
        for(int i=0;i<grid.length;i++){
            for(int j=0;j<grid[0].length;j++){
                if(grid[i][j]=='1'){
                    //进入循环
                    dfs(i,j,grid);
                    res++;
                }
            }
        }
        return res;
    }
    //上左右进行置零操作
    private void dfs(int i,int j,char [][]grid){
        int row=grid.length;
        int col=grid[0].length;
        if(i<0||i>=row||j<0||j>=col||grid[i][j]=='0'){
            return;
        }else{
            grid[i][j]='0';
            dfs(i+1,j,grid);
            dfs(i-1,j,grid);
            dfs(i,j-1,grid);
            dfs(i,j+1,grid);
        }
    }
}
```

有点回溯的影子 ，回溯的本质就是穷举递归