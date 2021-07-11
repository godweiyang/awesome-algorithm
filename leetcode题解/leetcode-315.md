---
title: 每日算法系列【LeetCode 315】计算右侧小于当前元素的个数
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-08 19:37:46
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
给定一个整数数组 nums ，按要求返回一个新数组 counts 。数组 counts 有该性质： counts[i] 的值是 nums[i] 右侧小于 nums[i] 的元素的数量。

**示例1**
```text
输入：
[5,2,6,1]
输出：
[2,1,1,0]
解释：
5 的右侧有 2 个更小的元素 2 和 1 。
2 的右侧仅有 1 个更小的元素 1 。
6 的右侧有 1 个更小的元素 1 。
1 的右侧有 0 个更小的元素。
```

## 题解
这题没有给数据范围，但是用脚想也知道不能暴力做（ $O(n^2)$ ）。

这题其实有多种解法，这里简单介绍三种解法。

### 树状数组
如果你不熟悉这个数据结构的话，你只需要记住它的功能就行。

树状数组是一个数组，有两种操作。
一个是对某个位置的元素加值或减值，一个是查询第一个位置到某个位置的元素之和。
暴力的话每次查询操作复杂度都是 $O(n)$ ，而树状数组可以做到 $O(\log n)$ 。

具体细节不介绍了，有现成的模板，会用就行了。

有了这等好东西，就可以把问题这么转化了：
新建一个数组 bit ，其中 bit[i] 表示i这个数出现的次数。
从右边最后一个数开始向左遍历，每遇到一个数 nums[i] ，就把 bit[nums[i]] 加 1 ，表示这个数多了一个。
然后查询 bit[0] 到 bit[nums[i]-1] 中的所有次数之和，就表明了当前时刻有多少数比 nums[i] 小。
这一步查询操作正好用到树状数组，最后总的时间复杂度为 $O(n \log n)$ 。

### 归并排序
归并排序算法想必大家应该很熟悉了。
就是将数组划分为左右两个长度相等的子数组，然后分别递归排序，得到左右两个有序的子数组。
然后就是合并了，只要用两个头指针，分别指着两个子数组的开头，然后分别向右移动合并就行了。

那么在这题中怎么用呢？
假设左右两个子数组为 a[l], ..., a[m] 和 a[m+1], ..., a[r] ，头指针分别为 i = l 和 j = m + 1 。
然后开始合并，首先 j 向右移动，直到 a[j] >= a[i] ，也就是在右半部分子数组中找到所有小于 a[i] 的数。
然后把这些数依次放入临时数组中，并得到结论：右半部分子数组中比 a[i] 小的数有 j - m - 1 个。
然后把 a[i] 也推进临时数组里，重复进行上述过程，直到 i > m 。
最后如果右半部分数组还剩一些数，说明它们是最大的，推入临时数组就行了，最后把临时数组里的数复制进原数组，这部分就排好序了。

要注意的是排序后原来的下标会丢失，所以用一个 pair 类型保存每一个数和它原来的下标。

### 二叉搜索树
这种方法也很显然。
从最右边一个数开始构建二叉搜索树，结点保存这个数和右边比它小的数的数量。
如果新插入一个数，就插入到二叉搜索树中，沿途记得要更新经过的每个结点的数量。
如果经过一个结点，并且插入的数比结点的数小，那么就在左子树中继续寻找插入位置，并且结点数量加 1 。
如果插入的数比结点的数大，那么就在右子树中寻找，并且插入的数对应的答案加上该结点的数量。

具体这里就不实现了，主要考察的是数据结构，不想写了。。。

## 代码
### 树状数组
```cpp
class Solution {
public:
    static const int MAXN = 100000;
    int bit[MAXN], x[MAXN], y[MAXN];

    vector<int> countSmaller(vector<int>& nums) {
        int n = nums.size();
        for (int i = 0; i < n; ++i) {
            x[i] = y[i] = nums[i];
        }

        sort(y, y + n);
        int len = unique(y, y + n) - y;
        for (int i = 0; i < n; ++i) {
            x[i] = lower_bound(y, y + len, x[i]) - y + 1;
        }

        memset(bit, 0, sizeof bit);
        vector<int> res;
        for (int i = n-1; i >= 0; --i) {
            res.push_back(sum(x[i]-1));
            add(x[i], 1);
        }
        reverse(res.begin(), res.end());
        return res;
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
```

### 归并排序
```cpp
class Solution {
public:
    static const int MAXN = 100000;
    int cnt[MAXN];
    pair<int, int> x[MAXN], y[MAXN];

    vector<int> countSmaller(vector<int>& nums) {
        int n = nums.size();
        for (int i = 0; i < n; ++i) {
            x[i] = make_pair(nums[i], i);
        }
        memset(cnt, 0, sizeof cnt);
        merge_sort(0, n-1);
        vector<int> res(cnt, cnt+n);
        return res;
    }

    void merge_sort(int l, int r) {
        if (l >= r) return;
        int m = (l + r) >> 1;
        merge_sort(l, m);
        merge_sort(m+1, r);
        int idl = l, idr = m + 1, idx = l;
        while (idl <= m) {
            while (idr <= r && x[idr].first < x[idl].first) {
                y[idx++] = x[idr];
                idr++;
            }
            cnt[x[idl].second] += idr - m - 1;
            y[idx++] = x[idl];
            idl++;
        }
        for (int i = l; i < idr; ++i) {
            x[i] = y[i];
        }
    }
};
```
