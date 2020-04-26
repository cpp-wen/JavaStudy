```java
class Solution {
    public int getSum(int a, int b) {
        //这种题目能怎么办 ，只能硬背了 刚把得
        int sum,carry;
        sum=a^b;
        carry =(a&b)<<1;
        if(carry!=0){
            return getSum(sum,carry);
        }
        return sum;
    }
}
```

