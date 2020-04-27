```java
public int countSquares(int [][]matrix){
        int m=matrix.length;
        int n=matrix[0].length;
        int len=Math.min(m,n);
        boolean [][][]dp=new boolean[m][n][len];
        int count=0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                dp[i][j][0]=(matrix[i][j]==1);
                count+=matrix[i][j];
            }
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j <n ; j++) {
                for (int k = 0; k < len; k++) {
                    //三维dp一般人还真想不到
                    dp[i][j][k]=(matrix[i][j]==1&&dp[i-1][j][k-1]&&dp[i][j-1][k-1]&&dp[i-1][j-1][k-1]);
                    if(dp[i][j][k]){
                        count++;
                    }
                }
            }
        }
        return count;
    }
```



```java
class Solution {
        public int countSquares(int[][] matrix) {
            int m = matrix.length;
            int n = matrix[0].length;
            int[][] dp = new int[m][n];
            int ans = 0;
            // 预处理每一行和每一列
            for (int i = 0; i < m; i++) {
                ans += dp[i][0] = matrix[i][0];
            }
            for (int j = 0; j < n; j++) {
                ans += dp[0][j] = matrix[0][j];
            }
            // 上述过程(0, 0)判断了两次, 如果matrix[0][0] == 1，说明ans多算了一个
            if (matrix[0][0] == 1) {
                ans--;
            }
            for (int i = 1; i < m; i++) {
                for (int j = 1; j < n; j++) {
                    if (matrix[i][j] == 1) {
                        dp[i][j] = Math.min(Math.min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                        ans += dp[i][j];
                    }
                }
            }
            return ans;
        }
    }
```

这个dp