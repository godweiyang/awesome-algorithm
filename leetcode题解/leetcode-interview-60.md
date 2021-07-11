---
title: 【每日算法Day 98】慈善赌神godweiyang教你算骰子点数概率！
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-12 20:41:45
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

## 题目链接
[LeetCode 面试题60. n个骰子的点数](https://leetcode-cn.com/problems/nge-tou-zi-de-dian-shu-lcof/ "LeetCode 面试题60. n个骰子的点数")

## 题目描述
把 `n` 个骰子扔在地上，所有骰子朝上一面的点数之和为 `s`。输入 `n`，打印出 `s` 的所有可能的值出现的概率。

你需要用一个浮点数数组返回答案，其中第 `i` 个元素代表这 `n` 个骰子所能掷出的点数集合中第 `i` 小的那个的概率。

**说明：**
* `1 <= n <= 11`

**示例1**
```text
输入：
1
输出:
[0.16667,0.16667,0.16667,0.16667,0.16667,0.16667]
```

**示例2**
```text
输入：
2
输出:
[0.02778,0.05556,0.08333,0.11111,0.13889,0.16667,0.13889,0.11111,0.08333,0.05556,0.02778]
```

## 题解
令 $dp[n][s]$ 表示投掷 $n$ 个骰子，点数为 $s$ 的方法数。那么可以根据最后一个骰子的点数情况（$1$ 到 $6$），递归进行计算：
$$
dp[n][s] = \sum_{i=1}^{6}{dp[n-1][s-i]}
$$
当然还得加一些约束，例如 $n-1$ 个骰子的点数范围是 $[n-1, 6(n-1)]$ ，所以一定有 $n-1 \le s-i \le 6(n-1)$ ，即 $s-6(n-1) \le i \le s-(n-1)$。所以综上 $i$ 的范围是 $\max{\{1, s-6(n-1)\}} \le i \le \min{\{6, s-(n-1)\}}$，最后的转移方程就是：
$$
dp[n][s] = \sum_{i=\max{\{1, s-6(n-1)\}}}^{\min{\{6, s-(n-1)\}}}{dp[n-1][s-i]}
$$
但是，考虑到在计算 $n-1$ 个骰子时，如果 $i < s-6(n-1)$ ，那么 $s-i > 6(n-1)$ ，也就是 $dp[n-1][s-i]$ 是根本不会被计算的。所以初始化的时候如果都是 $0$ ，那么就不用管这个下界了，也就是转移方程为：
$$
dp[n][s] = \sum_{i=1}^{\min{\{6, s-(n-1)\}}}{dp[n-1][s-i]}
$$
此外，因为每次计算只会用到 $n-1$ 个骰子的方法数，所以第一个维度可以省去。但是注意计算的时候 $s$ 就得逆序遍历了，这样才不会覆盖掉 $n-1$ 个骰子的方案数，造成后面的计算错误。

最后答案就是 $\frac{dp[n][s]}{6^n}$ 。

## 代码
### 动态规划+空间优化（c++）
```cpp
class Solution {
public:
    vector<double> twoSum(int n) {
        vector<int> dp(6*n+1, 0);
        for (int i = 1; i <= 6; ++i) dp[i] = 1;
        for (int i = 2; i <= n; ++i) {
            for (int s = 6*i; s >= i; --s) {
                dp[s] = 0;
                for (int j = 1; j <= min(6, s-i+1); ++j) {
                    dp[s] += dp[s-j];
                }
            }
        }
        double total = pow(6, n);
        vector<double> res;
        for (int s = n; s <= 6*n; ++s) {
            res.push_back(dp[s]/total);
        }
        return res;
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)