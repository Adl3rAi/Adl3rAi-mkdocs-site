# Binary Search Tree Basics

| Difficulty |                           LeetCode                           |                             Note                             |
| :--------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     🟠      | [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/) | [230. Kth Smallest Element in a BST](#230-kth-smallest-element-in-a-bst) |
|     🟠      | [538. Convert BST to Greater Tree](https://leetcode.com/problems/convert-bst-to-greater-tree/) | [538. Convert BST to Greater Tree](#538-convert-bst-to-greater-tree) |
|     🟠      | [1038. Binary Search Tree to Greater Sum Tree](https://leetcode.com/problems/binary-search-tree-to-greater-sum-tree/) | [1038. Binary Search Tree to Greater Sum Tree](#1038-binary-search-tree-to-greater-sum-tree) |
|     🟠      | [98. Validate Binary Search Tree](https://leetcode.com/problems/validate-binary-search-tree/) | [98. Validate Binary Search Tree](#98-validate-binary-search-tree) |
|     🟠      | [450. Delete Node in a BST](https://leetcode.com/problems/delete-node-in-a-bst/) | [450. Delete Node in a BST](#450-delete-node-in-a-bst) |
|     🟢      | [700. Search in a Binary Search Tree](https://leetcode.com/problems/search-in-a-binary-search-tree/) | [700. Search in a Binary Search Tree](#700-search-in-a-binary-search-tree) |
|     🟠      | [701. Insert into a Binary Search Tree](https://leetcode.com/problems/insert-into-a-binary-search-tree/) | [701. Insert into a Binary Search Tree](#701-insert-into-a-binary-search-tree) |

### 

## 230. Kth Smallest Element in a BST

Given the `root` of a binary search tree, and an integer `k`, return *the* `kth` *smallest value (**1-indexed**) of all the values of the nodes in the tree*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/01/28/kthtree1.jpg)

```
Input: root = [3,1,4,null,2], k = 1
Output: 1
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/01/28/kthtree2.jpg)

```
Input: root = [5,3,6,2,4,null,null,1], k = 3
Output: 3
```

 

**Constraints:**

- The number of nodes in the tree is `n`.
- `1 <= k <= n <= 104`
- `0 <= Node.val <= 104`

**对于二叉搜索树(BST)而言，中序遍历是能够从小到大升序排列二叉树节点的，例如需要升序打印BST的节点值，如下：**

```cpp
void traverse(TreeNode* root) {
  if(root == nullptr) return;
  traverse(root->left);
  cout << root->val << " ";
  # 具体的操作取决于中序遍历的代码
  traverse(root->right);
}
```

对于本例，需要操作的不是打印`root->val`，而是记录当前的`root->val`在整棵搜索二叉树中的大小排名，则

```cpp
class Solution {
  int res = 0;
  int rank = 0;
  int kthSmallest(TreeNode* root, int k) {
    traverse(root, k);
    return res;
  }
  void traverse(TreeNode* root, int k) {
    if(root == nullptr) return;
    traverse(root->left, k);
    rank++;
    if(k == rank) {
      res = root->val;
      return;
    }
    traverse(root->right, k);
  }
};
```

## 538. Convert BST to Greater Tree

Given the `root` of a Binary Search Tree (BST), convert it to a Greater Tree such that every key of the original BST is changed to the original key plus the sum of all keys greater than the original key in BST.

As a reminder, a *binary search tree* is a tree that satisfies these constraints:

- The left subtree of a node contains only nodes with keys **less than** the node's key.
- The right subtree of a node contains only nodes with keys **greater than** the node's key.
- Both the left and right subtrees must also be binary search trees.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/05/02/tree.png)

```
Input: root = [4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
Output: [30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
```

**Example 2:**

```
Input: root = [0,null,1]
Output: [1,null,1]
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 104]`.
- `-104 <= Node.val <= 104`
- All the values in the tree are **unique**.
- `root` is guaranteed to be a valid binary search tree.

```cpp
class Solution {
  public:
  	TreeNode* convertBST(TreeNode* root) {
      traverse(root);
      return root;
    }
  	int sum = 0;
  	void traverse(TreeNode* root) {
      if(root == nullptr) return;
      traverse(root->right);
      sum += root->val;
      root->val = sum;
      traverse(root->left);
    }
};
```

## 1038. Binary Search Tree to Greater Sum Tree

Given the `root` of a Binary Search Tree (BST), convert it to a Greater Tree such that every key of the original BST is changed to the original key plus the sum of all keys greater than the original key in BST.

As a reminder, a *binary search tree* is a tree that satisfies these constraints:

- The left subtree of a node contains only nodes with keys **less than** the node's key.
- The right subtree of a node contains only nodes with keys **greater than** the node's key.
- Both the left and right subtrees must also be binary search trees.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/05/02/tree.png)

```
Input: root = [4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
Output: [30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
```

**Example 2:**

```
Input: root = [0,null,1]
Output: [1,null,1]
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[1, 100]`.
- `0 <= Node.val <= 100`
- All the values in the tree are **unique**.

```cpp
class Solution {
public:
    TreeNode* bstToGst(TreeNode* root) {
        traverse(root);
        return root;
    }
    int sum = 0;
    void traverse(TreeNode* root) {
        if(root == nullptr) return;
        traverse(root->right);
        sum += root->val;
        root->val = sum;
        traverse(root->left);
    }
};
```

## 98. Validate Binary Search Tree

Given the `root` of a binary tree, *determine if it is a valid binary search tree (BST)*.

A **valid BST** is defined as follows:

- The left subtree of a node contains only nodes with keys **less than** the node's key.
- The right subtree of a node contains only nodes with keys **greater than** the node's key.
- Both the left and right subtrees must also be binary search trees.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/12/01/tree1.jpg)

```
Input: root = [2,1,3]
Output: true
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/12/01/tree2.jpg)

```
Input: root = [5,1,4,null,null,3,6]
Output: false
Explanation: The root node's value is 5 but its right child's value is 4.
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[1, 104]`.
- `-231 <= Node.val <= 231 - 1`

```cpp
class Solution {
  bool isValidBST(TreeNode* root) {
    if(root == nullptr) return true;
    else if(isSmaller(root->left, root->val) && isGreater(root->right, root->val) && isValidBST(root->left) && isValidBST(root->right)) return true;
    else return false;
  }
  bool isSmaller(TreeNode* root, int data) {
    if(root == nullptr) return true;
    else if(root->val < data && isSmaller(root->left, data) && isSmaller(root->right, data)) return true;
    else return false;
  }
  bool isGreater(TreeNode* root, int data) {
    if(root == nullptr) return true;
    else if(root->val > data && isGreater(root->left, data) && isGreater(root->right, data)) return true;
    else return false;
  }
};
```

## 450. Delete Node in a BST

Given a root node reference of a BST and a key, delete the node with the given key in the BST. Return *the **root node reference** (possibly updated) of the BST*.

Basically, the deletion can be divided into two stages:

1. Search for a node to remove.
2. If the node is found, delete the node.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/09/04/del_node_1.jpg)

```
Input: root = [5,3,6,2,4,null,7], key = 3
Output: [5,4,6,2,null,null,7]
Explanation: Given key to delete is 3. So we find the node with value 3 and delete it.
One valid answer is [5,4,6,2,null,null,7], shown in the above BST.
Please notice that another valid answer is [5,2,6,null,4,null,7] and it's also accepted.
```

**Example 2:**

```
Input: root = [5,3,6,2,4,null,7], key = 0
Output: [5,3,6,2,4,null,7]
Explanation: The tree does not contain a node with value = 0.
```

**Example 3:**

```
Input: root = [], key = 0
Output: []
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 104]`.
- `-105 <= Node.val <= 105`
- Each node has a **unique** value.
- `root` is a valid binary search tree.
- `-105 <= key <= 105`

```cpp
class Solution {
public:
    TreeNode* deleteNode(TreeNode* root, int key) {
        if(root == nullptr) return root;
        else if(key < root->val) root->left = deleteNode(root->left, key);
        else if(key > root->val) root->right = deleteNode(root->right, key);
        else {
            if(root->left == nullptr && root->right == nullptr) {
                delete root;
                root = nullptr;
            }
            else if(root->left == nullptr && root->right != nullptr){
                TreeNode* temp = root;
                root = root->right;
                delete temp;
            }
            else if(root->left != nullptr && root->right == nullptr){
                TreeNode* temp = root;
                root = root->left;
                delete temp;
            }
            else {
                TreeNode* temp = findMin(root->right);
                root->val = temp->val;
                root->right = deleteNode(root->right, temp->val);
            }
        }
        return root;
    }
    TreeNode* findMin(TreeNode* root) {
        if(root == nullptr) return root;
        TreeNode* current = root;
        while(current->left != nullptr) {
            current = current->left;
        }
        return current;
    }
};
```

## 700. Search in a Binary Search Tree

You are given the `root` of a binary search tree (BST) and an integer `val`.

Find the node in the BST that the node's value equals `val` and return the subtree rooted with that node. If such a node does not exist, return `null`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/01/12/tree1.jpg)

```
Input: root = [4,2,7,1,3], val = 2
Output: [2,1,3]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/01/12/tree2.jpg)

```
Input: root = [4,2,7,1,3], val = 5
Output: []
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[1, 5000]`.
- `1 <= Node.val <= 107`
- `root` is a binary search tree.
- `1 <= val <= 107`

```cpp
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if(root == nullptr) {
            return nullptr;
        }
        else if(root->val < val) {
            return searchBST(root->right, val);
        }
        else if(root->val > val) {
            return searchBST(root->left, val);
        }
        return root;
    }
};
```

## 701. Insert into a Binary Search Tree

You are given the `root` node of a binary search tree (BST) and a `value` to insert into the tree. Return *the root node of the BST after the insertion*. It is **guaranteed** that the new value does not exist in the original BST.

**Notice** that there may exist multiple valid ways for the insertion, as long as the tree remains a BST after insertion. You can return **any of them**.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/05/insertbst.jpg)

```
Input: root = [4,2,7,1,3], val = 5
Output: [4,2,7,1,3,5]
Explanation: Another accepted tree is:
```

**Example 2:**

```
Input: root = [40,20,60,10,30,50,70], val = 25
Output: [40,20,60,10,30,50,70,null,null,25]
```

**Example 3:**

```
Input: root = [4,2,7,1,3,null,null,null,null,null,null], val = 5
Output: [4,2,7,1,3,5]
```

 

**Constraints:**

- The number of nodes in the tree will be in the range `[0, 104]`.
- `-108 <= Node.val <= 108`
- All the values `Node.val` are **unique**.
- `-108 <= val <= 108`
- It's **guaranteed** that `val` does not exist in the original BST.

```cpp
class Solution {
public:
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if(root == nullptr) root = new TreeNode(val);
        else if(val < root->val) {
            root->left = insertIntoBST(root->left, val);
        }
        else if(val > root->val) {
            root->right = insertIntoBST(root->right, val);
        }
        return root;
    }
};
```

