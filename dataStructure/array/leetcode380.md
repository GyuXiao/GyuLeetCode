### 常数时间完成插入、删除和获取随机数据 
```Java
// Java 实现

class RandomizedSet {

    private Map<Integer, Integer> valToIndex;
    private List<Integer> list;
    private Random r;

    /** Initialize your data structure here. */
    public RandomizedSet() {
        valToIndex = new HashMap<>();
        list = new ArrayList<>();
        r = new Random();
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    public boolean insert(int val) {
        if(valToIndex.containsKey(val)){
            return false;
        }
        list.add(val);
        valToIndex.put(val, list.size()-1);
        return true;
    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    public boolean remove(int val) {
        if(!valToIndex.containsKey(val)){
            return false;
        }
        int deleteIdx = valToIndex.get(val);
        int lastVal = list.get(list.size() - 1);
        list.set(deleteIdx, lastVal);// 把用最后一个元素覆盖待删除的元素
        list.remove(list.size() - 1);
        valToIndex.put(lastVal, deleteIdx);
        valToIndex.remove(val);
        return true;
    }
    
    /** Get a random element from the set. */
    public int getRandom() {
        return list.get(r.nextInt(list.size()));
    }
}

/**
 * Your RandomizedSet object will be instantiated and called as such:
 * RandomizedSet obj = new RandomizedSet();
 * boolean param_1 = obj.insert(val);
 * boolean param_2 = obj.remove(val);
 * int param_3 = obj.getRandom();
 */
```