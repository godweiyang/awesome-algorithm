---
title: 每日算法系列【LeetCode 123】买卖股票的最佳时机 III
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-26 21:07:29
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
给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

**示例1**
```text
输入：
[3,3,5,0,0,3,1,4]
输出：
6
解释：
在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。
```

**示例2**
```text
输入：
[1,2,3,4,5]
输出：
4
解释：
在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。   
注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。   
因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
```

**示例3**
```text
输入：
[7,6,4,3,1]
输出：
0
解释：
在这个情况下, 没有交易完成, 所以最大利润为 0。
```

## 题解
这是 **【买卖股票的最佳时机】** 系列题目的第三题。

本题中买卖次数变成了最多两次，那么我们可以照搬之前只能买卖一次的做法。首先如果我们假设第一只股票卖出去时价格是 $price[i]$ ，那么它之前的最优买入价格（也就是最低的价格）计算方法和第一题相同，只需要用一个变量存储就行了。而第二次买卖我们只需要知道 $price[i]$ 右边进行一次买卖最多能赚到多少钱就行了。这可以通过从右向左倒过来预处理处理，方法和第一题完全相同。

记第 $i$ 只股票左边（包含）买卖一次最大利润为 $left[i]$ ，右边（包含）买卖一次最大利润为 $right[i]$ ，那么最终的答案就是：
$$
\sum_{0 < i < n-2}{left[i] + right[i+1]}
$$

时间复杂度是 $O(n)$ 。

## 代码
### python
```python
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        n = len(prices)
        if n == 0: return 0
        dp = [0] * n
        minn = prices[0]
        for i in range(1, n):
            dp[i] = max(dp[i-1], prices[i]-minn)
            minn = min(minn, prices[i])
        maxx, maxp, res = prices[-1], 0, max(dp)
        for i in range(n-2, 0, -1):
            maxp = max(maxp, maxx-prices[i])
            maxx = max(maxx, prices[i])
            res = max(res, dp[i-1]+maxp)
        return res
```