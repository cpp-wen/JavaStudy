```java
public static int[] twoSum(int[] nums, int target) {
    int []res=new int[2];
    HashMap<Integer,Integer> hashMap=new HashMap<>();
    for(int i=0;i<nums.length;i++){
        int other=target-nums[i];
        if(hashMap.containsKey(other)){
            res[0]=i;
            res[1]=hashMap.get(other);
            break;
        }
        hashMap.put(nums[i],i);//语句的顺序不能错 要先
    }
    return res;
}
```

思路：

- 双指针 （不可行改变了原来的数组导致下标改变）
- hashMap （注意hashmap中put时间 ）[3,2,4] target =6 时put语句位置不对会导致结果出错



