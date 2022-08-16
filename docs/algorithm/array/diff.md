# Difference Array

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟠      | [1109. Corporate Fight Bookings](https://leetcode.com/problems/corporate-flight-bookings/) |[1109. Corporate Fight Bookings](#1109-corporate-flight-bookings)      |
|     🟠      | [1094. Car Pooling](https://leetcode.com/problems/car-pooling/) |[1094. Car Pooling](#1094-car-pooling)      |



前缀和数组PreSum Array应用于原始数组`nums`元素不会改变的情况下

```java
class PrefixSum {
  private int[] prefix;
  public PrefixSum(int[] nums) {
    prefix = new int[nums.length+1];
    for(int i = 1; i < prefix.length; i++) {
      prefix[i] = prefix[i-1] + nums[i-1];
      // prefix[i]表示nums[0...,i-1]的和
    }
  }
  public int query(int i, int j) {
    return prefix[j+1] - prefix[i];
  }
}
```

而差分数组的适用场景是频繁对原始数组的某个区间的元素进行增减

```java
int[] diff = new int[nums.length];
diff[0] = nums[0];
for(int i = 1; i < nums.length; i++) {
  diff[i] = nums[i] - nums[i-1];
}
```

根据`diff`差分数组可以反推出原始数组`nums`

```java
int[] res = new int[diff.length];
res[0] = diff[0];
for(int i = 1; i < diff.length; i++) {
  res[i] = res[i-1] + diff[i];
}
```

如果需要快速进行区间增减，例如需要对区间`nums[i..j]`的元素全部`+3`，那么只需要`diff[i] += 3`，然后再让`diff[j+1] -= 3`

```java
class Difference {
  private int[] diff;
  public Difference(int[] nums) {
    assert nums.length > 0;
    diff = new int[nums.length];
    diff[0] = nums[0];
    for(int i = 1; i < nums.length; i++) {
      diff[i] = nums[i] - nums[i-1];
    }
  }
  public void increment(int i, int j, int val) {
    diff[i] += val;
    if(j+1 < diff.length) {
      diff[j+1] -= val;
    }
  }
  public int[] result() {
    int[] res = new int[diff.length];
    res[0] = diff[0];
    for(int i = 1; i < diff.length; i++) {
      res[i] = res[i-1] + diff[i];
    }
    return res;
  }
}
```

## 1109. Corporate Flight Bookings

There are `n` flights that are labeled from `1` to `n`.

You are given an array of flight bookings `bookings`, where `bookings[i] = [firsti, lasti, seatsi]` represents a booking for flights `firsti` through `lasti` (**inclusive**) with `seatsi` seats reserved for **each flight** in the range.

Return *an array* `answer` *of length* `n`*, where* `answer[i]` *is the total number of seats reserved for flight* `i`.

 

**Example 1:**

```
Input: bookings = [[1,2,10],[2,3,20],[2,5,25]], n = 5
Output: [10,55,45,25,25]
Explanation:
Flight labels:        1   2   3   4   5
Booking 1 reserved:  10  10
Booking 2 reserved:      20  20
Booking 3 reserved:      25  25  25  25
Total seats:         10  55  45  25  25
Hence, answer = [10,55,45,25,25]
```

**Example 2:**

```
Input: bookings = [[1,2,10],[2,2,15]], n = 2
Output: [10,25]
Explanation:
Flight labels:        1   2
Booking 1 reserved:  10  10
Booking 2 reserved:      15
Total seats:         10  25
Hence, answer = [10,25]
```

 

**Constraints:**

- `1 <= n <= 2 * 104`
- `1 <= bookings.length <= 2 * 104`
- `bookings[i].length == 3`
- `1 <= firsti <= lasti <= n`
- `1 <= seatsi <= 104`

```java
class Solution {
    public int[] corpFlightBookings(int[][] bookings, int n) {
        int[] nums = new int[n];
        Difference df = new Difference(nums);
        for(int[] booking : bookings) {
            int i = booking[0] - 1;
            int j = booking[1] - 1;
            int val = booking[2];
            df.increment(i,j,val);
        }
        return df.result();
    }
}
class Difference {
    private int[] diff;
    public Difference(int[] nums) {
        assert nums.length > 0;
        diff = new int[nums.length];
        diff[0] = nums[0];
        for(int i = 1; i < nums.length; i++) {
            diff[i] = nums[i] - nums[i-1];
        }
    }
    public void increment(int i, int j, int val) {
        diff[i] += val;
        if(j+1 < diff.length) {
            diff[j+1] -= val;
        }
    }
    public int[] result() {
        int[] res = new int[diff.length];
        res[0] = diff[0];
        for(int i = 1; i < diff.length; i++) {
            res[i] = res[i-1] + diff[i];
        }
        return res;
    }
}
```

## 1094. Car Pooling

There is a car with `capacity` empty seats. The vehicle only drives east (i.e., it cannot turn around and drive west).

You are given the integer `capacity` and an array `trips` where `trips[i] = [numPassengersi, fromi, toi]` indicates that the `ith` trip has `numPassengersi` passengers and the locations to pick them up and drop them off are `fromi` and `toi` respectively. The locations are given as the number of kilometers due east from the car's initial location.

Return `true` *if it is possible to pick up and drop off all passengers for all the given trips, or* `false` *otherwise*.

 

**Example 1:**

```
Input: trips = [[2,1,5],[3,3,7]], capacity = 4
Output: false
```

**Example 2:**

```
Input: trips = [[2,1,5],[3,3,7]], capacity = 5
Output: true
```

 

**Constraints:**

- `1 <= trips.length <= 1000`
- `trips[i].length == 3`
- `1 <= numPassengersi <= 100`
- `0 <= fromi < toi <= 1000`
- `1 <= capacity <= 105`

```java
class Solution {
    public boolean carPooling(int[][] trips, int capacity) {
        int[] nums = new int[1001];
        Difference df = new Difference(nums);
        for(int[] trip : trips) {
            int val = trip[0];
            int i = trip[1];
            int j = trip[2] - 1;
            df.increment(i,j,val);
        }
        int[] res = df.result();
        for(int i = 0; i < res.length; i++) {
            if(capacity < res[i]) {
                return false;
            }
        }
        return true;
    }
}
class Difference {
    private int[] diff;
    public Difference(int[] nums) {
        assert nums.length > 0;
        diff = new int[nums.length];
        diff[0] = nums[0];
        for(int i = 1; i < nums.length; i++) {
            diff[i] = nums[i] - nums[i-1];
        }
    }
    public void increment(int i, int j, int val) {
        diff[i] += val;
        if(j+1 < diff.length) {
            diff[j+1] -= val;
        }
    }
    public int[] result() {
        int[] res = new int[diff.length];
        res[0] = diff[0];
        for(int i = 1; i < diff.length; i++) {
            res[i] = res[i-1] + diff[i];
        }
        return res;
    }
}
```

