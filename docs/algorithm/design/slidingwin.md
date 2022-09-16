# O(1) Maximum in a Sliding Window

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🔴      | [239. Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/) |[239. Sliding Window Maximum](#239-sliding-window-maximum)      |

 ## 239. Sliding Window Maximum

You are given an array of integers `nums`, there is a sliding window of size `k` which is moving from the very left of the array to the very right. You can only see the `k` numbers in the window. Each time the sliding window moves right by one position.

Return *the max sliding window*.

 

**Example 1:**

```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation: 
Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**Example 2:**

```
Input: nums = [1], k = 1
Output: [1]
```

 

**Constraints:**

- `1 <= nums.length <= 105`
- `-104 <= nums[i] <= 104`
- `1 <= k <= nums.length`

```java
class Solution {
    class MonotonicQueue {
        LinkedList<Integer> q = new LinkedList<>();
        public void push(int n) {
            while(!q.isEmpty() && q.getLast() < n) {
                q.pollLast();
            }
            q.addLast(n);
        }
        
        public int max() {
            return q.getFirst();
        }
        
        public void pop(int n) {
            if (n == q.getFirst()) {
                q.pollFirst();
            }
        }
    }
    
    public int[] maxSlidingWindow(int[] nums, int k) {
        MonotonicQueue window = new MonotonicQueue();
        List<Integer> res = new ArrayList<>();
        for(int i = 0; i < nums.length; i++) {
            if (i < k - 1) {
                window.push(nums[i]);
            }
            else {
                window.push(nums[i]);
                res.add(window.max());
                window.pop(nums[i - k + 1]);
            }
        }
        int[] arr = new int[res.size()];
        for(int i = 0; i < res.size(); i++) {
            arr[i] = res.get(i);
        }
        return arr;
    }
}
```

通过在`push()`阶段就进行了大小比较，使得`Queue`为递减顺序排列，最大值为表头元素。