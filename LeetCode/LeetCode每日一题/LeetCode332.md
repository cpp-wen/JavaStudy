

```java
class Solution {
    HashMap<String,PriorityQueue<String>> map=new HashMap<>();
    LinkedList<String> res=new LinkedList<String>();
    public List<String> findItinerary(List<List<String>> tickets) {
        //hashmap保存 开始地点以及 下一跳地址 深搜 +回溯的思想 hashmap<String,HashSet<>()> 保存下一跳
        //大佬题解 换hashset变优先队列 解决字符串排序问题   学习dfs 递归写法
        //这边是默认最下排序一定有解 如果最小排序没有解可能就过不了
        for(List<String> ticket:tickets){
            String s1=ticket.get(0);
            String s2=ticket.get(1);
            if(!map.containsKey(s1)){
                PriorityQueue<String> pq=new PriorityQueue<>();
                map.put(s1,pq);
            }
            map.get(s1).add(s2);
        }
        dfs("JFK");
        return res;
    }
    private void dfs(String src){
        PriorityQueue<String> pq=map.get(src);
        while(pq!=null&&!pq.isEmpty()){
            dfs(pq.poll());
        }
        res.addFirst(src);
    }
}
```

