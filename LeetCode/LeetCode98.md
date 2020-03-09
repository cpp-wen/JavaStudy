```java
class Solution {
     public boolean isValidBST(TreeNode root) {
        return validate(root,Long.MIN_VALUE,Long.MAX_VALUE);
    }
    private boolean validate(TreeNode node,long min,long max){
        if(node==null){
            return true;
        }
        if(node.val<=min|| node.val>=max){
            return false;
        }
        return validate(node.left,min,node.val)&&validate(node.right,node.val,max);
    }
}
```

思路 

- 中序遍历升序
- 递归遍历