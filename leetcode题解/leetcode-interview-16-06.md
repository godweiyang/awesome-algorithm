---
title: 【每日算法Day 97】经典面试题：求两个数组最小差
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-11 21:22:08
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

## 题目链接
[LeetCode 面试题 16.06. 最小差](https://leetcode-cn.com/problems/smallest-difference-lcci/ "LeetCode 面试题 16.06. 最小差")

## 题目描述
给定两个整数数组 `a` 和 `b`，计算具有最小差绝对值的一对数值（每个数组中取一个值），并返回该对数值的差。

**说明：**
* `1 <= a.length, b.length <= 100000`
* `-2147483648 <= a[i], b[i] <= 2147483647`
* 正确结果在区间 `[-2147483648, 2147483647]` 内

**示例1**
```text
输入：
{1, 3, 15, 11, 2}, {23, 127, 235, 19, 8}
输出:
3，即数值对(11, 8)
```

## 题解
首先最暴力的方法就是 $O(n^2)$ 暴力枚举两个数组中的所有数对，然后计算绝对值最小的差值，这样显然是会超时的。

所以我们先分别对两个数组从小到大进行排序，然后用双指针的方法来计算。
* 初始的时候 $i = 0, j = 0$ 分别指着两个数组的第一个元素。
* 然后计算 $a[i]-b[j]$ 的绝对值，如果比当前最小值还要小，就更新最小值。
* 然后判断 $a[i]$ 和 $b[j]$ 的大小关系。如果 $a[i] > b[j]$ ，那么如果增大 $i$ ，差值只会越来越大，所以只能增大 $j$ 。同理如果 $a[i] < b[j]$ ，那就增大 $i$ 。
* 最后如果其中一个数组遍历完了就结束遍历。

是不是有点类似归并排序的合并数组过程？但是这里有个区别，最后遍历完之后，一定会有某个数组还没遍历完。而那些没遍历的数字其实都大于另一个数组中最大的数，所以没有必要再和另一个数组最大值做差值了。这样差值只会越来越大，没有意义。

最终的时间复杂度就是 $O(n \log n)$ 。

## 代码
### c++
```cpp
class Solution {
public:
    int smallestDifference(vector<int>& a, vector<int>& b) {
        sort(a.begin(), a.end());
        sort(b.begin(), b.end());
        int n = a.size(), m = b.size();
        int i = 0, j = 0;
        long res = INT_MAX;
        while (i < n && j < m) {
            res = min(res, abs(long(a[i])-long(b[j])));
            if (a[i] > b[j]) ++j;
            else ++i;
        }
        return res;
    }
};
```

### python
```python
class Solution:
    def smallestDifference(self, a: List[int], b: List[int]) -> int:
        a.sort()
        b.sort()
        n, m = len(a), len(b)
        i, j, res = 0, 0, 2147483647
        while i < n and j < m:
            res = min(res, abs(a[i]-b[j]))
            if a[i] > b[j]: j += 1
            else: i += 1
        return res
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)