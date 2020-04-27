```java
class Solution {
    public List<List<Integer>> subsets(int[] nums) {
        //明明是回溯做 ，位运算
        //位运算的思想太巧妙了 
        List<List<Integer>> res=new ArrayList<>();
        int length=(int)Math.pow(2,nums.length);
        for(int i=0;i<length;i++){
            List<Integer> list=new ArrayList<Integer>();
            for(int j=0;j<nums.length;j++){
                //这边比较难理解 将每一位右移&1 添加到数组中
                if((i>>>j&1)==1){
                    list.add(nums[j]);
                }
            }
            res.add(list);
        }
        return res;
    }
}
```

