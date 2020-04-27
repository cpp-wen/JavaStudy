```java
class Solution {
    public int[] gardenNoAdj(int N, int[][] paths) {
        //图中的简单题 普通题中的中等题 先直接过
        //看了答案感觉还是很简单的
        Map<Integer,Set<Integer>> graph=new HashMap<>();
        for(int i=0;i<N;i++){
            graph.put(i,new HashSet<>());
        }
        for(int []path:paths){
            //将临接表分别放入到集合中
            graph.get(path[0]-1).add(path[1]-1);
            graph.get(path[1]-1).add(path[0]-1);
        }
        int res[]=new int[N];
        for(int i=0;i<N;i++){
            boolean []used=new boolean[5];
            for(int adj:graph.get(i)){
                //去除旁边花园使用过的颜色
                used[res[adj]]=true;
            }
            for(int j=1;j<=4;j++){
                //因为肯定有结果 所以 这边颜色不用回溯的办法
                if(!used[j]){
                    res[i]=j;
                }
            }

        }
        return res;

    }
}
```

