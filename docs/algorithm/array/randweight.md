# Random Pick with Weight

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟠      | [528. Random Pick with Weight](https://leetcode.com/problems/random-pick-with-weight/) |[528. Random Pick with Weight](#528-random-pick-with-weight)      |



二分搜索算法中，当目标元素`target`不存在于数组`nums`中时，搜索左侧边界的二分搜索的返回值为：

1. **返回的这个值是`nums`中大于等于`target`的最小元素索引**
2. **返回的这个值是`target`应该插入在`nums`中的索引位置**
3. **返回的这个值是`nums`中小于`target`的元素个数**

## 528. Random Pick with Weight

You are given a **0-indexed** array of positive integers `w` where `w[i]` describes the **weight** of the `ith` index.

You need to implement the function `pickIndex()`, which **randomly** picks an index in the range `[0, w.length - 1]` (**inclusive**) and returns it. The **probability** of picking an index `i` is `w[i] / sum(w)`.

- For example, if `w = [1, 3]`, the probability of picking index `0` is `1 / (1 + 3) = 0.25` (i.e., `25%`), and the probability of picking index `1` is `3 / (1 + 3) = 0.75` (i.e., `75%`).

 

**Example 1:**

```
Input
["Solution","pickIndex"]
[[[1]],[]]
Output
[null,0]

Explanation
Solution solution = new Solution([1]);
solution.pickIndex(); // return 0. The only option is to return 0 since there is only one element in w.
```

**Example 2:**

```
Input
["Solution","pickIndex","pickIndex","pickIndex","pickIndex","pickIndex"]
[[[1,3]],[],[],[],[],[]]
Output
[null,1,1,1,1,0]

Explanation
Solution solution = new Solution([1, 3]);
solution.pickIndex(); // return 1. It is returning the second element (index = 1) that has a probability of 3/4.
solution.pickIndex(); // return 1
solution.pickIndex(); // return 1
solution.pickIndex(); // return 1
solution.pickIndex(); // return 0. It is returning the first element (index = 0) that has a probability of 1/4.

Since this is a randomization problem, multiple answers are allowed.
All of the following outputs can be considered correct:
[null,1,1,1,1,0]
[null,1,1,1,1,1]
[null,1,1,1,0,0]
[null,1,1,1,0,1]
[null,1,0,1,0,0]
......
and so on.
```

 

**Constraints:**

- `1 <= w.length <= 104`
- `1 <= w[i] <= 105`
- `pickIndex` will be called at most `104` times.

```java
class Solution {
    private int[] preSum;
    private Random rand = new Random();
    public Solution(int[] w) {
        int n = w.length;
        preSum = new int[n+1];
        preSum[0] = 0;
        for(int i = 1; i <= n; i++) {
            preSum[i] = preSum[i-1] + w[i-1];
        }
    }
    
    public int pickIndex() {
        int n = preSum.length;
        int target = rand.nextInt(preSum[n-1]) + 1;
        return leftBound(preSum, target) - 1;
    }
    
    private int leftBound(int[] nums, int target) {
        if(nums.length == 0) return -1;
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
}
```

