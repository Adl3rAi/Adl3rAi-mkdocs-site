# Minimum Path Sum

## 64. Minimum Path Sum

Given a `m x n` `grid` filled with non-negative numbers, find a path from top left to bottom right, which minimizes the sum of all numbers along its path.

**Note:** You can only move either down or right at any point in time.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/05/minpath.jpg)

```
Input: grid = [[1,3,1],[1,5,1],[4,2,1]]
Output: 7
Explanation: Because the path 1 → 3 → 1 → 1 → 1 minimizes the sum.
```

**Example 2:**

```
Input: grid = [[1,2,3],[4,5,6]]
Output: 12
```

 

**Constraints:**

- `m == grid.length`
- `n == grid[i].length`
- `1 <= m, n <= 200`
- `0 <= grid[i][j] <= 100`

```java
class Solution {
  public int minPathSum(int[][] grid) {
    int m = grid.length;
    int n = grid[0].length;
    int[][] dp = new int[m][n];
    int sum1 = 0;
    int sum2 = 0;
    for(int i = 0; i < m; i++) {
      sum1 += grid[i][0];
      dp[i][0] = sum1;
    }
    for(int j = 0; j < n; j++) {
      sum2 += grid[0][j];
      dp[0][j] = sum2;
    }
    for(int i = 1; i < m; i++) {
      for(int j = 1; j < n; j++) {
        dp[i][j] = Math.min(dp[i-1][j], dp[i][j-1]) + grid[i][j];
      }
    }
    return dp[m-1][n-1];
  }
}
```

## 174. Dungeon Game

The demons had captured the princess and imprisoned her in **the bottom-right corner** of a `dungeon`. The `dungeon` consists of `m x n` rooms laid out in a 2D grid. Our valiant knight was initially positioned in **the top-left room** and must fight his way through `dungeon` to rescue the princess.

The knight has an initial health point represented by a positive integer. If at any point his health point drops to `0` or below, he dies immediately.

Some of the rooms are guarded by demons (represented by negative integers), so the knight loses health upon entering these rooms; other rooms are either empty (represented as 0) or contain magic orbs that increase the knight's health (represented by positive integers).

To reach the princess as quickly as possible, the knight decides to move only **rightward** or **downward** in each step.

Return *the knight's minimum initial health so that he can rescue the princess*.

**Note** that any room can contain threats or power-ups, even the first room the knight enters and the bottom-right room where the princess is imprisoned.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/13/dungeon-grid-1.jpg)

```
Input: dungeon = [[-2,-3,3],[-5,-10,1],[10,30,-5]]
Output: 7
Explanation: The initial health of the knight must be at least 7 if he follows the optimal path: RIGHT-> RIGHT -> DOWN -> DOWN.
```

**Example 2:**

```
Input: dungeon = [[0]]
Output: 1
```

 

**Constraints:**

- `m == dungeon.length`
- `n == dungeon[i].length`
- `1 <= m, n <= 200`
- `-1000 <= dungeon[i][j] <= 1000`

```java
class Solution {
  public int[][] memo;
  public int calculateMinimumHP(int[][] dungeon) {
    int m = dungeon.length;
    int n = dungeon[0].length;
    memo = new int[m][n];
    for(int[] row : memo) {
      Arrays.fill(row, -1);
    }
    return dp(dungeon, 0, 0);
  }
  public int dp(int[][] grid, int i, int j) {
    int m = grid.length;
    int n = grid[0].length;
    if(i == m-1 && j == n-1) {
      return grid[i][j] >= 0 ? 1 : -grid[i][j] + 1;
    }
    if(i == m || j == n) {
      return Integer.MAX_VALUE;
    }
    if(memo[i][j] != -1) return memo[i][j];
    int res = Math.min(
    	dp(grid, i, j+1), dp(grid, i+1, j)
    ) - grid[i][j];
    memo[i][j] = res <= 0 ? 1 : res;
  }
}
```

