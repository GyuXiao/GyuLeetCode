### 原地移除数组的指定元素
```Java
// Java 实现
// 前后指针

class Solution {
    public int removeElement(int[] nums, int val) {
        int slow, fast;
        slow = fast = 0;

        while(fast < nums.length){
            if(nums[fast] != val){
                nums[slow] = nums[fast];
                slow++;
            }
            fast++;
        }
        return slow;
    }
}
```