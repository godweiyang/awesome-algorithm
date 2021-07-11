---
title: 【每日算法Day 88】超越妹妹教你如何做这道排序题
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-02 00:48:56
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

![每日一图：我老婆可爱不？](1.jpg)

## 题目链接
[LeetCode 面试题 16.16. 部分排序](https://leetcode-cn.com/problems/sub-sort-lcci/ "LeetCode 面试题 16.16. 部分排序")

## 题目描述
给定一个整数数组，编写一个函数，找出索引 `m` 和 `n`，只要将索引区间 `[m, n]` 的元素排好序，整个数组就是有序的。注意：`n-m` 尽量最小，也就是说，找出符合条件的最短序列。函数返回值为 `[m, n]`，若不存在这样的 `m` 和 `n`（例如整个数组是有序的），请返回 `[-1, -1]`。

**示例1**
```text
输入：
[1,2,4,7,10,11,7,12,6,7,16,18,19]
输出:
[3,9]
```

**说明：**
* `0 <= len(array) <= 1000000`

## 题解
首先虽然题目没说，但是实际运行下来数列是单调递增的，所以我们下面默认数列是递增的。

那么对于元素 `a[i]` 来说，如果它左边存在大于 `a[i]` 的元素，那么 `a[i]` 是一定要参与到排序里去的。或者说如果它右边存在小于 `a[i]` 的元素，那么 `a[i]` 也是要参与到排序里去的。

所以我们只需要寻找最靠右的那个数（满足左边存在大于它的数），和最靠左的那个数（满足右边存在小于它的数），那么这两个数之间就是要排序的区间了。

为什么最靠右的那个（满足左边存在大于它的数）数一定能保证右边没有更小的数了呢？因为如果右边还有更小的数，那么那个更小的数才是更靠右的啊，这就矛盾了。

所以我们只需要从左到右扫描一遍，用一个变量维护一下最大值就行了，然后反向再遍历一遍，维护一个最小值。

## 代码
### c++
```cpp
class Solution {
public:
    vector<int> subSort(vector<int>& array) {
        int n = array.size();
        int maxx = INT_MIN, minn = INT_MAX;
        int l = -1, r = -1;
        for (int i = 0; i < n; ++i) {
            if (array[i] < maxx) r = i;
            else maxx = array[i];
        }
        for (int i = n-1; i >= 0; --i) {
            if (array[i] > minn) l = i;
            else minn = array[i];
        }
        return {l, r};
    }
};
```

### python
```python
class Solution:
    def subSort(self, array: List[int]) -> List[int]:
        n = len(array)
        maxx, minn = -10000000, 10000000
        l, r = -1, -1
        for i in range(n):
            if array[i] < maxx: r = i
            else: maxx = array[i]
        for i in range(n-1, -1, -1):
            if array[i] > minn: l = i
            else: minn = array[i]
        return [l, r]
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)