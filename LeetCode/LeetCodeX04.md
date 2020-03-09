```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        //时间复杂度 O(m+n) 空间复杂度O(1)
       if(matrix==null||matrix.length==0){
           return false;
       }
       int row=matrix.length;
       int col=matrix[0].length;
       int m=0;
       int n=col-1;
       while(m<row&&n>=0){
           if(matrix[m][n]<target){
               m++;
           }else if(matrix[m][n]>target){
               n--;
           }else{
               return true;
           }
       }
       return false;
    }
}
```

思路 

-  借鉴二叉树 的思想 从右上角开始遍历