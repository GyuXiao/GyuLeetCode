### 删除有序数组中的重复项
```Java
// Java 实现
// 前后指针

class Solution {
    public int removeDuplicates(int[] nums) {
        int low = 0;
        int fast = 1;
        while(fast < nums.length){//保证[0,low+1)的数组没有重复数字
            if(nums[low] != nums[fast]){
                low++;
                nums[low] = nums[fast];
            }
            fast++;
        }
        return low+1;
    }
}
```