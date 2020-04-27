```java
class Solution {
    public String longestPalindrome(String s) {
        int len = s.length();
        if (len < 2) {
            return s;
        }
        boolean dp[][] = new boolean[len][len];
        for (int i = 0; i < len; i++) {
            dp[i][i] = true;
        }
        int maxlen = 1;
        int start = 0;
        //for循环顺序要能够被dp所使用
        for (int j = 1; j < len; j++) {
            for (int i = 0; i < j; i++) {
                if (s.charAt(i) == s.charAt(j)) {
                    //j 是后面的那个元素 i是前面的那个元素
                    //小于3
                    if (j - i < 3) {
                        dp[i][j] = true;
                    } else {
                        dp[i][j] = dp[i + 1][j - 1];
                    }
                } else {
                    dp[i][j] = false;
                }
                if (dp[i][j]) {
                    int current=j-i+1;
                    if(current>maxlen){
                        maxlen=current;
                        start=i;
                    }
                }
            }

        }
        return s.substring(start,start+maxlen);
    }
   
}
```

