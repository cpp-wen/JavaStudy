```java
class Solution {
    public int trap(int[] height) {
        int sum=0;
        for (int i = 1; i <height.length; i++) {
            int maxLeft=0;
            int maxRight=0;
            for(int j=i;j>=0;j--){
                maxLeft=Math.max(maxLeft,height[j]);
            }
            for (int j = i; j <height.length ; j++) {
                maxRight=Math.max(maxRight,height[j]);
            }
            if(height[i]<Math.min(maxLeft,maxRight)){
                sum+=Math.min(maxLeft,maxRight)-height[i];
            }
        }
        return sum;
    }
}
```

思路 

- 找到当前这个节点的左右两端最矮的那个节点。只有当最短的那个节点都比当前节点高才进行一个添加雨点的操作 。这边有使用双指针的一个思想。 







```java
class Solution {
    public int trap(int[] height) {
        if(height==null||height.length<=1){
            return 0;
        }
        //思路找到数组中每个元素左右两个最高的元素 进行相减
        //数组是length 链表是size
        //优化的算法 相当于进行一个dp优化，使用数组保存每个节点的左右最大值
        int res=0;
        for(int i=1;i<height.length-1;i++){
            int leftMax=0;
            int rightMax=0;
            int leftStart=0;
            int rightStart=height.length-1;
            while(leftStart<i){
                leftMax=Math.max(leftMax,height[leftStart]);
                ++leftStart;
            }
            while(rightStart>i){
                rightMax=Math.max(rightMax,height[rightStart]);
                --rightStart;
            }
            int min=Math.min(leftMax,rightMax);
            if(min>height[i]){
                res+=min-height[i];
            }
        }
        return res;
    }
}
```

第二遍自己写的代码 效率不是很高