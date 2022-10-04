# Stock Problem

对于股票交易问题，状态(即变量)有三种：一是天数，二是允许交易的最大次数，三是当前的持有状态(`1`表示持有，`0`表示未持有)，那么三维数组的遍历为

```python
dp[i][k][0/1]
0 <= i <= n-1 # n为天数
1 <= k <= K # K为交易数的上限
for 0 <= i < n:
  for 1 <= k <= K:
    for s in {0,1}:
      dp[i][k][s] = max(buy, sell, rest)
```

最终答案一定是`dp[n-1][K][0]`

状态转移方程分为两种情况

```python
dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1]+prices[i])
```

```python
dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0]-prices[i])
```

```python
# base case
dp[-1][...][0] = dp[...][0][0] = 0
dp[-1][...][1] = dp[...][0][1] = -infinity #不可能持有股票
```

## 121. Best Time to Buy and Sell Stock

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

You want to maximize your profit by choosing a **single day** to buy one stock and choosing a **different day in the future** to sell that stock.

Return *the maximum profit you can achieve from this transaction*. If you cannot achieve any profit, return `0`.

 

**Example 1:**

```
Input: prices = [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.
```

**Example 2:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transactions are done and the max profit = 0.
```

 

**Constraints:**

- `1 <= prices.length <= 105`
- `0 <= prices[i] <= 104`

本例限定条件是最多只能进行一次交易，即`k==1`

先写出状态转移方程

```java
dp[i][1][0] = Math.max(dp[i-1][1][0], dp[i-1][1][1]+prices[i]);
dp[i][1][1] = Math.max(dp[i-1][1][1], dp[i-1][0][0] - prices[i]);
// k = 0 dp[i-1][0][0] = 0
// 化简后
dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
dp[i][1] = Math.max(dp[i-1][1], -prices[i])
```

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        int[][] dp = new int[n][2];
        for(int i = 0; i < n; i++) {
            if(i == 0) {
                // base case
                dp[i][0] = 0;
                dp[i][1] = -prices[i];
                continue;
            }
            dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1]+prices[i]);
            dp[i][1] = Math.max(dp[i-1][1], -prices[i]);
        }
        return dp[n-1][0];
    }
}
```

## 122. Best Time to Buy and Sell Stock II

You are given an integer array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

On each day, you may decide to buy and/or sell the stock. You can only hold **at most one** share of the stock at any time. However, you can buy it then immediately sell it on the **same day**.

Find and return *the **maximum** profit you can achieve*.

 

**Example 1:**

```
Input: prices = [7,1,5,3,6,4]
Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 3 (price = 5), profit = 5-1 = 4.
Then buy on day 4 (price = 3) and sell on day 5 (price = 6), profit = 6-3 = 3.
Total profit is 4 + 3 = 7.
```

**Example 2:**

```
Input: prices = [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
Total profit is 4.
```

**Example 3:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: There is no way to make a positive profit, so we never buy the stock to achieve the maximum profit of 0.
```

 

**Constraints:**

- `1 <= prices.length <= 3 * 104`
- `0 <= prices[i] <= 104`

同理，先写出状态转移方程

```java
dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1]+prices[i]);
dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i]);
// 本例中，k无上限 k == k -1
dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] + prices[i]);
dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0] - prices[i]);
```

```java
class Solution {
    public int maxProfit(int[] prices) {
        int n = prices.length;
        int[][] dp = new int[n][2];
        for(int i = 0; i < n; i++) {
            if(i == 0) {
                // base case
                dp[i][0] = 0;
                dp[i][1] = -prices[i];
                continue;
            }
            dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1]+prices[i]);
            dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0]-prices[i]);
        }
        return dp[n-1][0];
    }
}
```

## 309. Best Time to Buy and Sell Stock with Cooldown

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

Find the maximum profit you can achieve. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times) with the following restrictions:

- After you sell your stock, you cannot buy stock on the next day (i.e., cooldown one day).

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

 

**Example 1:**

```
Input: prices = [1,2,3,0,2]
Output: 3
Explanation: transactions = [buy, sell, cooldown, buy, sell]
```

**Example 2:**

```
Input: prices = [1]
Output: 0
```

 

**Constraints:**

- `1 <= prices.length <= 5000`
- `0 <= prices[i] <= 1000`

```java
class Solution {
  public int maxProfit(int[] prices) {
    int n = prices.length();
    int[][] dp = new int[n][2];
    /*
    dp[i][k][1] = max(dp[i-1][k][1],dp[i-2][k-1][0]-prices[i])
    dp[i][k][0] = max(dp[i-1][k][0],dp[i-1][k][1]+prices[i])
    k -> infinity
    dp[i][1] = max(dp[i-1][1], dp[i-2][0]-prices[i])
    dp[i][0] = max(dp[i-1][0], dp[i-1][1]+prices[i])
    i == 0
    dp[0][1] = max(dp[-1][1],dp[-2][0]-prices[0]) = -prices[0]
    dp[0][0] = 0
    i == 1
    dp[1][1] = max(dp[0][1], dp[-1][0]-prices[i]) = max(dp[0][1], -prices[i])
    */
    for(int i = 0; i < n; i++) {
      if(i-1 == -1) {
        dp[i][0] = 0;
        dp[i][1] = -prices[i];
        continue;
      }
      if(i-2 == -1) {
        dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1]+prices[i]);
        dp[i][1] = Math.max(dp[i-1][1],-prices[i]);
        continue;
      }
      dp[i][0] = Math.max(dp[i-1][0],dp[i-1][1]+prices[i]);
      dp[i][1] = Math.max(dp[i-1][1],dp[i-2][0]-prices[i]);
    }
    return dp[n-1][0];
  }
}
```

## 714. Best Time to Buy and Sell Stock with Transaction Fee

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day, and an integer `fee` representing a transaction fee.

Find the maximum profit you can achieve. You may complete as many transactions as you like, but you need to pay the transaction fee for each transaction.

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

 

**Example 1:**

```
Input: prices = [1,3,2,8,4,9], fee = 2
Output: 8
Explanation: The maximum profit can be achieved by:
- Buying at prices[0] = 1
- Selling at prices[3] = 8
- Buying at prices[4] = 4
- Selling at prices[5] = 9
The total profit is ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```

**Example 2:**

```
Input: prices = [1,3,7,5,10,3], fee = 3
Output: 6
```

 

**Constraints:**

- `1 <= prices.length <= 5 * 104`
- `1 <= prices[i] < 5 * 104`
- `0 <= fee < 5 * 104`

```java
class Solution {
  public maxProfit(int[] prices, int fee) {
    int n = prices.length;
    int[][] dp = new int[n][2];
    for(int i = 0; i < n; i++) {
      if(i == 0) {
        dp[i][0] = 0;
        dp[i][1] = -prices[i] - fee;
        continue;
      }
      dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1]+prices[i]);
      dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0]-prices[i]-fee);
    }
    return dp[n-1][0];
  }
}
```

## 123. Best Time to Buy and Sell Stock III

You are given an array `prices` where `prices[i]` is the price of a given stock on the `ith` day.

Find the maximum profit you can achieve. You may complete **at most two transactions**.

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

 

**Example 1:**

```
Input: prices = [3,3,5,0,0,3,1,4]
Output: 6
Explanation: Buy on day 4 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3.
Then buy on day 7 (price = 1) and sell on day 8 (price = 4), profit = 4-1 = 3.
```

**Example 2:**

```
Input: prices = [1,2,3,4,5]
Output: 4
Explanation: Buy on day 1 (price = 1) and sell on day 5 (price = 5), profit = 5-1 = 4.
Note that you cannot buy on day 1, buy on day 2 and sell them later, as you are engaging multiple transactions at the same time. You must sell before buying again.
```

**Example 3:**

```
Input: prices = [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.
```

 

**Constraints:**

- `1 <= prices.length <= 105`
- `0 <= prices[i] <= 105`

```java
class Solution {
  public int maxProfit(int[] prices) {
    int n = prices.length;
    int max_k = 2;
    for(int i = 0; i < n; i++) {
      for(int k = max_k; k >= 1; k--) {
        // base case
        if(i == 0) {
          dp[i][k][0] = 0;
          dp[i][k][1] = -prices[i];
          continue;
        }
        dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1]+prices[i]);
        dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-1][k-1][0]-prices[i]);
      }
    }
    return dp[n-1][max_k][0];
  }
}
```

## 188. Best Time to Buy and Sell Stock IV

You are given an integer array `prices` where `prices[i]` is the price of a given stock on the `ith` day, and an integer `k`.

Find the maximum profit you can achieve. You may complete at most `k` transactions.

**Note:** You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

 

**Example 1:**

```
Input: k = 2, prices = [2,4,1]
Output: 2
Explanation: Buy on day 1 (price = 2) and sell on day 2 (price = 4), profit = 4-2 = 2.
```

**Example 2:**

```
Input: k = 2, prices = [3,2,6,5,0,3]
Output: 7
Explanation: Buy on day 2 (price = 2) and sell on day 3 (price = 6), profit = 6-2 = 4. Then buy on day 5 (price = 0) and sell on day 6 (price = 3), profit = 3-0 = 3.
```

 

**Constraints:**

- `1 <= k <= 100`
- `1 <= prices.length <= 1000`
- `0 <= prices[i] <= 1000`

```java
class Solution {
    public int maxProfit(int ks, int[] prices) {
        /*
        dp[i][k][0] = max(dp[i-1][k][0],dp[i-1][k][1]+prices[i])
        dp[i][k][1] = max(dp[i-1][k][1],dp[i-1][k-1][0]-prices[i])
        
        dp[0][k][0]=m(dp[-1][k][0],dp[-1][k][1]+prices[i]) = 0
        dp[1][k][1]=m(dp[0][k][1],dp[0][k-1][0]-prices[i]) = -prices[i]
        
        dp[i][0][0]=m(dp[i-1][0][0],dp[i-1][0][1]+prices[i]) = 0
        dp[i][0][1]=m(dp[i-1][0][1],dp[i-1][-1][0]-prices[i]) = -infinity
        
        dp[-1][_][0] = 0
        dp[_][0][0] = 0
        dp[-1][_][1] = -infinity
        dp[_][0][1] = -infinity
        */
        int n = prices.length;
        int max_k = ks;
        if(n <= 0) return 0;
        if(max_k > n / 2) return maxProfit_inf(prices);
        int[][][] dp = new int[n][max_k+1][2];
        for(int i = 0; i < n; i++) {
            dp[i][0][1] = Integer.MIN_VALUE;
            dp[i][0][0] = 0;
        }
        for(int i = 0; i < n; i++) {
            for(int k = max_k; k >= 1; k--) {
                if(i == 0) {
                    dp[i][k][0] = 0;
                    dp[i][k][1] = -prices[i];
                    continue;
                }
                dp[i][k][0] = Math.max(dp[i-1][k][0], dp[i-1][k][1]+prices[i]);
                dp[i][k][1] = Math.max(dp[i-1][k][1], dp[i-1][k-1][0]-prices[i]);
            }
        }
        return dp[n-1][max_k][0];
    }
    public int maxProfit_inf(int[] prices) {
        int n = prices.length;
        int[][] dp = new int[n][2];
        for(int i = 0; i < n; i++) {
            if(i == 0) {
                // base case
                dp[i][0] = 0;
                dp[i][1] = -prices[i];
                continue;
            }
            dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1]+prices[i]);
            dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0]-prices[i]);
        }
        return dp[n-1][0];
    }
}
```

