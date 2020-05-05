#### [743. 网络延迟时间](https://leetcode-cn.com/problems/network-delay-time/)

有 N 个网络节点，标记为 1 到 N。

给定一个列表 times，表示信号经过有向边的传递时间。 times[i] = (u, v, w)，其中 u 是源节点，v 是目标节点， w 是一个信号从源节点传递到目标节点的时间。

现在，我们从某个节点 K 发出一个信号。需要多久才能使所有节点都收到信号？如果不能使所有节点收到信号，返回 -1。

 

示例：

![](https://pic-go-youdaoyun-image.oss-cn-beijing.aliyuncs.com/pic-go-youdaoyun-image/20200505112818.png)

> 输入：times = [[2,1,1],[2,3,1],[3,4,1]], N = 4, K = 2
> 输出：2


注意:

- N 的范围在 [1, 100] 之间。
- K 的范围在 [1, N] 之间。
- times 的长度在 [1, 6000] 之间。
- 所有的边 times[i] = (u, v, w) 都有 1 <= u, v <= N 且 0 <= w <= 100。





```java
class Solution {
    //建立邻接表 使用bfs 的思想进行广度遍历最后判断map中元素个数是否达到所有n 以及挑选最大时间点
    //基本思路部分正确 
        public int networkDelayTime(int[][] times, int N, int K) {
        //Dij算法
        //初始化邻接矩阵 -1表示 i j之间无通路
        int [][]graph=new int[N+1][N+1];
        for(int i=1;i<N+1;i++){
            for(int j=1;j<N+1;j++){
                graph[i][j]=-1;
            }
        }    
        for(int []edge:times){
            //构建 邻接矩阵 距离
            graph[edge[0]][edge[1]]=edge[2];
        }
        //k - 每个节点的距离 初始化 max 
        int []dist=new int [N+1];
        for(int i=1;i<N+1;i++){
            dist[i]=Integer.MAX_VALUE;
        }
        dist[K]=0;
        //创建链表 将当前节点所能到达的节点添加到 链表中不断记录最小距离
        Queue<Integer> q=new LinkedList<>();
        q.add(K);
        while(!q.isEmpty()){
            //已到达节点
            Integer poll=q.poll();
            for(int i=1;i<N+1;i++){
                if(graph[poll][i]==-1)continue;
                //如果距离更小 则进行更新
                if(dist[i]>dist[poll]+graph[poll][i]){
                    dist[i]=dist[poll]+graph[poll][i];
                    if(!q.contains(i)){
                        q.add(i);
                    }
                }
            }
        }
        int res=0;
        //遍历判断
        for(int i=1;i<N+1;i++){
            if(dist[i]==Integer.MAX_VALUE) return -1;
            res=Math.max(res,dist[i]);
        }
        return res;
    }
}
```

