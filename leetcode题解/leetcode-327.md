---
title: 【LeetCode 327】区间和的个数
top: false
cover: false
toc: true
mathjax: true
date: 2020-11-07 17:49:30
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
给定一个整数数组 $nums$，返回区间和在 $[lower, upper]$ 之间的个数，包含 $lower$ 和 $upper$。
区间和 $S(i, j)$ 表示在 $nums$ 中，位置从 $i$ 到 $j$ 的元素之和，包含 $i$ 和 $j$ $(i \le j)$。

**示例1**
```text
输入：
nums = [-2,5,-1], lower = -2, upper = 2,
输出：
3
解释：
3个区间分别是: [0,0], [2,2], [0,2]，它们表示的和分别为: -2, -1, 2。
```

## 题解
最暴力的解法就是 $O(n^2)$ 枚举所有区间，然后判断，显然会超时。

对于区间 $[i, j]$ 来说，它的和可以表示为 $pre[j] - pre[i]$ ，其中 $pre[i]$ 是前缀和，表示第 $0$ 个到第 $i$ 个元素之和。

如果这个区间满足条件，那就有 $lower \le pre[j] - pre[i] \le upper$。如果我们遍历每个 $j$ 的话，只要求出满足 $pre[j] - upper \le pre[i] \le pre[j] - lower$ 的左端点 $i$ 的数量即可。

如果将 $pre$ 数组排好序，那就能直接用二分法找出满足条件的数量了。但是这里需要在遍历右端点 $j$ 的时候动态插入 $pre[j]$ ，然后只对 $pre[0]$ 到 $pre[j]$ 进行排序。而目前应该没有什么数据结构能很快的同时支持动态有序插入和二分查找。

因此这里可以采用树状数组（或线段树），来求取一段区间的和是多少。而树状数组的值表示这个数有没有出现过（0或1）。

对于注意到这里有负数出现，因此需要先做离散化，对于每个前缀和 $pre[i]$，只需要离散化三个值：$pre[i]$、$pre[i]-lower$、$pre[i]-upper$ 即可。

## 代码
### c++
```cpp
typedef long long ll;

class BIT {
private:
    static const int MAXN = 100010;
    int bit[MAXN];

public:
    BIT() {
        memset(bit, 0, sizeof bit);
    }

    int lowbit(int x) {
        return x&(-x);
    }

    void add(int i, int x) {
        while (i < MAXN) {
            bit[i] += x;
            i += lowbit(i);
        }
    }

    void sub(int i, int x) {
        while (i < MAXN) {
            bit[i] -= x;
            i += lowbit(i);
        }
    }

    int sum(int i) {
        int s = 0;
        while (i > 0) {
            s += bit[i];
            i -= lowbit(i);
        }
        return s;
    }
};

class ID {
private:
    unordered_map<ll, int> mp;
    set<ll> st;
    int idx;

public:
    ID() {
        mp.clear();
        st.clear();
        idx = 1;
    }

    void addNum(ll x) {
        st.insert(x);
    }

    void proj() {
        for (ll x: st) {
            mp[x] = idx++;
        }
    }

    int getID(ll x) {
        return mp[x];
    }
};

class Solution {
public:
    int countRangeSum(vector<int>& nums, int lower, int upper) {
        int n = nums.size();
        ll sum = 0, res = 0;
        ID id = ID();
        BIT bit = BIT();
        
        id.addNum(0);
        for (int i = 0; i < n; ++i) {
            sum += nums[i];
            id.addNum(sum);
            id.addNum(sum-lower);
            id.addNum(sum-upper);
        }
        id.proj();
        
        bit.add(id.getID(0), 1);
        sum = 0;
        for (int i = 0; i < n; ++i) {
            sum += nums[i];
            int lb = id.getID(sum-upper), rb = id.getID(sum-lower);
            res += bit.sum(rb) - bit.sum(lb-1);
            bit.add(id.getID(sum), 1);
        }
        return res;
    }
};
```
