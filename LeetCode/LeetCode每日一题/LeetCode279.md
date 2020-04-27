```java
class Solution {
    public int numSquares(int n) {
        //bfs肯定可以
        //dp的做法看了题解
        int []array=new int[n+1];
        Arrays.fill(array,Integer.MAX_VALUE);
        array[0]=0;
        array[1]=1;
        for(int i=2;i<=n;i++){
            for(int j=1;j*j<=i;j++){
                array[i]=Math.min(array[i],array[i-j*j]+1);
            }
        }
        return array[n];
    }
}
```

动态规划和bfs以及数学方法都可以实现。数学方法不推荐