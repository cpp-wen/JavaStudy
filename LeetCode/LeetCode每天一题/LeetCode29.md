```java
class Solution {
    public int divide(int dividend, int divisor) {
        //用while循环做这样的话效率不是很高
        //使用位运算来减少减法的次数
        if(divisor==0){
            return 0;
        }
        if(dividend==Integer.MIN_VALUE&&divisor==-1){
            return Integer.MAX_VALUE;
        }
        boolean negavate=false;
        negavate=(dividend^divisor)<0;
        int res=0;
        //这边有﹣max的情况所以需要将long放在abs里面
        long a=Math.abs((long)dividend);
        long b=Math.abs((long)divisor);
        for(int i=32;i>=0;i--){
            if((a>>i)>=b){
                //因为不能使用乘法所以使用位运算
                res+=1<<i;
                //同上
                a-=b<<i;
            }
        }
        return negavate?-res:res;
    }
}
```

这题很巧妙利用二分来实现，但是国外这题的评分很低