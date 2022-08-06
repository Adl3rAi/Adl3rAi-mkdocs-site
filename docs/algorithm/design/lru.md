# Least Recently Used(LRU) cache

| Difficulty |                          LeetCode                          | Note |
| :--------: | :--------------------------------------------------------: | :--: |
|     🟠      | [146. LRU Cache](https://leetcode.com/problems/lru-cache/) |[146. LRU Cache](#146-lru-cache)      |

## 146. LRU Cache

Design a data structure that follows the constraints of a **[Least Recently Used (LRU) cache](https://en.wikipedia.org/wiki/Cache_replacement_policies#LRU)**.

Implement the `LRUCache` class:

- `LRUCache(int capacity)` Initialize the LRU cache with **positive** size `capacity`.
- `int get(int key)` Return the value of the `key` if the key exists, otherwise return `-1`.
- `void put(int key, int value)` Update the value of the `key` if the `key` exists. Otherwise, add the `key-value` pair to the cache. If the number of keys exceeds the `capacity` from this operation, **evict** the least recently used key.

The functions `get` and `put` must each run in `O(1)` average time complexity.

 

**Example 1:**

```
Input
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
Output
[null, null, null, 1, null, -1, null, -1, 3, 4]

Explanation
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // cache is {1=1}
lRUCache.put(2, 2); // cache is {1=1, 2=2}
lRUCache.get(1);    // return 1
lRUCache.put(3, 3); // LRU key was 2, evicts key 2, cache is {1=1, 3=3}
lRUCache.get(2);    // returns -1 (not found)
lRUCache.put(4, 4); // LRU key was 1, evicts key 1, cache is {4=4, 3=3}
lRUCache.get(1);    // return -1 (not found)
lRUCache.get(3);    // return 3
lRUCache.get(4);    // return 4
```

 

**Constraints:**

- `1 <= capacity <= 3000`
- `0 <= key <= 104`
- `0 <= value <= 105`
- At most 2` * 105` calls will be made to `get` and `put`.

设计数据结构首先要了解需求，什么是**LRU Cache**

可以通过手机后台的形式理解，也就是旧内容为新内容腾位置

* 如果打开**旧应用**，则旧应用的时序位置为**“最新”**
* 如果打开**新应用**：
  * 如果此时缓存仍有剩余，则在缓存中添加**新应用**，并且该**新应用**的时序位置为**“最新”**
  * 如果此时缓存已经满，则在缓存中删去时序位置为**“最旧”**的**旧应用**，并且将**新应用**添加至缓存中，且该**新应用**的时序位置为**“最新”**

综上，**LRU Cache**的设计要求是一要有时序，已区分“最新”和“最旧”；二要有“一一对应”(**<u>哈希表</u>**)；三是各数据也要有前后顺序之分(**<u>链表</u>**)

并且题设中要求的时间复杂度为`O(1)`，那么应使用双向哈希链表

定义双向哈希链表节点

```cpp
struct Node {
  int key;
  int val;
  Node* prev;
  Node* next;
  Node(int k, int v): key(k), val(v), prev(nullptr), next(nullptr) {};
};
```

定义双向哈希链表

```cpp
class DoubleList {
  private:
  Node* head;
  Node* tail;
  int size;
  public:
  DoubleList(){
    head = new head(0,0);
    tail = new tail(0,0);
    head->next = tail;
    tail->prev = head;
    size = 0;
  }
  // 将双向链表靠近尾部位置设定为时序上的“新”
  // 那么如下则为打开一个新应用
  void addLast(Node* x) {
    x->prev = tail->prev;
    x->next = tail;
    tail->prev->next = x;
    tail->prev = x;
    size++;
  }
  // 将双向链表靠近头部位置设定为时序上的“旧”
  // 那么如下则为删去一个旧应用
  void remove(Node* x) {
    x->prev->next = x->next;
    x->next->prev = x->prev;
    size--;
  }
  Node* removeFirst() {
    if(head->next == tail) return nullptr;
    Node* first = head->next;
    remove(first);
    return first;
  }
  // 返回一个双向哈希链表的size
  int cap() {
    return size;
  }
};
```

尽量避免**LRU**直接操作`mp`和`cache`的细节，可以提供一些中间**API**

```cpp
class LRUCache {
  private:
  map<int, Node*> mp;
  DoubleList cache;
  int cap;
  // 打开旧有的应用，使其在时序上为“最新”
  void makeRecently(int key) {
    Node* x = mp[key];
    cache.remove(x);
    cache.addLast(x);
  }
  // 打开新的应用，在mp中添加新应用，并在时序上设为“最新”
  void addRecently(int key, int val) {
    Node* x = new Node(key, val);
    cache.addLast(x);
    mp[key] = x;
  }
  void deleteKey(int key) {
    Node* x = mp[key];
    cache.remove(x);
    mp.erase(key);
  }
  void removeLeastRecently() {
    Node* deletedNode = cache.removeFirst();
    int deletedKey = deletedNode->key;
    mp.erase(deletedKey);
  }
  public:
  LRUCache(int capacity) {
    this->cap = capacity;
  }
  int get(int key) {
    if(!mp.count(key)) {
      return -1;
    }
    makeRecently(key);
    return mp[key]->val;
  }
  void put(int key, int val) {
    if(mp.count(key)){
      deleteKey(key);
      addRecently(key, val);
      return;
    }
    if(cap == cache.cap()) {
      removeLeastRecently();
    }
    addRecently(key, val);
  }
};
```

使用`java`自带的`LinkedHashMap`

```java
class LRUCache {
    int cap;
    LinkedHashMap<Integer, Integer> cache = new LinkedHashMap<>();
    public LRUCache(int capacity) {
        this.cap = capacity;
    }
    
    public int get(int key) {
        if(!cache.containsKey(key)) {
            return -1;
        }
        makeRecently(key);
        return cache.get(key);
    }
    
    public void put(int key, int val) {
        if(cache.containsKey(key)) {
            cache.put(key, val);
            makeRecently(key);
            return;
        }
        if(cache.size() == this.cap) {
            int olderKey = cache.keySet().iterator().next();
            cache.remove(olderKey);
        }
        cache.put(key, val);
    }
    
    private void makeRecently(int key) {
        int val = cache.get(key);
        cache.remove(key);
        cache.put(key, val);
    }
}
```

