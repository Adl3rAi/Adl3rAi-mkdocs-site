# Freedom Trail

## 514. Freedom Trail

In the video game Fallout 4, the quest **"Road to Freedom"** requires players to reach a metal dial called the **"Freedom Trail Ring"** and use the dial to spell a specific keyword to open the door.

Given a string `ring` that represents the code engraved on the outer ring and another string `key` that represents the keyword that needs to be spelled, return *the minimum number of steps to spell all the characters in the keyword*.

Initially, the first character of the ring is aligned at the `"12:00"` direction. You should spell all the characters in `key` one by one by rotating `ring` clockwise or anticlockwise to make each character of the string key aligned at the `"12:00"` direction and then by pressing the center button.

At the stage of rotating the ring to spell the key character `key[i]`:

1. You can rotate the ring clockwise or anticlockwise by one place, which counts as **one step**. The final purpose of the rotation is to align one of `ring`'s characters at the `"12:00"` direction, where this character must equal `key[i]`.
2. If the character `key[i]` has been aligned at the `"12:00"` direction, press the center button to spell, which also counts as **one step**. After the pressing, you could begin to spell the next character in the key (next stage). Otherwise, you have finished all the spelling.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/10/22/ring.jpg)

```
Input: ring = "godding", key = "gd"
Output: 4
Explanation:
For the first key character 'g', since it is already in place, we just need 1 step to spell this character. 
For the second key character 'd', we need to rotate the ring "godding" anticlockwise by two steps to make it become "ddinggo".
Also, we need 1 more step for spelling.
So the final output is 4.
```

**Example 2:**

```
Input: ring = "godding", key = "godding"
Output: 13
```

 

**Constraints:**

- `1 <= ring.length, key.length <= 100`
- `ring` and `key` consist of only lower case English letters.
- It is guaranteed that `key` could always be spelled by rotating `ring`.

本例中，

状态为<u>**当前需要输入的字符**</u>`key[j]`和<u>**当前圆盘指针的位置**</u>`ring[i]`

那么`int dp(String ring, int i, String key, int j)`函数的定义为

当圆盘指针指向`ring[i]`时，输入字符串`key[j..]`至少需要`dp(ring,i,key,j)`次操作。则需要`return dp(ring,0,key,0)`

```java
int dp(String ring, int i, String key, int j) {
  // base case 完成输入时
  if(j == key.length()) return 0;
}
```

```java
class Solution {
  HashMap<Character, List<Integer>> charToIndex = new HashMap<>();
  int[][] memo;
  int findRotateSteps(String ring, String key) {
    int m = ring.length();
    int n = key.length();
    memo = new int[m][n];
    for(int i = 0; i < ring.length(); i++) {
      char c = ring.charAt(i);
      if(!charToIndex.containsKey(c)) {
        charToIndex.put(c, new LinkedList<>());
      }
      charToIndex.get(c).add(i);
    }
    return dp(ring, 0, key, 0);
  }
  
  int dp(String ring, int i, String key, int j) {
    if(j == key.length()) return 0;
    if(memo[i][j] != 0) return memo[i][j];
    int n = ring.length();
    int res = Integer.MAX_VALUE;
    for(int k : charToIndex.get(key.charAt(j))) {
      int delta = Math.abs(k-i);
      delta = Math.min(delta, n - delta);
      int subProblem = dp(ring, k, key, j+1);
      res = Math.min(res, 1+delta+subProblem);
    }
    memo[i][j] = res;
    return res;
  }
}
```

