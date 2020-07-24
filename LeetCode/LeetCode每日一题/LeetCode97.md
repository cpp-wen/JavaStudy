#### [97. 交错字符串](https://leetcode-cn.com/problems/interleaving-string/)

给定三个字符串 *s1*, *s2*, *s3*, 验证 *s3* 是否是由 *s1* 和 *s2* 交错组成的。

**示例 1:**

```
输入: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
输出: true
```

**示例 2:**

```
输入: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
输出: false
```



主要思路两种 

1. dfs 深搜
2. dp动态规划实现

```java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        //dfs 实现 记录s1 和s2 下标 进行dfs
        return helper(s1,0,s1.length()-1,s2,0,s2.length()-1,s3,0,s3.length()-1);
    }
    public boolean helper(String s1,int a,int b,String s2,int c,int d,String s3,int e,int f){
        if(b+1+d+1!=f+1){
            return false;
        }
        for(int i=e;i<=f;i++){
            //dfs 这边分情况比较复杂   根据是否相同进行比较判断
            if(a<=b&&c<=d&&s1.charAt(a)==s2.charAt(c)){
                if(s1.charAt(a)==s3.charAt(i)){
                    return helper(s1,a+1,b,s2,c,d,s3,i+1,f)||helper(s1,a,b,s2,c+1,d,s3,i+1,f);
                }else{
                    return false;
                }
            }
            else{
                if(a<=b&&s1.charAt(a)==s3.charAt(i)){
                    a++;
                }else if(c<=d&&s2.charAt(c)==s3.charAt(i)){
                    c++;
                }else{
                    return false;
                }
            }
        }
        return true;
    }
}
```



```
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        int m = s1.length(), n = s2.length();
        if (s3.length() != m + n) return false;
        // 动态规划，dp[i,j]表示s1前i字符能与s2前j字符组成s3前i+j个字符；
        boolean[][] dp = new boolean[m+1][n+1];
        dp[0][0] = true;
        for (int i = 1; i <= m && s1.charAt(i-1) == s3.charAt(i-1); i++) dp[i][0] = true; // 不相符直接终止
        for (int j = 1; j <= n && s2.charAt(j-1) == s3.charAt(j-1); j++) dp[0][j] = true; // 不相符直接终止
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                dp[i][j] = (dp[i - 1][j] && s3.charAt(i + j - 1) == s1.charAt(i - 1))
                    || (dp[i][j - 1] && s3.charAt(i + j - 1) == s2.charAt(j - 1));
            }
        }
        return dp[m][n];
    }
}

```

