# Frame

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟢      | [509. Fibonacci Number](https://leetcode.com/problems/fibonacci-number/) |[509. Fibonacci Number](#509-fibonacci-number)      |
|     🟠      | [322. Coin Change](https://leetcode.com/problems/coin-change/) |[322. Coin Change](#322-coin-change)      |



动态规划的一般形式为求最值，例如<u>**最长递增子序列**</u>，<u>**最小编辑距离**</u>等等。主要的方法就是穷举，穷举需要正确的状态转移方程，并且要通过备忘录或者表格来避免穷举过程中的重叠子问题。

明确base case -> 明确变量 -> 定义`dp`数组/函数的定义，关键就在于`dp`数组/函数的定义

```python
# top-down function
def dp(arr1, arr2, ...):
  for option in options:
    res = 最值(result, dp(arr1, arr2, ...))
  return res
```

```python
# bottom-up dp-array
# base case
dp[0][0][...] = base case
for arr1 in [arr1s]:
  for arr2 in [arr2s]:
    for ...
    	dp[arr1][arr2][...] = 最值(option1, option2...)
```

## 509. Fibonacci Number

The **Fibonacci numbers**, commonly denoted `F(n)` form a sequence, called the **Fibonacci sequence**, such that each number is the sum of the two preceding ones, starting from `0` and `1`. That is,

```
F(0) = 0, F(1) = 1
F(n) = F(n - 1) + F(n - 2), for n > 1.
```

Given `n`, calculate `F(n)`.

 

**Example 1:**

```
Input: n = 2
Output: 1
Explanation: F(2) = F(1) + F(0) = 1 + 0 = 1.
```

**Example 2:**

```
Input: n = 3
Output: 2
Explanation: F(3) = F(2) + F(1) = 1 + 1 = 2.
```

**Example 3:**

```
Input: n = 4
Output: 3
Explanation: F(4) = F(3) + F(2) = 2 + 1 = 3.
```

 

**Constraints:**

- `0 <= n <= 30`

```java
class Solution {
  public int fib(int n) {
    if(n == 0) return 0;
    int[] dp = new int[n+1];
    // base case
    dp[0] = 0;
    dp[1] = 1;
    for(int i = 2; i < n; i++) {
      dp[i] = dp[i-1] + dp[i-2];
    }
    return dp[n];
  }
}
```

例如上述斐波那契数列题例，

`dp[0] = 0; dp[1] = 1;`为base case；

变量仅为`int fib(int n)`中的`int n`；

状态转移方程为`dp[i] = dp[i-1] + dp[i-2];`

```java
int fib(int n) {
  int[] memo = new int[n+1];
  return helper(memo,n);
}

int helper(int[] memo, int n) {
  // base case
  if(n == 0 || n == 1) return n;
  if(memo[n] != 0) return memo[n];
  memo[n] = helper(memo, n-1) + helper(memo, n-2);
  return memo[n];
}
```

## 322. Coin Change

You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money.

Return *the fewest number of coins that you need to make up that amount*. If that amount of money cannot be made up by any combination of the coins, return `-1`.

You may assume that you have an infinite number of each kind of coin.

 

**Example 1:**

```
Input: coins = [1,2,5], amount = 11
Output: 3
Explanation: 11 = 5 + 5 + 1
```

**Example 2:**

```
Input: coins = [2], amount = 3
Output: -1
```

**Example 3:**

```
Input: coins = [1], amount = 0
Output: 0
```

 

**Constraints:**

- `1 <= coins.length <= 12`
- `1 <= coins[i] <= 231 - 1`
- `0 <= amount <= 104`

```java
class Solution {
  public int coinChange(int[] coins, int amount) {
    // dp[i]指当amount == i时，return最少的硬币数
    int[] dp = new int[amount+1];
    Arrays.fill(dp, amount+1);
    dp[0] = 0;
    for(int i = 0; i < dp.length; i++) {
      for(int coin : coins) {
        if(coin > i) {
          dp[i] = Math.min(dp[i], 1+dp[i-coin]);
        }
      }
    }
    return dp[amount] == amount + 1 ? -1 : dp[amount];
  }
}
```

