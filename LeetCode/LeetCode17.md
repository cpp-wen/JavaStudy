```java
class Solution {
    //整体思路采用bfs 来实现 time O(n^2) space o(n)
   public List<String> letterCombinations(String digits) {
        //其实思路跟我想的 一样 实现优化更多
        LinkedList<String> res=new LinkedList<>();
        String []array=new String[]{" ","1","abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"};
        if(digits.length()==0){
            return res;
        }
        //如果链表不为空就不断的放入添加后的元素
        res.add("");
        for(int i=0;i<digits.length();i++){
            int  number=digits.charAt(i)-'0';
            String string=array[number];
            //当前数组的长度不满足 到下标为i 的长度就进入循环 
            while(res.peek().length()!=i+1){
                String a=res.pop();
                for(char c:string.toCharArray()){
                    res.add(a+c);
                }
            }
        }
        return res;
    }
}
```

思路 

- 链表实现
- dfs+回溯 效率更高