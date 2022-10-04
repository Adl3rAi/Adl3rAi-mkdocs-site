# Regular Expression Matching

| Difficulty |              LeetCode               | Note |
| :--------: | :---------------------------------: | :--: |
|     🔴      | [10. Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/) |[10. Regular Expression Matching](#10-regular-expression-matching)      |

## 10. Regular Expression Matching
Given an input string s and a pattern p, implement regular expression matching with support for '.' and '*' where:

'.' Matches any single character.​​​​
'*' Matches zero or more of the preceding element.
The matching should cover the entire input string (not partial).

 

Example 1:

```
Input: s = "aa", p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

Example 2:
```
Input: s = "aa", p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```

Example 3:
```
Input: s = "ab", p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
```

Constraints:

1 <= s.length <= 20
1 <= p.length <= 30
s contains only lowercase English letters.
p contains only lowercase English letters, '.', and '*'.
It is guaranteed for each appearance of the character '*', there will be a previous valid character to match.

一旦涉及到两个字符串的穷举，理应条件反射式地想到动态规划。

两个指针`i`,`j`分别在`s`和`p`上移动，如果最后两个指针都能移动到字符串的末尾，那么匹配成功，反之则失败。

如果不考虑`*`，只需要查看`s[i]`和`p[j]`是否匹配即可

```java
bool isMatch(string s, string p) {
  int i = 0;
  int j = 0;
  while (i < s.size() && j < p.size()) {
    if(s[i] == p[j] || p[j] == '.') {
      i++;
      j++;
    } else {
      return false;
    }
  }
  return i == j
}
```

当`p[j+1]`为`*`时，分情况讨论：

#1. 如果`s[i] == p[j]`，有两种情况：

​	#1.1 `p[j]`匹配多个字符，`s="aaa", p="a*"`，那么`p[0]`会通过`*`匹配3个字符`"a"`

​	#1.2 `s="aa", p="a*aa"`，只会匹配0次

#2. 如果`s[i] != p[j]`，`p[j]`只能匹配0次，然后看下一个字符是否能和`s[i]`匹配，`s="aa", p="b*aa"`

```java
if (s[i] == p[j] || p[j] == '.') {
  if(j < p.size() - 1 && p[j+1] == '*') {
    // 有*通配符，可以匹配0次或多次
  } else {
    // 无通配符，只能匹配1次
    i++;
    j++;
  }
} else {
  // 不匹配 
  if (j < p.size() - 1 && p[j+1] == '*') {
    // 有*通配符，只能匹配0次
  } else {
    // 无*通配符，无法进行匹配
    return false;
  }
}
```

```java
class Solution {
    public int memo[][];
    public boolean isMatch(String s, String p) {
        int m = s.length();
        int n = p.length();
        memo = new int[m][n];
        for(int i = 0; i < m; i++) {
            Arrays.fill(memo[i],-1);
        }
        if(dp(s,0,p,0) == 0) {
            return true;
        } else {
            return false;
        }
    }
    public int dp(String s, int i, String p, int j) {
        int m = s.length();
        int n = p.length();
        // base case
        if(j == n) {
            if(i == m) {
                return 0; // true
            } else {
                return 1; // false
            }
        }
        if(i == m) {
            if((n - j) % 2 == 1) {
                return 1;
            }
            for(; j+1 < n; j += 2) {
                if(p.charAt(j+1) != '*') {
                    return 1;
                }
            }
            return 0;
        }
        if(memo[i][j] != -1) return memo[i][j];
        int res = 1;
        if(s.charAt(i) == p.charAt(j) || p.charAt(j) == '.') {
            if(j + 1 < n && p.charAt(j+1) == '*') {
                res = Math.min(dp(s,i,p,j+2), dp(s,i+1,p,j));
            } else {
                res = dp(s,i+1,p,j+1);
            }
        } else {
            if(j + 1 < n && p.charAt(j+1) == '*') {
                res = dp(s,i,p,j+2);
            } else {
                res = 1;
            }
        }
        memo[i][j] = res;
        return res;
    }
}
```

