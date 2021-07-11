---
title: 每日算法系列【LeetCode 121】买卖股票的最佳时机
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-24 20:26:13
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
给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。

注意你不能在买入股票前卖出股票。

**示例1**
```text
输入：
[7,1,5,3,6,4]
输出：
5
解释：
在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
```

**示例2**
```text
输入：
[7,6,4,3,1]
输出：
0
解释：
在这种情况下, 没有交易完成, 所以最大利润为 0。
```

## 题解
这是 **【买卖股票的最佳时机】** 系列题目的第一题。

这道题目要求只能买卖一次股票。所以最佳策略一定是挑一个最低的价格买入，再挑一个最高的价格卖出。但是还有另一个限制条件，那就是你得先买，然后才能卖出去。

所以我们只需要枚举每个股票，把它当作卖出的那一只股票，然后只需要求出它之前价格最低的那一只股票就行了。价格最低的股票可以用一个变量来进行维护，然后枚举所有卖出的股票，减去它之前价格最低的那一只股票，然后和最优答案进行比较，最终就能得到最多能赚多少钱了。

最终答案就是：
$$
\max_{0 \le i < n}{\left\{price[i] - \min_{0 \le j < i}{\left\{price[j]\right\}}\right\}}
$$

时间复杂度是 $O(n)$ 。

## 代码
### python
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        if n == 0: return 0
        minn, res = prices[0], 0
        for i in range(1, n):
            res = max(res, prices[i]-minn)
            minn = min(minn, prices[i])
        return res
```