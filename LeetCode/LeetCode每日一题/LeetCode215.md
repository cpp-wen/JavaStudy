#### [215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

> 示例 1:
>
> 输入: [3,2,1,5,6,4] 和 k = 2
> 输出: 5
> 示例 2:
>
> 输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
> 输出: 4
> 说明:
>
> 你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

```java
class Solution {
    public int findKthLargest(int[] nums, int k) {
        //创建一个优先队列直接放入元素 创建一个最小堆 每次将最小的元素抛出保存下来的元素就是最大的元素
        //排序完 后选取元素
        //计数排序 就是太耗空间 
        //快排的切分思想
        //库函数直接排序
        int len=nums.length;
        int left=0;
        int right=len-1;
        //第k个元素
        int target=len-k;
        while(true){
            //快速排序 每次都能定位一个元素
            int index=partition(nums,left,right);
            if(index==target){
                return nums[index];
            }else if(index<target){
                left=index+1;
            }else{
                right=index-1;
            }
        }
    }
    public int partition(int []nums,int left,int right){
        int pivot=nums[left];
        int j=left;
        for(int i=left+1;i<=right;i++){
            if(nums[i]<pivot){
                //小于 pivot 元素交换到前面 j每次指向的是下一个比pivot小的元素
                j++;
                //因为这边有一个j++使得 所以left元素会被滞空
                swap(nums,j,i);
            }
        }
        swap(nums,j,left);
        return j;
    }
    private void swap(int []nums,int index1,int index2){
        int temp=nums[index1];
        nums[index1]=nums[index2];
        nums[index2]=temp;
    }
}
```

