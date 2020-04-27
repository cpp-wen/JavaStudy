```java
class Solution {
    public int maxArea(int[] height) {
        int l=0;
        int r=height.length-1;
        int res=(r-l)*Math.min(height[l],height[r]);
        int max=res;
        while(l<r){
            if(height[l]<height[r]){
                l++;
                res=(r-l)*Math.min(height[l],height[r]);
                max=Math.max(res,max);
            }else{
                r--;
                res=(r-l)*Math.min(height[l],height[r]);
                max=Math.max(res,max);
            }
        }
        return max;
    }
}
```

思路 

创建左右两个指针，依次修改最小那边的边界值。原因为当范围缩小的时候，只有最小值 改变，盛水的体积才会上升。

优化思路是将res的计算放在while后面 减少计算的次数。

```
class Solution {
    public int maxArea(int[] height) {
        int l=0;
        int r=height.length-1;
        int max;
        while(l<r){
        int res=(r-l)*Math.min(height[l],height[r]);
        	if(res>max){
        		max=res;
        	}
            if(height[l]<height[r]){
                l++;
            }else{
                r--;
            }
        }
        return max;
    }
}
```

