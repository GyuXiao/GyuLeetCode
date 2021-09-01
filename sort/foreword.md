### 冒泡排序
```Java
// Java 实现

public class BubbleSort implements IArraySort {
    @Override
    public int[] sort(int[] sourceArray) throws Exception {
        // 对 arr 进行拷贝，不改变参数内容
        int[] arr = Arrays.copyOf(sourceArray, sourceArray.length);
        for (int i = 1; i < arr.length; i++) {
            // 设定一个标记，若为true，则表示此次循环没有进行交换，也就是待排序列已经有序，排序已经完成。
            boolean flag = true;
            for (int j = 0; j < arr.length - i; j++) {
                if (arr[j] > arr[j + 1]) {
                    int tmp = arr[j];
                    arr[j] = arr[j + 1];
                    arr[j + 1] = tmp;
                    flag = false;
                }
            }
            if (flag) {
                break;
            }
        }
        return arr;
    }
}
```

### 归并排序
算法步骤：
1，申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列  
2，设定两个指针，最初位置分别为两个已经排序序列的起始位置  
3，比较两个指针所指向的元素，选择相对小的元素并放到合并空间，并移动指针到下一位置  
4，重复步骤3直到某一指针达到序列尾  
5，将另一序列剩下的所有元素直接复制到合并序列尾  
```Java
// Java 实现

public class MergeSort implements IArraySort {
    @Override
    public int[] sort(int[] sourceArray) throws Exception {
        // 对 arr 进行拷贝，不改变参数内容
        int[] arr = Arrays.copyOf(sourceArray, sourceArray.length);
        if (arr.length < 2) {
            return arr;
        }
        int middle = (int) Math.floor(arr.length / 2);
        int[] left = Arrays.copyOfRange(arr, 0, middle);
        int[] right = Arrays.copyOfRange(arr, middle, arr.length);
        return merge(sort(left), sort(right));
    }

    protected int[] merge(int[] left, int[] right) {
        int[] result = new int[left.length + right.length];
        int i = 0;
        while (left.length > 0 && right.length > 0) {
            if (left[0] <= right[0]) {
                result[i++] = left[0];
                left = Arrays.copyOfRange(left, 1, left.length);
            } else {
                result[i++] = right[0];
                right = Arrays.copyOfRange(right, 1, right.length);
            }
        }
        while (left.length > 0) {
            result[i++] = left[0];
            left = Arrays.copyOfRange(left, 1, left.length);
        }
        while (right.length > 0) {
            result[i++] = right[0];
            right = Arrays.copyOfRange(right, 1, right.length);
        }
        return result;
    }
}
```

### 堆排序
1，将待排序序列构建成一个堆H[0...n-1]，根据升序或降序需求选择大顶堆或小顶堆  
2，把堆首和堆尾互换  
3，把堆的尺寸缩小，并调用shift_down(0)，目的是把新的数组顶端数据调整到相应位置；  
4，重复步骤2，直到堆的尺寸为1  
```Java
// Java 实现

public class HeapSort implements IArraySort {
    @Override
    public int[] sort(int[] sourceArray) throws Exception {
        // 对 arr 进行拷贝，不改变参数内容
        int[] arr = Arrays.copyOf(sourceArray, sourceArray.length);
        int len = arr.length;
        buildMaxHeap(arr, len);
        for (int i = len - 1; i > 0; i--) {
            swap(arr, 0, i);
            len--;
            // 为什么这里还要 heapify?
            // 因为这里我们是在模拟从堆里取值并放到 arr 中，每次取值都相当于删除节点操作，因此堆的结构都会发生变化
            // 调用 heapify，正是对结构的调整
            heapify(arr, 0, len);
        }
        return arr;
    }

    // 建一个 大顶堆
    private void buildMaxHeap(int[] arr, int len) {
        for (int i = (int) Math.floor(len / 2); i >= 0; i--) {
            heapify(arr, i, len);
        }
    }

    // 以递归形式，将一个数组堆化
    private void heapify(int[] arr, int i, int len) {
        int left = 2 * i + 1;
        int right = 2 * i + 2;
        int largest = i;
        if (left < len && arr[left] > arr[largest]) {
            largest = left;
        }
        if (right < len && arr[right] > arr[largest]) {
            largest = right;
        }
        if (largest != i) {
            swap(arr, i, largest);
            heapify(arr, largest, len);
        }
    }

    private void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```