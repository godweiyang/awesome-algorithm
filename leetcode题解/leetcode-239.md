---
title: 每日算法系列【LeetCode 239】滑动窗口最大值
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-31 10:43:45
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
给定一个数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。

返回滑动窗口中的最大值。

**示例1**
```text
输入：
nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出：
[3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**提示**
你可以假设 k 总是有效的，在输入数组不为空的情况下，1 ≤ k ≤ 输入数组的大小。

## 题解
### 双端单调队列
本题要找长度为 k 的区间的最大值。模拟这个区间的移动过程，可以发现，右边增加一个数，左边必然会去掉一个数。

那么最大的数有什么性质呢？可以发现，如果扫描区间末尾，在已经遍历过的数之中，一个数 a 在 b 前面，并且 a 还比 b 小，那么 a 在之后的区间里永远无法成为最大值。

所以我们遍历到一个数时，它之前的所有比它小的数都可以去掉了，只保留比它大的数就行了。这就让人想到了之前介绍过的单调栈，但是本题中是先进先出，所以要改用单调队列。此外队列末尾不仅要增加元素，还得维护单调递减，适当去除一些元素，所以队列两端都得有插入和删除的功能。所以本题要使用双端队列，而队列中的元素又是单调递减的，所以又是双端单调队列。

这样思路就很明确了：
* 遍历元素 nums[i] ，然后跟队列尾部元素比较，如果比尾部元素大，就出队，然后继续比较，直到 nums[i] 小于尾部元素，然后将它入队。
* 然后用一下队列首部元素的下标，计算出队列中区间的长度，如果大于 k 了，那么队首元素就要出队。
* 最后队首元素就是当前区间的最大值。

### 分块法
试想如果我们将数组划分为相同大小的若干块，每一块中最大值都是知道的话，那么要求区间最大值，只需要看它在哪几块里就行了。

那么块的大小应该设成多少呢？

如果块大小为 k ，就可以发现长度为 k 的区间 [i, j] 要么正好就是一个完整的块，要么跨越了两个相邻块。那么我们只需要知道 i 到它那块末尾元素中最大值，以及 j 到它那块开头最大值就行了，两个部分合并求最大值就是区间的最大值了。而每个元素到它自己那块的开头和末尾的最大值都可以预处理出来，方法和求前缀和类似。

那为什么块大小不能是其他值呢？如果块大小大于 k ，那么会出现区间完全包含于一块之中的情况，那就和不分块一样了。如果块大小小于 k ，那么就会出现区间横跨了好几块，那么还得遍历中间块的最大值。极端情况下如果块大小为 1 ，那么就等于暴力求解。


## 代码
### 双端单调队列（c++）
```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        deque<int> Q;
        vector<int> res;
        for (int i = 0; i < n; ++i) {
            while (!Q.empty() && nums[i] >= nums[Q.back()]) Q.pop_back();
            Q.push_back(i);
            if (i - Q.front() + 1 > k) Q.pop_front();
            if (i >= k-1) res.push_back(nums[Q.front()]);
        }
        return res;
    }
};
```

### 双端单调队列+数组实现（c++）
```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> Q(n, 0);
        vector<int> res;
        int l = 0, r = 0;
        for (int i = 0; i < n; ++i) {
            while (r-l > 0 && nums[i] >= nums[Q[r-1]]) r--;
            Q[r++] = i;
            if (i - Q[l] + 1 > k) l++;
            if (i >= k-1) res.push_back(nums[Q[l]]);
        }
        return res;
    }
};
```

### 分块法（c++）
```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> lmax(n, 0), rmax(n, 0);
        vector<int> res;
        if (!n) return res;
        for (int i = 0; i < n; ++i) {
            if (i%k == 0) lmax[i] = nums[i];
            else lmax[i] = max(lmax[i-1], nums[i]);
        }
        for (int i = n-1; i >= 0; --i) {
            if ((i+1)%k == 0 || i == n-1) rmax[i] = nums[i];
            else rmax[i] = max(rmax[i+1], nums[i]);
        }
        for (int i = k-1; i < n; ++i) {
            res.push_back(max(lmax[i], rmax[i-k+1]));
        }
        return res;
    }
};
```

### 双端单调队列（python）
```python
import collections

class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        Q = collections.deque()
        res = []
        for i in range(n):
            while len(Q) > 0 and nums[i] >= nums[Q[-1]]:
                Q.pop()
            Q.append(i)
            if i - Q[0] + 1 > k:
                Q.popleft()
            if i >= k-1:
                res.append(nums[Q[0]])
        return res
```

### 双端单调队列+数组实现（python）
```python
import collections

class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        Q = [0] * n
        res = []
        l, r = 0, 0
        for i in range(n):
            while r-l > 0 and nums[i] >= nums[Q[r-1]]:
                r -= 1
            Q[r] = i
            r += 1
            if i - Q[l] + 1 > k:
                l += 1 
            if i >= k-1:
                res.append(nums[Q[l]])
        return res
```

### 分块法（python）
```python
import collections

class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        lmax, rmax = [0] * n, [0] * n
        res = []
        if n == 0:
            return res
        for i in range(n):
            if i%k == 0:
                lmax[i] = nums[i]
            else:
                lmax[i] = max(lmax[i-1], nums[i])
        for i in range(n-1, -1, -1):
            if (i+1)%k == 0 or i == n-1:
                rmax[i] = nums[i]
            else:
                rmax[i] = max(rmax[i+1], nums[i])
        for i in range(k-1, n):
            res.append(max(lmax[i], rmax[i-k+1]))
        return res
```

### 后记
双端队列在 c++ 和 python 中都有 deque 的实现，如果你不会用的话，也可以用一个数组自己模拟一下，我觉得反而更加方便一点。