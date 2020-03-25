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