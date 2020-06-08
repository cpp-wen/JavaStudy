#### [345. 反转字符串中的元音字母](https://leetcode-cn.com/problems/reverse-vowels-of-a-string/)

编写一个函数，以字符串作为输入，反转该字符串中的元音字母。

**示例 1:**

```
输入: "hello"
输出: "holle"
```

**示例 2:**

```
输入: "leetcode"
输出: "leotcede"
```

**说明:**
元音字母不包含字母"y"。

```java
class Solution {
    public String reverseVowels(String s) {
        //元音字母aeiou
        String a="aeiouAEIOU";
        int left=0;
        int right=s.length()-1;
        char[] array=s.toCharArray();
        while(left<right){
            while(left<right&&!a.contains(""+array[left])){
                left++;
            }
            while(left<right&&!a.contains(""+array[right])){
                right--;
            }
            //交换两个指针元素
            char tmep=array[left];
            array[left]=array[right];
            array[right]=tmep;
            left++;
            right--;
        }
        return String.valueOf(array);
    }
}
```

