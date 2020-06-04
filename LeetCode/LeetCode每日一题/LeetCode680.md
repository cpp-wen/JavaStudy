#### [680. 验证回文字符串 Ⅱ](https://leetcode-cn.com/problems/valid-palindrome-ii/)

难度简单173

给定一个非空字符串 `s`，**最多**删除一个字符。判断是否能成为回文字符串。

**示例 1:**

```
输入: "aba"
输出: True
```

**示例 2:**

```
输入: "abca"
输出: True
解释: 你可以删除c字符。
```

**注意:**

1. 字符串只包含从 a-z 的小写字母。字符串的最大长度是50000。



```java
class Solution {
    public boolean validPalindrome(String s) {
        //暴力 删除每一个然后暴力 过一遍
        //或者直接给左右指针的方式 遇到不相同就分成两个字符串 再进行比较
        int i=0;
        int j=s.length()-1;
        while(i<j){
            if(s.charAt(i)!=s.charAt(j)){
                return help(s,i+1,j)||help(s,i,j-1);
            }
            i++;
            j--;
        }
        return true;
    }
    public boolean help(String s,int i,int j){
        while(i<j){
            if(s.charAt(i)!=s.charAt(j)){
                return false;
            }
            i++;
            j--;
        }
        return true;
    }
}
```

