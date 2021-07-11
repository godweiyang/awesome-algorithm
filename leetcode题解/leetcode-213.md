---
title: 【每日算法Day 105】打家劫舍第二弹：看好你的电瓶车！
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-19 12:18:19
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

## 题目链接
[LeetCode 213. 打家劫舍 II](https://leetcode-cn.com/problems/house-robber-ii/ "LeetCode 213. 打家劫舍 II")

**往期回顾：打家劫舍 I ：**  
[【每日算法Day 104】偷电瓶的周某今天放出来了，还不赶紧做这道题防范一下！](https://godweiyang.com/2020/04/18/leetcode-198/ "【每日算法Day 104】偷电瓶的周某今天放出来了，还不赶紧做这道题防范一下！")

## 题目描述
你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都围成一圈，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你在不触动警报装置的情况下，能够偷窃到的最高金额。

**示例1**
```text
输入：
[2,3,2]
输出:
3
```

**示例2**
```text
输入：
[1,2,3,1]
输出:
4
```

## 题解
这题和上一题唯一区别就是首尾只能选一个偷，那么我们可以分为两种情况。

如果不偷第一个，那么问题就变成了在后 $n-1$ 个里面偷取的最大价值。

如果不偷最后一个，那么问题就变成了在前 $n-1$ 个里面偷取的最大价值。

而这两个转化后的问题就没有首尾连接的约束了，可以直接采用上一题的解法求解，转移方程还是：
$$
dp[i] = \max{\{dp[i-1], dp[i-2]+p[i]\}}
$$

最终取两种情况的较大值就行了。

## 代码
### c++
```cpp
class Solution {
public:
    int rob1(vector<int>& nums, int l, int r) {
        int prepre = 0, pre = 0, now = 0;
        for (int i = l; i <= r; ++i) {
            now = max(pre, prepre+nums[i]);
            prepre = pre;
            pre = now;
        }
        return now;
    }

    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 1) return nums[0];
        int a = rob1(nums, 0, n-2);
        int b = rob1(nums, 1, n-1);
        return max(a, b);
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)