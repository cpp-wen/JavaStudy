```java
class Solution {
    public int[][] updateMatrix(int[][] matrix) {
        //好像dp可以做 两次遍历 一个从上左开始 一个从下右开始  三次遍历 第三次 是上下左右都来一次
        //多源bfs 想法将0元素全部入队，然后 设置step， 这边为了避免死循环 使用了重新设置值为0
        //使用dp做
        int row=matrix.length;
        int col=matrix[0].length;
        for (int i = 0; i < row; i++) {
            for (int j = 0; j < col; j++) {
                if(matrix[i][j]==1){
                    matrix[i][j]=row+col;
                }
                if(i>0){
                    matrix[i][j]=Math.min(matrix[i-1][j]+1,matrix[i][j]);
                }
                if(j>0){
                    matrix[i][j]=Math.min(matrix[i][j-1]+1,matrix[i][j]);
                }
            }
        }
        //从右下往左上进行遍历
        for (int i = row-1; i >=0 ; i--) {
            for (int j = col-1; j >=0 ; j--) {
                if(i<row-1){ 
                    matrix[i][j] = Math.min(matrix[i][j], matrix[i + 1][j] + 1);
                }
                if(j<col-1){
                    matrix[i][j]=Math.min(matrix[i][j],matrix[i][j+1]+1);
                }
            }
        }
        return matrix;
    }
}
```



