# Least Frequently Used(LFU) Cache

| Difficulty |                          LeetCode                          | Note |
| :--------: | :--------------------------------------------------------: | :--: |
|     🔴      | [460. LFU Cache](https://leetcode.com/problems/lfu-cache/) |  [460. LFU Cache](#460-lfu-cache)  |



## 460. LFU Cache

Design and implement a data structure for a [Least Frequently Used (LFU)](https://en.wikipedia.org/wiki/Least_frequently_used) cache.

Implement the `LFUCache` class:

- `LFUCache(int capacity)` Initializes the object with the `capacity` of the data structure.
- `int get(int key)` Gets the value of the `key` if the `key` exists in the cache. Otherwise, returns `-1`.
- `void put(int key, int value)` Update the value of the `key` if present, or inserts the `key` if not already present. When the cache reaches its `capacity`, it should invalidate and remove the **least frequently used** key before inserting a new item. For this problem, when there is a **tie** (i.e., two or more keys with the same frequency), the **least recently used** `key` would be invalidated.

To determine the least frequently used key, a **use counter** is maintained for each key in the cache. The key with the smallest **use counter** is the least frequently used key.

When a key is first inserted into the cache, its **use counter** is set to `1` (due to the `put` operation). The **use counter** for a key in the cache is incremented either a `get` or `put` operation is called on it.

The functions `get` and `put` must each run in `O(1)` average time complexity.

 

**Example 1:**

```
Input
["LFUCache", "put", "put", "get", "put", "get", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [3], [4, 4], [1], [3], [4]]
Output
[null, null, null, 1, null, -1, 3, null, -1, 3, 4]

Explanation
// cnt(x) = the use counter for key x
// cache=[] will show the last used order for tiebreakers (leftmost element is  most recent)
LFUCache lfu = new LFUCache(2);
lfu.put(1, 1);   // cache=[1,_], cnt(1)=1
lfu.put(2, 2);   // cache=[2,1], cnt(2)=1, cnt(1)=1
lfu.get(1);      // return 1
                 // cache=[1,2], cnt(2)=1, cnt(1)=2
lfu.put(3, 3);   // 2 is the LFU key because cnt(2)=1 is the smallest, invalidate 2.
                 // cache=[3,1], cnt(3)=1, cnt(1)=2
lfu.get(2);      // return -1 (not found)
lfu.get(3);      // return 3
                 // cache=[3,1], cnt(3)=2, cnt(1)=2
lfu.put(4, 4);   // Both 1 and 3 have the same cnt, but 1 is LRU, invalidate 1.
                 // cache=[4,3], cnt(4)=1, cnt(3)=2
lfu.get(1);      // return -1 (not found)
lfu.get(3);      // return 3
                 // cache=[3,4], cnt(4)=1, cnt(3)=3
lfu.get(4);      // return 4
                 // cache=[4,3], cnt(4)=2, cnt(3)=3
```

 

**Constraints:**

- `0 <= capacity <= 104`
- `0 <= key <= 105`
- `0 <= value <= 109`
- At most `2 * 105` calls will be made to `get` and `put`.

```java
class LFUCache {
  // 构造容量为capacity的缓存
  public LFUCache(int capacity) {}
  // 在缓存中查询key
  public int get(int key) {}
  // 将key和val存入缓存
  public void put(int key, int val) {}
}
```

`int get(int key)`，如果`key`存在，则`return val`；否则`return -1`

`void put(int key, int val)`插入或修改缓存，如果`key`已存在，将对应的值修改为`val`；如果`key`不存在，则插入`(key, val)`

当缓存容量达到`capacity`时，在插入新的键值对之前，删除`freq`最低的键值对；如果`freq`最低有多个对，则删除其中最旧的

```java
LFUCache cache = new LFUCache(2);
// 容量为2
// insert 2 pairs (key, val), freq = 1
cache.put(1,10);
cache.put(2,20);
// query key = 1 return val
// return 10, its freq = 2
cache.get(1);
// capacity is full, remove key = 2(its freq is the least)
// insert(3,30), its freq = 1;
cache.put(3,30);
// key = 2 is removed, will return -1
cache.get(2);
```

* 使用一个`HashMap`储存`key`到`val`的映射，可以快速计算`get(key)`

```java
HashMap<Integer, Integer> keyToVal;
```

* 使用一个`HashMap`储存`key`到`freq`的映射，可以快速操作`key`对应的`freq`

```java
  HashMap<Integer, Integer> keyToFreq;
```

* LFU核心算法：
  * `freq`到`key`的映射，找到`freq`最小的`key`
  * 将`freq`最小的`key`删除，快速得到当前所有`key`最小的`freq`是多少。且时间复杂度`O(1)`，必然不能遍历，则需要`minFreq`记录最小`freq`
  * 可能有多个`key`拥有相同的`freq`，所以`freq`对应的是一个`key`列表
  * `freq`对应的`key`列表**存在时序**，便于快速查找并删除最旧的`key`
  * 能够快速删除`key`列表中的任何一个`key`，如果频次为`freq`的某个`key`被访问，其频次就会相应变为`freq+1`，则`key`在`freq`对应的列表中被删去，添加到`freq+1`对应的列表中

```java
HashMap<Integer, LinkedHashSet<Integer>> freqToKeys;
int minFreq = 0;
```

```java
class LFUCache {
  int cap;
  int minFreq;
  HashMap<Integer, Integer> keyToVal;
  HashMap<Integer, Integer> keyToFreq;
  HashMap<Integer, LinkedHashSet<Integer>> freqToKeys;
  public LFUCache(int capacity) {
    keyToVal = new HashMap<>();
    keyToFreq = new HashMap<>();
    freqToKeys = new HashMap<>();
    this.cap = capacity;
    this.minFreq = 0;
  }
  public int get(int key) {
    if(!keyToVal.containsKey(key)) return -1;
    increaseFreq(key);
    return keyToVal.get(key);
  }
  public void put(int key, int val) {
    if(this.cap <= 0) return;
    if(keyToVal.containsKey(key)) {
      keyToVal.put(key, val);
      increaseFreq(key);
      return;
    }
    if(this.cap <= keyToVal.size()) {
      removeMinFreqKey();
    }
    keyToVal.put(keu,val);
    keyToFreq.put(key,1);
    freqToKeys.putIfAbsent(1, new LinkedHashSet<>());
    freqToKeys.get(1).add(key);
    this.
  }
}
```

```java
private void removeMinFreqKey() {
  LinkedHashSet<Integer> keyList = freqToKeys.get(this.minFreq);
  int deletedKey = keyList.iterator().next();
  keyList.remove(deletedKey);
  if(keyList.isEmpty()) {
    freqToKeys.remove(this.minFreq);
  }
  keyToVal.remove(deletedKey);
  keyToFreq.remove(deletedKey);
}

private void increaseFreq(int key) {
  int freq = keyToFreq.get(key);
  keyToFreq.put(key, freq+1);
  freqToKeys.get(freq).remove(key);
  freqToKeys.putIfAbsent(freq+1, new LinkedHashSet<>());
  freqToKeys.get(freq+1).add(key);
  if(freqToKeys.get(freq).isEmpty()) {
    freqToKeys.remove(freq);
    if(freq == this.minFreq) {
      this.minFreq++;
    }
  }
}
```
