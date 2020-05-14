#### [179. 最大数](https://leetcode-cn.com/problems/largest-number/)

给定一组非负整数，重新排列它们的顺序使之组成一个最大的整数。

```
示例 1:

输入: [10,2]
输出: 210
示例 2:

输入: [3,30,34,5,9]
输出: 9534330
说明: 输出结果可能非常大，所以你需要返回一个字符串而不是整数。
```

```java
class Solution {
    public String largestNumber(int[] nums) {
        //桶排序实现比较复杂
        //直接重新实现comparator 比较器
        if(nums==null||nums.length==0){
            return "";
        }
        //string到字符串的转换 然后重新定义比较函数
        String[]str=new String[nums.length];
        for(int i=0;i<str.length;i++){
            str[i]=String.valueOf(nums[i]);
        }
        //实现比较器
        Arrays.sort(str,new Comparator<String>(){
            @Override 
            public int compare(String o1,String o2){
                //compareTo 返回值为1 为升序 ，-1 降序
                //返回1 的时候进行交换 -1不交换D
                return (o2+o1).compareTo((o1+o2));
            }
        });
        StringBuilder sb=new StringBuilder();
        for(String s:str){
            sb.append(s);
        }
        String res=sb.toString();
        if(res.charAt(0)=='0'){
            return "0";
        }
        return res;
    }
}
```



