### 汉诺塔问题
```Java
// Java 实现

class Solution {
    public void hanota(List<Integer> A, List<Integer> B, List<Integer> C) {
        move(A.size(), A, B, C);
    }
    private void move(int n, List<Integer>A, List<Integer>B, List<Integer>C){
        // 递归基
        if(n == 1){
            C.add(A.remove(A.size()-1));
            return;
        }
        // 前 n-1 块，从 A 全拿起并暂放到 B
        move(n-1, A, C, B);
        // 将第 n 块从 A 拿起并放到 C
        C.add(A.remove(A.size()-1));
        // 将前 n-1 块，从暂存区 B 全拿起并放到 C
        move(n-1, B, A, C);
    }
}
```

### 用锅铲给烧饼排序
```Java
// Java 实现
// 记录反转操作序列

class Solution {
    LinkedList<Integer> res = new LinkedList<>();
    List<Integer> pancakeSort(int[] cakes){
        sort(cakes, cakes.length);
        return res;
    }

    void sort(int[] cakes, int n){
        if(n == 1){return;}
        int maxCake = 0;
        int maxCakeIdx = 0;
        for(int i = 0; i < cakes.length; i++){
            if(cakes[i] > maxCake){
                maxCake = cakes[i];
                maxCakeIdx = i;
            }
        }
        // 第一次翻转，将最大饼翻到最上面
        reverse(cakes, 0, maxCakeIdx);
        res.add(maxCakeIdx + 1);
        // 第二次翻转，将最大饼翻到最下面
        reverse(cakes 0, n - 1);
        res.add(n);
        // 递归下去
        sort(cakes, n - 1);
    }

    void reverse(int[] arr, int i, int j){
        while(i < j){
            int tmp = arr[i];
            arr[i] = arr[j];
            arr[j] = tmp;
            i++; j--;
        }
    }
}

```