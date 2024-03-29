---
title: 每日算法系列【LeetCode 330】按要求补齐数组
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-15 00:03:07
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
给定一个已排序的正整数数组 nums ，和一个正整数 n 。从 [1, n] 区间内选取任意个数字补充到 nums 中，使得 [1, n] 区间内的任何数字都可以用 nums 中某几个数字的和来表示。请输出满足上述要求的最少需要补充的数字个数。

**示例1**
```text
输入：
nums = [1,3], n = 6
输出：
1
解释：
根据 nums 里现有的组合 [1], [3], [1,3]，可以得出 1, 3, 4。
现在如果我们将 2 添加到 nums 中， 组合变为: [1], [2], [3], [1,3], [2,3], [1,2,3]。
其和可以表示数字 1, 2, 3, 4, 5, 6，能够覆盖 [1, 6] 区间里所有的数。
所以我们最少需要添加一个数字。
```

**示例2**
```text
输入：
nums = [1,5,10], n = 20
输出：
2
解释：
我们需要添加 [2, 4]。
```

**示例3**
```text
输入：
nums = [1,2,2], n = 5
输出：
0
```

## 题解
首先这题没有说数据范围，根据正解的时间复杂度，推测出 nums.length 的大小在 1e5 左右，而 n 的大小在 int 的最大值左右。

而不考虑数据范围，我刚开始的想法是，首先考虑简化问题：**用 nums 数组中的数字可以表示出多少个不同的正整数？**
这可以用动态规划来解决，令 dp[S][i] 表示用前 i 个数凑出和 S 是否可行，那么状态转移方程就是： dp[S][i] = dp[S-nums[i]][i-1] || dp[S][i-1] 。
然后遍历 dp[i][nums.length-1] ，如果发现等于 0 ，就说明 nums 数组无法凑出 i 这个和，于是新增加一个数 i ，并且将 [i, 2i)中的所有 dp 值都改成 1，直到 [1, n] 全部被覆盖了。
后来看了才发现，我弱智了，这样不仅没必要，而且 n 太大会炸裂。

正解很简单。首先题目中有个词“已排序”，其实不是很重要，没排序的话我排个序也不怎么耗时间。那排完序怎么办呢，思路还是刚刚的思路，只是不用动态规划了。

试想从最小的 1 开始，如果 1 不在数组里，那一定要补上一个 1 的，然后 [1, 2) 范围里的数都可以被表示出来了。然后看下一个数，如果大于 2 ，那么 2 是没有办法通过数组里的数表示出来的，因为比它小的数只能凑出 [1, 2) ，所以 2 也要补上。如果下一个数小于等于 2 ，那么我们可以利用目前的数凑出 [1, 4) 里面的数，然后继续往下遍历，直到能够凑出 [1, n+1) 里面的数。

一般情况下，如果遍历到 nums[i-1] 时，可以表示出 [1, S) 范围内的数，那么如果 nums[i] > S ，那么需要补上 S ，并且可表示范围更新为 [1, 2S)，然后继续看 nums[i] ；否则的话可表示范围更新为 [1, S+nums[i]) ，然后看 nums[i+1] 就行了。

这样就比原来的思路简化了很多了，那么时间复杂度怎么样呢？
因为 S 每次更新有两种情况，要么乘以 2 ，要么加上了 nums[i] ，所以最终时间复杂度是 $O(m + \log n)$ 。

## 代码
### c++
```cpp
class Solution {
public:
    int minPatches(vector<int>& nums, int n) {
        int len = nums.size(), idx = 0, res = 0;
        long long r = 1;
        while (r <= n) {
            if (idx < len && nums[idx] <= r) {
                r += nums[idx++];
            } else {
                res++;
                r += r;
            }
        }
        return res;
    }
};
```
### python
```python
class Solution:
    def minPatches(self, nums: List[int], n: int) -> int:
        length = len(nums)
        idx = res = 0
        r = 1
        while r <= n:
            if idx < length and nums[idx] <= r:
                r += nums[idx]
                idx += 1
            else:
                res += 1
                r += r
        return res
```