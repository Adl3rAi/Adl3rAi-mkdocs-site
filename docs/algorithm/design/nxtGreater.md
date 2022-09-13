# Next Greater Element with `Stack`

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | ---- |
|     🟢      | [496. Next Greater Element I](https://leetcode.com/problems/next-greater-element-i/) |[496. Next Greater Element I](#496-next-greater-element-i)      |
|     🟠      | [739. Daily Temperatures](https://leetcode.com/problems/daily-temperatures/) |[739. Daily Temperatures](#739-daily-temperatures)      |
|     🟠      | [503. Next Greater Element II](https://leetcode.com/problems/next-greater-element-ii/) |[503. Next Greater Element II](#503-next-greater-element-ii)      |

## 496. Next Greater Element I

The **next greater element** of some element `x` in an array is the **first greater** element that is **to the right** of `x` in the same array.

You are given two **distinct 0-indexed** integer arrays `nums1` and `nums2`, where `nums1` is a subset of `nums2`.

For each `0 <= i < nums1.length`, find the index `j` such that `nums1[i] == nums2[j]` and determine the **next greater element** of `nums2[j]` in `nums2`. If there is no next greater element, then the answer for this query is `-1`.

Return *an array* `ans` *of length* `nums1.length` *such that* `ans[i]` *is the **next greater element** as described above.*

 

**Example 1:**

```
Input: nums1 = [4,1,2], nums2 = [1,3,4,2]
Output: [-1,3,-1]
Explanation: The next greater element for each value of nums1 is as follows:
- 4 is underlined in nums2 = [1,3,4,2]. There is no next greater element, so the answer is -1.
- 1 is underlined in nums2 = [1,3,4,2]. The next greater element is 3.
- 2 is underlined in nums2 = [1,3,4,2]. There is no next greater element, so the answer is -1.
```

**Example 2:**

```
Input: nums1 = [2,4], nums2 = [1,2,3,4]
Output: [3,-1]
Explanation: The next greater element for each value of nums1 is as follows:
- 2 is underlined in nums2 = [1,2,3,4]. The next greater element is 3.
- 4 is underlined in nums2 = [1,2,3,4]. There is no next greater element, so the answer is -1.
```

 

**Constraints:**

- `1 <= nums1.length <= nums2.length <= 1000`
- `0 <= nums1[i], nums2[i] <= 104`
- All integers in `nums1` and `nums2` are **unique**.
- All the integers of `nums1` also appear in `nums2`.

等价于，`nums2 = [1,3,2,4]`求得`res = [3,4,4,-1]`即可

思路是`nums2`将元素从后往前添加入一个`Stack`，当`Stack`非空时，比较当前`nums2[i]`和`Stack.peek()`(之前已经添加到`Stack`当中的)，如果`nums2[i] >= Stack.peek()`则弹出顶部元素，如果`Stack`依然非空，则此时顶部元素即位`nums2[i]`之后的第一个大于其的元素，即可将其添加到`res`数组中，如果`Stack`为空，则为-1，比较完`nums2[i]`和`Stack`中的元素之后，可讲`nums2[i]`添加入`Stack`中，成为下次循环中的被比较对象之一。

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        int[] greater = result(nums2);
        HashMap<Integer, Integer> greaterMap = new HashMap<>();
        for(int i = 0; i < nums2.length; i++) {
            greaterMap.put(nums2[i], greater[i]);
        }
        int[] res = new int[nums1.length];
        for(int i = 0; i < nums1.length; i++) {
            res[i] = greaterMap.get(nums1[i]);
        }
        return res;
    }
    
    private int[] result(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        Stack<Integer> stack = new Stack<>();
        for(int i = n - 1; i >= 0; i--) {
            while(!stack.isEmpty() && nums[i] >= stack.peek()) {
                stack.pop();
            }
            res[i] = stack.isEmpty() ? -1 : stack.peek();
            stack.push(nums[i]);
        }
        return res;
    }
}
```

## 739. Daily Temperatures

Given an array of integers `temperatures` represents the daily temperatures, return *an array* `answer` *such that* `answer[i]` *is the number of days you have to wait after the* `ith` *day to get a warmer temperature*. If there is no future day for which this is possible, keep `answer[i] == 0` instead.

 

**Example 1:**

```
Input: temperatures = [73,74,75,71,69,72,76,73]
Output: [1,1,4,2,1,1,0,0]
```

**Example 2:**

```
Input: temperatures = [30,40,50,60]
Output: [1,1,1,0]
```

**Example 3:**

```
Input: temperatures = [30,60,90]
Output: [1,1,0]
```

 

**Constraints:**

- `1 <= temperatures.length <= 105`
- `30 <= temperatures[i] <= 100`

```java
class Solution {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] res = new int[n];
        Stack<Integer> s = new Stack<>();
        for(int i = n - 1; i >= 0; i--) {
            while(!s.empty() && temperatures[s.peek()] <= temperatures[i]) {
                s.pop();
            }
            res[i] = s.isEmpty() ? 0 : s.peek() - i;
            s.push(i);
        }
        return res;
    }
}
```

## 503. Next Greater Element II

Given a circular integer array `nums` (i.e., the next element of `nums[nums.length - 1]` is `nums[0]`), return *the **next greater number** for every element in* `nums`.

The **next greater number** of a number `x` is the first greater number to its traversing-order next in the array, which means you could search circularly to find its next greater number. If it doesn't exist, return `-1` for this number.

 

**Example 1:**

```
Input: nums = [1,2,1]
Output: [2,-1,2]
Explanation: The first 1's next greater number is 2; 
The number 2 can't find next greater number. 
The second 1's next greater number needs to search circularly, which is also 2.
```

**Example 2:**

```
Input: nums = [1,2,3,4,3]
Output: [2,3,4,-1,4]
```

 

**Constraints:**

- `1 <= nums.length <= 104`
- `-109 <= nums[i] <= 109`

```java
class Solution {
    public int[] nextGreaterElements(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];
        Stack<Integer> s = new Stack<>();
        for (int i = 2 * n - 1; i >= 0; i--) {
            while(!s.isEmpty() && nums[i % n] >= s.peek()) {
                s.pop();
            }
            res[i % n] = s.isEmpty() ? -1 : s.peek();
            s.push(nums[i % n]);
        }
        return res;
    }
}
```

