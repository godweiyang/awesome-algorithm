---
title: 每日算法系列【LeetCode 328】奇偶链表
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-14 11:49:30
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 关注公众号【算法码上来】，每日算法干货马上就来！

![](/medias/contact.jpg)

## 题目描述
给定一个单链表，把所有的奇数节点和偶数节点分别排在一起。请注意，这里的奇数节点和偶数节点指的是节点编号的奇偶性，而不是节点的值的奇偶性。

请尝试使用原地算法完成。你的算法的空间复杂度应为 O(1)，时间复杂度应为 O(nodes)，nodes 为节点总数。

**示例1**
```text
输入：
1->2->3->4->5->NULL
输出：
1->3->5->2->4->NULL
```

**示例2**
```text
输入：
2->1->3->5->6->4->7->NULL
输出：
2->3->6->7->1->5->4->NULL
```

**提示**
* 应当保持奇数节点和偶数节点的相对顺序。
* 链表的第一个节点视为奇数节点，第二个节点视为偶数节点，以此类推。

## 题解
本题要求使用原地算法，也就是不允许额外新建一个链表，只能使用常数的空间复杂度来实现。

要把奇数位置串起来，再把偶数位置串起来，最后把偶数位置链表接到奇数位置链表末尾。因为 `head` 表示的就是奇数位置链表的第一个结点，所以我们只需要再新建一个变量 `even_head` 指向 `head->next` ，也就是偶数位置链表的第一个结点。

此外还需要新建两个指针 `odd` 和 `even` 分别指向当前遍历到的奇偶结点，初始时分别指向奇偶头结点。

接下来只需要分成奇偶两条链，各自串联下去就行了。也就是每次把 `odd->next` 指向 `odd->next->next` ，把 `even->next` 指向 `even->next->next` 。也就是隔了一个元素，把当前结点下一个结点指向它的下一个和它奇偶位置相同的结点。注意的是，这里一定要先改变 `even->next` ，再改变 `odd->next` 。因为 `odd` 是在 `even` 前一个的，先改变它指向的下一个元素并不会影响 `even` 后面的元素。但是如果你先改变了 `even` 指向的下一个元素，那么 `odd->next->next` 就变了，就无法指向正确的结点了。

如果我们换个写法，先把 `odd->next` 指向 `even->next` ，再把 `even->next` 指向 `even->next->next` ，你就能很清楚的看出来了，必须先改变 `odd->next` ，因为它依赖于 `even->next` 。

最后把 `odd` 指向 `odd->next` ，把 `even` 指向 `even->next` ，继续遍历下一个结点。

什么时候停止呢？链表的最后一个结点要么是奇数结点，要么是偶数结点。如果是偶数结点，那么最后 `even` 不为空，但是它的下一个结点 `even->next` 为空，这时候结束遍历。如果是奇数结点，那么最后 `odd` 不为空，但是 `even` 为空，那么也结束遍历。综上，如果 `even` 或者 `even->next` 为空的时候，结束遍历。

最后只需要把 `odd` 的下一个结点指向 `even_head` 就能把两个链表串起来了。

时间复杂度是 $O(n)$ ，空间复杂度是 $O(1)$ ，因为只用到了 3 个额外指针。

## 代码
### c++
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */

class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        if (head == NULL) return NULL;
        ListNode* even_head = head->next;
        ListNode* odd = head;
        ListNode* even = head->next;
        while (even && even->next) {
            odd->next = even->next;
            even->next = even->next->next;
            odd = odd->next;
            even = even->next;
        }
        odd->next = even_head;
        return head;
    }
};
```

### python
```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def oddEvenList(self, head: ListNode) -> ListNode:
        if head is None:
            return head
        even_head, odd, even = head.next, head, head.next
        while even is not None and even.next is not None:
            odd.next = even.next
            even.next = even.next.next
            odd = odd.next
            even = even.next
        odd.next = even_head
        return head
```
