```java
class Solution {
    public int[] singleNumber(int[] nums) {
        //异或之后如何操作
        //思路是全部异或后找到最低不相同的那个元素将数组分为两个部分之后分批异或就可以了
        int temp=0;
        for(int a:nums){
            temp^=a;
        }
        //进行原码跟反码刚好就是最右边的一位相同
        temp&=(-temp);
        int res[]=new int[2];
        for(int a:nums){
            //这边==0 原因是如果==1的话那么结果有问题
            if((a&temp)==0){
                res[0]^=a;
            }else{
                res[1]^=a;
            }
        }
        return res;
    }
}
```

位运算的进阶