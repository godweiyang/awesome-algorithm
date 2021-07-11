---
title: 每日算法系列【LeetCode 287】寻找重复数
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-19 12:48:13
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
给定一个包含 $n + 1$ 个整数的数组 $nums$，其数字都在 $1$ 到 $n$ 之间（包括 $1$ 和 $n$），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。

**说明**
* 不能更改原数组（假设数组是只读的）。
* 只能使用额外的 $O(1)$ 的空间。
* 时间复杂度小于 $O(n^2)$ 。
* 数组中只有一个重复的数字，但它可能不止重复出现一次。

**示例1**
```text
输入：
[1,3,4,2,2]
输出：
2
```

**示例2**
```text
输入：
[3,1,3,4,2]
输出：
3
```

## 题解
### 二分法
根据抽屉原理，如果大小为 $m$ 的抽屉里放了大于 $m$ 个数，那么一定有一个抽屉里至少放了两个数。

那我们不妨统计一下数组中有多少个数满足 $1 \le nums[i] \le m$ ，数量记为 $cnt$ 。

如果 $cnt > m$ ，那么根据抽屉原理，$1$ 到 $m$ 中一定有一个数出现了至少两次。

反之如果 $cnt \le m$ ，那么说明满足 $m+1 \le nums[i] \le n$ 的数的数量是 $n+1-cnt \ge n-m+1$ ，而抽屉大小是 $n-m$ ，所以根据抽屉原理，$m+1$ 到 $n$ 中一定有一个数出现了至少两次。

综上，可以采用二分法，不断缩小 $m$ 的范围，最终得到这个出现至少两次的数的值。

时间复杂度 $O(n \log n)$ 。

### 快慢指针法
因为 $nums$ 中数据范围是 $1$ 到 $n$ ，所以 $nums[0] = x \neq 0$ 。那么接着用 $x$ 作为下标来索引值，得到 $nums[x] = y$ 。如果 $y$ 已经出现过了 ，那么重复值已经找到了。否则的话 $y$ 还没出现过的话，继续用 $y$ 作为下标来索引，直到出现重复值。

可以发现按照这种方法索引下去，形成了一个链，也就是 $0 \to x \to y \to \cdots$ 。最终这条链末端一定会产生出一个环，那么环的入口一定就是那个重复的数。

举个例子，如下图所示，最终环产生在了 $2 \to 4 \to 2$ 上面，而 $2$ 又是链进入环的入口，所以重复的数就是 $2$ 。

![](1.png)

那么如何求**链表中的环**呢？这其实是一道面试经常会问到的经典题，标准解法就是用两个**快慢指针**。

初始时两个指针指着链表头结点，然后同时移动。慢指针一次移动一个结点，快指针一次移动两个结点。当下一次快指针又和慢指针相遇时，停止移动。然后用第三个指针指着头结点，慢指针留在原地，两者同时移动，都是一次移动一个结点，直到相遇。这时两者指着的结点就是环的入口了。

大家可以用上面的例子自己画图演示一下，我下面严格证明一下为什么这样是对的。

假设如下图所示，链表中链的长度是 $m$ ，环的长度是 $n$ 。

![](4.jpg)

假设慢指针和快指针第一次相遇时，慢指针移动的距离是 $x$ ，那么快指针移动距离就是 $2x$ 。

可以列出等式 $2x - x = kn$ ，也就是两者距离差值一定是环长度的正整数 $k$ 倍，同时 $k$ 是使得 $x = kn \ge m$ 的最小正整数，即 $(k-1)n < m \le kn$。这时候慢指针离入口的距离是 $n - (x - m) = m - (k - 1)n$ ，也就是说，慢指针只需要再多绕 $k-1$ 个环的长度，就能恰好和从头结点而来的指针相遇在入口处。

时间复杂度 $O(n)$ 。

**扩展：**  

那有人可能会问了，那要是链最后回到了 $0$ ，不就没有链，只有环了吗？哪来的入口？这是对的，所以本题中限制了 $nums$ 数组里都是大于 $0$ 的，如果范围是 $0$ 到 $n-1$ 的话，不能直接用数值但下标索引了，不然会出现下面这种情况，也就是 $0$ 也在环里了。

![](2.jpg)

其实这种情况我们只需要稍稍修改一下索引，让 $0$ 不可能出现在环里就行了，也就是让 $nums[i] + 1$ 当作索引，如下图所示。最终重复的数只需要减去 $1$ 就行了。

![](3.jpg)


## 代码
### 二分法（c++）
```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int n = nums.size() - 1;
        int l = 1, r = n;
        while (l < r) {
            int m = l+(r-l)/2;
            int cnt = 0;
            for (int i = 0; i < n+1; ++i) {
                if (nums[i] <= m) cnt++;
            }
            if (cnt > m) r = m;
            else l = m + 1;
        }
        return l;
    }
};
```

### 快慢指针法（c++）
```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int slow = 0, fast = 0;
        while (true) {
            slow = nums[slow];
            fast = nums[nums[fast]];
            if (slow == fast) break;
        }
        int find = 0;
        while (find != slow) {
            slow = nums[slow];
            find = nums[find];
        }
        return find;
    }
};
```

### 二分法（python）
```python
class Solution:
    def findDuplicate(self, nums):
        n = len(nums) - 1
        l, r = 1, n
        while l < r:
            m = l+(r-l)//2
            cnt = sum([x<=m for x in nums])
            if cnt > m:
                r = m
            else:
                l = m + 1
        return l
```

### 快慢指针法（python）
```python
class Solution:
    def findDuplicate(self, nums):
        slow, fast = 0, 0
        while True:
            slow = nums[slow]
            fast = nums[nums[fast]]
            if slow == fast:
                break
        find = 0
        while find != slow:
            slow = nums[slow]
            find = nums[find]
        return find
```