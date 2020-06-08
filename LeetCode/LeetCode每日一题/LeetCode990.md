#### [990. 等式方程的可满足性](https://leetcode-cn.com/problems/satisfiability-of-equality-equations/)

给定一个由表示变量之间关系的字符串方程组成的数组，每个字符串方程 `equations[i]` 的长度为 `4`，并采用两种不同的形式之一：`"a==b"` 或 `"a!=b"`。在这里，a 和 b 是小写字母（不一定不同），表示单字母变量名。

只有当可以将整数分配给变量名，以便满足所有给定的方程时才返回 `true`，否则返回 `false`。 

**示例 1：**

```
输入：["a==b","b!=a"]
输出：false
解释：如果我们指定，a = 1 且 b = 1，那么可以满足第一个方程，但无法满足第二个方程。没有办法分配变量同时满足这两个方程。
```

**示例 2：**

```
输出：["b==a","a==b"]
输入：true
解释：我们可以指定 a = 1 且 b = 1 以满足满足这两个方程。
```

**示例 3：**

```
输入：["a==b","b==c","a==c"]
输出：true
```

**示例 4：**

```
输入：["a==b","b!=c","c==a"]
输出：false
```

**示例 5：**

```
输入：["c==c","b==d","x!=z"]
输出：true
```

 

**提示：**

1. `1 <= equations.length <= 500`
2. `equations[i].length == 4`
3. `equations[i][0]` 和 `equations[i][3]` 是小写字母
4. `equations[i][1]` 要么是 `'='`，要么是 `'!'`
5. `equations[i][2]` 是 `'='`

```java
public class Solution {
public boolean equationsPossible(String[] equations) {
    UnionFind unionFind = new UnionFind(26);

    for (String equation : equations) {
        char[] charArray = equation.toCharArray();
        if (charArray[1] == '=') {
            int index1 = charArray[0] - 'a';
            int index2 = charArray[3] - 'a';
            unionFind.union(index1, index2);
        }
    }

    for (String equation : equations) {
        char[] charArray = equation.toCharArray();
        if (charArray[1] == '!') {
            int index1 = charArray[0] - 'a';
            int index2 = charArray[3] - 'a';

            if (unionFind.isConnected(index1, index2)) {
                // 如果合并失败，表示等式有矛盾，根据题意，返回 false
                return false;
            }
        }
    }

    // 如果检查了所有不等式，都没有发现矛盾，返回 true
    return true;
}

private class UnionFind {
    
    private int[] parent;

    public UnionFind(int n) {
        parent = new int[n];
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
    }

    public int find(int x) {
        while (x != parent[x]) {
            parent[x] = parent[parent[x]];
            x = parent[x];
        }
        return x;
    }

    /**
     * @param x
     * @param y
     * @return 如果合并成功，返回 true
     */
    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        parent[rootX] = rootY;
    }

    public boolean isConnected(int x, int y) {
        return find(x) == find(y);
    }
}
```


​    
 
​    
