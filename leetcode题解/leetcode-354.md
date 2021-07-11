---
title: 每日算法系列【LeetCode 354】俄罗斯套娃信封问题
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-16 20:28:17
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
给定一些标记了宽度和高度的信封，宽度和高度以整数对形式 (w, h) 出现。当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。

请计算最多能有多少个信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。

**说明:**
不允许旋转信封。

**示例1**
```text
输入：
envelopes = [[5,4],[6,4],[6,7],[2,3]]
输出：
3
解释：
最多信封的个数为 3, 组合为: [2,3] => [5,4] => [6,7]。
```

## 题解
题目要求矩形一个套着一个，然后求出最多套多少个，而一个矩形能套在另一个矩形上面的条件是长宽都大于另一个。

那么我们可以按照长度从小到大排个序，这样只有排在后面的矩形可以套在前面的矩形上。但是宽度也有限制条件，也得大于前面的矩形，那么问题就转化成了，把宽度看成一个序列，找到一个最长的上升序列，序列的长度就是我们要的答案。但是这里有个问题，就是矩形如果是相同长度，它们的宽度按照什么来排序呢？如果也是从小到大排，那么可能会出现多个相同长度的矩形套在一起，这是不符合题意的。所以我们对相同长度的矩形采取宽度降序的方法排序，这样它们之中最多只会被选中一个了。

那么问题就变成了经典的**最长上升子序列问题了**。

### 方法1
用 dp[i] 表示以第 i 个元素 a[i] 结尾的最长子序列的长度，那么我们可以遍历所有 a[i] 之前的元素 a[j] ，如果 a[j] 小于 a[i] ，那就说明 a[i] 可以加在 a[j] 后面，然后长度就变成了 dp[j] + 1 。所以遍历所有符合条件的 j ，找到长度最长的，然后更新 dp[i] = dp[j] + 1 。最后的答案就是 dp 数组中最大的值。

这种方法时间复杂度是 $O(n^2)$，如果序列太长会超时。本题中 c++ 没有超时，但是 python 超时了。

### 方法2
那么有什么方法来优化呢？下面介绍一种二分优化方法。

这次假设 dp[len] 表示长度为 len 的上升子序列最后一个元素的最小值。这个值要尽量小，什么意思呢？也就是相同长度的上升序列，最后一个元素小的那个序列，后面可以加的元素可选择余地肯定更大。那么这个数组怎么更新呢？

初始的时候 len 就是 0 ，因为没有找到任何上升子序列。如果现在找到了长度为 len 的子序列，然后最后一个元素最小值是 dp[len] ，这时候来了一个新元素 a[i] ，如果它比 dp[len] 大，说明 a[i] 可以加在 dp[len] 后面，那么 len 就变成了 len + 1 ， 并且 dp[len + 1] 更新为 a[i]。那如果 a[i] 小于等于 dp[len] 呢？那就继续往前遍历，找到第一个 dp[l] < a[i] <= dp[l+1] 的位置，这个位置说明了什么呢？说明了 a[i] 可以加在 dp[l] 后面构成长度为 l + 1 的子序列，并且 dp[l+1] 可以变得更小，所以更新为 a[i] 。这样 a[i] 就处理完了，最后 len 就是答案。

但是这样看起来复杂度没有变啊，其实这里有一个很好的性质，因为长度越大的序列，最后一个元素的最小值一定是大于长度小的序列最后一个元素的，所以 dp 数组是单调递增的，这样我们就可以用二分来寻找 a[i] 适合插入的位置。时间复杂度降到了 $O(n\log n)$。

## 代码
### 方法1（c++）
```cpp
class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        int n = envelopes.size();
        if (n < 1) return 0;
        sort(envelopes.begin(), envelopes.end(), cmp);
        int res = 1;
        vector<int> dp(n, 1);
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                if (envelopes[j][1] < envelopes[i][1]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
            res = max(res, dp[i]);
        }
        return res;
    }

    static bool cmp(const vector<int>& a, const vector<int>& b) {
        if (a[0] != b[0]) return a[0] < b[0];
        return a[1] > b[1];
    }
};
```

### 方法1（python）
```python
class Solution:
    def maxEnvelopes(self, arr: List[List[int]]) -> int:
        n = len(arr)
        arr.sort(key=lambda x: (x[0], -x[1]))
        nums = [i[1] for i in arr]
        dp = [1] * n
        res = 0
        for i in range(n):
            for j in range(i):
                if nums[j] < nums[i]:
                    dp[i] = max(dp[i], dp[j] + 1)
            res = max(res, dp[i])
        return res
```

### 方法2（c++）
```cpp
class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        int n = envelopes.size();
        if (n < 1) return 0;
        sort(envelopes.begin(), envelopes.end(), cmp);
        vector<int> dp(n+1, INT_MAX);
        int len = 0;
        for (int i = 0; i < n; ++i) {
            int idx = lower_bound(dp.begin(), dp.end(), envelopes[i][1]) - dp.begin();
            dp[idx] = envelopes[i][1];
            len = max(len, idx);
        }
        return len+1;
    }

    static bool cmp(const vector<int>& a, const vector<int>& b) {
        if (a[0] != b[0]) return a[0] < b[0];
        return a[1] > b[1];
    }
};
```

### 方法2（python）
```python
from bisect import bisect_left

class Solution:
    def maxEnvelopes(self, arr: List[List[int]]) -> int:
        arr.sort(key=lambda x: (x[0], -x[1]))
        nums = [i[1] for i in arr]
        dp = []
        for i in range(len(nums)):
            idx = bisect_left(dp, nums[i])
            if idx == len(dp):
                dp.append(nums[i])
            else:
                dp[idx] = nums[i]
        return len(dp)
```

## 后记
这题还有智障解法：就是两两遍历每一个矩形对，根据包含关系建立一个拓扑图，然后求图上的最长距离。没错，我刚开始就是这么想的，我是智障。这个方法没有错，还真能过这题，就是时间太慢了。