```java
class Solution {
    int res=0;
    public int minimumTotal(List<List<Integer>> triangle) {
        //从小向上进行递归
        for(int i=triangle.size()-2;i>=0;i--){
            List<Integer> list=triangle.get(i);
            List<Integer> next=triangle.get(i+1);
            for(int k=0;k<list.size();k++){
                int temp=list.get(k);
                list.set(k,temp+Math.min(next.get(k),next.get(k+1)));
            }
        }
        return triangle.get(0).get(0);
    }
}
```

代码效率不是特别高，记录一下等待二刷的时候 记录更多思路