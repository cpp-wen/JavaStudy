```java
class Solution {
    public boolean wordBreak(String s, List<String> wordDict) {
        //不断递归删除s中出现的wordDict 的值，如果最后wordDict的值为0的时候就表示返回true 当出现重复元素的时候会出问题
        //题目意思理解错误 题目的意思是能否拆分s，使得 s中元素中元素都匹配wordDict 
        int length=s.length();
        boolean dp[]=new boolean[length+1];
        dp[0]=true;
        for(int i=1;i<=length;i++){
            for(int j=0;j<i;j++){
                //这边是判断leet是否在wordDict中以及leet 后面一个元素为开始到j 以及wordDict元素中
                if(dp[j]&&wordDict.contains(s.substring(j,i))){
                    dp[i]=true;
                    //break 减枝
                }
            }
        }
        return dp[length];
    }
}
```

