# Rabin-Karp Algorithm

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟠      | [187. Repeated DNA Sequences](https://leetcode.com/problems/repeated-dna-sequences/) |[187. Repeated DNA Sequences](#187-repeated-dna-sequences)      |
|     🟢      | [28. Implement strStr()](https://leetcode.com/problems/implement-strstr/) |[28. Implement strStr()](#28-implement-strstr)      |



## 187. Repeated DNA Sequences

The **DNA sequence** is composed of a series of nucleotides abbreviated as `'A'`, `'C'`, `'G'`, and `'T'`.

- For example, `"ACGAATTCCG"` is a **DNA sequence**.

When studying **DNA**, it is useful to identify repeated sequences within the DNA.

Given a string `s` that represents a **DNA sequence**, return all the **`10`-letter-long** sequences (substrings) that occur more than once in a DNA molecule. You may return the answer in **any order**.

 

**Example 1:**

```
Input: s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT"
Output: ["AAAAACCCCC","CCCCCAAAAA"]
```

**Example 2:**

```
Input: s = "AAAAAAAAAAAAA"
Output: ["AAAAAAAAAA"]
```

 

**Constraints:**

- `1 <= s.length <= 105`
- `s[i]` is either `'A'`, `'C'`, `'G'`, or `'T'`.

Sliding Window写法

```java
class Solution {
  public List<String> findRepeatedDnaSequences(String s) {
    HashSet<String> seen = new HashSet<>();
    HashSet<String> res = new HashSet<>();
    String window = "";
    int left = 0;
    int right = 0;
    int len = 10;
    while(right < s.length()) {
      char c = s.charAt(right);
      window += c;
      right++;
      if(right - left == len) {
        if(seen.contains(window)) {
          res.add(window);
        }
        else {
          seen.add(window);
        }
        window = window.substring(1);
        left++;
      }
    }
    List<String> finalRes = new LinkedList<>(res);
    return finalRes;
  }
}
```

```java
class Solution {
  List<String> findRepeatedDnaSequences(String s) {
    int[] nums = new int[s.length()];
    for(int i = 0; i < nums.length; i++) {
      switch(s.charAt(i)) {
        case 'A':
          nums[i] = 0;
          break;
        case 'G':
          nums[i] = 1;
         	break;
        case 'C':
          nums[i] = 2;
          break;
        case 'T':
          nums[i] = 3;
          break;
      }
    }
    HashSet<Integer> seen = new HashSet<>();
    HashSet<String> res = new HashSet<>();
    int L = 10;
    int R = 4;
    int RL = (int) Math.pow(R, L-1);
    int windowHash = 0;
    int left = 0;
    int right = 0;
    while(right < nums.length) {
      windowHash = R * windowHash + nums[right];
      right++;
      if(right - left == L) {
        if(seen.contains(windowHash)) {
          res.add(s.substring(left,right));
        }
        else {
          seen.add(windowHash);
        }
        windowHash = windowHash - nums[left] * RL;
        left++;
      }
    }
    return new LinkedList<>(res);
  }
}
```

**不要每次都去一个字符一个字符地比较子串和模式串，而是维护一个滑动窗口，运用滑动哈希算法一边滑动一边计算字符中的哈希值，使用哈希值去和模式串的哈希值比较，从而避免截取子串，以降低算法复杂度。**

以ASCII码为例，ASCII本质上由0~255共256个数字，分别对应英文字符和英文符号，一个长度为`L`的字符串，可以等价理解为一个`L`位的256进制数字，这个数字可以唯一标识这个字符串，可以作为哈希值。

```java
String txt;
String pat;

int L = pat.length();
int R = 256;
int RL = (int) Math.(R,L-1);
int windowHash = 0;
long patHash = 0;
for(int i = 0; i < pat.length(); i++) {
  patHash = R * patHash + pat.charAt(i);
}
int left = 0;
int right = 0;
while(right < txt.length()) {
  windowHash = R * windowHash + txt[right];
  right++;
  if(right - left == L) {
    if(patHash == windowHash) {
      return left;
    }
    windowHash = windowHash - txt[left] * RL;
    left++;
  }
}
return -1;
```

上述代码存在一个问题就是**整型溢出**

为将一个很大的数字映射到一个较小的范围内，采用**求模**的方法

但通过求模后的哈希值也无法和原始字符串一一对应，可能出现一对多的情况，即哈希冲突

在Rabin-Karp算法中，当`windowHash == patHash`时，使用暴力匹配算法检查一下窗口中的字符串和`pat`是否相同就可以避免哈希冲突

```java
int rabinKarp(String txt, String pat) {
  int L = pat.length();
  int R = 256;
  long Q = 1658598167;
  long RL = 1;
  for(int i = 1; i <= L-1; i++) {
    RL = (RL * R) % Q;
  }
  long patHash = 0;
  for(int i = 0; i < pat.length(); i++) {
    patHash = (R * patHash + pat.charAt(i)) % Q;
  }
  long windowHash = 0;
  int left = 0;
  int right = 0;
  while(right < txt.length()) {
    windowHash = ((R*windowHash) % Q + txt.charAt(right)) % Q;
    right++;
    if(right - left == L) {
      if(windowHash == patHash) {
        if(pat.equals(txt.substring(left,right))) {
          return left;
        }
      }
      windowHash = (windowHash - (txt.charAt(left)*RL)%Q + Q) % Q;
      left++;
    }
  }
  return -1;
}
```

## 28. Implement strStr()

mplement [strStr()](http://www.cplusplus.com/reference/cstring/strstr/).

Given two strings `needle` and `haystack`, return the index of the first occurrence of `needle` in `haystack`, or `-1` if `needle` is not part of `haystack`.

**Clarification:**

What should we return when `needle` is an empty string? This is a great question to ask during an interview.

For the purpose of this problem, we will return 0 when `needle` is an empty string. This is consistent to C's [strstr()](http://www.cplusplus.com/reference/cstring/strstr/) and Java's [indexOf()](https://docs.oracle.com/javase/7/docs/api/java/lang/String.html#indexOf(java.lang.String)).

 

**Example 1:**

```
Input: haystack = "hello", needle = "ll"
Output: 2
```

**Example 2:**

```
Input: haystack = "aaaaa", needle = "bba"
Output: -1
```

 

**Constraints:**

- `1 <= haystack.length, needle.length <= 104`
- `haystack` and `needle` consist of only lowercase English characters.

```java
class Solution {
    public int strStr(String haystack, String needle) {
        int l = needle.length();
        int r = 256;
        long q = 1658598167;
        long rl = 1;
        for(int i = 1; i <= l - 1; i++) {
            rl = (rl * r) % q;
        }
        long needleHash = 0;
        for(int i = 0; i < needle.length(); i++) {
            needleHash = (r * needleHash + 				needle.charAt(i)) % q;
        }
        long windowHash = 0;
        int left = 0;
        int right = 0;
        while(right < haystack.length()) {
            windowHash = ((r * windowHash) % q + haystack.charAt(right)) % q;
            right++;
            if(right - left == l) {
                if(windowHash == needleHash) {
                    if(needle.equals(haystack.substring(left,right))) {
                        return left;
                    }
                }
                windowHash = (windowHash - (haystack.charAt(left)*rl)%q+q)%q;
                left++;
            }
        }
        return -1;
    }
}
```

