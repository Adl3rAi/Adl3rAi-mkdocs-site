# 2D-Array Traverse

| Difficulty |                           LeetCode                           | Note |
| :--------: | :----------------------------------------------------------: | :--: |
|     🟠      | [48. Rotate Image](https://leetcode.com/problems/rotate-image/) |[48. Rotate Image](#48-rotate-image)      |
|     🟠      | [54. Spiral Matrix](https://leetcode.com/problems/spiral-matrix/) |[54. Spiral Matrix](#54-spiral-matrix)      |
|     🟠      | [59. Spiral Matrix II](https://leetcode.com/problems/spiral-matrix-ii/) |[59. Spiral Matrix II](#59-spiral-matrix-ii)      |

## 48. Rotate Image

You are given an `n x n` 2D `matrix` representing an image, rotate the image by **90** degrees (clockwise).

You have to rotate the image [**in-place**](https://en.wikipedia.org/wiki/In-place_algorithm), which means you have to modify the input 2D matrix directly. **DO NOT** allocate another 2D matrix and do the rotation.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/08/28/mat1.jpg)

```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [[7,4,1],[8,5,2],[9,6,3]]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/08/28/mat2.jpg)

```
Input: matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]
Output: [[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]
```

 

**Constraints:**

- `n == matrix.length == matrix[i].length`
- `1 <= n <= 20`
- `-1000 <= matrix[i][j] <= 1000`

```java
class Solution {
    public void rotate(int[][] matrix) {
        int n = matrix.length;
        for(int i = 0; i < n; i++) {
            for(int j = 0; j <= i; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        for(int[] row : matrix) {
            reverse(row);
        }
    }
    private void reverse(int[] arr) {
        int i = 0;
        int j = arr.length - 1;
        while(j > i) {
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            i++;
            j--;
        }
    }
}
```

## 54. Spiral Matrix

Given an `m x n` `matrix`, return *all elements of the* `matrix` *in spiral order*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/13/spiral1.jpg)

```
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [1,2,3,6,9,8,7,4,5]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/11/13/spiral.jpg)

```
Input: matrix = [[1,2,3,4],[5,6,7,8],[9,10,11,12]]
Output: [1,2,3,4,8,12,11,10,9,5,6,7]
```

 

**Constraints:**

- `m == matrix.length`
- `n == matrix[i].length`
- `1 <= m, n <= 10`
- `-100 <= matrix[i][j] <= 100`

```java
class Solution {
    public List<Integer> spiralOrder(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;
        int upper = 0;
        int lower = m - 1;
        int left = 0;
        int right = n - 1;
        List<Integer> res = new LinkedList<>();
        while(res.size() < m * n) {
            if(upper <= lower) {
                for(int j = left; j <= right; j++) {
                    res.add(matrix[upper][j]);
                }
                upper++;
            }
            if(left <= right) {
                for(int i = upper; i <= lower; i++) {
                    res.add(matrix[i][right]);
                }
                right--;
            }
            if(upper <= lower) {
                for(int j = right; j >= left; j--) {
                    res.add(matrix[lower][j]);
                }
                lower--;
            }
            if(left <= right) {
                for(int i = lower; i >= upper; i--) {
                    res.add(matrix[i][left]);
                }
                left++;
            }
        }
        return res;
    }
}
```

## 59. Spiral Matrix II

Given a positive integer `n`, generate an `n x n` `matrix` filled with elements from `1` to `n2` in spiral order.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/13/spiraln.jpg)

```
Input: n = 3
Output: [[1,2,3],[8,9,4],[7,6,5]]
```

**Example 2:**

```
Input: n = 1
Output: [[1]]
```

 

**Constraints:**

- `1 <= n <= 20`

```java
class Solution {
    public int[][] generateMatrix(int n) {
        int[][] matrix = new int[n][n];
        int upper = 0;
        int lower = n-1;
        int left = 0;
        int right = n-1;
        int num = 1;
        while(num <= n * n) {
            if(upper <= lower) {
                for(int j = left; j <= right; j++) {
                    matrix[upper][j] = num++;
                }
                upper++;
            }
            if(left <= right) {
                for(int i = upper; i <= lower; i++) {
                    matrix[i][right] = num++;
                }
                right--;
            }
            if(upper <= lower) {
                for(int j = right; j >= left; j--) {
                    matrix[lower][j] = num++;
                }
                lower--;
            }
            if(left <= right) {
                for(int i = lower; i >= upper; i--) {
                    matrix[i][left] = num++;
                }
                left++;
            }
        }
        return matrix;
    }
}
```

