#### [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

难度中等423

给出一个区间的集合，请合并所有重叠的区间。

**示例 1:**

```
输入: [[1,3],[2,6],[8,10],[15,18]]
输出: [[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**示例 2:**

```
输入: [[1,4],[4,5]]
输出: [[1,5]]
解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。
```

```java
 //基本思路是先根据第一维进行排序 然后循环进行合并
        if(intervals==null||intervals.length==0){
            return  new int[0][0];
        }
        Arrays.sort(intervals, (o1, o2) -> o1[0]-o2[0]);
        int length = intervals.length;
        int i=0;
        ArrayList <int []> arrayList=new ArrayList<>();
        while (i <length){
            int left=intervals[i][0];
            int right=intervals[i][1];
            //更新右边界 如果下一个节点的左边界小于等于右边界
            while(i+1<length&&right>=intervals[i+1][0]){
                i++;
                //  [[1,4],[2,3]] 所以要比较一次
                right=Math.max(intervals[i][1],right);
            }
            arrayList.add(new int[]{left,right});
            i++;
        }
        //转换为 数组
        return arrayList.toArray( new int[0][]);
```

```java
class Solution {
   public int[][] merge(int[][] intervals) {
        // 先根据第一维度进行排序 排序后判断第二维是否大于等于第一维的 是则合并 否则继续
        // 排序
        if(intervals.length==0||intervals.length==1){
            return intervals;
        }
        Arrays.sort(intervals, new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[0]-o2[0];
            }
        });
        int left=0;
        int right=1;
        ArrayList<int[]> res=new ArrayList<>();
        while(right<intervals.length){
            if(intervals[left][1]>=intervals[right][0]){
                intervals[left][1]=Math.max(intervals[left][1],intervals[right][1]);
            }else{
                res.add(intervals[left]);
                left=right;
            }
            right++;
        }
        //最后指针left需要添加
        res.add(intervals[left]);
        return res.toArray( new int[0][]);
    }
}
```

