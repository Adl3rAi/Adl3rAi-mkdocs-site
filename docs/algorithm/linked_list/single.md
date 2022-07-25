# Single Linked List

| Difficulty |                           LeetCode                           |                             Note                             |
| :--------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
|     🟢      | [21. Merge Two Sorted Lists](https://leetcode.com/problems/merge-two-sorted-lists/) | [21. Merge Two Sorted Lists](#21-merge-two-sorted-lists) |
|     🔴      | [23. Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/) | [23. Merge k Sorted Lists](#23-merge-k-sorted-lists) |
|     🟢      | [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) | [141. Linked List Cycle](#141-linked-list-cycle) |
|     🟠      | [142. Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/) | [142. Linked List Cycle](#142-linked-list-cycle-ii) |
|     🟢      | [876. Middle of the Linked List](https://leetcode.com/problems/middle-of-the-linked-list/) | [876. Middle of the Linked List](#876-middle-of-the-linked-list) |
|     🟠      | [19. Remove Nth Node From End of Lis](https://leetcode.com/problems/remove-nth-node-from-end-of-list/) | [19. Remove Nth Node From End of Lis](#19-remove-nth-node-from-end-of-list) |

## 21. Merge Two Sorted Lists

You are given the heads of two sorted linked lists `list1` and `list2`.

Merge the two lists in a one **sorted** list. The list should be made by splicing together the nodes of the first two lists.

Return *the head of the merged linked list*.

**Example 1:**

[![img](https://camo.githubusercontent.com/04964ab99b30c236e748d06907f2ecefa2ef7f4d0c4a7cb6837ceb122772a31e/68747470733a2f2f6173736574732e6c656574636f64652e636f6d2f75706c6f6164732f323032302f31302f30332f6d657267655f6578312e6a7067)](https://camo.githubusercontent.com/04964ab99b30c236e748d06907f2ecefa2ef7f4d0c4a7cb6837ceb122772a31e/68747470733a2f2f6173736574732e6c656574636f64652e636f6d2f75706c6f6164732f323032302f31302f30332f6d657267655f6578312e6a7067)

```
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

**Example 2:**

```
Input: list1 = [], list2 = []
Output: []
```

**Example 3:**

```
Input: list1 = [], list2 = [0]
Output: [0]
```

**Constraints:**

- The number of nodes in both lists is in the range `[0, 50]`.
- `-100 <= Node.val <= 100`
- Both `list1` and `list2` are sorted in **non-decreasing** order.

```cpp
class Solution {
  public:
  	ListNode* mergeTwoLists(listNode* list1, ListNode* list2) {
      ListNode* dummy = new ListNode();
      dummy->val = -1;
      ListNode* p = dummy;
      ListNode* p1 = list1;
      ListNode* p2 = list2;
      while(p1 != nullptr && p2 != nullptr) {
        if(p1->val <= p2->val) {
          p->next = p1;
          p1 = p1->next;
        }
        else {
          p->next = p2;
          p2 = p2->next;
        }
        p = p->next;
      }
      if(p1 != nullptr) p->next = p1;
      if(p2 != nullptr) p->next = p2;
      return dummy->next;
    }
}
```

## 23. Merge k Sorted Lists

You are given an array of `k` linked-lists `lists`, each linked-list is sorted in ascending order.

*Merge all the linked-lists into one sorted linked-list and return it.*

 

**Example 1:**

```
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
Explanation: The linked-lists are:
[
  1->4->5,
  1->3->4,
  2->6
]
merging them into one sorted list:
1->1->2->3->4->4->5->6
```

**Example 2:**

```
Input: lists = []
Output: []
```

**Example 3:**

```
Input: lists = [[]]
Output: []
```

 

**Constraints:**

- `k == lists.length`
- `0 <= k <= 104`
- `0 <= lists[i].length <= 500`
- `-104 <= lists[i][j] <= 104`
- `lists[i]` is sorted in **ascending order**.
- The sum of `lists[i].length` will not exceed `104`.

```cpp
class Solution {
  public:
  	ListNode* mergeKLists(vector<ListNode*>& lists) {
      priority_queue<ListNode*, vector<ListNode*>, cmp> pq;
      for(ListNode* head : lists) {
        if(head != nullptr) pq.push(head);
      }
      ListNode* dummy = new ListNode();
      ListNode* p = dummy;
      while(!pq.empty()) {
        ListNode* top = pq.top();
        p->next = top;
        p = p->next;
        pq.top();
        if(top->next){
          pq.push(top->next);
        }
      }
      return dummy->next;
    }
  	struct cmp {
      bool operator()(const ListNode* a, const ListNode* b) {
        return a->val > b->val;
      }
    };
}
```

## 141. Linked List Cycle

Given `head`, the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to. **Note that `pos` is not passed as a parameter**.

Return `true` *if there is a cycle in the linked list*. Otherwise, return `false`.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

```
Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

```
Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 0th node.
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)

```
Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

 

**Constraints:**

- The number of the nodes in the list is in the range `[0, 104]`.
- `-105 <= Node.val <= 105`
- `pos` is `-1` or a **valid index** in the linked-list.

```cpp
class Solution {
  public:
  	bool hasCycle(ListNode* head) {
      ListNode* slow = head;
      ListNode* fast = head;
      while(fast != nullptr && fast->next != nullptr){
        slow = slow->next;
        fast = fast->next->next;
        if(fast == slow) return true;
      }
      return false;
    }
}
```

## 142. Linked List Cycle II

Given the `head` of a linked list, return *the node where the cycle begins. If there is no cycle, return* `null`.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to (**0-indexed**). It is `-1` if there is no cycle. **Note that** `pos` **is not passed as a parameter**.

**Do not modify** the linked list.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist.png)

```
Input: head = [3,2,0,-4], pos = 1
Output: tail connects to node index 1
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test2.png)

```
Input: head = [1,2], pos = 0
Output: tail connects to node index 0
Explanation: There is a cycle in the linked list, where tail connects to the first node.
```

**Example 3:**

![img](https://assets.leetcode.com/uploads/2018/12/07/circularlinkedlist_test3.png)

```
Input: head = [1], pos = -1
Output: no cycle
Explanation: There is no cycle in the linked list.
```

 

**Constraints:**

- The number of the nodes in the list is in the range `[0, 104]`.
- `-105 <= Node.val <= 105`
- `pos` is `-1` or a **valid index** in the linked-list.

```cpp
class Solution {
  public:
  	ListNode* detectCycle(ListNode* head) {
      ListNode* slow = head;
      ListNode* fast = head;
      while(fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
        if(slow == fast) {
          break;
        }
      }
      if(fast == nullptr || fast->next == nullptr) {
        return nullptr;
      }
      else {
        slow = head;
        while(slow != fast) {
          slow = slow->next;
          fast = fast->next;
        }
        return slow;
      }
    }
}
```

## 876. Middle of the Linked List

Given the `head` of a singly linked list, return *the middle node of the linked list*.

If there are two middle nodes, return **the second middle** node.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2021/07/23/lc-midlist1.jpg)

```
Input: head = [1,2,3,4,5]
Output: [3,4,5]
Explanation: The middle node of the list is node 3.
```

**Example 2:**

![img](https://assets.leetcode.com/uploads/2021/07/23/lc-midlist2.jpg)

```
Input: head = [1,2,3,4,5,6]
Output: [4,5,6]
Explanation: Since the list has two middle nodes with values 3 and 4, we return the second one.
```

 

**Constraints:**

- The number of nodes in the list is in the range `[1, 100]`.
- `1 <= Node.val <= 100`

```cpp
class Solution {
  public:
  	ListNode* middleNode(ListNode* head) {
      ListNode* slow = head;
      ListNode* fast = head;
      while(fast != nullptr && fast->next != nullptr) {
        slow = slow->next;
        fast = fast->next->next;
      }
      return slow;
    }
}
```

## 19. Remove Nth Node From End of List

Given the `head` of a linked list, remove the `nth` node from the end of the list and return its head.

 

**Example 1:**

![img](https://assets.leetcode.com/uploads/2020/10/03/remove_ex1.jpg)

```
Input: head = [1,2,3,4,5], n = 2
Output: [1,2,3,5]
```

**Example 2:**

```
Input: head = [1], n = 1
Output: []
```

**Example 3:**

```
Input: head = [1,2], n = 1
Output: [1]
```

 

**Constraints:**

- The number of nodes in the list is `sz`.
- `1 <= sz <= 30`
- `0 <= Node.val <= 100`
- `1 <= n <= sz`

```cpp
class Solution {
  public:
  	ListNode* removeNthFromEnd(ListNode* head, int n) {
      ListNode* dummy = new ListNode();
      dummy->val = -1;
      dummy->next = head;
      ListNode* p = findFromEnd(dummy, n+1);
      p->next = p->next->next;
      return dummy->next;
    }
  private:
  	ListNode* findFromEnd(ListNode* head, int k) {
      ListNode* p1 = head;
      for(int i = 0; i < k; i++) {
        p1 = p1->next;
      }
      ListNode* p2 = head;
      while(p1 != nullptr) {
        p2 = p2->next;
        p1 = p1->next;
      }
      return p2;
    }
}
```

