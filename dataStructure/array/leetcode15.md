### 三数之和
```Java
// Java 实现
// 重点在于去重，也就是剪枝，不然一定超时
// 本质还是双指针的使用

class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        if(nums == null || nums.length < 3){
            return res;
        }
        // 先排序
        Arrays.sort(nums);
        // l 最大是nums.length-2，因为 r 从 nums.length-1 开始向左移动
        for(int l = 0; l < nums.length - 1 && nums[l] <= 0; l++){
            if(l > 0 && nums[l-1] == nums[l]){
                continue;
            }
            // 转化为两数之和
            int target = 0 - nums[l];
            int mid = l + 1;
            int r = nums.length - 1;

            while(mid < r){
                if(nums[mid] + nums[r] == target){
                    res.add(Arrays.asList(nums[l], nums[mid], nums[r]));
                    // 当前的 mid 和 r 要保存，为去重做准备
                    int curMid = nums[mid];
                    int curR = nums[r];
                    // 剪枝
                    while(mid < r && nums[mid] == curMid){mid++;}
                    while(mid < r && nums[r] == curR){r--;}
                }
                else if(nums[mid] + nums[r] > target){
                    r--;
                }
                else if(nums[mid] + nums[r] < target){
                    mid++;
                }
            }
        }
        return res;
    }
}
```