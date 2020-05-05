#### [1106. 解析布尔表达式](https://leetcode-cn.com/problems/parsing-a-boolean-expression/)

给你一个以字符串形式表述的布尔表达式，返回该式的运算结果。

有效的表达式需遵循以下约定：

- `"t"`，运算结果为 `True`
- `"f"`，运算结果为 `False`
- `"!(expr)"`，运算过程为对内部表达式 `expr` 进行逻辑 **非的运算**（NOT）
- `"&(expr1,expr2,...)"`，运算过程为对 2 个或以上内部表达式 `expr1, expr2, ...` 进行逻辑 **与的运算**（AND）
- `"|(expr1,expr2,...)"`，运算过程为对 2 个或以上内部表达式 `expr1, expr2, ...` 进行逻辑 **或的运算**（OR）

**示例 1：**

```
输入：expression = "!(f)"
输出：true
```

**示例 2：**

```
输入：expression = "|(f,t)"
输出：true
```

**示例 3：**

```
输入：expression = "&(t,f)"
输出：false
```

**示例 4：**

```
输入：expression = "|(&(t,f,t),!(t))"
输出：false
```

**提示：**

- `1 <= expression.length <= 20000`
- `expression[i]` 由 `{'(', ')', '&', '|', '!', 't', 'f', ','}` 中的字符组成。
- `expression` 是以上述形式给出的有效表达式，表示一个布尔值。



思路：

1. 联想波兰表达式，将计算的结果放栈中进行第二轮的运算，知道栈的元素为0
2. 使用递归的思想，将每个表达式分割出来 然后在进行合并

**栈实现**

1. 使用堆栈存储除“、”和“)”之外的字符;
2. 如果我们找到了一个’)’ ，继续从堆栈中弹出字符，直到找到一个’(’ ; 将弹出的字符添加到一个集合中。
3. 弹出操作符‘)’后弹出，根据操作符将相应的结果推入堆栈。
4. 重复上面的步骤直到结束，剩下的就是结果。

```java
class Solution {
    public boolean parseBoolExpr(String expression) {
        //定义一个双向队列来实现栈
        Deque<Character> stk=new ArrayDeque<>();
        for(int i=0;i<expression.length();i++){
            char c=expression.charAt(i);
            //判断当前元素是否为子表达式的结尾
            if(c==')'){
                Set<Character> seen=new HashSet<>();//记录子表达式中元素的值
                while(stk.peek()!='('){
                    seen.add(stk.pop());
                }
                stk.pop();//将(出栈
                char opeator=stk.pop(); //操作符出栈
                if(opeator=='&'){
                    stk.push(seen.contains('f')?'f':'t');//& 只要出现f 即为false
                }else if(opeator=='|'){
                    stk.push(seen.contains('t')?'t':'f');//| 只要出现t 即为true
                }else{
                    stk.push(seen.contains('t')?'f':'t');//！ 取反就可以
                }
            }
            else if(c!=','){
                stk.push(c);
            }
        }
        return stk.pop()=='t';
    }
}
```



**递归实现**

```java
public boolean parseBoolExpr(String expression) {
        return parse(expression, 0, expression.length());
    }
    private boolean parse(String s, int lo, int hi) {
        char c = s.charAt(lo);
        if (hi - lo == 1) return c == 't'; // 无操作符 直接返回元素的值
        boolean ans = c == '&'; // only when c is &, set ans to true; otherwise false.
        for (int i = lo + 2, start = i, level = 0; i < hi; ++i) {
            char d = s.charAt(i);
            if (level == 0 && (d == ',' || d == ')')) { // 找到一个有效的子表达式
                boolean cur = parse(s, start, i); // 返回子表达式的结果
                start = i + 1; // 下一个子表达式开始的下标
                if (c == '&') ans &= cur;
                else if (c == '|') ans |= cur;
                else ans = !cur; // c == '!'.
            }
            if (d == '(') ++level;
            if (d == ')') --level;
        }
        return ans;
    }
```







