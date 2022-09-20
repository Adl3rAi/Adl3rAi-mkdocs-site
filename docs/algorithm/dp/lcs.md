# Longest Common Subsequence(LCS)

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟠      | [1143. Longest Common Subsequence](https://leetcode.com/problems/longest-common-subsequence/submissions/) |[1143. Longest Common Subsequence](#1143-longest-common-subsequence)      |
|     🟠      | [583. Delete Operation for Two Strings](https://leetcode.com/problems/delete-operation-for-two-strings/) | [583. Delete Operation for Two Strings](#583-delete-operation-for-two-strings)     |
|     🟠      | [712. Minimum ACSII Delete Sum for Two Strings](https://leetcode.com/problems/minimum-ascii-delete-sum-for-two-strings/) |[712. Minimum ACSII Delete Sum for Two Strings](#712-minimum-ascii-delete-sum-for-strings)      |

## 1143. Longest Common Subsequence

Given two strings `text1` and `text2`, return *the length of their longest **common subsequence**.* If there is no **common subsequence**, return `0`.

A **subsequence** of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters.

- For example, `"ace"` is a subsequence of `"abcde"`.

A **common subsequence** of two strings is a subsequence that is common to both strings.

 

**Example 1:**

```
Input: text1 = "abcde", text2 = "ace" 
Output: 3  
Explanation: The longest common subsequence is "ace" and its length is 3.
```

**Example 2:**

```
Input: text1 = "abc", text2 = "abc"
Output: 3
Explanation: The longest common subsequence is "abc" and its length is 3.
```

**Example 3:**

```
Input: text1 = "abc", text2 = "def"
Output: 0
Explanation: There is no such common subsequence, so the result is 0.
```

 

**Constraints:**

- `1 <= text1.length, text2.length <= 1000`
- `text1` and `text2` consist of only lowercase English characters.

```java
class Solution {
  int[][] memo;
  public int LongestCommonSubsequence(String text1, String text2) {
    int m = text1.length();
    int n = text2.length();
    memo = new int[m][n];
    for(int[] row : memo) {
      Arrays.fill(row, -1);
    }
    return dp(s1, 0, s2, 0);
  }
  // dp == s1[i,...]&s2[j,...]的lcs
  public int dp(String s1, int i, String s2, int j) {
    // base case: s1[0,..,s1.length()-1]和s2[0,..,s2.length()-1]则dp(s1, s1.length(), s2, s2.length())时lcs一定是0
    if(i == s1.length() || j == s2.length()) return 0;
    if(memo[i][j] != -1) return memo[i][j];
    if(s1.charAt(i) == s2.charAt(j)) {
      memo[i][j] = 1 + dp(s1, i+1, s2, j+1);
    }
    else {
      memo[i][j] = Math.max(dp(s1, i+1, s2, j), dp(s1, i, s2, j+1));
    }
    return memo[i][j];
  }
}
```

```java
class Solution {
  public int longestCommonSubsequence(String text1, String text2) {
    int m = text1.length();
    int n = text2.length();
    // dp[i][j]指的是s1[0..,i-1]&s2[0..,j-1]
    int[][] dp = new int[m+1][n+1];
    for(int i = 1; i <= m; i++) {
      for(int j = 1; j <= n; j++) {
        if(text1.charAt(i) == text2.charAt(j)) {
          dp[i][j] = 1 + dp[i-1][j-1];
        }
        else {
          dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
        }
      }
    }
    return dp[m][n];
  }
}
```

## 583. Delete Operation for Two Strings

Given two strings `word1` and `word2`, return *the minimum number of **steps** required to make* `word1` *and* `word2` *the same*.

In one **step**, you can delete exactly one character in either string.

 

**Example 1:**

```
Input: word1 = "sea", word2 = "eat"
Output: 2
Explanation: You need one step to make "sea" to "ea" and another step to make "eat" to "ea".
```

**Example 2:**

```
Input: word1 = "leetcode", word2 = "etco"
Output: 4
```

 

**Constraints:**

- `1 <= word1.length, word2.length <= 500`
- `word1` and `word2` consist of only lowercase English letters.

```java
class Solution {
  public int minDistance(String word1, String word2) {
    int m = word1.length();
    int n = word2.length();
    return m - lcs(word1, word2) + n - lcs(word1, word2);
  }
  private int lcs(String word1, String word2) {
    int m = s1.length();
    int n = s2.length();
    int[][] dp = new int[m+1][n+1];
    for(int i = 1; i <= m; i++) {
      for(int j = 1; j <= n; j++) {
        if(s1.charAt(i-1) == s2.charAt(j-1)) {
          dp[i][j] = 1 + dp[i-1][j-1];
        }
        else {
          dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
        }
      }
    }
    return dp[m][n];
  }
}
```

## 712. Minimum ASCII Delete Sum for Strings

Given two strings `s1` and `s2`, return *the lowest **ASCII** sum of deleted characters to make two strings equal*.

 

**Example 1:**

```
Input: s1 = "sea", s2 = "eat"
Output: 231
Explanation: Deleting "s" from "sea" adds the ASCII value of "s" (115) to the sum.
Deleting "t" from "eat" adds 116 to the sum.
At the end, both strings are equal, and 115 + 116 = 231 is the minimum sum possible to achieve this.
```

**Example 2:**

```
Input: s1 = "delete", s2 = "leet"
Output: 403
Explanation: Deleting "dee" from "delete" to turn the string into "let",
adds 100[d] + 101[e] + 101[e] to the sum.
Deleting "e" from "leet" adds 101[e] to the sum.
At the end, both strings are equal to "let", and the answer is 100+101+101+101 = 403.
If instead we turned both strings into "lee" or "eet", we would get answers of 433 or 417, which are higher.
```

 

**Constraints:**

- `1 <= s1.length, s2.length <= 1000`
- `s1` and `s2` consist of lowercase English letters.

```java
class Solution {
  int memo[][];
  public int minimumDeleteSum(String s1, String s2) {
    
  }
  public int dp(String s1, int i, String s2, int j) {
    int res = 0;
    if(i == s1.length()) {
      for(; j < s2.length(); j++) {
        res += s2.charAt(j);
      }
      return res;
    }
    if(j == s2.length()) {
      for(; i < s1.length(); i++) {
        res += s1.charAt(i);
      }
      return res;
    }
    if(memo[i][j] != -1) return memo[i][j];
    if(s1.charAt(i) == s2.charAt(j)) {
      memo[i][j] = dp(s1, i+1, s2, j+1);
    }
    else {
      memo[i][j] = Math.min(
        s1.charAt(i) + dp(s1, i+1, s2, j),
        s2.charAt(j) + dp(s1, i, s2, j+1)
      );
    }
    return memo[i][j];
  }
}
```

