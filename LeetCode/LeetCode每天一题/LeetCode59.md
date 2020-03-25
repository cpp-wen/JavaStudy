```java
class MaxQueue {
    //思路是用两个队列来保存最大值的结果和整个队列
    private Deque<Integer> queue;
    private Deque<Integer> help;
    public MaxQueue() {
        queue=new ArrayDeque<Integer>();
        help=new ArrayDeque<Integer>();
    }
    
    public int max_value() {
        if(help.isEmpty()){
            return -1;
        }
        return help.peek();
    }
    
    public void push_back(int value) {
        //放入元素
        queue.offer(value);
        //比这个元素小的元素出队列
        while(!help.isEmpty()&&help.peekLast()<value){
            help.pollLast();
        }
        help.offer(value);
    }
    
    public int pop_front() {
        //放出元素
        if(queue.isEmpty()){
            return -1;
        }
        int val=queue.pop();
        if(help.peek()==val){
            help.pop();
        }
        return val;


    }
}

/**
 * Your MaxQueue object will be instantiated and called as such:
 * MaxQueue obj = new MaxQueue();
 * int param_1 = obj.max_value();
 * obj.push_back(value);
 * int param_3 = obj.pop_front();
 */
```

思路

-  思路是用两个队列来保存最大值的结果和整个队列