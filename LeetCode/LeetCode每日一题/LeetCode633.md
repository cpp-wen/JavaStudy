#### [633. 平方数之和](https://leetcode-cn.com/problems/sum-of-square-numbers/)

给定一个非负整数 `c` ，你要判断是否存在两个整数 `a` 和 `b`，使得 a2 + b2 = c。

**示例1:**

```
输入: 5
输出: True
解释: 1 * 1 + 2 * 2 = 5
```

 

**示例2:**

```
输入: 3
输出: False
```

```java
class Solution {
    public boolean judgeSquareSum(int c) {
        //双指针方式计算
        int left=0;
        int right=(int)Math.sqrt(c)+1;
        while(left<=right){
            int sum=left*left+right*right;
            if(sum>c){
                right--;
            }else if(sum<c){
                left++;
            }else{
                return true;
            }
        }
        return false;
    }
}
```

