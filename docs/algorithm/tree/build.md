# Binary Tree Build

| Difficulty |                           LeetCode                           |                             Note                             |
| :--------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     🟠      | [654. Maximum Binary Tree](https://leetcode.com/problems/maximum-binary-tree/) | [654. Maximum Binary Tree](#654-maximum-binary-tree) |
|     🟠      | [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/) | [105. Construct Binary Tree from Preorder and Inorder Traversal](#105-construct-binary-tree-from-preorder-and-inorder-traversal) |
|     🟠      | [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/) | [106. Construct Binary Tree from Inorder and Postorder Traversal](#106-construct-binary-tree-from-inorder-and-postorder-traversal) |
|     🟠      | [889. Construct Binary Tree from Preorder and Postorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-postorder-traversal/) | [889. Construct Binary Tree from Preorder and Postorder Traversal](#889-construct-binary-tree-from-preorder-and-postorder-traversal) |

## 654. Maximum Binary Tree

You are given an integer array `nums` with no duplicates. A **maximum binary tree** can be built recursively from `nums` using the following algorithm:

1. Create a root node whose value is the maximum value in `nums`.
2. Recursively build the left subtree on the **subarray prefix** to the **left** of the maximum value.
3. Recursively build the right subtree on the **subarray suffix** to the **right** of the maximum value.

Return *the **maximum binary tree** built from* `nums`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/12/24/tree1.jpg)

```
Input: nums = [3,2,1,6,0,5]
Output: [6,3,5,null,2,0,null,null,1]
Explanation: The recursive calls are as follow:
- The largest value in [3,2,1,6,0,5] is 6. Left prefix is [3,2,1] and right suffix is [0,5].
    - The largest value in [3,2,1] is 3. Left prefix is [] and right suffix is [2,1].
        - Empty array, so no child.
        - The largest value in [2,1] is 2. Left prefix is [] and right suffix is [1].
            - Empty array, so no child.
            - Only one element, so child is a node with value 1.
    - The largest value in [0,5] is 5. Left prefix is [0] and right suffix is [].
        - Only one element, so child is a node with value 0.
        - Empty array, so no child.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/12/24/tree2.jpg)

```
Input: nums = [3,2,1]
Output: [3,null,2,null,1]
```

 

**Constraints:**

- `1 <= nums.length <= 1000`
- `0 <= nums[i] <= 1000`
- All integers in `nums` are **unique**.

```cpp
class Solution {
  TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
    return construct(nums, 0, nums.size()-1);
  }
  TreeNode* construct(vector<int>& nums, int lo, int hi) {
    if(lo > hi) return nullptr;
    int index = -1;
    int maxVal = INT_MIN;
    for(int i = 0; i <= hi; i++) {
      if(maxVal < nums[i]) {
        index = i;
        maxVal = nums[i];
      }
    }
    TreeNode* root = new TreeNode();
    root->val = maxVal;
    root->left = construct(nums, lo, index-1);
    root->right = construct(nums, index+1, hi);
    return root;
  }
}
```

## 105. Construct Binary Tree from Preorder and Inorder Traversal

Given two integer arrays `preorder` and `inorder` where `preorder` is the preorder traversal of a binary tree and `inorder` is the inorder traversal of the same tree, construct and return *the binary tree*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/tree.jpg)

```
Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]
```

**Example 2:**

```
Input: preorder = [-1], inorder = [-1]
Output: [-1]
```

 

**Constraints:**

- `1 <= preorder.length <= 3000`
- `inorder.length == preorder.length`
- `-3000 <= preorder[i], inorder[i] <= 3000`
- `preorder` and `inorder` consist of **unique** values.
- Each value of `inorder` also appears in `preorder`.
- `preorder` is **guaranteed** to be the preorder traversal of the tree.
- `inorder` is **guaranteed** to be the inorder traversal of the tree.

```cpp
class Solution {
  public:
  	TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
      return build(preorder, 0, preorder.size()-1, inorder, 0, inorder.size()-1);
    }
  	TreeNode* build(vector<int>& preorder, int preStart, int preEnd, vector<int>& inorder, int inStart, int inEnd) {
      if(preStart > preEnd) {
        return nullptr;
      }
      int rootVal = preorder[preStart];
      int index = 0;
      for(int i = inStart; i <= inEnd; i++) {
        if(inorder[i] == rootVal) {
          index = i;
          break;
        }
      }
      TreeNode* root = new TreeNode();
      root->val = rootVal;
      int leftSize = index - inStart;
      root->left = build(preorder, preStart+1, preStart+leftSize, inorder, inStart, index-1);
      root->right = build(preorder, preStart+leftSize+1, preEnd, inorder, index+1, inEnd);
      return root;
    }
}
```

## 106. Construct Binary Tree from Inorder and Postorder Traversal

Given two integer arrays `inorder` and `postorder` where `inorder` is the inorder traversal of a binary tree and `postorder` is the postorder traversal of the same tree, construct and return *the binary tree*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/tree.jpg)

```
Input: inorder = [9,3,15,20,7], postorder = [9,15,7,20,3]
Output: [3,9,20,null,null,15,7]
```

**Example 2:**

```
Input: inorder = [-1], postorder = [-1]
Output: [-1]
```

 

**Constraints:**

- `1 <= inorder.length <= 3000`
- `postorder.length == inorder.length`
- `-3000 <= inorder[i], postorder[i] <= 3000`
- `inorder` and `postorder` consist of **unique** values.
- Each value of `postorder` also appears in `inorder`.
- `inorder` is **guaranteed** to be the inorder traversal of the tree.
- `postorder` is **guaranteed** to be the postorder traversal of the tree.

```cpp
class Solution {
  public:
  	TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
      
    }
  	TreeNode* build(vector<int>& inorder, int inStart, int inEnd, vector<int>& postorder, int postStart, int postEnd) {
      if(inStart > inEnd) return nullptr;
      int rootVal = postorder[postEnd];
      int index = 0;
      for(int i = 0; i < inorder.size(); i++) {
        if(inorder[i] == rootVal) {
          index = i;
          break;
        }
      }
      int leftSize = index - inStart;
      TreeNode* root = new TreeNode();
      root->val = rootVal;
      root->left = build(inorder, inStart, index-1, postorder, postStart, postStart+leftSize-1);
      root->right = build(inorder, index+1, inEnd, postorder, postStart+leftSize, postEnd-1);
      return root;
    }
}
```

## 889. Construct Binary Tree from Preorder and Postorder Traversal

Given two integer arrays, `preorder` and `postorder` where `preorder` is the preorder traversal of a binary tree of **distinct** values and `postorder` is the postorder traversal of the same tree, reconstruct and return *the binary tree*.

If there exist multiple answers, you can **return any** of them.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/07/24/lc-prepost.jpg)

```
Input: preorder = [1,2,4,5,3,6,7], postorder = [4,5,2,6,7,3,1]
Output: [1,2,3,4,5,6,7]
```

**Example 2:**

```
Input: preorder = [1], postorder = [1]
Output: [1]
```

 

**Constraints:**

- `1 <= preorder.length <= 30`
- `1 <= preorder[i] <= preorder.length`
- All the values of `preorder` are **unique**.
- `postorder.length == preorder.length`
- `1 <= postorder[i] <= postorder.length`
- All the values of `postorder` are **unique**.
- It is guaranteed that `preorder` and `postorder` are the preorder traversal and postorder traversal of the same binary tree.

```cpp
class Solution {
public:
    TreeNode* constructFromPrePost(vector<int>& preorder, vector<int>& postorder) {
        return build(preorder, 0, preorder.size()-1, postorder, 0, postorder.size()-1);
    }
    TreeNode* build(vector<int>& preorder, int prestart, int preend, vector<int>& postorder, int poststart, int postend) {
        if(prestart > preend) return nullptr;
        if(prestart == preend) {
            return new TreeNode(preorder[prestart]);
        }
        int rootVal = preorder[prestart];
        int leftRootVal = preorder[prestart+1];
        int index;
        for(int i = 0; i < postorder.size(); i++) {
            if(postorder[i] == leftRootVal) {
                index = i;
                break;
            }
        }
        int leftSize = index - poststart + 1;
        TreeNode* root = new TreeNode(rootVal);
        root->left = build(preorder, prestart + 1, prestart + leftSize, postorder, poststart, index);
        root->right = build(preorder, prestart + leftSize + 1, preend, postorder, index + 1, postend - 1);
        return root;
    }
};
```

