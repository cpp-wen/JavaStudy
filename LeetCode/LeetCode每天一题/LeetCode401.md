```java
/*
思路：回溯，使用数组存储LED数，回溯函数中使用两个int存储小时和分钟
时间复杂度：O（2^n）*/
class Solution {
    List<String> result;
    public List<String> readBinaryWatch(int num) {
        result=new ArrayList<String>();
        //判断输入
        if(num<0)
            return result;
        //LED灯，前四个为小时，后六个为分钟
        int[] nums=new int[]{8,4,2,1,32,16,8,4,2,1};
        backTrack(num,nums,0,0,0);
        return result;
    }
    public void backTrack(int num,int[] nums,int start,int hour,int minute){
        if(num==0){
            //判断时间是否正确
            if(hour>11||minute>59)
                return;
            StringBuilder tmp=new StringBuilder();
            //小时
            tmp.append(hour);
            tmp.append(":");
            //分钟
            if(minute<10)
                tmp.append(0);
            tmp.append(minute);
            result.add(new String(tmp));
            return ;
        }
        for(int i=start;i<nums.length;i++){
            /*回溯做选择*/
            //判断是小时还是分钟
            if(i<4)//小时
                hour+=nums[i];
            else
                minute+=nums[i];
            //递归
            backTrack(num-1,nums,i+1,hour,minute);
            /*回溯取消选择*/
            if(i<4)//小时
                hour-=nums[i];
            else
                minute-=nums[i];
        }
    }
}
```

二进制算法代码

```java
class Solution {
    public List<String> readBinaryWatch(int num) {
        List<String>res=new ArrayList<String>();
        for(int i=0;i<12;i++){
            for(int j=0;j<60;j++){
                if(Integer.bitCount(i)+Integer.bitCount(j)==num){
                    res.add(String.format("%d:%02d",i,j));
                }
            }
        }
        return res;   
    }
}
```

