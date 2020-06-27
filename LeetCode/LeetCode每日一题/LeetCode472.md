#### [472. 连接词](https://leetcode-cn.com/problems/concatenated-words/)

给定一个**不含重复**单词的列表，编写一个程序，返回给定单词列表中所有的连接词。

连接词的定义为：一个字符串完全是由至少两个给定数组中的单词组成的。

**示例:**

```
输入: ["cat","cats","catsdogcats","dog","dogcatsdog","hippopotamuses","rat","ratcatdogcat"]

输出: ["catsdogcats","dogcatsdog","ratcatdogcat"]

解释: "catsdogcats"由"cats", "dog" 和 "cats"组成; 
     "dogcatsdog"由"dog", "cats"和"dog"组成; 
     "ratcatdogcat"由"rat", "cat", "dog"和"cat"组成。
```

**说明:**

1. 给定数组的元素总数不超过 `10000`。
2. 给定数组中元素的长度总和不超过 `600000`。
3. 所有输入字符串只包含小写字母。
4. 不需要考虑答案输出的顺序。



```java
class Solution {
    public List<String> findAllConcatenatedWordsInADict(String[] words) {
        Trie trie = new Trie();

        List<String> res = new ArrayList<>();

        for (String s : words) {
            trie.add(s);
        }

        for (String s : words) {
            if (trie.checkIsConcatenatedWord(s, 0, 0)) {
                res.add(s);
            }
        }

        return res;
    }
    class Trie{
        //内部类
        class Node{
            private boolean isWord;
            private Map<Character,Node> next;
            public Node(){
                this(false);
            }
            public Node(boolean f){
                this.isWord=f;
                //todo ？原文使用 treemap
                next=new TreeMap<Character,Node>();
            }
        }
        private Node root;
        public Trie(){
            root=new Node();
        }
        public void add(String word){
            Node cur=root;
            for(int i=0;i<word.length();i++){
                char c=word.charAt(i);
                if(cur.next.get(c)==null){
                    cur.next.put(c,new Node());
                }
                cur=cur.next.get(c);
            }
            //这边不用判断了 直接设置为true就可以了
            cur.isWord=true;
        }
        public boolean checkIsConcatenatedWord(String word,int index,int count){
            Node cur =root;
            for(int i=index;i<word.length();i++){
                char c=word.charAt(i);
                if(cur.next.get(c)==null){
                    return false;
                }
                if(cur.next.get(c).isWord){
                    //判断 是否达到word 长度
                    if(i==word.length()-1){
                        return count>=1;
                    }
                    //count+1 i+1 
                    if(checkIsConcatenatedWord(word,i+1,count+1)){
                        return true;
                    }
                }
                cur=cur.next.get(c);
            }
            return false;
        }
    }
}
```

