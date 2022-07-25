# Reverse Linked List

| Difficulty |                           LeetCode                           |                             Note                             |
| :--------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     🟢      | [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/) | [206. Reverse Linked List](#206-reverse-linked-list) |
|     🟠      | [92. Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/) | [92. Reverse Linked List II](#92-reverse-linked-list-ii) |
|     🔴      | [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/) | [25. Reverse Nodes in k-Group](#25-reverse-nodes-in-k-group) |

## 206. Reverse Linked List

Given the `head` of a singly linked list, reverse the list, and return *the reversed list*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex1.jpg)

```
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/02/19/rev1ex2.jpg)

```
Input: head = [1,2]
Output: [2,1]
```

**Example 3:**

```
Input: head = []
Output: []
```

 

**Constraints:**

- The number of nodes in the list is the range `[0, 5000]`.
- `-5000 <= Node.val <= 5000`

```cpp
class Solution {
  public:
  	ListNode* reverseList(ListNode* head) {
      ListNode* prev = nullptr;
      ListNode* curr = head;
      while(curr != nullptr) {
        ListNode* next = curr->next;
        curr->next = prev;
        prev = curr;
        curr = next;
      }
      head = prev;
      return head;
    }
}
```

## 92. Reverse Linked List II

Given the `head` of a singly linked list and two integers `left` and `right` where `left <= right`, reverse the nodes of the list from position `left` to position `right`, and return *the reversed list*.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/02/19/rev2ex2.jpg)

```
Input: head = [1,2,3,4,5], left = 2, right = 4
Output: [1,4,3,2,5]
```

**Example 2:**

```
Input: head = [5], left = 1, right = 1
Output: [5]
```

 

**Constraints:**

- The number of nodes in the list is `n`.
- `1 <= n <= 500`
- `-500 <= Node.val <= 500`
- `1 <= left <= right <= n`

```cpp
class Solution {
  public:
  ListNode* succ = nullptr;
  ListNode* reverseBetween(ListNode* head, int left, int right) {
    if(left == 1) return reverseN(head, right);
    else {
      head->next = reverseBetween(head->next, left-1, right-1);
      return head;
    }
  }
  ListNode* reverseN(ListNode* head, int n) {
    if(n == 1) {
      succ = head -> next;
      return head;
    }
    ListNode* last = reverseN(head->next, n-1);
    head->next->next = head;
    head->next = succ;
    return last;
  }
}
```

## 25 Reverse Nodes in k-Group

Given the `head` of a linked list, reverse the nodes of the list `k` at a time, and return *the modified list*.

`k` is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of `k` then left-out nodes, in the end, should remain as it is.

You may not alter the values in the list's nodes, only nodes themselves may be changed.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex1.jpg)

```
Input: head = [1,2,3,4,5], k = 2
Output: [2,1,4,3,5]
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2020/10/03/reverse_ex2.jpg)

```
Input: head = [1,2,3,4,5], k = 3
Output: [3,2,1,4,5]
```

 

**Constraints:**

- The number of nodes in the list is `n`.
- `1 <= k <= n <= 5000`
- `0 <= Node.val <= 1000`

```cpp
class Solution {
  public:
  	ListNode* reverseKGroup(ListNode* head, int k) {
      ListNode* a = head;
      ListNode* b = head;
      for(int i = 0; i < k; i++) {
        if(b == nullptr) return head;
        b = b->next;
      }
      ListNode* newHead = reverse(a,b);
      a->next = reverseKGroup(b, k);
      return newHead;
    }
  	ListNode* reverse(ListNode* a, ListNode* b) {
      ListNode* pre = nullptr;
        ListNode* cur = a;
        ListNode* nxt = a;
        while(cur != b) {
            nxt = cur->next;
            cur->next = pre;
            pre = cur;
            cur = nxt;
        }
        return pre;
    }
}
```

