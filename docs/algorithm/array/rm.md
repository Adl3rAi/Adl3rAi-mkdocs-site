# Remove Duplicate Elements with `Stack`

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | ---- |
|     🟠      | [316. Remove Duplicate Letters](https://leetcode.com/problems/remove-duplicate-letters/) |[316. Remove Duplicate Letters](#316-remove-duplicate-letters)      |

## 316. Remove Duplicate Letters

Given a string `s`, remove duplicate letters so that every letter appears once and only once. You must make sure your result is **the smallest in lexicographical order** among all possible results.

 

**Example 1:**

```
Input: s = "bcabc"
Output: "abc"
```

**Example 2:**

```
Input: s = "cbacdcbc"
Output: "acdb"
```

 

**Constraints:**

- `1 <= s.length <= 104`
- `s` consists of lowercase English letters.

 

**Note:** This question is the same as 1081: https://leetcode.com/problems/smallest-subsequence-of-distinct-characters/

```java
class Solution {
    public String smallestSubsequence(String s) {
        Stack<Character> stk = new Stack<>();
        boolean[] inStack = new boolean[256];
        int[] count = new int[256];
        for(int i = 0; i < s.length(); i++) {
            count[s.charAt(i)]++;
        }
        for(char c : s.toCharArray()) {
            count[c]--;
            if(inStack[c]) continue;
            while(!stk.isEmpty() && stk.peek() > c) {
                if(count[stk.peek()] == 0) {
                    break;
                }
                inStack[stk.pop()] = false;
            }
            stk.push(c);
            inStack[c] = true;
        }
        
        StringBuilder sb = new StringBuilder();
        while(!stk.isEmpty()) {
            sb.append(stk.pop());
        }
        return sb.reverse().toString();
    }
}
```

