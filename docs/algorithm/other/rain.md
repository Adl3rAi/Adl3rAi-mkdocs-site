# Trapping Rain Water

## 42. Trapping Rain Water

Given `n` non-negative integers representing an elevation map where the width of each bar is `1`, compute how much water it can trap after raining.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/10/22/rainwatertrap.png)

```
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this case, 6 units of rain water (blue section) are being trapped.
```

**Example 2:**

```
Input: height = [4,2,0,3,2,5]
Output: 9
```

 

**Constraints:**

- `n == height.length`
- `1 <= n <= 2 * 104`
- `0 <= height[i] <= 105`

```java
class Solution {
  public int trap(int[] height) {
    int left = 0;
    int right = height.length - 1;
    int res = 0;
    int lMax = 0;
    int rMax = 0;
    while(left < right) {
      lMax = Math.max(lMax, height[left]);
      rMax = Math.max(rMax, height[right]);
      if(lMax < rMax) {
        res += lMax - height[left];
        left++;
      } else {
        res += rMax - height[right];
        right--;
      }
    }
    return res;
  }
}
```

```java
lMax = Math.max(lMax, height[left]);
// lMax表示[0..left]的最高点
rMax = Math.max(rMax, height[right]);
// rMax表示[right..n-1]的最高点
```

对于`height[left]`而言，只需要右侧存在高于`lMax`的高点，即可计算水量

```java
res += lMax - height[left];
```

## 11. Container With Most Water

You are given an integer array `height` of length `n`. There are `n` vertical lines drawn such that the two endpoints of the `ith` line are `(i, 0)` and `(i, height[i])`.

Find two lines that together with the x-axis form a container, such that the container contains the most water.

Return *the maximum amount of water a container can store*.

**Notice** that you may not slant the container.

 

**Example 1:**

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

```
Input: height = [1,8,6,2,5,4,8,3,7]
Output: 49
Explanation: The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.
```

**Example 2:**

```
Input: height = [1,1]
Output: 1
```

 

**Constraints:**

- `n == height.length`
- `2 <= n <= 105`
- `0 <= height[i] <= 104`

```java
class Solution {
  public int maxArea(int[] height) {
    int left = 0;
    int right = height.length - 1;
    int res = 0;
    while(left < right) {
      int area = Math.min(height[left],height[right]) * (right - left);
      res = Math.max(res, area);
      if(height[left] < height[right]) left++;
      else right--;
    }
    return res;
  }
}
```

