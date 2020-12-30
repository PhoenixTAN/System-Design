# LFU Cache Leetcode 460

请你为 最不经常使用（LFU）缓存算法设计并实现数据结构。

实现 LFUCache 类：

- LFUCache(int capacity) - 用数据结构的容量 capacity 初始化对象
- int get(int key) - 如果键存在于缓存中，则获取键的值，否则返回 -1。
- void put(int key, int value) - 如果键已存在，则变更其值；如果键不存在，请插入键值对。当缓存达到其容量时，则应该在插入新项之前，使最不经常使用的项无效。在此问题中，当存在平局（即两个或更多个键具有相同使用频率）时，应该去除 最久未使用 的键。
- **注意「项的使用次数」就是自插入该项以来对其调用 get 和 put 函数的次数之和。使用次数会在对应项被移除后置为 0 。**

 

进阶：

你是否可以在 O(1) 时间复杂度内执行两项操作？

示例：
```
输入：
["LFUCache", "put", "put", "get", "put", "get", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [3], [4, 4], [1], [3], [4]]
输出：
[null, null, null, 1, null, -1, 3, null, -1, 3, 4]

解释：
LFUCache lFUCache = new LFUCache(2);
lFUCache.put(1, 1);
lFUCache.put(2, 2);
lFUCache.get(1);      // 返回 1
lFUCache.put(3, 3);   // 去除键 2
lFUCache.get(2);      // 返回 -1（未找到）
lFUCache.get(3);      // 返回 3
lFUCache.put(4, 4);   // 去除键 1
lFUCache.get(1);      // 返回 -1（未找到）
lFUCache.get(3);      // 返回 3
lFUCache.get(4);      // 返回 4
```

提示：
```
0 <= capacity, key, value <= 104
最多调用 105 次 get 和 put 方法
```

## 解决方案

双哈希表+单链表。

- 一个哈希表存储key和node.
- 另一个哈希表key是frequency, value是linked list.
- 同时使用int minFrequency来维护当前最不常使用的node.
- linked list越靠前的node就是最近使用的。

## 代码
```java
class LFUCache {

    private int currentMinFrequency;
    private int CAPACITY;
    private Map<Integer, Node> cache;
    private Map<Integer, LinkedList<Node>> frequencyTable;

    public LFUCache(int capacity) {
        this.CAPACITY = capacity;
        this.currentMinFrequency = 0;
        cache = new HashMap<Integer, Node>();
        frequencyTable = new HashMap<Integer, LinkedList<Node>>();
    }
    

    public int get(int key) {
        if ( CAPACITY <= 0 ) {
            return -1;
        }

        if ( !cache.containsKey(key) ) {
            return -1;
        }

        Node node = cache.get(key);
        int value = node.value;
        int frequency = node.frequency;
        // remove the first element of the linked list
        frequencyTable.get(frequency).remove(node);

        // remove the node from frequence table
        if ( frequencyTable.get(frequency).size() == 0 ) {
            frequencyTable.remove(frequency);
            // update current minn frequency
            if ( currentMinFrequency == frequency ) {
                currentMinFrequency++;
            }
        }

        // add the node into frequence table
        LinkedList<Node> list = frequencyTable.getOrDefault(frequency + 1, new LinkedList<Node>());
        // add to the new node into the head of this linked list
        Node newHead = new Node(key, value, frequency + 1);
        list.offerFirst(newHead);
        // update frequcncy table
        frequencyTable.put(frequency + 1, list);
        // update cache
        cache.put(key, newHead);

        return value;

    }
    
    public void put(int key, int value) {

        if ( CAPACITY <= 0 ) {
            return ;
        }
        
        if ( !cache.containsKey(key) ) {
            
            // if the cache is full, remove the LFU node
            if ( cache.size() == CAPACITY ) {
                // get the tail node of freqTalbe[minFreq]
                Node node = frequencyTable.get(currentMinFrequency).peekLast();
                cache.remove(node.key);
                frequencyTable.get(currentMinFrequency).pollLast();
                if ( frequencyTable.get(currentMinFrequency).size() == 0 ) {
                    frequencyTable.remove(currentMinFrequency);
                }
            }

            LinkedList<Node> list = frequencyTable.getOrDefault(1, new LinkedList<Node>());
            Node newHead = new Node(key, value, 1);
            list.offerFirst(newHead);
            frequencyTable.put(1, list);
            cache.put(key, newHead);
            currentMinFrequency = 1;
        }
        else {
            // put the key-value pair and update the frequency table
            Node node = cache.get(key);
            int frequency = node.frequency;
            frequencyTable.get(frequency).remove(node);
            if ( frequencyTable.get(frequency).size() == 0 ) {
                frequencyTable.remove(frequency);
                if ( currentMinFrequency == frequency ) {
                    currentMinFrequency++;
                }
            }

            LinkedList<Node> list = frequencyTable.getOrDefault(frequency + 1, new LinkedList<Node>());
            Node newHead = new Node(key, value, frequency + 1);
            list.offerFirst(newHead);
            frequencyTable.put(frequency + 1, list);
            cache.put(key, newHead);
        }
    }

    class Node {
        int key;
        int value;
        int frequency;

        public Node(int key, int value, int frequency) {
            this.key = key;
            this.value = value;
            this.frequency = frequency;
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