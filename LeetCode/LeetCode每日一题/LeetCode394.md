#### [394. 字符串解码](https://leetcode-cn.com/problems/decode-string/)

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: `k[encoded_string]`，表示其中方括号内部的 *encoded_string* 正好重复 *k* 次。注意 *k* 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 *k* ，例如不会出现像 `3a` 或 `2[4]` 的输入。

**示例:**

```
s = "3[a]2[bc]", 返回 "aaabcbc".
s = "3[a2[c]]", 返回 "accaccacc".
s = "2[abc]3[cd]ef", 返回 "abcabccdcdcdef".
```

```java
class Solution {
    public String decodeString(String s) {
        //感觉是用栈
        if(s.length()==0){
            return "";
        }
        Stack<Character> stack=new Stack<>();
        char[] Characters=s.toCharArray();
        int i=0;
        stack.add(Characters[i]);
        i++;
        while(i<s.length()){
            if(Characters[i]==']'){
                StringBuilder tempBuilder =new StringBuilder();
                char c=stack.pop();
                while(c!='['){
                    tempBuilder.append(c);
                    c=stack.pop();
                }
                char []tempArray=tempBuilder.toString().toCharArray();
                StringBuilder tempNumber=new StringBuilder();
                while(!stack.isEmpty()&&Character.isDigit(stack.peek())){
                    tempNumber.append(stack.pop());
                }
                // 转int 
                int t=Integer.parseInt(tempNumber.reverse().toString());
                while(t!=0){
                    for(int k=tempArray.length-1;k>=0;k--){
                        stack.add(tempArray[k]);
                    }
                    t--;
                }
            }
            else{
                stack.add(Characters[i]);
            }
            i++;
        }
        StringBuilder stringBuilder =new StringBuilder();
        while(!stack.isEmpty()){
            stringBuilder.append(stack.pop());
        }
        return stringBuilder.reverse().toString();

    }
}
```



