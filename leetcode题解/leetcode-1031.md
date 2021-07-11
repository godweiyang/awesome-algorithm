---
title: 每日算法系列【LeetCode 1031】两个非重叠子数组的最大和
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-15 20:56:17
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
给出非负整数数组 A ，返回两个非重叠（连续）子数组中元素的最大和，子数组的长度分别为 L 和 M。（这里需要澄清的是，长为 L 的子数组可以出现在长为 M 的子数组之前或之后。）

**示例1**
```text
输入：
A = [0,6,5,2,2,5,1,9,4], L = 1, M = 2
输出：
20
解释：
子数组的一种选择中，[9] 长度为 1，[6,5] 长度为 2。
```

**示例2**
```text
输入：
A = [3,8,1,3,2,1,8,9,0], L = 3, M = 2
输出：
29
解释：
子数组的一种选择中，[3,8,1] 长度为 3，[8,9] 长度为 2。
```

**示例3**
```text
输入：
A = [2,1,5,6,0,9,5,0,3,8], L = 4, M = 3
输出：
31
解释：
子数组的一种选择中，[5,6,0,9] 长度为 4，[0,3,8] 长度为 3。
```

**提示**
* L >= 1
* M >= 1
* L + M <= A.length <= 1000
* 0 <= A[i] <= 1000

## 题解
这题意思就是找到两段给定长度的、不重合的、连续的区间，使得两段区间和最大。

因为长度是给定的，所以我们只需要预处理好前缀和 sum ，然后给定区间右端点，就可以直接算出区间和。
那么如果枚举两段区间的右端点，时间复杂度也才 $O(n^2)$ ，极限情况下也就 1e6 左右，貌似也还可以接受。

那有没有更快的方法呢？试试动态规划！因为两段区间有前后顺序，我们不妨假设长度为 L 的区间在后面。
用 dpm[i] 表示前 i 个数中长度为 M 的区间和的最大值。
那么 dpm[i] = max{dpm[i-1], sum[i] - sum[i-M]} ，也就是要么取最后 M 个数，要么最后一个数不取，在前 i - 1 个数里面找答案。
然后 dpm 全部处理完之后，遍历数组，假设长度为 L 的区间以 A[i] 结束，那么我们只需要在 A[0] 到 A[i-L] 中间找长度为 M 的区间最大和就行了，那答案不就是上面求好的 dpm[i-L] 吗？这样最终时间复杂度就是 $O(n)$ 了。

结束了吗？并没有！空间还能不能优化呢？其实当我们遍历长度为 L 的区间时，长度为 M 的区间不用每次都重新遍历，可以重复利用之前的结果，每次向右移动直到和长度为 L 的区间衔接上为止。这样就等于用了两个指针，分别指向了两个区间的右端点，总共最多移动 2n 次就行了。

## 代码
### 动态规划（c++）
```cpp
class Solution {
public:
    static const int N = 1010;
    int dpl[N], dpm[N], sum[N];

    int maxSumTwoNoOverlap(vector<int>& A, int L, int M) {
        memset(dpl, 0, sizeof dpl);
        memset(dpm, 0, sizeof dpm);
        sum[0] = 0;
        int n = A.size();
        for (int i = 0; i < n; ++i) {
            sum[i+1] = sum[i] + A[i];
        }
        dpl[L] = sum[L];
        dpm[M] = sum[M];
        int res = 0;
        for (int i = 0; i < n; ++i) {
            if (i >= L) {
                dpl[i+1] = max(dpl[i], sum[i+1] - sum[i+1-L]);
                res = max(res, sum[i+1] - sum[i+1-L] + dpm[i+1-L]);
            }
            if (i >= M) {
                dpm[i+1] = max(dpm[i], sum[i+1] - sum[i+1-M]);
                res = max(res, sum[i+1] - sum[i+1-M] + dpl[i+1-M]);
            }
        }
        return res;
    }
};
```

### 动态规划（python）
```python
class Solution:
    def maxSumTwoNoOverlap(self, A: List[int], L: int, M: int) -> int:
        N = 1010
        dpl = [0] * N
        dpm = [0] * N
        sum = [0] * N
        n = len(A)
        for i in range(n):
            sum[i+1] = sum[i] + A[i]
        dpl[L] = sum[L]
        dpm[M] = sum[M]
        res = 0
        for i in range(n):
            if i >= L:
                dpl[i+1] = max(dpl[i], sum[i+1] - sum[i+1-L])
                res = max(res, sum[i+1] - sum[i+1-L] + dpm[i+1-L])
            if i >= M:
                dpm[i+1] = max(dpm[i], sum[i+1] - sum[i+1-M])
                res = max(res, sum[i+1] - sum[i+1-M] + dpl[i+1-M])
        return res
```

### 指针法（c++）
```cpp
class Solution {
public:
    static const int N = 1010;
    int sum[N];

    int maxSumTwoNoOverlap(vector<int>& A, int L, int M) {
        sum[0] = 0;
        int n = A.size();
        for (int i = 0; i < n; ++i) {
            sum[i+1] = sum[i] + A[i];
        }
        int lmax = 0, mmax = 0, res = 0;
        for (int i = L+M; i <= n; ++i) {
            lmax = max(lmax, sum[i-M] - sum[i-M-L]);
            mmax = max(mmax, sum[i-L] - sum[i-L-M]);
            res = max(res, lmax + sum[i] - sum[i-M]);
            res = max(res, mmax + sum[i] - sum[i-L]);
        }
        return res;
    }
};
```

### 指针法（python）
```python
class Solution:
    def maxSumTwoNoOverlap(self, A: List[int], L: int, M: int) -> int:
        N = 1010
        sum = [0] * N
        n = len(A)
        for i in range(n):
            sum[i+1] = sum[i] + A[i]
        lmax = mmax = res = 0
        for i in range(L+M, n+1):
            lmax = max(lmax, sum[i-M] - sum[i-M-L])
            mmax = max(mmax, sum[i-L] - sum[i-L-M])
            res = max(res, lmax + sum[i] - sum[i-M])
            res = max(res, mmax + sum[i] - sum[i-L])
        return res
```

## 后记
思考问题要从简单往困难思考，先想想暴力怎么做？再想想怎么优化它。就算做出来了，也不要拘泥于一种解法，还有代码上能否优化？变量、写法上能否更优美一点？

当然很熟练了之后这些都不用考虑了，上来像我一样直接一步到位就行了，嘻嘻。