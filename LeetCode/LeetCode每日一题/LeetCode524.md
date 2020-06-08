#### [524. 通过删除字母匹配到字典里最长单词](https://leetcode-cn.com/problems/longest-word-in-dictionary-through-deleting/)

给定一个字符串和一个字符串字典，找到字典里面最长的字符串，该字符串可以通过删除给定字符串的某些字符来得到。如果答案不止一个，返回长度最长且字典顺序最小的字符串。如果答案不存在，则返回空字符串。

**示例 1:**

```
输入:
s = "abpcplea", d = ["ale","apple","monkey","plea"]

输出: 
"apple"
```

**示例 2:**

```
输入:
s = "abpcplea", d = ["a","b","c"]

输出: 
"a"
```

**说明:**

1. 所有输入的字符串只包含小写字母。
2. 字典的大小不会超过 1000。
3. 所有输入的字符串长度不会超过 1000。

```java
class Solution {
    public String findLongestWord(String s, List<String> d) {
        //hash来做 时间复杂度有点高
        //双指针通过对比s和d字符串进行比较最后获得答案
        String res="";
        for(int i=0;i<d.size();i++){
            String tempS=d.get(i);
            int tempSLength=tempS.length();
            if(res.length()>tempSLength){
                continue;
            }
            int left=0;
            int right=0;
            while(left<s.length()){
                if(s.charAt(left)==tempS.charAt(right)){
                    right++;
                }
                left++;
                if(right==tempSLength){
                    //这边进行比较
                    if(tempSLength==res.length()){
                        //进行比较
                        if(res.compareTo(tempS)>0){
                            res=tempS;
                            
                        }
                    }else{
                        res=tempS;
                       
                    }
                    break;
                }
            }
        }
        return res;
    }
}
```

