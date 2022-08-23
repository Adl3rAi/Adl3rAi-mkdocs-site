# Binary Search

二分查找框架

```java
int binarySearch(int[] nums, int target) {
  int left = 0;
  int right = ... ;
  while(...) {
    int mid = left + (right - left) / 2;
    if(nums[mid] == target) {
      ...
    }
    else if(nums[mid] < target) {
      left = ...
    }
    else if(nums[mid] > target) {
      right = ...
    }
  }
  return ...;
}
```

## 704. Binary Search

Given an array of integers `nums` which is sorted in ascending order, and an integer `target`, write a function to search `target` in `nums`. If `target` exists, then return its index. Otherwise, return `-1`.

You must write an algorithm with `O(log n)` runtime complexity.

 

**Example 1:**

```
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
Explanation: 9 exists in nums and its index is 4
```

**Example 2:**

```
Input: nums = [-1,0,3,5,9,12], target = 2
Output: -1
Explanation: 2 does not exist in nums so return -1
```

 

**Constraints:**

- `1 <= nums.length <= 104`
- `-104 < nums[i], target < 104`
- All the integers in `nums` are **unique**.
- `nums` is sorted in ascending order.

```java
class Solution {
  public int search(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while(left <= right) {
      int mid = left + (right - left) / 2;
      if(nums[mid] == target) {
        return mid;
      }
      else if(nums[mid] < target) {
        left = mid + 1;
      }
      else if(nums[mid] > target) {
        right = mid - 1;
      }
    }
    return -1;
  }
}
```

1. **`while`循环的条件是`<=`而非`<`，由于初始化时`right == nums.length-1`，是最后一个元素的索引，区间是`[left,right]`，`while(left <= right)`循环终止的条件是`left == right + 1`，而`while(left < right)`循环终止的条件是`left == right`，区间`[right,right]`非空，漏去了索引`right`**
2. **同上述理由，搜索区间为`[left,right]`那么，当`nums[mid]`已经被查找过后，搜索区间的终点或起点应该为`nums[mid-1]`或`nums[mid+1]`**
3. **该算法的缺陷在于当存在重复元素时，只会返回最中间的元素**

---

如果需要寻找左侧边界的二分搜索

```java
int left_bound(int[] nums, int target) {
  int left = 0;
  int right = nums.length;
  while(left < right) {
    int mid = left + (right - left) / 2;
    if(nums[mid] == target) {
      right = mid;
    }
    else if(nums[mid] < target) {
      left = mid + 1;
    }
    else if(nums[mid] > target) {
      right = mid;
    }
  }
  return left;
}
```

1. `while(left < right)`，`right = nums.length`而非`nums.length-1`

   每次循环的搜索区间是`[left,right)`

   `while(left < right)`的终止条件是`right == left`，区间`[left,left)`为空

2. 不存在`return -1`的操作，如果`nums`中不存在`target`，在返回时额外判断一下`nums[left]`是否等于`target`

3. `left = mid + 1, right = mid`，搜索区间为`[left,right)`，当`nums[mid]`被检测过，左侧搜索区间为`[left,mid)`，右侧搜索区间为`[mid+1,right)`

4. `nums[mid] == target; right = mid`使得算法能够搜索左侧边界

5. `return left`等价于`return right`，因为`while`循环终结的条件就是`left == right`

```java
int left_bound(int[] nums, int target) {
  int left = 0;
  int right = nums.length - 1;
  while(left <= right) {
    int mid = left + (right - left) / 2;
    if(nums[mid] < target) {
      left = mid + 1;
    }
    else if(nums[mid] > target) {
      right = mid - 1;
    }
    else if(nums[mid] == target) {
      right == mid - 1;
    }
  }
  if(left == nums.length) return -1;
  return nums[left] == target ? left : -1;
}
```

---

寻找右侧边界的二分查找

```java
int right_bound(int[] nums, int target) {
  int left = 0;
  int right = nums.length;
  while(left < right) {
    int mid = left + (right - left) / 2;
    if(nums[mid] == target) {
      left = mid + 1;
    }
    else if(nums[mid] < target) {
      left = mid + 1;
    }
    else if(nums[mid] > target) {
      right = mid;
    }
  }
  return left - 1;
}
```

返回`left-1`或`right-1`，而不是`left`或`right`，原因在于，搜索右侧边界时锁定右边界的条件判断是

```java
if(nums[mid] == target) {
  left = mid + 1;
}
```

`mid = left - 1`，`return mid`等价于`return left-1`

```java
while(left < right) {
  ...
}
if(left - 1 < 0) return -1;
return nums[left-1] == target ? (left - 1) : -1;
```

## 34. Find First and Last Position of Element in Sorted Array

Given an array of integers `nums` sorted in non-decreasing order, find the starting and ending position of a given `target` value.

If `target` is not found in the array, return `[-1, -1]`.

You must write an algorithm with `O(log n)` runtime complexity.

 

**Example 1:**

```
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]
```

**Example 2:**

```
Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

**Example 3:**

```
Input: nums = [], target = 0
Output: [-1,-1]
```

 

**Constraints:**

- `0 <= nums.length <= 105`
- `-109 <= nums[i] <= 109`
- `nums` is a non-decreasing array.
- `-109 <= target <= 109`

```java
class Solution {
    public int[] searchRange(int[] nums, int target) {
        int left = leftBound(nums,target);
        int right = rightBound(nums,target);
        return new int[]{left,right};
    }
    public int leftBound(int[] nums, int target) {
        int left = 0;
        int right = nums.length-1;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] == target) {
                right = mid - 1;
            }
            else if(nums[mid] < target) {
                left = mid + 1;
            }
            else if(nums[mid] > target) {
                right = mid - 1;
            }
        }
        if(left == nums.length) return -1;
        return nums[left] == target ? left : -1;
    }
    public int rightBound(int[] nums, int target) {
        int left = 0;
        int right = nums.length - 1;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if(nums[mid] == target) {
                left = mid + 1;
            }
            else if(nums[mid] < target) {
                left = mid + 1;
            }
            else if(nums[mid] > target) {
                right = mid - 1;
            }
        }
        if(left - 1 < 0) return -1;
        return nums[left-1] == target ? (left-1) : -1;
    }
}
```

