```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        //滑动窗口的思想
        ArrayList<int []> list=new ArrayList<>();
        int l=1;
        int r=1;
        int sum=0;
        for(;r<target;r++){
            sum+=r;
            while(sum>target){
                sum-=l++;
            }
            if(sum==target){
                int temp[]=new int[r-l+1];
                for(int i=0;i<temp.length;i++){
                    temp[i]=l+i;
                }
                list.add(temp);
            }
        }
        int[][] res = new int[list.size()][];
        for (int i = 0; i < res.length; i++) {
            res[i] = list.get(i);
        }
        return res;
    }
}
```

思路

- 遇到连续的区间就应该想到滑动窗口 。具体的细节在编写代码时候再修改吧，