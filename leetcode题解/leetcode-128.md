---
title: 每日算法系列【LeetCode 128】最长连续序列
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-03 11:30:51
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
给定一个未排序的整数数组，找出最长连续序列的长度。

要求算法的时间复杂度为 $O(n)$。

**示例1**
```text
输入：
[100, 4, 200, 1, 3, 2]
输出：
4
解释：
最长连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

## 题解
### 哈希表
因为题目要求 $O(n)$ 的时间复杂度，所以不能排序。

我们可以遍历每个数 $x$，假设它是某个连续序列的开头，那么首先要满足 $x-1$ 不在数组中，然后从 $x+1$ 开始逐渐增大，看最大多少还在数组里。

实现上查询数字在不在数组里可以采用哈希表，复杂度是 $O(1)$ 的。虽然看起来遍历每个数是 $O(n)$ ，以它为开头逐渐增大又是 $O(n)$ ，但是我们其实只会对开头的数遍历最大能达到多少。这样两层循环总的遍历次数其实还是 $O(n)$ 的。

总的时间复杂度就是 $O(n)$ 。

### 并查集
我们可以把任意两个相差为 $1$ 的数之间连上边，那么数组就变成了若干个子树，我们只需要求结点数量最多的那个子树就行了。

用并查集可以实现连接两个连续序列，合并成一个连续序列，并且快速查询这个序列长度是多少。

首先初始的时候，数组中的每个数都自成一个子树（它自己就是根结点）。然后遍历每一个数 $x$ ，如果 $x+1$ 也在数组中，那就合并这两个数所在的子树，并且统计合并后的子树大小。

总的时间复杂度也是 $O(n)$ 。

## 代码
### 哈希表（c++）
```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_map<int, int> mp;
        for (auto x : nums) mp[x] = 1;
        int res = 0;
        for (auto x : nums) {
            if (mp.count(x-1)) continue;
            int y = x;
            while (mp.count(++y));
            res = max(res, y-x);
        }
        return res;
    }
};
```

### 并查集（c++）
```cpp
class Solution {
public:
    unordered_map<int, int> fa, cnt;

    int find(int x) {
        return x==fa[x] ? x : fa[x]=find(fa[x]);
    }

    int merge(int x, int y) {
        x = find(x);
        y = find(y);
        if (x == y) return cnt[x];
        fa[y] = x;
        cnt[x] += cnt[y];
        return cnt[x];
    }

    int longestConsecutive(vector<int>& nums) {
        if (!nums.size()) return 0;
        for (auto x : nums) {
            fa[x] = x;
            cnt[x] = 1;
        }
        int res = 1;
        for (auto x : nums) {
            if (fa.count(x+1)) {
                res = max(res, merge(x, x+1));
            }
        }
        return res;
    }
};
```
