#### [45. 跳跃游戏 II](https://leetcode-cn.com/problems/jump-game-ii/)

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

你的目标是使用最少的跳跃次数到达数组的最后一个位置。

**示例:**

> 输入: [2,3,1,1,4]
> 输出: 2
> 解释: 跳到最后一个位置的最小跳跃数是 2。
>      从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。

**说明:**

假设你总是可以到达数组的最后一个位置。

**思路**：

1. 使用dp动态规划来做，数据量大的时候会超时

2. 隐式bfs 类似滑窗+bfs的结合 同时有贪心的部分思想

   > 主要观点是基于贪婪。 假设当前跳转的范围是[ curBegin，curEnd ] ，curFarthest 是[ curBegin，curEnd ]中所有点都能到达的最远点。 一旦当前点到达 curEnd，然后触发另一个跳转，并用 curFarthest 设置新的 curEnd，然后保持上面的步骤

```java
class Solution {
    public int jump(int[] nums) {
       //dp动态规划 用来保存 dp 数组初始化的值为Max.Integer 来计算
       //配合减枝条
    // 隐式bfs 贪心不是很明显 有点像滑动窗口的思想 
        if(nums.length==1){
            return  0;
        }
        int reach=0;
        int nextreach=nums[0];
        int step=0;
        for(int i=0;i<nums.length;i++){
            nextreach=Math.max(i+nums[i],nextreach);
            if(nextreach>=nums.length-1){
                return step+1;
            }
            if(i==reach){
                step++;
                reach=nextreach;
            }
        }
        return step;
    }
}
```

