# Randomized Set

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟠      | [380. Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/) |      |



设计一个时间复杂度为O(1)的数据结构，包括`insert(val)`, `remove(val)`, `getRandom`

首先考虑对于**插入**和**删除**两样操作，什么数据结构的时间复杂度是O(1)?

必然是一个哈希的数据结构，例如`HashSet`，其中`LinkedHashSet`通过哈希表配合双链表实现了有序性，但却无法实现`getRandom`函数

如果需要等概率选择元素，只能采用**原始数组**

但是**原始数组**如何实现O(1)复杂度的插入和删除?

将目标元素放在数组的尾部即可

通过一个`valToIndex`的`HashMap`储存`nums[index]`和`index`的关联关系，以实现`nums[index]`和`nums[nums.size()-1]`即末尾元素，之间的互换，互换后并修改`valToIndex`的对应关系，之后即可以删去目标元素，且时间复杂度为O(1)

## 380. Insert Delete GetRandom O(1)

Implement the `RandomizedSet` class:

- `RandomizedSet()` Initializes the `RandomizedSet` object.
- `bool insert(int val)` Inserts an item `val` into the set if not present. Returns `true` if the item was not present, `false` otherwise.
- `bool remove(int val)` Removes an item `val` from the set if present. Returns `true` if the item was present, `false` otherwise.
- `int getRandom()` Returns a random element from the current set of elements (it's guaranteed that at least one element exists when this method is called). Each element must have the **same probability** of being returned.

You must implement the functions of the class such that each function works in **average** `O(1)` time complexity.

 

**Example 1:**

```
Input
["RandomizedSet", "insert", "remove", "insert", "getRandom", "remove", "insert", "getRandom"]
[[], [1], [2], [2], [], [1], [2], []]
Output
[null, true, false, true, 2, true, false, 2]

Explanation
RandomizedSet randomizedSet = new RandomizedSet();
randomizedSet.insert(1); // Inserts 1 to the set. Returns true as 1 was inserted successfully.
randomizedSet.remove(2); // Returns false as 2 does not exist in the set.
randomizedSet.insert(2); // Inserts 2 to the set, returns true. Set now contains [1,2].
randomizedSet.getRandom(); // getRandom() should return either 1 or 2 randomly.
randomizedSet.remove(1); // Removes 1 from the set, returns true. Set now contains [2].
randomizedSet.insert(2); // 2 was already in the set, so return false.
randomizedSet.getRandom(); // Since 2 is the only number in the set, getRandom() will always return 2.
```

 

**Constraints:**

- `-231 <= val <= 231 - 1`
- At most `2 * ``105` calls will be made to `insert`, `remove`, and `getRandom`.
- There will be **at least one** element in the data structure when `getRandom` is called.

```java
class RandomizedSet {
    public ArrayList<Integer> nums;
    public HashMap<Integer, Integer> valToIndex;
    
    public RandomizedSet() {
        nums = new ArrayList<>();
        valToIndex = new HashMap<>();
    }
    
    public boolean insert(int val) {
        if(valToIndex.containsKey(val)) {
            return false;
        }
        valToIndex.put(val, nums.size());
        nums.add(val);
        return true;
    }
    
    public boolean remove(int val) {
        if(!valToIndex.containsKey(val)) {
            return false;
        }
        int index = valToIndex.get(val); // 是一个index
        int back = nums.get(nums.size()-1); // 是一个value
        valToIndex.put(back, index); // (val,index)
        int temp = nums.get(index); // 是一个val
        nums.set(index, back);
        nums.set(nums.size()-1, temp);
        nums.remove(nums.size()-1);
        valToIndex.remove(val);
        return true;
    }
    
    public int getRandom() {
        Random rand = new Random();
        return nums.get((int)(Math.random() * nums.size()));
    }
}
```

