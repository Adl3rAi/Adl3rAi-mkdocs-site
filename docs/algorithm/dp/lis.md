# Longest Increasing Subsequence(LIS)

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟠      | [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/submissions/) |[300. Longest Increasing Subsequence](#300-longest-increasing-subsequence)      |



## 300. Longest Increasing Subsequence

Given an integer array `nums`, return the length of the longest strictly increasing subsequence.

A **subsequence** is a sequence that can be derived from an array by deleting some or no elements without changing the order of the remaining elements. For example, `[3,6,2,7]` is a subsequence of the array `[0,3,1,6,2,2,7]`.

 

**Example 1:**

```
Input: nums = [10,9,2,5,3,7,101,18]
Output: 4
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.
```

**Example 2:**

```
Input: nums = [0,1,0,3,2,3]
Output: 4
```

**Example 3:**

```
Input: nums = [7,7,7,7,7,7,7]
Output: 1
```

 

**Constraints:**

- `1 <= nums.length <= 2500`
- `-104 <= nums[i] <= 104`

```java
class Solution {
  public int lengthOfLIS(int[] nums) {
    int[] dp = new int[nums.length];
    Arrays.fill(dp, 1);
    for(int i = 0; i < nums.length; i++) {
      for(int j = 0; j < i; j++) {
        if(nums[i] > nums[j]) {
          dp[i] = Math.max(dp[i], dp[j]+1);
        }
      }
    }
    int res = 0;
    for(int i = 0; i < dp.length; i++) {
      res = Math.max(res, dp[i]);
    }
    return res;
  }
}
```

