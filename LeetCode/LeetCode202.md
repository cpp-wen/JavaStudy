```java
class Solution {
    public boolean isHappy(int n) {
        //暴力求解 不行 
        //利用hash来实现如果出现重复的数组表明这数肯定不是快乐数
        //快慢指针来实现
        int slow=n;
        int fast=n;
        do{
            slow=Sum(slow);
            fast=Sum(fast);
            fast=Sum(fast);
        }while(slow!=fast);
        if(fast==1){
            return true;
        }else{
            return false;
        }
    }
    private  int Sum(int n){
        int sum=0;
        while(n!=0){
            sum+=(n%10)*(n%10);
            n/=10;
        }
        return sum;
    }
}
```

思路 

- 暴力
- 快慢指针
- hash列表