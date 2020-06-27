#### [218. 天际线问题](https://leetcode-cn.com/problems/the-skyline-problem/)

城市的天际线是从远处观看该城市中所有建筑物形成的轮廓的外部轮廓。现在，假设您获得了城市风光照片（图A）上**显示的所有建筑物的位置和高度**，请编写一个程序以输出由这些建筑物**形成的天际线**（图B）。

[![Buildings](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/skyline1.png) ](https://leetcode-cn.com/static/images/problemset/skyline1.jpg)[![Skyline Contour](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/22/skyline2.png)](https://leetcode-cn.com/static/images/problemset/skyline2.jpg)

每个建筑物的几何信息用三元组 `[Li，Ri，Hi]` 表示，其中 `Li` 和 `Ri` 分别是第 i 座建筑物左右边缘的 x 坐标，`Hi` 是其高度。可以保证 `0 ≤ Li, Ri ≤ INT_MAX`, `0 < Hi ≤ INT_MAX` 和 `Ri - Li > 0`。您可以假设所有建筑物都是在绝对平坦且高度为 0 的表面上的完美矩形。

例如，图A中所有建筑物的尺寸记录为：`[ [2 9 10], [3 7 15], [5 12 12], [15 20 10], [19 24 8] ] `。

输出是以 `[ [x1,y1], [x2, y2], [x3, y3], ... ]` 格式的“**关键点**”（图B中的红点）的列表，它们唯一地定义了天际线。**关键点是水平线段的左端点**。请注意，最右侧建筑物的最后一个关键点仅用于标记天际线的终点，并始终为零高度。此外，任何两个相邻建筑物之间的地面都应被视为天际线轮廓的一部分。

例如，图B中的天际线应该表示为：`[ [2 10], [3 15], [7 12], [12 0], [15 10], [20 8], [24, 0] ]`。

**说明:**

- 任何输入列表中的建筑物数量保证在 `[0, 10000]` 范围内。
- 输入列表已经按左 `x` 坐标 `Li` 进行升序排列。
- 输出列表必须按 x 位排序。
- 输出天际线中不得有连续的相同高度的水平线。例如 `[...[2 3], [4 5], [7 5], [11 5], [12 7]...]` 是不正确的答案；三条高度为 5 的线应该在最终输出中合并为一个：`[...[2 3], [4 5], [12 7], ...]`

```java
public List<List<Integer>> getSkyline(int[][] buildings) {
        // x轴从小到大排序，如果x相等，则按照高度从低到高排序
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[0] != b[0] ? a[0] - b[0] : a[1] - b[1]);
        for (int[] building : buildings) {
            // 左端点和高度入队，高度为负值说明是左端点
            pq.offer(new int[] { building[0], -building[2] });
            // 右端点和高度入队
            pq.offer(new int[] { building[1], building[2] });
        }

        List<List<Integer>> res = new ArrayList<>();

        // 降序排列
        TreeMap<Integer, Integer> heights = new TreeMap<>((a, b) -> b - a);
        heights.put(0, 1);
        int left = 0, height = 0;
        
        while (!pq.isEmpty()) {
            int[] arr = pq.poll();
            // 如果是左端点
            if (arr[1] < 0) {
                // 高度 --> 高度 + 1
                heights.put(-arr[1], heights.getOrDefault(-arr[1], 0) + 1);
            } 
            // 右端点
            else {
                // 高度 --> 高度 - 1
                heights.put(arr[1], heights.get(arr[1]) - 1);
                // 说明左右端点都已经遍历完
                if (heights.get(arr[1]) == 0) heights.remove(arr[1]);
            }
            // heights是以降序的方式排列的，所以以下会获得最大高度
            int maxHeight = heights.keySet().iterator().next();
            // 如果最大高度不变，则说明当前建筑高度在一个比它高的建筑下面，不做操作
            if (maxHeight != height) {
                left = arr[0];
                height = maxHeight;
                res.add(Arrays.asList(left, height));
            }
        }
        return res;
    }
```

感觉这个解法应该是利用单调堆来实现的