#### [1371. 每个元音包含偶数次的最长子字符串](https://leetcode-cn.com/problems/find-the-longest-substring-containing-vowels-in-even-counts/)

难度中等145

给你一个字符串 `s` ，请你返回满足以下条件的最长子字符串的长度：每个元音字母，即 'a'，'e'，'i'，'o'，'u' ，在子字符串中都恰好出现了偶数次。

 

**示例 1：**

```
输入：s = "eleetminicoworoep"
输出：13
解释：最长子字符串是 "leetminicowor" ，它包含 e，i，o 各 2 个，以及 0 个 a，u 。
```

**示例 2：**

```
输入：s = "leetcodeisgreat"
输出：5
解释：最长子字符串是 "leetc" ，其中包含 2 个 e 。
```

**示例 3：**

```
输入：s = "bcbcbc"
输出：6
解释：这个示例中，字符串 "bcbcbc" 本身就是最长的，因为所有的元音 a，e，i，o，u 都出现了 0 次。
```

 

**提示：**

- `1 <= s.length <= 5 x 10^5`
- `s` 只包含小写英文字母。



思路：

一开始想到用滑窗来做，发现窗户都划没了还是无法实现，看答案学会使用 map记录 数值，以及异或前缀和构建字典树的方式来实现 

```java
class Solution {
    private static final String VOWELS="aeiou";
    public int findTheLongestSubstring(String s) {
        Map<Integer,Integer> map=new HashMap<>();
        int size=s.length();
        int state=0;
        int maxSize=0;
        map.putIfAbsent(0,-1);
        for(int i=0;i<size;i++){
            for(int k=0;k<VOWELS.length();k++){
                if(s.charAt(i)==VOWELS.charAt(k)){
                    state^=(1<<(VOWELS.length()-k-1));
                    break;
                }
            }
            if(map.containsKey(state)){
                maxSize=Math.max(maxSize,i-map.get(state));
            }
            map.putIfAbsent(state,i);
        }
        return maxSize;
    }
}
```

