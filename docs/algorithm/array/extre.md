# Extremum and Binary Search

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟠      | [875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/) |[875. Koko Eating Bananas](#875-koko-eating-bananas)      |
|     🟠      | [1011. Capacity To Ship Packages Within D Days](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/) |[1011. Capacity To Ship Packages Within D Days](#1011-capacity-to-ship-packages-within-d-days)      |
|     🔴      | [410. Split Array Largest Sum](https://leetcode.com/problems/split-array-largest-sum/) |[410. Split Array Largest Sum](#410-split-array-largest-sum)      |



已知自变量`x`，关于`x`的单调函数`f(x)`，目标值`target`，试求约束条件`f(x) == target`时`x=?`

## 875. Koko Eating Bananas

Koko loves to eat bananas. There are `n` piles of bananas, the `ith` pile has `piles[i]` bananas. The guards have gone and will come back in `h` hours.

Koko can decide her bananas-per-hour eating speed of `k`. Each hour, she chooses some pile of bananas and eats `k` bananas from that pile. If the pile has less than `k` bananas, she eats all of them instead and will not eat any more bananas during this hour.

Koko likes to eat slowly but still wants to finish eating all the bananas before the guards return.

Return *the minimum integer* `k` *such that she can eat all the bananas within* `h` *hours*.

 

**Example 1:**

```
Input: piles = [3,6,7,11], h = 8
Output: 4
```

**Example 2:**

```
Input: piles = [30,11,23,4,20], h = 5
Output: 30
```

**Example 3:**

```
Input: piles = [30,11,23,4,20], h = 6
Output: 23
```

 

**Constraints:**

- `1 <= piles.length <= 104`
- `piles.length <= h <= 109`
- `1 <= piles[i] <= 109`

```java
class Solution {
  public int minEatingSpeed(int[] piles, int h) {
        int left = 1;
        int right = 1000000000 + 1;
        while(left < right) {
            int mid = left + (right - left) / 2;
            if(f(piles, mid) == h) {
                right = mid;
            }
            else if(f(piles, mid) < h) {
                right = mid;
            }
            else if(f(piles, mid) > h) {
                left = mid + 1;
            }
        }
        return left;
    }
    private int f(int[] piles, int x) {
        int hours = 0;
        for(int i = 0; i < piles.length; i++) {
            hours += piles[i] / x;
            if(piles[i] % x > 0) {
                hours++;
            }
        }
        return hours;
    }
}
```

**注意：本例中函数`f(x)`是递减的，当`f(piles, mid) != h`时，注意`left`与`right`的修改**

## 1011. Capacity To Ship Packages Within D Days

A conveyor belt has packages that must be shipped from one port to another within `days` days.

The `ith` package on the conveyor belt has a weight of `weights[i]`. Each day, we load the ship with packages on the conveyor belt (in the order given by `weights`). We may not load more weight than the maximum weight capacity of the ship.

Return the least weight capacity of the ship that will result in all the packages on the conveyor belt being shipped within `days` days.

 

**Example 1:**

```
Input: weights = [1,2,3,4,5,6,7,8,9,10], days = 5
Output: 15
Explanation: A ship capacity of 15 is the minimum to ship all the packages in 5 days like this:
1st day: 1, 2, 3, 4, 5
2nd day: 6, 7
3rd day: 8
4th day: 9
5th day: 10

Note that the cargo must be shipped in the order given, so using a ship of capacity 14 and splitting the packages into parts like (2, 3, 4, 5), (1, 6, 7), (8), (9), (10) is not allowed.
```

**Example 2:**

```
Input: weights = [3,2,2,4,1,4], days = 3
Output: 6
Explanation: A ship capacity of 6 is the minimum to ship all the packages in 3 days like this:
1st day: 3, 2
2nd day: 2, 4
3rd day: 1, 4
```

**Example 3:**

```
Input: weights = [1,2,3,1,1], days = 4
Output: 3
Explanation:
1st day: 1
2nd day: 2
3rd day: 3
4th day: 1, 1
```

 

**Constraints:**

- `1 <= days <= weights.length <= 5 * 104`
- `1 <= weights[i] <= 500`

```java
class Solution {
    public int shipWithinDays(int[] weights, int days) {
        int left = 0;
        int right = 1;
        for(int w : weights) {
            left = Math.max(left, w);
            right += w;
        }
        while(left < right) {
            int mid = left + (right - left) / 2;
            if(f(weights, mid) == days) {
                right = mid;
            }
            else if(f(weights, mid) < days) {
                right = mid;
            }
            else if(f(weights, mid) > days) {
                left = mid + 1;
            }
        }
        return left;
    }
    private int f(int[] weights, int x) {
        int days = 0;
        for(int i = 0; i < weights.length;) {
            int cap = x;
            while(i < weights.length) {
                if(cap < weights[i]) break;
                else cap -= weights[i];
                i++;
            }
            days++;
        }
        return days;
    }
}
```

## 410. Split Array Largest Sum

Given an array `nums` which consists of non-negative integers and an integer `m`, you can split the array into `m` non-empty continuous subarrays.

Write an algorithm to minimize the largest sum among these `m` subarrays.

 

**Example 1:**

```
Input: nums = [7,2,5,10,8], m = 2
Output: 18
Explanation:
There are four ways to split nums into two subarrays.
The best way is to split it into [7,2,5] and [10,8],
where the largest sum among the two subarrays is only 18.
```

**Example 2:**

```
Input: nums = [1,2,3,4,5], m = 2
Output: 9
```

**Example 3:**

```
Input: nums = [1,4,4], m = 3
Output: 4
```

 

**Constraints:**

- `1 <= nums.length <= 1000`
- `0 <= nums[i] <= 106`
- `1 <= m <= min(50, nums.length)`

本题等价于1011

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        int left = 0;
        int right = 1;
        for(int i = 0; i < nums.length; i++) {
            left = Math.max(left, nums[i]);
            right += nums[i];
        }
        while(left < right) {
            int mid = left + (right - left) / 2;
            if(f(nums, mid) == m) {
                right = mid;
            }
            else if(f(nums, mid) < m) {
                right = mid;
            }
            else if(f(nums, mid) > m) {
                left = mid + 1;
            }
        }
        return left;
    }
    private int f(int[] nums, int x) {
        int m = 0;
        for(int i = 0; i < nums.length;) {
            int sum = x;
            while(i < nums.length) {
                if(sum < nums[i]) break;
                else sum -= nums[i];
                i++;
            }
            m++;
        }
        return m;
    }
}
```

