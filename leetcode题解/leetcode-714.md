---
title: 每日算法系列【LeetCode 714】买卖股票的最佳时机含手续费
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-28 20:26:13
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
给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每次交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

**示例1**
```text
输入：
prices = [1, 3, 2, 8, 4, 9], fee = 2
输出：
8
解释：
能够达到的最大利润:  
在此处买入 prices[0] = 1
在此处卖出 prices[3] = 8
在此处买入 prices[4] = 4
在此处卖出 prices[5] = 9
总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
```

**提示**
* 0 < prices.length <= 50000.
* 0 < prices[i] < 50000.
* 0 <= fee < 50000.

## 题解
这是 **【买卖股票的最佳时机】** 系列题目的第五题。

这题其实就是在系列题目第二题基础上加了个手续费，也就是无限次买卖股票，但是每次卖的时候都得交一笔手续费。

这时候就不能和第二题一样，每次连续上升子区间都买卖一次了，因为如果买卖一次都不够交手续费的话，就不能进行买卖。或者一段连续下降子区间的差值小于手续费，那么就得把这段下降子区间也包含进去，前后只卖买一次。

所以我们换个思路，还是沿用第四题的动态规划思路。令 $dp0[i]$ 为第 $i$ 只股票之前（包含）买卖（最后一次操作是买）可以获得的最大利润， $dp1[i]$ 为第 $i$ 只股票之前（包含）买卖（最后一次操作是卖）可以获得的最大利润。那么类似的有如下转移方程：
$$
\begin{aligned}
dp0[i] &= \max{\left\{dp0[i-1], dp1[i-1] - price[i]\right\}} \\\
dp1[i] &= \max{\left\{dp1[i-1], dp0[i-1] + price[i] - fee\right\}}
\end{aligned}
$$

初始情况就是 $dp0[0] = -price[0]$ 和 $dp1[0] = 0$ 。

此外这里还可以优化去掉一个维度，因为每个时刻状态只与前一个时刻有关。

时间复杂度是 $O(n)$ 。

## 代码
### python
```python
class Solution:
    def maxProfit(self, prices: List[int], fee: int) -> int:
        dp0, dp1 = -prices[0], 0
        for p in prices[1:]:
            dp1 = max(dp1, dp0+p-fee)
            dp0 = max(dp0, dp1-p)
        return dp1
```