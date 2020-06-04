#### [14. 最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/)

难度简单1006

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

**示例 1:**

```
输入: ["flower","flow","flight"]
输出: "fl"
```

**示例 2:**

```
输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
```

**说明:**

所有输入只包含小写字母 `a-z` 。



```java
class Solution {
    public String longestCommonPrefix(String[] strs) {
        
        if(strs==null||strs.length==0){
            return "";
        }
        String res=strs[0];
        for(int i=1;i<strs.length;i++){
            while(!strs[i].startsWith(res)){
                res=res.substring(0,res.length()-1);
                if(res.length()==0){
                    return "";
                }
            }
        }
        return res;
    
        // StringBuffer result=new StringBuffer("");
        // if(strs.length==0){
        //     return result.toString();
        // }
        // int min=min(strs);
        // int start=0;
        // while(start<min){
        //     for(int i=0;i<strs.length-1;i++){
        //         if(strs[i].charAt(start)!=strs[i+1].charAt(start)){
        //             return result.toString();
        //         }
        //     }
        //     result.append(strs[0].charAt(start));
        //     start++;
        // }
        // return result.toString();
    }

    //找出数组最小的长度 对象当然是方法了()  数组直接length size arraylist
    public static int min(String []strs){
        int min=strs[0].length();
        int count=1;
        while(count<strs.length){
            if(strs[count].length()<min){
                min=strs[count].length();
            }
            count++;
        }
        return min;
    }
}
```

