```java
class Solution {
    public int maxProduct(String[] words) {
        //暴力 异或+ 随机减枝  
        //用位运算+数组hash来实现 太巧妙了
        int length=words.length;
        int[]arr=new int[length];
        for(int i=0;i<length;i++){
            int llength=words[i].length();
            for(int j=0;j<llength;++j){
                //1左移
                arr[i]|=1<<(words[i].charAt(j)-'a');
            }
        }
        int ans=0;
        for(int i=0;i<length;i++){
            for(int j=i+1;j<length;j++){
                if((arr[i]& arr[j])==0){
                    int k=words[i].length()*words[j].length();
                    ans=ans<k?k:ans;
                }
            }
        }
        return ans;
    }

}
```
