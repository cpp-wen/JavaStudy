#### [202. 快乐数](https://leetcode-cn.com/problems/happy-number/)

编写一个算法来判断一个数 n 是不是快乐数。

「快乐数」定义为：对于一个正整数，每一次将该数替换为它每个位置上的数字的平方和，然后重复这个过程直到这个数变为 1，也可能是 无限循环 但始终变不到 1。如果 可以变为  1，那么这个数就是快乐数。

如果 n 是快乐数就返回 True ；不是，则返回 False 。

>**示例：**

```
输入：19
输出：true
解释：
12 + 92 = 82
82 + 22 = 68
62 + 82 = 100
12 + 02 + 02 = 1
```

思路 

- 暴力
- 快慢指针
- hash列表

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

```java
class Solution {
    public boolean isHappy(int n) {
        //循环100次 如果还不等于1 那么就不是快乐数
        int sum=0;
        for(int i=0;i<100;i++){
            while(n!=0){
                sum+=Math.pow(n%10,2);
                n/=10;
            }
            if(sum==1){
                return true;
            }else{
                n=sum;
                //一开始漏了这句话
                sum=0;
            }
        }
        return false;
    }
}
```

