# Distance of Two Strings

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🔴      | [72. Edit Distance](https://leetcode.com/problems/edit-distance/) |[72. Edit Distance](#72-edit-distance)      |

## 72. Edit Distance

Given two strings `word1` and `word2`, return *the minimum number of operations required to convert `word1` to `word2`*.

You have the following three operations permitted on a word:

- Insert a character
- Delete a character
- Replace a character

 

**Example 1:**

```
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
horse -> rorse (replace 'h' with 'r')
rorse -> rose (remove 'r')
rose -> ros (remove 'e')
```

**Example 2:**

```
Input: word1 = "intention", word2 = "execution"
Output: 5
Explanation: 
intention -> inention (remove 't')
inention -> enention (replace 'i' with 'e')
enention -> exention (replace 'n' with 'x')
exention -> exection (replace 'n' with 'c')
exection -> execution (insert 'u')
```

 

**Constraints:**

- `0 <= word1.length, word2.length <= 500`
- `word1` and `word2` consist of lowercase English letters.

```java
class Solution {
  public int minDistance(String word1, String word2) {
    int m = word1.length();
    int n = word2.length();
    int[][] dp = new dp[m+1][n+1];
    // dp[i][j]表示s1[0..,i-1]和s2[0..,j-1]
    for(int i = 1; i <= m; i++) {
      dp[i][0] = i;
    }
    for(int j = 1; j <= n; j++) {
      dp[0][j] = j;
    }
    for(int i = 1; i <= m; i++) {
      for(int j = 1; j <= n; j++) {
        if(word1.charAt(i-1) == word2.charAt(j-1)) {
          dp[i][j] = dp[i-1][j-1];
        }
        else {
          dp[i][j] = min(
          	dp[i-1][j-1] + 1,
            dp[i-1][j] + 1,
            dp[i][j-1] + 1
          );
        }
      }
    }
    return dp[m][n];
  }
  int min(int a, int b, int c) {
    return Math.min(a, Math.min(b,c));
  }
}
```

