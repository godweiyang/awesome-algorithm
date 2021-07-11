---
title: 【每日算法Day 104】偷电瓶的周某今天放出来了，还不赶紧做这道题防范一下！
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-18 13:18:51
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 偷电瓶的周某今天（2020.04.18）出来啦，打工是不可能打工的，这辈子都不可能打工的，大家可要小心咯。
今天开始讲解 LeetCode **打家劫舍**系列三道题目，给大家防范一下！

## 题目链接
[LeetCode 198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/ "LeetCode 198. 打家劫舍")

## 题目描述
> 题面描述略有改动，不影响题意。

你是一个专业的小偷，计划偷窃沿路的电瓶车电瓶。每个电瓶价值不一样，影响你偷窃的唯一制约因素就是相邻的电瓶车装有相互连通的防盗系统，如果两辆相邻的电瓶车的电瓶同时被偷，系统会自动报警。

给定一个代表每辆电瓶车电瓶价值的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

**示例1**
```text
输入：
[1,2,3,1]
输出:
4
```

**示例2**
```text
输入：
[2,7,9,3,1]
输出:
12
```

## 题解
用 $dp[i]$ 表示偷前 $i$ 辆车电瓶可以获得的最大价值，那么对于第 $i$ 辆车来说，有两种选择。

如果偷第 $i$ 辆车的电瓶，那么第 $i-1$ 辆车电瓶就不能偷了，能获得的最大价值就是 $dp[i-2] + p[i]$ 。

如果不偷第 $i$ 辆车的电瓶，那么最大价值就等价于偷前 $i-1$ 辆车的电瓶能获得的最大价值 $dp[i-1]$ 。

所以最终取两者最大值即可：
$$
dp[i] = \max{\{dp[i-1], dp[i-2]+p[i]\}}
$$

可以发现，每次计算其实只要用到前两个元素，所以每次维护最后两个值即可，可以将空间优化到常数空间。

## 代码
### c++
```cpp
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        int prepre = 0, pre = 0, now = 0;
        for (int i = 0; i < n; ++i) {
            now = max(pre, prepre+nums[i]);
            prepre = pre;
            pre = now;
        }
        return now;
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)