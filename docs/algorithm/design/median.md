# Median from Data Stream

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🔴      | [295. Find Median from Data Stream](https://leetcode.com/problems/find-median-from-data-stream/) |[295. Find Median from Data Stream](#295-find-median-from-data-stream)      |

## 295. Find Median from Data Stream

The **median** is the middle value in an ordered integer list. If the size of the list is even, there is no middle value and the median is the mean of the two middle values.

- For example, for `arr = [2,3,4]`, the median is `3`.
- For example, for `arr = [2,3]`, the median is `(2 + 3) / 2 = 2.5`.

Implement the MedianFinder class:

- `MedianFinder()` initializes the `MedianFinder` object.
- `void addNum(int num)` adds the integer `num` from the data stream to the data structure.
- `double findMedian()` returns the median of all elements so far. Answers within `10-5` of the actual answer will be accepted.

 

**Example 1:**

```
Input
["MedianFinder", "addNum", "addNum", "findMedian", "addNum", "findMedian"]
[[], [1], [2], [], [3], []]
Output
[null, null, null, 1.5, null, 2.0]

Explanation
MedianFinder medianFinder = new MedianFinder();
medianFinder.addNum(1);    // arr = [1]
medianFinder.addNum(2);    // arr = [1, 2]
medianFinder.findMedian(); // return 1.5 (i.e., (1 + 2) / 2)
medianFinder.addNum(3);    // arr[1, 2, 3]
medianFinder.findMedian(); // return 2.0
```

 

**Constraints:**

- `-105 <= num <= 105`
- There will be at least one element in the data structure before calling `findMedian`.
- At most `5 * 104` calls will be made to `addNum` and `findMedian`.

 

**Follow up:**

- If all integer numbers from the stream are in the range `[0, 100]`, how would you optimize your solution?
- If `99%` of all integer numbers from the stream are in the range `[0, 100]`, how would you optimize your solution?

对于数据流的中位数，思路依然是“对半分，取中间”。按照数字大小，从小到大排列，并且分成两堆。当数字总数为奇数时，其中一堆比另一堆多一个数，那么多出来的数就是中位数；当数字总数为偶数时，两堆数量一样多，则取(小)堆中的最大值，(大)堆中的最小值，取平均即可。

考虑到要自动排序，其中小堆应从大到小，大堆应从小到大，以方便取中间的数，自然采用的数据结构是`PriorityQueue<Integer>`

向该数据结构添加数字的时候，<u>原则上是向元素更少的堆中添加，并且小堆中的每一个数字都小于大堆中的数字</u>。如果是向小堆添加，则先添加数，之后弹出最大数到大堆之中；如果是向大堆添加，则先添加数，之后弹出最小数到小堆之中。其中，最值一定是在`PriorityQueue<Integer>.peek()`

```java
class MedianFinder {
    PriorityQueue<Integer> large;
    PriorityQueue<Integer> small;
    
    public MedianFinder() {
        large = new PriorityQueue<>();
        small = new PriorityQueue<>((a,b) -> {
            return b - a;
        });
    }
    
    public void addNum(int num) {
        if(small.size() >= large.size()) {
            small.offer(num);
            large.offer(small.poll());
        }
        else {
            large.offer(num);
            small.offer(large.poll());
        }
    }
    
    public double findMedian() {
        if(large.size() < small.size()) {
            return small.peek();
        }
        else if(large.size() > small.size()) {
            return large.peek();
        }
        return (large.peek() + small.peek()) / 2.0;
    }
}
```

