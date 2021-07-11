---
title: 每日算法系列【LeetCode 309】最佳买卖股票时机含冷冻期
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-29 20:26:13
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
给定一个整数数组，其中第 i 个元素代表了第 i 天的股票价格 。​

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

**示例1**
```text
输入：
[1,2,3,0,2]
输出：
3
解释：
对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```

## 题解
这是 **【买卖股票的最佳时机】** 系列题目的第六题。

这题其实就是在系列题目第二题基础上加了个限制条件，也就是每次卖了之后，隔一天才能继续买。

模仿第五题，还是采用动态规划。令 $dp0[i]$ 为第 $i$ 只股票之前（包含）买卖（最后一次操作是买）可以获得的最大利润， $dp1[i]$ 为第 $i$ 只股票之前（包含）买卖（最后一次操作是卖）可以获得的最大利润。那么类似的有如下转移方程：
$$
\begin{aligned}
dp0[i] &= \max{\left\{dp0[i-1], dp1[i-2] - price[i]\right\}} \\\
dp1[i] &= \max{\left\{dp1[i-1], dp0[i-1] + price[i]\right\}}
\end{aligned}
$$

初始情况就是 $dp0[0] = -price[0]$ 和 $dp1[0] = 0$ 。

和第二、五题区别就是 $dp0[i]$ 的转移方程中有个 $dp1[i-2]$ ，也就是得隔一项，去算前前一天之前（包含）的买卖最大利润。

时间复杂度是 $O(n)$ 。

## 代码
### python
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        if n == 0: return 0
        dp0 = [-prices[0]] * n
        dp1 = [0] * n
        for i in range(1, n):
            dp0[i] = max(dp0[i-1], -prices[i])
            if i >= 2:
                dp0[i] = max(dp0[i], dp1[i-2]-prices[i])
            dp1[i] = max(dp1[i-1], dp0[i-1]+prices[i])
        return dp1[n-1]
```