```java
class Solution {
    public static int [][]p;
    public int[][] kClosest(int[][] points, int K) {
        //会用O(n) 优先队列来一遍
        //分治的思想 因为题目中说可以按照顺序返回任意顺序 所以答案可以不用排序降低时间复杂度
        this.p=points;
        work(0,p.length-1,K);
        return Arrays.copyOfRange(p,0,K);
    }
    public void work(int low,int high,int K){
        if(low>=high) return;
        int temp=dist(low);
        int j=low;
        for(int i=low+1;i<high+1;i++){
            //这边思想是运用快排的思想
            if(dist(i)<=temp){
                j++;
                if(i!=j){
                    swap(i,j);
                }
            }
        }
        swap(low,j);
        if(j-low+1<K) work(j+1,high,K-(j-low+1));
        else if(j-low+1>K) work(low,j,K);
        else return;
    }
    public void swap(int i,int j){
        int t0=p[i][0];
        int t1=p[i][1];
        p[i][0]=p[j][0];
        p[i][1]=p[j][1];
        p[j][0]=t0;
        p[j][1]=t1;
    }
    public int dist(int i){
        return p[i][0]*p[i][0]+p[i][1]*p[i][1];
    }
}
```

堆的思路不贴了，使用java优先队列实现难度不是很大

大顶堆用来求最小TOPk 

小顶堆用来求最大TOPk