# Quick Sort

| Difficulty |                           LeetCode                           |                             Note                             |
| :--------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     🟠      | [912. Sort an Array](https://leetcode.com/problems/sort-an-array/) | [912. Sort an Array]() |
|     🟠      | [215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/) | [215. Kth Largest Element in an Array]() |

**快速排序的本质是构建二叉搜索树**

```cpp
void sort(vector<int>& nums, int lo, int hi) {
  if(lo >= hi) return;
  int p = partition(nums, lo, hi);
  sort(nums, lo, p - 1);
  sort(nums, p + 1, hi);
}
```

同二叉树的前序遍历比较一下

```cpp
void traverse(TreeNode* root) {
  cout << root->val << " ";
  traverse(root->left);
  traverse(root->right);
}
```

快排也是二叉树的前序遍历

```cpp
class Quick {
  public:
  void sort(vector<int>& nums) {
    shuffle(nums);
    sort(nums, 0, nums.size()-1);
  }
  private:
  void sort(vector<int>& nums, int lo, int hi) {
    if(lo >= hi) return;
    int p = partition(nums, lo, hi);
    sort(nums, lo, p-1);
    sort(nums, p+1, hi);
  }
  int partition(vector<int>& nums, int lo, int hi) {
    int pivot = nums[lo];
    int i = lo + 1;
    int j = hi;
    while(i <= j) {
      while(i < hi && nums[i] <= pivot) {
        i++;
      }
      while(j > lo && nums[j] > pivot) {
        j--;
      }
      if(i >= j) {
        break;
      }
      swap(nums, i, j);
    }
    swap(nums, lo, j);
    return j;
  }
  void shuffle(vector<int>& nums) {
    int n = nums.size();
    for(int i = 0; i < n; i++) {
      int r = i + rand() % (n - i);
      swap(nums, i, r);
    }
  }
  void swap(vector<int>& nums, int i, int j) {
    int temp = nums[i];
    nums[i] = nums[j];
    nums[j] = temp;
  }
};
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
// 同上文
```

## 215. Kth Largest Element in an Array

Given an integer array `nums` and an integer `k`, return *the* `kth` *largest element in the array*.

Note that it is the `kth` largest element in the sorted order, not the `kth` distinct element.

You must solve it in `O(n)` time complexity.

 

**Example 1:**

```
Input: nums = [3,2,1,5,6,4], k = 2
Output: 5
```

**Example 2:**

```
Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
```

 

**Constraints:**

- `1 <= k <= nums.length <= 105`
- `-104 <= nums[i] <= 104`

```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        shuffle(nums);
        int lo = 0;
        int hi = nums.size() - 1;
        k = nums.size() - k;
        while(lo <= hi) {
            int p = partition(nums, lo, hi);
            if(p < k) {
                lo = p + 1;
            }
            else if(p > k) {
                hi = p - 1;
            }
            else {
                return nums[p];
            }
        }
        return -1;
    }
    int partition(vector<int>& nums, int lo, int hi) {
        int pivot = nums[lo];
        int i = lo + 1;
        int j = hi;
        while(i <= j) {
            while(i < hi && nums[i] <= pivot) {
                i++;
            }
            while(j > lo && nums[j] > pivot) {
                j--;
            }
            if(i >= j) {
                break;
            }
            swap(nums, i, j);
        }
        swap(nums, lo, j);
        return j;
    }
    void shuffle(vector<int>& nums) {
        int n = nums.size();
        for(int i = 0; i < n; i++) {
            int r = i + rand() % (n - i);
            swap(nums, i, r);
        }
    }
    void swap(vector<int>& nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
};
```

