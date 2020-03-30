```java
class Solution {
    public double myPow(double x, int n) {
        //这边二分的思想是 用递归一样
        //直接判断可以分为几组然后进行想乘就可以 最后乘以1
        //快速幂的思想 只要乘最多31次就好
        double res=1.0;
        for(int i=n;i!=0;i/=2){
            if(i%2!=0){
                res*=x;
            }
            x*=x;
        }
        return n<0?1.0/res:res;
    }
}
```

快速幂的思想 感觉很有用