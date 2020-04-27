

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        //基本思路 左右指针
        Arrays.sort(nums);
        List<List<Integer>> res=new ArrayList<>();
        for(int i=0;i<nums.length;i++){
            if(i>0&&nums[i]==nums[i-1]){
                continue;//去重
            }
            if(nums[i]>0){
                return res;
            }
            int number=nums[i];
            int l=i+1;
            int r=nums.length-1;
            while(l<r){
                if(number+nums[l]+nums[r]<0){
                    l++;
                }else if(number+nums[l]+nums[r]>0){
                    r--;
                }else{
                    List<Integer> signResult=new ArrayList<>();
                    signResult.add(nums[i]);
                    signResult.add(nums[l]);
                    signResult.add(nums[r]);
                    res.add(signResult);
                    while(l<r&&nums[l]==nums[(l+1)]){l++;}//去掉重复的元素
                    while(l<r&&nums[r]==nums[r-1]){r--;}//去掉重复的元素
                    l++;
                    r--;
                }
            }
        }
        return res;
    }
}
```

思路

- 左右指针，每次将一个元素作为主元素，剩下两个元素在指针范围内寻找。找到元素和为0便加入到集合中。一开始思路是从下标为1开始，如果遇到当前下标元素和后一个下标元素相同就i+1，中间元素不能重复  左指针从0开始 右指针从length-1开始，案例【0,0,0】给我暴击。后面思路变成了以0作为开始节点。l指针为0节点的下一个节点。r指针为length-1；