# Binary Tree Basics and Traverse

| Difficulty |                           LeetCode                           |                             Note                             |
| :--------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     🟢      | [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/) | [104. Maximum Depth of Binary Tree](#104-maximum-depth-of-binary-tree) |
|     🟢      | [144. Binary Tree Preorder Traversal](#144-binary-tree-preorder-traversal) | [144. Binary Tree Preorder Traversal](#144-binary-tree-preorder-traversal) |
|     🟢      | [543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/) | [543. Diameter of Binary Tree](#543-diameter-of-binary-tree) |
|     🔴      | [124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/) | [124. Binary Tree Maximum Path Sum](#124-binary-tree-maximum-path-sum) |
|     🟢      | [226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/) | [226. Invert Binary Tree](#226-invert-binary-tree) |
|     🟠      | [116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/) | [116. Populating Next Right Pointers in Each Node](#116-populating-next-right-pointers-in-each-note) |
|     🟠      | [114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/) | [114. Flatten Binary Tree to Linked List](#114-flatten-binary-tree-to-linked-list) |
|     🟠      | [652. Find Duplicate Subtree](https://leetcode.com/problems/find-duplicate-subtrees/) | [652. Find Duplicate Subtree](#652-find-duplicate-subtrees) |

## 104. Maximum Depth of Binary Tree

Given the `root` of a binary tree, return *its maximum depth*.

A binary tree's **maximum depth** is the number of nodes along the longest path from the root node down to the farthest leaf node.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/11/26/tmp-tree.jpg)

```
Input: root = [3,9,20,null,null,15,7]
Output: 3
```

**Example 2:**

```
Input: root = [1,null,2]
Output: 2
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 104]`.
- `-100 <= Node.val <= 100`

```cpp
class Solution {
  public:
  	int maxDepth(TreeNode* root) {
      if(root == nullptr) {
        return 0;
      }
      int leftMax = maxDepth(root->left);
      int rightMax = maxDepth(root->right);
      return max(leftMax, rightMax) + 1;
    }
}
```

**理解递归的关键在于把握住<u>函数的定义</u>,不要掉入具体的递归之中。例如本题中**`int maxDepth(TreeNode* root)`**的定义是**<u>返回以`root`为根的二叉树的最大深度</u>，理解到这一步就足够了。

考虑base case时，就是空二叉树，空二叉树的深度为0，就可以直接`return 0`；

对于一棵非空二叉树而言，最大深度 = max(左子树最大深度，右子树最大深度) + 1

## 144. Binary Tree Preorder Traversal

Given the `root` of a binary tree, return *the preorder traversal of its nodes' values*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_1.jpg)

```
Input: root = [1,null,2,3]
Output: [1,2,3]
```

**Example 2:**

```
Input: root = []
Output: []
```

**Example 3:**

```
Input: root = [1]
Output: [1]
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 100]`.
- `-100 <= Node.val <= 100`

```cpp
class Solution {
  public:
  	vector<int> res;
  	vector<int> preorderTraversal(TreeNode* root){
      traverse(root);
      return res;
    }
  	void traverse(TreeNode* root) {
      if(root == nullptr) return;
      res.push_back(root->val);
      traverse(root->left);
      traverse(root->right);
    }
}
```

`traverse(TreeNode*)`函数用于(前序)遍历二叉树，(前序)遍历的顺序是`NodeVal -> leftTree -> rightTree`，且base case是空二叉树时`return`

## 543. Diameter of Binary Tree

Given the `root` of a binary tree, return *the length of the **diameter** of the tree*.

The **diameter** of a binary tree is the **length** of the longest path between any two nodes in a tree. This path may or may not pass through the `root`.

The **length** of a path between two nodes is represented by the number of edges between them.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/06/diamtree.jpg)

```
Input: root = [1,2,3,4,5]
Output: 3
Explanation: 3 is the length of the path [4,2,1,3] or [5,2,1,3].
```

**Example 2:**

```
Input: root = [1,2]
Output: 1
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[1, 104]`.
- `-100 <= Node.val <= 100`

```cpp
class Solution {
  public:
  	int maxDiameter = 0;
  	int diameterOfBinaryTree(TreeNode* root) {
      int depth = maxDepth(root);
      return maxDiameter;
    }
  	int maxDepth(TreeNode* root) {
      if(root == nullptr) return 0;
      int left = maxDepth(root->left);
      int right = maxDepth(root->right);
      int diameter = left + right;
      maxDiameter = max(maxDiameter, diameter);
      return max(left,right) + 1;
    }
}
```

理解`int maxDepth(TreeNode* root)`的含义，在求某二叉树的最大深度的框架中顺便计算出直径，并相互比较以得出最大直径。`diameter = left + right;`

## 124. Binary Tree Maximum Path Sum

A **path** in a binary tree is a sequence of nodes where each pair of adjacent nodes in the sequence has an edge connecting them. A node can only appear in the sequence **at most once**. Note that the path does not need to pass through the root.

The **path sum** of a path is the sum of the node's values in the path.

Given the `root` of a binary tree, return *the maximum **path sum** of any **non-empty** path*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/13/exx1.jpg)

```
Input: root = [1,2,3]
Output: 6
Explanation: The optimal path is 2 -> 1 -> 3 with a path sum of 2 + 1 + 3 = 6.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/10/13/exx2.jpg)

```
Input: root = [-10,9,20,null,null,15,7]
Output: 42
Explanation: The optimal path is 15 -> 20 -> 7 with a path sum of 15 + 20 + 7 = 42.
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[1, 3 * 104]`.
- `-1000 <= Node.val <= 1000`

```cpp
class Solution {
  public:
  	int res = INT_MIN;
  	int maxPathSum(TreeNode* root) {
      if(root == nullptr) return 0;
      int oneSideMaxVal = oneSideMax(root);
      return res;
    }
  	int oneSideMax(TreeNode* root) {
      if(root == nullptr) return 0;
      int left = max(0, oneSideMax(root->left));
      int right = max(0, oneSideMax(root->right));
      int pathMaxSum = root->val + left + right;
      res = max(res, pathMaxSum);
      return max(left, right) + root->val;
    }
}
```

理解`int oneSideMax(TreeNode* root)`的含义就是指：一定通过`root`节点的最大单向路径和（要么是`root->leftTree`，要么是`root->rightTree`），其中通过两次递归遍历了二叉树中的每一个节点，从而计算出了每一个节点的`pathMaxSum`

## 226. Invert Binary Tree

Given the `root` of a binary tree, invert the tree, and return *its root*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/03/14/invert1-tree.jpg)

```
Input: root = [4,2,7,1,3,6,9]
Output: [4,7,2,9,6,3,1]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/03/14/invert2-tree.jpg)

```
Input: root = [2,1,3]
Output: [2,3,1]
```

**Example 3:**

```
Input: root = []
Output: []
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 100]`.
- `-100 <= Node.val <= 100`

```cpp
class Solution {
  public:
  	TreeNode* invertTree(TreeNode* root) {
      traverse(root);
      return root;
    }
  	void traverse(TreeNode* root) {
      if(root == nullptr) return;
      TreeNode* temp = root->left;
      root->left = root->right;
      root->right = temp;
      traverse(root->left);
      traverse(root->right);
    }
}
```

## 116. Populating Next Right Pointers in Each Note

You are given a **perfect binary tree** where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:

```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)

```
Input: root = [1,2,3,4,5,6,7]
Output: [1,#,2,3,#,4,5,6,7,#]
Explanation: Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.
```

**Example 2:**

```
Input: root = []
Output: []
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 212 - 1]`.
- `-1000 <= Node.val <= 1000`

```cpp
class Solution {
  Node* connect(Node* root) {
    if(root == nullptr) return root;
    traverse(root->left, root->right);
    return root;
  }
  void traverse(Node* node1, Node* node2) {
    if(node1 == nullptr && node2 == nullptr) return;
    node1->next = node2;
    traverse(node1->left, node1->right);
    traverse(node1->right, node2->left);
    traverse(node2->left, node2->right);
  }
}
```

## 114. Flatten Binary Tree to Linked List

Given the `root` of a binary tree, flatten the tree into a "linked list":

- The "linked list" should use the same `TreeNode` class where the `right` child pointer points to the next node in the list and the `left` child pointer is always `null`.
- The "linked list" should be in the same order as a [**pre-order** **traversal**](https://en.wikipedia.org/wiki/Tree_traversal#Pre-order,_NLR) of the binary tree.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/01/14/flaten.jpg)

```
Input: root = [1,2,5,3,4,null,6]
Output: [1,null,2,null,3,null,4,null,5,null,6]
```

**Example 2:**

```
Input: root = []
Output: []
```

**Example 3:**

```
Input: root = [0]
Output: [0]
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[0, 2000]`.
- `-100 <= Node.val <= 100`

```cpp
class Solution {
  void flatten(TreeNode* root) {
    if(root == nullptr) return;
    flatten(root->left);
    flatten(root->right);
    TreeNode* left = root->left;
    TreeNode* right = root->right;
    root->left = nullptr;
    root->right = left;
    TreeNode* p = root;
    while(p->right != nullptr) {
      p = p->right;
    }
    p->right = right;
  }
}
```

还是要关注函数`void flatten(TreeNode* root)`的定义：将二叉树拉平成一条链表，根据递归的思想拉平二叉树的步骤是先将左、右子树拉平，再将右链表接到左链表，再将左链表接到右侧。则是<u>先遍历，再操作，本质上就是后序遍历。</u>

## 652. Find Duplicate Subtrees

Given the `root` of a binary tree, return all **duplicate subtrees**.

For each kind of duplicate subtrees, you only need to return the root node of any **one** of them.

Two trees are **duplicate** if they have the **same structure** with the **same node values**.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/08/16/e1.jpg)

```
Input: root = [1,2,3,4,null,2,4,null,null,4]
Output: [[2,4],[4]]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/08/16/e2.jpg)

```
Input: root = [2,1,1]
Output: [[1]]
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2020/08/16/e33.jpg)

```
Input: root = [2,2,2,3,null,3,null]
Output: [[2,3],[3]]
```

 

**Constraints:**

- The number of the nodes in the tree will be in the range `[1, 10^4]`
- `-200 <= Node.val <= 200`

```cpp
class Solution {
  public:
    map<string, int> memo;
    vector<TreeNode*> res;
  	vector<TreeNode*> findDuplicateSubtree(TreeNode* root) {
      string subTree = traverse(root);
      return res;
    }
  	string traverse(TreeNode* root) {
      if(root == nullptr) return "#";
      string left = traverse(root->left);
      string right = traverse(root->right);
      string subTree = left + "," + right + "," + to_string(root->val);
      int freq;
      if(!memo.count(subTree)) {
        freq = 0;
      }
      else {
        freq = memo[subTree];
      }
      if(freq == 1) {
        res.push_back(root);
      }
      memo[subTree] = freq + 1;
      return subTree;
    }
}
```

