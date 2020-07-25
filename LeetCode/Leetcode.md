# LeetCode算法

- [回溯](#回溯)

- [String](#String)

- [哈希](#哈希)

- [链表](#链表)

- [二分查找](#二分查找)

- [双指针or滑动窗口](#双指针or滑动窗口)

- [快慢指针](#快慢指针)

- [动态规划](#动态规划)

- [数组](#数组)

- [树](#树)

- [前缀树](#前缀树)

- [贪心](#贪心)

- [dfs+bfs](#dfs+bfs)

- [设计类](#设计类)

- [堆 排序 分治](#堆 排序 分治)

  

| 题目                                                         | 难度 | 题解                                             | 类型 |
| ------------------------------------------------------------ | ---- | ------------------------------------------------ | ---- |
| <span id = "哈希" style="font-size:150%;"><b>回溯</b></span> |  |  |  |
| [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/) | 中等 | [LeetCode22](./LeetCode每日一题/LeetCode22.md) | |
| [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/) | 中等 | [LeetCode39](./LeetCode每日一题/LeetCode39.md) | |
| [40. 组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/) | 中等 | [LeetCode40](./LeetCode每日一题/LeetCode40.md) | |
| [46. 全排列](https://leetcode-cn.com/problems/permutations/) | 中等 | [LeetCode46](./LeetCode每日一题/LeetCode46.md) | |
| [47. 全排列 II](https://leetcode-cn.com/problems/permutations-ii/) | 中等 | [LeetCode47](./LeetCode每日一题/LeetCode47.md) | |
| [79. 单词搜索](https://leetcode-cn.com/problems/word-search/) | 中等 | [LeetCode79](./LeetCode每日一题/LeetCode79.md) | |
| [90. 子集 II](https://leetcode-cn.com/problems/subsets-ii/) | 中等 | [LeetCode90](./LeetCode每日一题/LeetCode90.md) | |
| [131. 分割回文串](https://leetcode-cn.com/problems/palindrome-partitioning/) | 中等 | [LeetCode131](./LeetCode每日一题/LeetCode131.md) | |
| [216. 组合总和 III](https://leetcode-cn.com/problems/combination-sum-iii/) | 中等 | [LeetCode216](./LeetCode每日一题/LeetCode216.md) | |
| [401. 二进制手表](https://leetcode-cn.com/problems/binary-watch/) | 简单 | [LeetCode401](./LeetCode每日一题/LeetCode401.md) | |
| [212. 单词搜索 II](https://leetcode-cn.com/problems/word-search-ii/) | 困难 | [LeetCode212](./LeetCode每日一题/LeetCode212.md) | |
| <span id = "String" style="font-size:150%;"><b>String</b></span> |  |  |  |
| [179. 最大数](https://leetcode-cn.com/problems/largest-number/) | 中等 | [LeetCode179](./LeetCode每日一题/LeetCode179.md) |  |
| [680. 验证回文字符串 Ⅱ](https://leetcode-cn.com/problems/valid-palindrome-ii/) | 简单 | [LeetCode680](./LeetCode每日一题/LeetCode680.md) | |
| [227. 基本计算器 II](https://leetcode-cn.com/problems/basic-calculator-ii/) | 中等 | [LeetCode227](./LeetCode每日一题/LeetCode227.md) | |
| [14. 最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/) | 简单 | [LeetCode14](./LeetCode每日一题/LeetCode14.md) | |
| <span id = "哈希" style="font-size:150%;"><b>哈希</b></span> |      |                                                  |      |
| [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)     | 简单 | [LeetCode01](./LeetCode每日一题/LeetCode01.md)   |      |
| [36. 有效的数独](https://leetcode-cn.com/problems/valid-sudoku/) | 中等 | [LeetCode36](./LeetCode每日一题/LeetCode36.md)  |      |
| [560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/) | 中等 | [LeetCode560](./LeetCode每日一题/LeetCode560.md) |      |
|  |  | | |
| <span id = "链表" style="font-size:150%;"><b>链表</b></span> |      |                                                  |      |
| [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/) | 简单 | [LeetCode206](./LeetCode每日一题/LeetCode206.md) |      |
| [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/) | 中等 | [LeetCode2](./LeetCode每日一题/LeetCode02.md)    |      |
| [138. 复制带随机指针的链表](https://leetcode-cn.com/problems/copy-list-with-random-pointer/) | 中等 | [LeetCode138](./LeetCode每日一题/LeetCode138.md) |      |
| [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/) | 困难 | [LeetCode138](./LeetCode每日一题/LeetCode25.md)  |      |
| [445. 两数相加 II](https://leetcode-cn.com/problems/add-two-numbers-ii/) | 中等 | [LeetCode445](./LeetCode每日一题/LeetCode445.md) |      |
| [148. 排序链表](https://leetcode-cn.com/problems/sort-list/) | 中等 | [LeetCode148](./LeetCode每日一题/LeetCode148.md) | |
| <span id = "二分查找" style="font-size:150%;"><b>二分查找</b></span> |      |                                                  |      |
| [29. 两数相除](https://leetcode-cn.com/problems/divide-two-integers/) | 中等 | [LeetCode29](./LeetCode每日一题/LeetCode29.md)   |      |
| [410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/) | 困难 | [LeetCode410](./LeetCode每日一题/LeetCode410.md) | dp也可解 |
| <span id = "双指针or滑动窗口" style="font-size:150%;"><b>双指针or滑动窗口</b></span> |      |                                                  |      |
| [167. 两数之和 II - 输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/) | 简单 | [LeetCode167](./LeetCode每日一题/LeetCode167.md) | |
| [633. 平方数之和](https://leetcode-cn.com/problems/sum-of-square-numbers/) | 简单 | [LeetCode633](./LeetCode每日一题/LeetCode633.md) | |
| [345. 反转字符串中的元音字母](https://leetcode-cn.com/problems/reverse-vowels-of-a-string/) | 简单 | [LeetCode345](./LeetCode每日一题/LeetCode345.md) | |
| [680. 验证回文字符串 Ⅱ](https://leetcode-cn.com/problems/valid-palindrome-ii/) | 简单 | [LeetCode680](./LeetCode每日一题/LeetCode680.md) | |
| [88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/) | 简单 | [LeetCode88](./LeetCode每日一题/LeetCode88.md) | |
| [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/) | 简单 | [LeetCode141](./LeetCode每日一题/LeetCode141.md) | |
| [524. 通过删除字母匹配到字典里最长单词](https://leetcode-cn.com/problems/longest-word-in-dictionary-through-deleting/) | 中等 | [LeetCode524](./LeetCode每日一题/LeetCode524.md) | |
| <span id = "数组" style="font-size:150%;"><b>数组</b></span> | | | |
| [215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/) | 中等 | [LeetCode215](./LeetCode每日一题/LeetCode215.md) | 一题多解 |
| [152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/) | 中等 | [LeetCode152](./LeetCode每日一题/LeetCode152.md) | 在线解法 |
| [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/) | 中等 | [LeetCode56](./LeetCode每日一题/LeetCode56.md) | 入门题目 |
| [41. 缺失的第一个正数](https://leetcode-cn.com/problems/first-missing-positive/) | 困难 | [LeetCode41](./LeetCode每日一题/LeetCode41.md) | 思路奇特 |
| [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/) | 中等 | [LeetCode287](./LeetCode每日一题/LeetCode287.md) | 双指针和数组结合 |
| [974. 和可被 K 整除的子数组](https://leetcode-cn.com/problems/subarray-sums-divisible-by-k/) | 中等 | [LeetCode974](./LeetCode每日一题/LeetCode974.md) | 前缀和 |
| [128. 最长连续序列](https://leetcode-cn.com/problems/longest-consecutive-sequence/) | 困难 | [LeetCode128](./LeetCode每日一题/LeetCode128.md) | hash算法 时间换空间 |
|  |  |  |  |
| <span id = "快慢指针" style="font-size:150%;"><b>快慢指针</b></span> | | | |
| [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/) | 简单 | [LeetCode141](./LeetCode每日一题/LeetCode141.md) |  |
| [202. 快乐数](https://leetcode-cn.com/problems/happy-number/) | 简单 | [LeetCode202](./LeetCode每日一题/LeetCode202.md) | 一题多解 |
|                                                              |      |                                                  |      |
| <span id = "动态规划" style="font-size:150%;"><b>动态规划</b></span> |  |  |  |
| [983. 最低票价](https://leetcode-cn.com/problems/minimum-cost-for-tickets/) | 中等 | [LeetCode983](./LeetCode每日一题/LeetCode983.md) | 纯dp |
| [542. 01 矩阵](https://leetcode-cn.com/problems/01-matrix/) | 中等 | [LeetCode542](./LeetCode每日一题/LeetCode542.md) | 矩阵问题 bfs可解决 dp更简单 |
| [474. 一和零](https://leetcode-cn.com/problems/ones-and-zeroes/) | 中等 | [LeetCode474](./LeetCode每日一题/LeetCode474.md) | 1和0  背包问题 |
| [416. 分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/) | 中等 | [LeetCode416](./LeetCode每日一题/LeetCode416.md) | 背包问题 变种 |
| [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/) | 中等 | [LeetCode322](./LeetCode每日一题/LeetCode322.md) | 零钱兑换  背包的动态规划 |
| [377. 组合总和 Ⅳ](https://leetcode-cn.com/problems/combination-sum-iv/) | 中等 | [LeetCode377](./LeetCode每日一题/LeetCode377.md) | 组合总数 |
| [300. 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/) | 中等 | [LeetCode300](./LeetCode每日一题/LeetCode300.md) | 最长上升子序列 |
| [64. 最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/) | 中等 | [LeetCode64](./LeetCode每日一题/LeetCode64.md) | 最短路径问题 二维dp 基础题 |
| [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/) | 中等 | [LeetCode05](./LeetCode每日一题/LeetCode05.md) | 最长回文子串（高频） |
| [72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/) | 困难 | [LeetCode72](./LeetCode每日一题/LeetCode72.md) | 编辑距离 |
| [120. 三角形最小路径和](https://leetcode-cn.com/problems/triangle/) | 中等 | [LeetCode120](./LeetCode每日一题/LeetCode120.md) | 三角形最小路径和 |
| [139. 单词拆分](https://leetcode-cn.com/problems/word-break/) | 中等 | [LeetCode139](./LeetCode每日一题/LeetCode139.md) | 单词拆分 |
| [279. 完全平方数](https://leetcode-cn.com/problems/perfect-squares/) | 中等 | [LeetCode279](./LeetCode每日一题/LeetCode279.md) | 完全平方数 |
| [213. 打家劫舍 II](https://leetcode-cn.com/problems/house-robber-ii/) | 中等 | [LeetCode213](./LeetCode每日一题/LeetCode213.md) | 打家劫舍问题2 |
| [309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) | 中等 | [LeetCode309](./LeetCode每日一题/LeetCode309.md) | 股票买卖 冻结问题 |
| [337. 打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/) | 中等 | [LeetCode337](./LeetCode每日一题/LeetCode337.md) | 打家劫舍问题3 |
| [1277. 统计全为 1 的正方形子矩阵](https://leetcode-cn.com/problems/count-square-submatrices-with-all-ones/) | 中等 | [LeetCode1277](./LeetCode每日一题/LeetCode1277.md) | 统计全为1的正方形子矩阵 |
| <span id = "树" style="font-size:150%;"><b>树</b></span> |  |  |  |
| [98. 验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/) | 中等 | [LeetCode98](./LeetCode每日一题/LeetCode98.md) |  |
| [112. 路径总和](https://leetcode-cn.com/problems/path-sum/) | 简单 | [LeetCode112](./LeetCode每日一题/LeetCode112.md) |  |
| [113. 路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/) | 中等 | [LeetCode113](./LeetCode每日一题/LeetCode113.md) |  |
| [129. 求根到叶子节点数字之和](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/) | 中等 | [LeetCode129](./LeetCode每日一题/LeetCode129.md) |  |
| [257. 二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/) | 简单 | [LeetCode257](./LeetCode每日一题/LeetCode257.md)             |  |
| [404. 左叶子之和](https://leetcode-cn.com/problems/sum-of-left-leaves/) | 简单 | [LeetCode404](./LeetCode每日一题/LeetCode404.md) |  |
| [572. 另一个树的子树](https://leetcode-cn.com/problems/subtree-of-another-tree/) | 简单 | [LeetCode572](./LeetCode每日一题/LeetCode572.md) | 双递归 感觉不算简单 |
| [236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/) | 中等 | [LeetCode236](./LeetCode每日一题/LeetCode236.md) |  |
| [103. 二叉树的锯齿形层次遍历](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/) | 中等 | [LeetCode103](./LeetCode每日一题/LeetCode103.md) | 栈或者双端链表 |
| <span id = "树" style="font-size:150%;"><b>前缀树</b></span> | | | |
| [472. 连接词](https://leetcode-cn.com/problems/concatenated-words/) | 困难 | [LeetCode472](./LeetCode每日一题/LeetCode472.md) | |
| [211. 添加与搜索单词 - 数据结构设计](https://leetcode-cn.com/problems/add-and-search-word-data-structure-design/) | 中等 | [LeetCode211](./LeetCode每日一题/LeetCode211.md) | |
| <span id = "贪心" style="font-size:150%;"><b>贪心</b></span> |      |                                                              |  |
| [55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/) | 中等 | [LeetCode55](../LeetCode每日一题/LeetCode55.md) |  |
| <span id = "dfs+bfs" style="font-size:150%;"><b>dfs+bfs</b></span> |      |                                                              |  |
| [332. 重新安排行程](https://leetcode-cn.com/problems/reconstruct-itinerary/) | 中等 | [LeetCode332](../LeetCode每日一题/LeetCode332.md)   |  |
| [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/) | 中等 | [LeetCode130](./LeetCode每日一题/LeetCode130.md)    |  |
| [93. 复原IP地址](https://leetcode-cn.com/problems/restore-ip-addresses/) | 中等 | [LeetCode93](./LeetCode每日一题/LeetCode93.md)      |  |
| [51. N皇后](https://leetcode-cn.com/problems/n-queens/)      | 困难 | [LeetCode51](./LeetCode每日一题/LeetCode51.md)      |  |
| [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/) | 中等 | [LeetCode17](./LeetCode每日一题/LeetCode17.md)      |  |
| [45. 跳跃游戏 II](https://leetcode-cn.com/problems/jump-game-ii/) | 困难 | [LeetCode45](./LeetCode每日一题/LeetCode45.md) |  |
| [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/) | 中等 | [LeetCode200](./LeetCode每日一题/LeetCode200.md) | |
| [329. 矩阵中的最长递增路径](https://leetcode-cn.com/problems/longest-increasing-path-in-a-matrix/) | 困难 | [LeetCode329](./LeetCode每日一题/LeetCode329.md) | 记忆化搜索 |
| <span id = "位运算" style="font-size:150%;"><b>位运算</b></span> |      |                                                              |  |
| [Pow(x, n)](https://leetcode-cn.com/problems/powx-n/)        | 中等 | [LeetCode50](./LeetCode每日一题/LeetCode50.md) | 快速幂思想 |
| [只出现一次的数字 III](https://leetcode-cn.com/problems/single-number-iii/) | 中等 | [LeetCode260](./LeetCode每日一题/LeetCode260.md)    |  |
| [两整数之和](https://leetcode-cn.com/problems/sum-of-two-integers/) | 简单 | [LeetCode371](./LeetCode每日一题/LeetCode371.md) | 机组知识 |
| [面试题 08.04. 幂集](https://leetcode-cn.com/problems/power-set-lcci/) | 中等 | [LeetCodeX0804](./LeetCode每日一题/LeetCodeX0804.md) | 位运算更加巧妙 |
| [最大单词长度乘积](https://leetcode-cn.com/problems/maximum-product-of-word-lengths/) | 中等 | [LeetCode318](./LeetCode每日一题/LeetCode318.md) | hash+位运算 |
| <span id = "图" style="font-size:150%;"><b>图</b></span> |      |                                                              |  |
| [不邻接植花](https://leetcode-cn.com/problems/flower-planting-with-no-adjacent/) | 简单 | [LeetCode1042](./LeetCode每日一题/LeetCode1042.md) |  |
| [课程表II](https://leetcode-cn.com/problems/course-schedule-ii/) | 中等 | [LeetCode210](./LeetCode每日一题/LeetCode210.md)   |  |
| [743. 网络延迟时间](https://leetcode-cn.com/problems/network-delay-time/) | 中等 | [LeetCode743](./LeetCode每日一题/LeetCode743.md) | 路径算法 |
| [684. 冗余连接](https://leetcode-cn.com/problems/redundant-connection/) | 中等 |  | 并查集 |
| <span id = "堆 排序 分治" style="font-size:150%;"><b>堆 排序 分治 栈</b></span> |      |                                                              |  |
| [最接近原点的 K 个点](https://leetcode-cn.com/problems/k-closest-points-to-origin/) | 中等 | [LeetCode973](./LeetCode每日一题/LeetCode973.md) | 堆 分治 |
| [394. 字符串解码](https://leetcode-cn.com/problems/decode-string/) | 中等 | [LeetCode394](./LeetCode每日一题/LeetCode394.md) | 栈 |
|  |  |  |  |
| <span id = "设计类" style="font-size:150%;"><b>设计类</b></span> |      |                                                              |  |
| [460. LFU缓存](https://leetcode-cn.com/problems/lfu-cache/) |困难|[LeetCode460](./LeetCode每日一题/LeetCode460.md)|  |
| [146. LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/) |中等|[LeetCode146](./LeetCode每日一题/LeetCode146.md)| 复杂度要求较低 |
