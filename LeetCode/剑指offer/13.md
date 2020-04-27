```java
class Solution {
    public int movingCount(int m, int n, int k) {
            //bfs进行广度优先遍历
            //dfs进行深度优先遍历也可以解决问题
            boolean [][]isfound=new boolean [m][n];
            return  dfs(0,0,m,n,k,isfound);
    }
    private int dfs(int i,int j,int m,int n,int k,boolean[][] array){
        if(i<0||i>=m||j<0||j>=n||array[i][j]||i/10+i%10+j/10+j%10>k){
            return 0;
        }
        //直接改变为 向下和右就可以
        array[i][j]=true;
        return  dfs(i+1,j,m,n,k,array)+dfs(i,j+1,m,n,k,array)+1;
    }
}
```

dfs和bfs都可以实现，相对来说 dfs的代码更加简洁