```java
class Solution {
    public int maxProfit(int[] prices) {
        int min=Integer.MAX_VALUE;
        int sum=0;
        for(int i=0;i<prices.length;i++){
            if(min>prices[i]){
                min=prices[i];
            }
            if(prices[i]-min>sum){
                sum=prices[i]-min;
            }
        }
        return sum;
    }
}
```

思路 

- dp 的思想，找到当前这个节点的前面最小值，然后当前这个节点最小值和前面这个元素值相减，得出值和结果相比，若更大则替换结果值。