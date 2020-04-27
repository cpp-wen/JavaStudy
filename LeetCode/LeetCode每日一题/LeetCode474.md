```java
public class LeetCode474 {
    public static void main(String[] args) {

    }
    private int[] change(String a) {
        char[] aa = a.toCharArray();
        int res[] = new int[2];
        for (char b : aa) {
            res[b - '0']++;
        }
        return res;
    }

    public int findMaxForm(String[] strs, int m, int n) {
        //想要求什么就将什么 作为dp的参数的一维
        int sLength = strs.length;
        int dp[][][] = new int[sLength + 1][m + 1][n + 1];
        for (int i = 1; i <= sLength; i++) {
            int[] temp = change(strs[i-1]);
            int jNumber=temp[0];
            int kNumber=temp[1];
            for (int j = 0; j <= m; j++) {
                for (int k = 0; k <= n; k++) {
                    dp[i][j][k]=dp[i-1][j][k];
                    if(j>=jNumber&&k>=kNumber){
                        dp[i][j][k]=Math.max(dp[i-1][j][k],dp[i-1][j-jNumber][k-kNumber]+1);
                    }
                }
            }
        }
        return dp[sLength][m][n];
    }
}
```

