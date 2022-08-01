# Merge Sort

| Difficulty |                           LeetCode                           |                             Note                             |
| :--------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     🟠      | [912. Sort an Array](https://leetcode.com/problems/sort-an-array/) | [912. Sort an Array](#912-sort-an-array) |
|     🔴      | [315. Count of Smaller Numbers After Self](https://leetcode.com/problems/count-of-smaller-numbers-after-self/) | [315. Count of Smaller Numbers After Self](#315-count-of-smaller-numbers-after-self) |
|     🔴      | [493. Reverse Pairs](https://leetcode.com/problems/reverse-pairs/) | [493. Reverse Pairs](#493-reverse-pairs) |
|     🔴      | [327. Count of Range Sum](https://leetcode.com/problems/count-of-range-sum/) | [327. Count of Range Sum](#327-count-of-range-sum) |

**归并排序的思路：先分组排序，再将分组排序后的结果合并**

```cpp
void sort(vector<int>& nums, int lo, int hi) {
  if(lo == hi) return; // 单独一个元素无需排列
  int mid = lo + (hi-lo)/2;
  sort(nums, lo, mid);
  sort(nums, mid+1, hi); // 分组排序
  merge(nums, lo, mid, hi); // 合并结果
}
vector<int> temp;
void merge(vector<int>& nums, int lo, int mid, int hi){	
	for(int i = lo; i <= hi; i++) {
    temp[i] = nums[i];
  }
  int i = lo;
  int j = mid+1;
  for(int p = lo; p <= hi; p++) {
    if(i == mid+1) {
      nums[p] = temp[j];
      j++;
    }
    else if(j == hi+1) {
      nums[p] = temp[i];
      i++;
    }
    else if(temp[i]>temp[j]) {
      nums[p] = temp[j];
      j++;
    }
    else {
      nums[p] = temp[i];
      i++;
    }
  }
}
```

## 912. Sort an Array

Given an array of integers `nums`, sort the array in ascending order.

 

**Example 1:**

```
Input: nums = [5,2,3,1]
Output: [1,2,3,5]
```

**Example 2:**

```
Input: nums = [5,1,1,2,0,0]
Output: [0,0,1,1,2,5]
```

 

**Constraints:**

- `1 <= nums.length <= 5 * 104`
- `-5 * 104 <= nums[i] <= 5 * 104`

```cpp
class Merge {
private:
    void sort(vector<int>& nums, int lo, int hi) {
        if(lo == hi) return;
        int mid = lo + (hi-lo)/2;
        sort(nums, lo, mid);
        sort(nums, mid+1, hi);
        merge(nums, lo, mid, hi);
    }
    vector<int> temp;
    void merge(vector<int>& nums, int lo, int mid, int hi) {
        for(int i = lo; i <= hi; i++) {
            temp[i] = nums[i];
        }
        int i = lo;
        int j = mid+1;
        for(int p = lo; p <= hi; p++) {
            if(i == mid + 1) {
                nums[p] = temp[j];
                j++;
            }
            else if(j == hi + 1) {
                nums[p] = temp[i];
                i++;
            }
            else if(temp[i] < temp[j]) {
                nums[p] = temp[i];
                i++;
            }
            else {
                nums[p] = temp[j];
                j++;
            }
        }
    }
public:
    void sort(vector<int>& nums) {
        temp.resize(nums.size());
        sort(nums, 0, nums.size()-1);
    }
};

class Solution {
public:
    vector<int> sortArray(vector<int>& nums) {
        Merge merge;
        merge.sort(nums);
        return nums;
    }
};
```

## 315. Count of Smaller Numbers After Self

You are given an integer array `nums` and you have to return a new `counts` array. The `counts` array has the property where `counts[i]` is the number of smaller elements to the right of `nums[i]`.

 

**Example 1:**

```
Input: nums = [5,2,6,1]
Output: [2,1,1,0]
Explanation:
To the right of 5 there are 2 smaller elements (2 and 1).
To the right of 2 there is only 1 smaller element (1).
To the right of 6 there is 1 smaller element (1).
To the right of 1 there is 0 smaller element.
```

**Example 2:**

```
Input: nums = [-1]
Output: [0]
```

**Example 3:**

```
Input: nums = [-1,-1]
Output: [0,0]
```

 

**Constraints:**

- `1 <= nums.length <= 105`
- `-104 <= nums[i] <= 104`

```cpp
struct Pair {
  int val;
  int id;
};
class Solution {
  private:
  vector<Pair> temp;
  vector<int> count;
  void sort(vector<Pair>& arr, int lo, int hi) {
    if(lo == hi) return;
    int mid = lo + (hi - lo) / 2;
    sort(arr, lo, mid);
    sort(arr, mid+1, hi);
    merge(arr, lo, mid, hi);
  }
  void merge(vector<Pair>& arr, int lo, int mid, int hi) {
    for(int i = lo; i <= hi; i++) {
      temp[i] = arr[i];
    }
    int i = lo;
    int j = mid + 1;
    for(int p = lo; p <= hi; p++) {
      if(i == mid + 1) {
        arr[p] = temp[j++];
      }
      else if(j == hi + 1) {
        arr[p] = temp[i++];
        count[arr[p].id] += j - mid - 1;
      }
      else if(temp[i].val > temp[j].val) {
        arr[p] = temp[j++];
      }
      else {
        arr[p] = temp[i++];
        count[arr[p].id] += j - mid - 1;
      }
    }
  }
public:
  vector<int> countSmaller(vector<int>& nums) {
    int n = nums.size();
    temp.resize(n);
    count.resize(n);
    vector<Pair> arr;
    arr.resize(n);
    for(int i = 0; i < n; i++) {
      Pair pair;
      pair.val = nums[i];
      pair.id = i;
      arr[i] = pair;
    }
    sort(arr, 0, n-1);
    vector<int> res;
    for(auto c : count) {
      res.push_back(c);
    }
    return res;
  }
};
```

## 493. Reverse Pairs

Given an integer array `nums`, return *the number of **reverse pairs** in the array*.

A reverse pair is a pair `(i, j)` where `0 <= i < j < nums.length` and `nums[i] > 2 * nums[j]`.

 

**Example 1:**

```
Input: nums = [1,3,2,3,1]
Output: 2
```

**Example 2:**

```
Input: nums = [2,4,3,5,1]
Output: 3
```

 

**Constraints:**

- `1 <= nums.length <= 5 * 104`
- `-231 <= nums[i] <= 231 - 1`

```cpp
class Solution {
public:
    int reversePairs(vector<int>& nums) {
        sort(nums);
        return count;
    }
    void sort(vector<int>& nums) {
        temp.resize(nums.size());
        sort(nums, 0, nums.size()-1);
    }
private:
    vector<int> temp;
    int count = 0;
    void sort(vector<int>& nums, int lo, int hi) {
        if(lo == hi) return;
        int mid = lo + (hi-lo)/2;
        sort(nums, lo, mid);
        sort(nums, mid+1, hi);
        merge(nums, lo, mid, hi);
    }
    void merge(vector<int>& nums, int lo, int mid, int hi) {
        for(int i = lo; i <= hi; i++) {
            temp[i] = nums[i];
        }
        int end = mid + 1;
        for(int i = lo; i <= mid; i++) {
            while(end <= hi && (long)nums[i] > (long)nums[end] * 2) {
                end++;
            }
            count += end - (mid + 1);
        };
        int i = lo;
        int j = mid + 1;
        for (int p = lo; p <= hi; p++) {
            if(i == mid + 1) {
                nums[p] = temp[j++];
            }
            else if(j == hi + 1) {
                nums[p] = temp[i++];
            }
            else if(temp[i] < temp[j]) {
                nums[p] = temp[i++];
            }
            else {
                nums[p] = temp[j++];
            }
        }
    }
};
```

## 327. Count of Range Sum

Given an integer array `nums` and two integers `lower` and `upper`, return *the number of range sums that lie in* `[lower, upper]` *inclusive*.

Range sum `S(i, j)` is defined as the sum of the elements in `nums` between indices `i` and `j` inclusive, where `i <= j`.

 

**Example 1:**

```
Input: nums = [-2,5,-1], lower = -2, upper = 2
Output: 3
Explanation: The three ranges are: [0,0], [2,2], and [0,2] and their respective sums are: -2, -1, 2.
```

**Example 2:**

```
Input: nums = [0], lower = 0, upper = 0
Output: 1
```

 

**Constraints:**

- `1 <= nums.length <= 105`
- `-231 <= nums[i] <= 231 - 1`
- `-105 <= lower <= upper <= 105`
- The answer is **guaranteed** to fit in a **32-bit** integer.

```cpp
class Solution {
public:
    int countRangeSum(vector<int>& nums, int lower, int upper) {
        this->lower = lower;
        this->upper = upper;
        vector<long> preSum;
        preSum.resize(nums.size()+1);
        for(int i = 0; i < nums.size(); i++) {
            preSum[i+1] = (long)nums[i] + preSum[i];
        }
        sort(preSum);
        return count;
    }
    void sort(vector<long>& nums) {
        temp.resize(nums.size());
        sort(nums, 0, nums.size()-1);
    }
private:
    int lower;
    int upper;
    int count = 0;
    vector<long> temp;
    void sort(vector<long>& nums, int lo, int hi) {
        if(lo == hi) return;
        int mid = lo + (hi - lo) / 2;
        sort(nums, lo, mid);
        sort(nums, mid+1, hi);
        merge(nums, lo, mid, hi);
    }
    void merge(vector<long>& nums, int lo, int mid, int hi) {
        for(int i = lo; i <= hi; i++) {
            temp[i] = nums[i];
        }
        int start = mid + 1;
        int end = mid + 1;
        for(int i = lo; i <= mid; i++) {
            while(start <= hi && nums[start] - nums[i] < lower) {
                start++;
            }
            while(end <= hi && nums[end] - nums[i] <= upper) {
                end++;
            }
            count += (end-start);
        }
        int i = lo;
        int j = mid + 1;
        for(int p = lo; p <= hi; p++) {
            if (i == mid + 1) {
                nums[p] = temp[j++];
            }
            else if(j == hi + 1) {
                nums[p] = temp[i++];
            }
            else if(temp[i] < temp[j]) {
                nums[p] = temp[i++];
            }
            else {
                nums[p] = temp[j++];
            }
        }
    }
};
```

