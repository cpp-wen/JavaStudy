#### [211. 添加与搜索单词 - 数据结构设计](https://leetcode-cn.com/problems/add-and-search-word-data-structure-design/)

设计一个支持以下两种操作的数据结构：

```
void addWord(word)
bool search(word)
```

search(word) 可以搜索文字或正则表达式字符串，字符串只包含字母 `.` 或 `a-z` 。 `.` 可以表示任何一个字母。

**示例:**

```
addWord("bad")
addWord("dad")
addWord("mad")
search("pad") -> false
search("bad") -> true
search(".ad") -> true
search("b..") -> true
```

**说明:**

你可以假设所有单词都是由小写字母 `a-z` 组成的。



```java
class WordDictionary {

    //前缀树 创建一个node 包含node[26]  char 当前那个字符  boolean 默认false
    //正则的话  暴力for 回溯的思想  dfs 就是回溯思想 
    //构建一棵前缀树
    Trie trie;
    private class Trie{
        TrieNode root;
        Trie(){
            root=new TrieNode();
        }
        void insert(String word){
            TrieNode node=root;
            int length=word.length();
            for(int i=0;i<length;i++){
                char curLetter =word.charAt(i);
                if(!node.containsKey(curLetter)){
                    node.put(curLetter,new TrieNode());
                }
                //遍历下一个节点
                node=node.get(curLetter);
            }
            node.isEnd=true;
        }
        boolean dfs(TrieNode root,String word,int index){
            // index 是字符串结束的下一个节点
            // 同时>=避免了前缀符合结束的标志 就停止dfs遍历
            if(index>=word.length()){
                return root.isEnd;
            }
            char curLetter=word.charAt(index);
            if(curLetter!='.'){
                if(root.containsKey(curLetter)){
                    return dfs(root.get(curLetter),word,index+1);
                }
                return false;
            }
            //for+dfs
            for(TrieNode link:root.links){
                if(null!=link&&dfs(link,word,index+1)){
                    return true;
                }
            }
            return false;

        }
        boolean search(String word){
            return dfs(root,word,0);
        }
    }
    /** Initialize your data structure here. */
    public WordDictionary() {
        trie =new Trie();
    }
    
    /** Adds a word into the data structure. */
    public void addWord(String word) {
        trie.insert(word);
    }
    
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    public boolean search(String word) {
        return trie.search(word);
    }
    //树节点
    private class TrieNode{
        TrieNode []links;
        final int R=26;
        boolean isEnd;
        TrieNode(){
            links=new TrieNode[R];
        }
        //TrieNode是否包含这个节点
        boolean containsKey(char ch){
            return links[ch-'a']!=null;
        }
        TrieNode get(char ch){
            return links[ch-'a'];
        }
        void put(char ch,TrieNode node){
            links[ch-'a']=node;
        }
    }

}

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary obj = new WordDictionary();
 * obj.addWord(word);
 * boolean param_2 = obj.search(word);
 */
```

