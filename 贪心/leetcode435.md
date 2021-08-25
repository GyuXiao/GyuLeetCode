### 题目要求
区间相交问题有很多实际应用，比如
今天有好几个活动，每个活动的时间是[start, end]表示开始和结束的时间，请问今天最多能参加几个活动呢？

### 代码实现
```Java
// Java 实现

class Solution {
    public int eraseOverlapIntervals(int[][] arr) {
        return arr.length - intervalSchedule(arr);
    }
    // 求出至多有多少区间可以不相交
    public int intervalSchedule(int[][] arr){
        if(arr == null || arr.length == 0){
            return 0;
        }
        Arrays.sort(arr, new Comparator<int[]>(){
            @Override
            public int compare(int[] a, int[] b){
                return a[1] - b[1];
            }
        });
        int count = 1;
        int xEnd = arr[0][1];
        for(int[] inteval: arr){
            if(inteval[0] >= xEnd){
                count++;
                xEnd = inteval[1];
            }
        }
        return count;
    }
}
```