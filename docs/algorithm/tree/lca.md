# Lowest Common Ancestor of a Binary Tree

| Difficulty |                           LeetCode                           |                             Note                             |
| :--------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     🟠      | [236. Lowest Common Ancestor of a Binary Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/) | [236. Lowest Common Ancestor of a Binary Tree](https://github.com/Adl3rAi/leetCodeNotes/blob/main/Binary_Tree.md#236-lowest-common-ancestor-of-a-binary-tree) |

找到二叉树两个节点的最小公共祖先首先要找到这两个节点的位置，那么问题就转变为`TreeNode* find(TreeNode* root, TreeNode* target)`

```cpp
TreeNode* find(TreeNode* root, TreeNode* target) {
  if(root == nullptr) return nullptr;
  // preorder,先找root
  if(root->val == target->val) return root;
  // root不是目标节点，去左右子树找
  TreeNode* left = find(root->left, target);
  TreeNode* right = find(root->right, target);
  // 左右子树哪一边找到了
  return left != nullptr ? left : right;
}
```

```cpp
TreeNode* find(TreeNode* root, int val1, int val2) {
  // base case
  if(root == nullptr) return nullptr;
  if(root->val == val1 || root->val == val2) {
    return root;
  }
  TreeNode* left = find(root->left, val1, val2);
  TreeNode* right = find(root->right, val1, val2);
  return left != nullptr ? left : right;
}
```

对于**Lowest Common Ancestor(LCA)**，对于一个节点的`left != nullptr && right != nullptr`时，则该节点就是一个**LCA**

或是，由于题设中每一个节点都在二叉树中，如果找到一个值为`val1`或`val2`的节点则直接返回，首先返回的节点则为一个**LCA**

## 236. Lowest Common Ancestor of a Binary Tree

Given a binary tree, find the lowest common ancestor (LCA) of two given nodes in the tree.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).”

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)

```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
Output: 3
Explanation: The LCA of nodes 5 and 1 is 3.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2018/12/14/binarytree.png)

```
Input: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
Output: 5
Explanation: The LCA of nodes 5 and 4 is 5, since a node can be a descendant of itself according to the LCA definition.
```

**Example 3:**

```
Input: root = [1,2], p = 1, q = 2
Output: 1
```

 

**Constraints:**

- The number of nodes in the tree is in the range `[2, 105]`.
- `-109 <= Node.val <= 109`
- All `Node.val` are **unique**.
- `p != q`
- `p` and `q` will exist in the tree.

```cpp
class Solution {
  public:
  TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    return find(root, p->val, q->val);
  }
  TreeNode* find(TreeNode* root, int val1, int val2) {
    if(root == nullptr) return nullptr;
    if(root->val == val1 || root->val == val2) {
      return root;
    }
    TreeNode* left = find(root->left, val1, val2);
    TreeNode* right = find(root->right ,val1, val2);
    if(left != nullptr && right != nullptr) {
      return root;
    }
    return left != nullptr ? left : right;
  }
};
```

