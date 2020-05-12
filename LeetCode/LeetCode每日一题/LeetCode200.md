#### [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

 

> 示例 1:
>
> 输入:
> 11110
> 11010
> 11000
> 00000
> 输出: 1
> 示例 2:
>
> 输入:
> 11000
> 11000
> 00100
> 00011
> 输出: 3
> 解释: 每座岛屿只能由水平和/或竖直方向上相邻的陆地连接而成。

思路 

1. 广度优先遍历直接上就可以

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



