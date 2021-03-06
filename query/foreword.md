## 寻找一个数
```Java
// Java 实现
// 最基本的二分查找了，建议多看几遍，毕竟万变不离其宗

int binarySearch(int[] nums, int target) {
    int left = 0; 
    int right = nums.length - 1; // 注意

    while(left <= right) { // 循环的终止条件是left == right + 1
        int mid = left + (right - left) / 2;
        if(nums[mid] == target)
            return mid; 
        else if (nums[mid] < target)
            left = mid + 1; // 注意
        else if (nums[mid] > target)
            right = mid - 1; // 注意
    }
    return -1;
}
```

## 寻找左侧边界的二分搜索
```Java
// Java 实现

int left_bound(int[] nums, int target) {
        if(nums.length == 0){return -1;}
    int left = 0; 
    int right = nums.length; // 所以下面是 < ，而不是 <=
    while(left < right) { // 终止循环的条件是left == right
        int mid = (right + left) / 2;
        if(nums[mid] == target)
            right = mid; // 找到target时不要立即返回，而是缩小搜索区间上界，在[left, right)继续搜，不断向左收缩
        else if (nums[mid] < target)
            left = mid + 1; // 去到[mid+1, right)
        else if (nums[mid] > target)
            right = mid; // 去到[left, mid)
    }
    // return left;
        if(left == nums.length){return -1;}
        return nums[left] == target ? left : -1;
}
```
## 寻找右侧边界的二分查找
```Java
// Java 实现

    public int right_bound(int[] nums, int target) {
        if(nums.length == 0){return -1;}
        int left = 0;
        int right = nums.length;
        while(left < right){ // 循环终止条件是left == right
            int mid = (left + right) / 2;
            // 当nums[mid] == target时，不要立即返回，而是增大「搜索区间」的下界left，使得区间不断向右收缩，达到锁定右侧边界的目的。
            if(nums[mid] == target){
                left = mid + 1; // 因为我们对left的更新必须是left = mid + 1，就是说 while 循环结束时，nums[left]一定不等于target了，而nums[left-1]可能是target。
            } else if(nums[mid] < target){
                left = mid + 1;
            } else if(nums[mid] > target){
                right = mid;
            }
        }
        // return left - 1;
        if(left == 0){return -1;}
        return nums[left-1] == target ? (left-1) : -1;
    }
```

# 小结一下
## 基本二分查找
因为我们初始化 right = nums.length - 1，所以决定了我们的「搜索区间」是 [left, right]
所以决定了 while (left <= right)，同时也决定了 left = mid+1 和 right = mid-1
因为我们只需找到一个 target 的索引即可
所以当 nums[mid] == target 时可以立即返回
## 寻找左侧边界的二分查找
因为我们初始化 right = nums.length，所以决定了我们的「搜索区间」是 [left, right)
所以决定了 while (left < right)
同时也决定了 left = mid + 1 和 right = mid；
因为我们需找到 target 的最左侧索引
所以当 nums[mid] == target 时不要立即返回，而要收紧右侧边界以锁定左侧边界，也就是right = mid
## 寻找左侧边界的二分查找
因为我们初始化 right = nums.length，所以决定了我们的「搜索区间」是 [left, right)
所以决定了 while (left < right)
同时也决定了 left = mid + 1 和 right = mid
因为我们需找到 target 的最右侧索引，所以当 nums[mid] == target 时不要立即返回
而要收紧左侧边界(left = mid + 1)以锁定右侧边界，又因为收紧左侧边界时必须 left = mid + 1
所以最后无论返回 left 还是 right，必须减一