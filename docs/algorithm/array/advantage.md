# Greedy Advantage Shuffle

| Difficulty |                           LeetCode                           |          Note           |
| :--------: | :----------------------------------------------------------: | :---------------------: |
|     🟠      | [870. Advantage Shuffle](https://leetcode.com/problems/advantage-shuffle/) | [870. Advanage Shuffle](#870-advantage-shuffle) |



给定两个长度相等的数组`nums1`和`nums2`，重新组织`nums1`中的元素位置，使得有尽可能多的`nums1[i]>nums2[i]`

```java
int n = nums1.length;
sort(nums1);
sort(nums2);
for(int i = n-1; i >= 0; i--) {
  if(nums1[i] > nums2[i]) {
    // comparable
  }
  else {
    // change to the weakest
  }
}
```

## 870. Advantage Shuffle

You are given two integer arrays `nums1` and `nums2` both of the same length. The **advantage** of `nums1` with respect to `nums2` is the number of indices `i` for which `nums1[i] > nums2[i]`.

Return *any permutation of* `nums1` *that maximizes its **advantage** with respect to* `nums2`.

 

**Example 1:**

```
Input: nums1 = [2,7,11,15], nums2 = [1,10,4,11]
Output: [2,11,7,15]
```

**Example 2:**

```
Input: nums1 = [12,24,8,32], nums2 = [13,25,32,11]
Output: [24,32,8,12]
```

 

**Constraints:**

- `1 <= nums1.length <= 105`
- `nums2.length == nums1.length`
- `0 <= nums1[i], nums2[i] <= 109`

```java
class Solution {
    public int[] advantageCount(int[] nums1, int[] nums2) {
        int n = nums1.length;
        PriorityQueue<int[]> maxpq = new PriorityQueue<>(
            (int[] pair1, int[] pair2) -> {
                return pair2[1] - pair1[1];
            }
        );
        for(int i = 0; i < n; i++) {
            maxpq.offer(new int[]{i, nums2[i]});
        }
        Arrays.sort(nums1);
        int left = 0;
        int right = n - 1;
        int[] res = new int[n];
        
        while(!maxpq.isEmpty()) {
            int[] pair = maxpq.poll();
            int i = pair[0];
            int maxVal = pair[1];
            if(maxVal < nums1[right]) {
                res[i] = nums1[right];
                right--;
            }
            else {
                res[i] = nums1[left];
                left++;
            }
        }
        return res;
    }
}
```

