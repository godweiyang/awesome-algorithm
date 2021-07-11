---
title: 每日算法系列【LeetCode 875】爱吃香蕉的珂珂
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-19 13:03:58
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
珂珂喜欢吃香蕉。这里有 N 堆香蕉，第 i 堆中有 piles[i] 根香蕉。警卫已经离开了，将在 H 小时后回来。

珂珂可以决定她吃香蕉的速度 K （单位：根/小时）。每个小时，她将会选择一堆香蕉，从中吃掉 K 根。如果这堆香蕉少于 K 根，她将吃掉这堆的所有香蕉，然后这一小时内不会再吃更多的香蕉。  

珂珂喜欢慢慢吃，但仍然想在警卫回来前吃掉所有的香蕉。

返回她可以在 H 小时内吃掉所有香蕉的最小速度 K（K 为整数）。

**示例1**
```text
输入：
piles = [3,6,7,11], H = 8
输出：
4
```

**示例2**
```text
输入：
piles = [30,11,23,4,20], H = 5
输出：
30
```

**示例3**
```text
输入：
piles = [30,11,23,4,20], H = 6
输出：
23
```

**提示**
* 1 <= piles.length <= 10^4
* piles.length <= H <= 10^9
* 1 <= piles[i] <= 10^9


## 题解
简单复述一下题意，就是有 N 堆香蕉，每堆有 piles[i] 个，现在要求一个整数速度 K ，吃一堆香蕉要的时间是 piles[i] / K （不是整数要上取整），问使得吃完所有香蕉所需总时间小于等于 H 的最小速度 K 是多少？

显然 K 越小，吃每堆香蕉所需要的时间就越长，总时间也就越长，那么自然而然可以想到二分答案 K 。

对于当前的 K ，我们遍历数组，算出总时间，如果总时间大于 H ，那就说明 K 太小了，还得提速；如果总时间小于等于 H ，那就说明速度 K 还可以降一点，总时间可能不变（因为存在上取整），也可能变大。

这样最终的时间复杂度仅仅只有 $O(n \log \mathcal{M})$ ，其中 $\mathcal{M}$ 是数组中的最大值，也就是二分上界。但其实这里还可以优化一下二分的上下界，比如上界，最大其实就是数组中的最大元素大小， K 再大也没有意义了。

## 代码
### c++
```cpp
class Solution {
public:
    int minEatingSpeed(vector<int>& piles, int H) {
        int maxv = *max_element(piles.begin(), piles.end());
        int n = piles.size();
        int l = 1, r = maxv;
        while (l < r) {
            int m = (r - l) / 2 + l;
            int cnt = 0;
            for (int i = 0; i < n; ++i) {
                cnt += (piles[i] + m - 1) / m;
            }
            if (cnt > H) l = m + 1;
            else r = m;
        }
        return r;
    }
};
```

### python
```python
class Solution:
    def minEatingSpeed(self, piles: List[int], H: int) -> int:
        maxv = max(piles)
        l = 1
        r = maxv
        while l < r:
            m = (r - l) // 2 + l
            cnt = 0
            for p in piles:
                cnt += (p + m - 1) // m
            if cnt > H:
                l = m + 1
            else:
                r = m
        return r
```

## 后记
注意上面的代码还是有几个小细节的：
* 二分终止条件设置的是 $l >= r$ ，所以 $l$ 的更新必须是 $l = m + 1$ ，因为如果 $l = r - 1$ 的话，$m$ 会等于 $l$ 。
* 为了防止整型溢出，计算 $l$ 和 $r$ 均值的时候不要写 $(l + r) / 2$ 。
* 上取整简单写法就是 $(p + m - 1) / m$ 。