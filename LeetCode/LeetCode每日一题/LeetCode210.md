```java
class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        //拓扑排序这个跟昨天的题目不是一样吗 保存一下记录的结果就可以了
        //添加了一个数组表达数组中元素的入队的个数
        ArrayList<ArrayList<Integer>> list=new ArrayList<>();
        int indegress[]=new int[numCourses];
        for(int i=0;i<numCourses;i++){
            list.add(new ArrayList<>());
        }
        int res[]=new int[numCourses];
        int k=0;
        int count=numCourses;
        for(int i=0;i<prerequisites.length;i++){
            indegress[prerequisites[i][0]]++;//入队元素放入 
            list.get(prerequisites[i][1]).add(prerequisites[i][0]);//如果当前元素出队，那么下个元素 入队的元素的个数都要减少
        }
        //空队列集合
        LinkedList<Integer> kong=new LinkedList<>();
        for(int i=0;i<numCourses;i++){
            if(indegress[i]==0){
                kong.offer(i);
            }
        }
        //开启bfs进行迭代的过程
        while(!kong.isEmpty()){
            int temp=kong.poll();
            res[k++]=temp;
            numCourses--;
            //一个元素如果出来的话，他后面的元素的入队都可以减少1
            for(int cur:list.get(temp)){
                indegress[cur]--;
                if(indegress[cur]==0){
                    kong.offer(cur);
                }
            }
        }

        return numCourses==0?res:new int[0];
    }
}
```
