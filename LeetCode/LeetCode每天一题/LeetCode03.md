```juava
class Solution {
 public int lengthOfLongestSubstring(String s) {
        int res=0;
        for(int i=0;i<s.length();i++){
            HashSet<Character> hashSet=new HashSet<>();
            for(int j=i;j<s.length();j++){
                if(hashSet.contains(s.charAt(j))){
                    break;
                }else{
                    hashSet.add(s.charAt(j));
                    res=Math.max(res,hashSet.size());
                }
            }
        }
        return res;
    }
}
```

```java
public static int lengthOfLongestSubstring(String s) {
        int []array=new int[256];
        int l=0;
        int r=0;
        int res=0;
        while(r<s.length()){
            if(array[s.charAt(r)]!=1){
                //放入元素  数组标记
                array[s.charAt(r++)]=1;
                res=Math.max(res,r-l);
            }else{
                //元素已经存在
                while(s.charAt(l)!=s.charAt(r)){
                    array[s.charAt(l)]=0;//设置 防止出现前面出现的元素在后面出现误判出现在数组中
                    l++;
                }
                r++;
                l++;
            }
        }
        return res;
    }
```



第二次打卡

```java
class Solution {
     public int lengthOfLongestSubstring(String s) {
         if(s.length()==0){
             return 0;
         }
        //使用左右指针+hashset来保存元素的位置达到一定值的后删除到这个元素后面可以
        int left = 0;
        int right = 0;

        //integer 当前元素的下标
        HashMap<Character, Integer> hashMap = new HashMap<>();
        int max = 0;
        while (right < s.length()) {
            char a = s.charAt(right);
            if (hashMap.containsKey(a)) {
                int temp = left;
                left = hashMap.get(a) + 1;
                //value前面的都要删除
                for (int i = temp; i < left; i++) {
                    char b = s.charAt(i);
                    hashMap.remove(b);
                }
            }
            hashMap.put(a, right);
            right++;
            max = Math.max(right - left, max);
        }
        return max;
    }
}
```





思路

- 双层for循环 使用hashset来保存是否有重复的元素出现 

- 使用滑动窗口，当元素未在线框中的时候就移动right指针 扩大窗口的大小，如果元素在线框内有重复就移动left指针到最靠近right指针的那个元素地方。

  - 定义一个 256 长度的数组是因为asc2表 到127 而asc2表拓展到255。

  - 滑动指针要注意指针的两边临界点，和指针区间大小计算。

    