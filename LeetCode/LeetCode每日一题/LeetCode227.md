#### [227. 基本计算器 II](https://leetcode-cn.com/problems/basic-calculator-ii/)

实现一个基本的计算器来计算一个简单的字符串表达式的值。

字符串表达式仅包含非负整数，`+`， `-` ，`*`，`/` 四种运算符和空格 ` `。 整数除法仅保留整数部分。

**示例 1:**

```
输入: "3+2*2"
输出: 7
```

**示例 2:**

```
输入: " 3/2 "
输出: 1
```

**示例 3:**

```
输入: " 3+5 / 2 "
输出: 5
```

**说明：**

- 你可以假设所给定的表达式都是有效的。
- 请**不要**使用内置的库函数 `eval`。



```java
class Solution {
    public int calculate(String s) {
        //暴力方法 1先去空格 转stringbuffer
        //遍历的时候 找到前后计算结果后在不断继续遍历
        //使用栈来解决
        char sign='+';
        Stack<Integer> numsStack=new Stack<>();
        int num=0;
        int result=0;
        for(int i=0;i<s.length();i++){
            char cur=s.charAt(i);
            if(cur>='0'){
                num=num*10-'0'+cur;
            }
            //+-*/ asc2码都小于 数字
            if((cur<'0'&&cur!=' ')||i==s.length()-1){
                switch(sign){
                    case '+':numsStack.push(num);break;
                    case '-':numsStack.push(-num);break;
                    case '*':numsStack.push(numsStack.pop()*num);break;
                    case '/':numsStack.push(numsStack.pop()/num);break;
                }
                //保存下一次计算的上一个符号位标记
                sign=cur;
                num=0;
            }
        }
        while(!numsStack.isEmpty()){
            result+=numsStack.pop();
        }
        return result;
    }
}
```

