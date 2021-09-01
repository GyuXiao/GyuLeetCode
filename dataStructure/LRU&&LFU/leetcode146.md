### 题意：
实现LRUCache类：
- LRUCache(int capacity)以正整数作为容量capacity初始化LRU缓存
- int get(int key)如果关键字key存在于缓存中，则返回关键字的值，否则返回-1
- void put(int key, int value)如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入改组[关键字-值]。**当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间**。
### 要求：
在O(1)的时间复杂度内完成这两种操作；

粮草未动，代码先行：

```Java
// Java 实现
// 双向链表 + 哈希表

class LRUCache {

    private Map<Integer, Node> map;
    private DoubleList cache;
    private int capacity;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }
    
    public int get(int key) {
        if(!map.containsKey(key)){
            return -1;
        }
        makeRecently(key);
        return map.get(key).val;
    }
    
    public void put(int key, int value) {
        if(map.containsKey(key)){
            deleteKey(key);
            addRecently(key, value);
            return;
        } else{
            if(cache.getSize() == capacity){
                removeLeastRecently();
            }
            addRecently(key, value);
        }
    }

    // 封装
    private void makeRecently(int key){
        Node x = map.get(key);
        cache.remove(x);
        cache.addLast(x);
    }

    private void addRecently(int key, int val){
        Node x = new Node(key, val);
        cache.addLast(x);
        map.put(key, x);
    }

    private void deleteKey(int key){
        Node x = map.get(key);
        cache.remove(x);
        map.remove(key);
    }

    private void removeLeastRecently(){
        Node deleteNode = cache.removeFirst();
        map.remove(deleteNode.key);
    }

    // 双向链表
    static class DoubleList{
        private Node head, tail;
        private int size;
        public DoubleList(){
            head = new Node(0, 0);
            tail = new Node(0, 0);
            head.next = tail;
            tail.prev = head;
            size = 0;
        }

        public void addLast(Node x){
            x.prev = tail.prev;
            x.next= tail;
            tail.prev.next = x;
            tail.prev = x;
            size++;
        }

        public void remove(Node x){
            x.prev.next = x.next;
            x.next.prev = x.prev;
            size--;
        }

        public Node removeFirst(){
            if(head.next == tail){
                return null;
            }
            Node first = head.next;
            remove(first);
            return first;
        }

        private int getSize(){
            return size;
        }
    }

    // 链表节点
    static class Node{
        int key, val;
        Node prev, next;
        Node(int key, int val){
            this.key = key;
            this.val = val;
        }
    }
}


/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache obj = new LRUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */
```

从题意出发，我们需要一个满足三个条件的数据结构以及相应的办法：
- 有时序性，以区分最近使用和很久没有使用的数据，同时当容量满了的时候，要删掉最久未使用的数据  -->  使用双向链表的头插法，那么要删除的节点就是尾指针指着的；
- 要在这个数据结构快速找到某个key是否存在，并返回其对应的value  --> 使用map；
- 每次访问这个数据结构中的某个 key，需要将这个元素变为最近使用的。也就是说，这个数据结构要支持在任意位置快速插入和删除元素   -->   不难，双向链表可以实现即可


### 提问环节
- 为什么使用双向链表而不是单链表？
删除元素时，要做到链表删除元素，除了需要自己的指针消息，还要前驱节点的指针，同时这里要求O(1)的时间，所以不能用单链表

- 哈希表已经存了key，那么链表中为什么还要存key和value？
上面提到删除节点，我们只聊到双向链表，却忽略了map表同样也要操作；
删除map表需要什么，需要key吧，key从哪里来，答案是双向链表。
如果双向链表只有value，那我们该如何删map里的节点呢？

也可以用LinkedHashMap来完成
```Java
class LRUCache {
    int capacity;
    LinkedHashMap<Integer, Integer> cache;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        cache = new LinkedHashMap<Integer, Integer>(capacity, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry eldest) {
                return cache.size() > capacity;
            }
        };
    }

    public int get(int key) {
        return cache.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
        cache.put(key, value);
    }
}
```

以下是扩展内容
# LFU
```Java
class LFUCache {
    // key 到 val 的映射，后文称为 KV 表
    HashMap<Integer, Integer> keyToVal;
    // key 到 freq 的映射，后文称为 KF 表
    HashMap<Integer, Integer> keyToFreq;
    // freq 到 key列表 的映射，后文称为 FK 表
    HashMap<Integer, LinkedHashSet<Integer>> freqToKeys;
    // 记录最小的频次
    int minFreq;
    // 记录 LFU 缓存的最大容量
    int cap;

    public LFUCache(int capacity) {
        keyToVal = new HashMap<>();
        keyToFreq = new HashMap<>();
        freqToKeys = new HashMap<>();
        this.minFreq = 0;
        this.cap = capacity;
    }
    
    public int get(int key) {
        if(!keyToVal.containsKey(key)){
            return -1;
        }
        // 增加 key 对应的 freq
        increaseFreq(key);
        return keyToVal.get(key);
    }
    
    public void put(int key, int value) {
        if(this.cap <= 0){return;}
        // 若key 存在， 修改对应value
        if(keyToVal.containsKey(key)){
            keyToVal.put(key, value);
            // key对应的freq加一
            increaseFreq(key);
            return;
        }
        // key 不存在，需要插入，另外如果容量达到上限，淘汰一个 freq 最小的 key
        if(this.cap <= keyToVal.size()){
            removeMinFreqKey();
        }
        // 插入key 和 value，对应的freq为1
        keyToVal.put(key, value);
        keyToFreq.put(key, 1);
        freqToKeys.putIfAbsent(1, new LinkedHashSet<>());
        freqToKeys.get(1).add(key);
        // 插入新的 key 后最小的 freq 肯定是1
        this.minFreq = 1;
    }

    private void removeMinFreqKey(){
        // freq 最小的key列表
        LinkedHashSet<Integer> keyList = freqToKeys.get(this.minFreq);
        // 其中最先被插入的那个 key 就是该被淘汰的 key
        int deleteKey = keyList.iterator().next();
        // 更新 FK 表
        keyList.remove(deleteKey);
        if(keyList.isEmpty()){
            freqToKeys.remove(this.minFreq);
        }
        // 更新 KV 表
        keyToVal.remove(deleteKey);
        // 更新 KF 表
        keyToFreq.remove(deleteKey);
    }

    private void increaseFreq(int key){
        int freq = keyToFreq.get(key);
        // 更新 KF 表
        keyToFreq.put(key, freq + 1);
        // 更新 FK 表
        // 将 key 从 freq 对用的列表中删除
        freqToKeys.get(freq).remove(key);
        // 将 key 加入 freq+1 对应的列表中
        freqToKeys.putIfAbsent(freq + 1, new LinkedHashSet<>());
        freqToKeys.get(freq+1).add(key);
        // 如果 freq 对应的列表为空， 移除这个 freq
        if(freqToKeys.get(freq).isEmpty()){
            freqToKeys.remove(freq);
            // 如果这个 freq 恰好是 minFreq， 更新 minFreq
            if(freq == this.minFreq){
                this.minFreq++;
            }
        }
    }
}

/**
 * Your LFUCache object will be instantiated and called as such:
 * LFUCache obj = new LFUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.put(key,value);
 */

```