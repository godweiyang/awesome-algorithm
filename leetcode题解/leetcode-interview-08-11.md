---
title: 【每日算法Day 109】五大解法，带你深入了解完全背包方案数
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-23 14:40:14
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 今天这题是完全背包问题 + 背包问题方案数，我一共列举了 5 种解法，层层递进优化。并且从两个角度殊途同归，最终优化到同一个式子。强烈建议掌握，对理解背包问题有很大帮助。

## 题目链接
[LeetCode 面试题 08.11. 硬币](https://leetcode-cn.com/problems/coin-lcci/ "LeetCode 面试题 08.11. 硬币")

## 题目描述
给定数量不限的硬币，币值为 `25` 分、`10` 分、`5` 分和 `1` 分，编写代码计算 `n` 分有几种表示法。(结果可能会很大，你需要将结果模上 `1000000007`)

**说明：**  
* `0 <= n (总金额) <= 1000000`

**示例1**
```text
输入：
n = 5
输出:
2
解释：
有两种方式可以凑成总金额:
5=5
5=1+1+1+1+1
```

**示例2**
```text
输入：
n = 10
输出:
4
解释：
有四种方式可以凑成总金额:
10=10
10=5+5
10=5+1+1+1+1+1
10=1+1+1+1+1+1+1+1+1+1
```

## 题解
首先我们规定一些记号，用 $p[i]$ 来表示第 $i$ 种硬币的面值，用 $dp[i][j]$ 表示用前 $i$ 种硬币组成面值 $j$ 的方案数。令 $n$ 表示需要表示的面值，$m$ 表示硬币数。

### 朴素想法（错误）
首先我们可以想到，最朴素的方法不就是**组成面值 $j$ 的方案数等于所有组成面值 $j-p[k]$ 的方案数之和**：
$$
dp[i][j] = \sum_{k=0}^{i}{dp[i][j-p[k]]}
$$

但是这样有个很严重的问题，就是会产生重复计算，也就是将 `6 = 1 + 5` 和 `6 = 5 + 1` 视为两种情况。

### 动态规划 1
朴素想法的纠正方法就是，**规定拆解后的数字是有序的**，这样就不会出现重复计算了。

那么具体怎么实现呢？其实只需要加上一个约束，也就是强制令 $p[k]$ 为组成面值 $j$ 的最大面值硬币。那么用掉它之后，组成面值 $j-p[k]$ 的最大面值硬币仍然只能是 $p[k]$ ，这样转移下去就一定是有序的，不会出现面值突然增大的情况。转移方程只需要修改一下转移后的可用硬币 $i$：
$$
dp[i][j] = \sum_{k=0}^{i}{dp[k][j-p[k]]}
$$

时间复杂度 $O(nm^2)$ ，空间复杂度 $O(nm)$ 。

### 动态规划 2（超时）
另一条思考路线是，我们**假设第 $i$ 个硬币用 $k$ 枚，然后枚举所有的 $k$** 就行了。转移方程很好写：
$$
dp[i][j] = \sum_{k=0}^{\lfloor j/p[i] \rfloor}{dp[i-1][j-k \cdot p[i]]}
$$

但是这样时间复杂度太高了，直接超时。

时间复杂度 $O(n^2m)$ ，空间复杂度 $O(nm)$ 。

### 转移方程优化
> 神奇的地方来了，上面两种方法，全部可以优化为同一个式子，仔细看好了。

**动态规划 1：**  

首先看第一个方法，转移方程为：
$$
dp[i][j] = \sum_{k=0}^{i}{dp[k][j-p[k]]}
$$
我们令 $i = i - 1$ ，又可以得到：
$$
dp[i-1][j] = \sum_{k=0}^{i-1}{dp[k][j-p[k]]}
$$
两式左右两边相减可以得到：
$$
dp[i][j] = dp[i-1][j] + dp[i][j-p[i]]
$$

**动态规划 2：**  

再看第二个方法，转移方程为：
$$
dp[i][j] = \sum_{k=0}^{\lfloor j/p[i] \rfloor}{dp[i-1][j-k \cdot p[i]]}
$$
令 $j = j - p[i]$ ，又可以得到：
$$
dp[i][j-p[i]] = \sum_{k=1}^{\lfloor j/p[i] \rfloor}{dp[i-1][j-k \cdot p[i]]}
$$
两式左右两边相减可以得到：
$$
dp[i][j] = dp[i-1][j] + dp[i][j-p[i]]
$$

**最终形式：**  

所以，最终**两个方法消去求和之后，形式是一样的！**都是：
$$
dp[i][j] = dp[i-1][j] + dp[i][j-p[i]]
$$

时间复杂度 $O(nm)$ ，空间复杂度 $O(nm)$ 。

### 空间优化
注意到，上面转移方程每个时刻 $i$ 其实只和 $i-1$ 还有 $i$ 时刻有关，所以可以把第一个维度消除掉。这样转移方程就变为了：
$$
dp[j] = dp[j] + dp[j-p[i]]
$$
但是需要特别注意的是，这里一共有三项，分别表示的是第 $i$ 时刻、第 $i-1$ 时刻、第 $i$ 时刻。所以在两层循环遍历的时候，**第一层循环必须是遍历硬币 $i$ ，第二层才是遍历组成的面值 $j$ ，这样才不会导致第 $i-1$ 时刻的值被覆盖掉无法访问。**

时间复杂度 $O(nm)$ ，空间复杂度 $O(n)$ 。

### 数学法
这个方法就只针对本题硬币种类比较少的情况了。

假设组成面值 $n$ 需要 $i$ 枚 `25` 分， $a$ 枚 `10` 分， $b$ 枚 `5` 分， $c$ 枚 `1` 分，那么有：
$$
n = 25i + 10a + 5b + c
$$
这里 $i$ 我们是需要枚举的，范围是 $[0, \lfloor n/25 \rfloor]$ ，所以我们令 $r = n - 25i$，那么就得到了：
$$
r = 10a + 5b + c
$$
那么 $a$ 的范围是 $[0, \lfloor r/10 \rfloor]$ 。而 $a$ 确定了之后， $b$ 的范围就是 $[0, \lfloor (r-10a)/5 \rfloor]$ 。而 $a, b$ 都确定了之后， $c$ 是唯一确定了的。所以最终的方案数就是：
$$
\begin{aligned}
\sum_{a=0}^{\lfloor r/10 \rfloor}{\sum_{b=0}^{\lfloor (r-10a)/5 \rfloor}{1}} &= \sum_{a=0}^{\lfloor r/10 \rfloor}{(\lfloor (r-10a)/5 \rfloor + 1)}\\\
&= \sum_{a=0}^{\lfloor r/10 \rfloor}{(\lfloor r/5 \rfloor -2a + 1)}\\\
&= (\lfloor r/10 \rfloor + 1)(\lfloor r/5 \rfloor + 1) - (\lfloor r/10 \rfloor + 1)\lfloor r/10 \rfloor \\\
& =(\lfloor r/10 \rfloor + 1)(\lfloor r/5 \rfloor - \lfloor r/10 \rfloor + 1)
\end{aligned}
$$

所以最终我们遍历 $i \in [0, \lfloor n/25 \rfloor]$，然后令 $r = n - 25i$。接着令 $x = \lfloor r/10 \rfloor$，$y = \lfloor r/5 \rfloor$，最后对 $(x+1)(y-x+1)$ 进行累加就行了：
$$
\sum_{i=0}^{\lfloor n/25 \rfloor}{(x+1)(y-x+1)}
$$

时间复杂度 $O(n)$ ，空间复杂度 $O(1)$ 。

## 代码
### 动态规划 1（c++）
```cpp
class Solution {
public:
    typedef long long ll;
    static const ll mod = 1e9 + 7;
    static const int N = 1000010;
    static const int M = 4;

    ll dp[M][N];
    int p[M] = {1, 5, 10, 25};

    int waysToChange(int n) {
        memset(dp, 0, sizeof dp);
        for (int i = 0; i < M; ++i) dp[i][0] = 1;
        for (int i = 0; i < M; ++i) {
            for (int j = 1; j <= n; ++j) {
                for (int k = 0; k <= i; ++k) {
                    if (j >= p[k]) {
                        (dp[i][j] += dp[k][j-p[k]]) %= mod;
                    }
                }
            }
        }
        return dp[M-1][n];
    }
};
```

### 动态规划 2（超时）（c++）
```cpp
class Solution {
public:
    typedef long long ll;
    static const ll mod = 1e9 + 7;
    static const int N = 1000010;
    static const int M = 4;

    ll dp[M][N];
    int p[M] = {1, 5, 10, 25};

    int waysToChange(int n) {
        memset(dp, 0, sizeof dp);
        for (int i = 0; i < M; ++i) dp[i][0] = 1;
        for (int i = 0; i <= n/p[0]; ++i) dp[0][i*p[0]] = 1;
        for (int i = 1; i < M; ++i) {
            for (int j = 1; j <= n; ++j) {
                for (int k = 0; k <= j/p[i]; ++k) {
                    (dp[i][j] += dp[i-1][j-k*p[i]]) %= mod;
                }
            }
        }
        return dp[M-1][n];
    }
};
```

### 转移方程优化（c++）
```cpp
class Solution {
public:
    typedef long long ll;
    static const ll mod = 1e9 + 7;
    static const int N = 1000010;
    static const int M = 4;

    ll dp[M][N];
    int p[M] = {1, 5, 10, 25};

    int waysToChange(int n) {
        memset(dp, 0, sizeof dp);
        for (int i = 0; i < M; ++i) dp[i][0] = 1;
        for (int i = 0; i <= n/p[0]; ++i) dp[0][i*p[0]] = 1;
        for (int i = 1; i < M; ++i) {
            for (int j = 1; j <= n; ++j) {
                dp[i][j] = dp[i-1][j];
                if (j >= p[i]) (dp[i][j] += dp[i][j-p[i]]) %= mod;
            }
        }
        return dp[M-1][n];
    }
};
```

### 空间优化（c++）
```cpp
class Solution {
public:
    typedef long long ll;
    static const ll mod = 1e9 + 7;
    static const int N = 1000010;
    static const int M = 4;

    ll dp[N];
    int p[M] = {1, 5, 10, 25};

    int waysToChange(int n) {
        memset(dp, 0, sizeof dp);
        dp[0] = 1;
        for (int j = 0; j < M; ++j) {
            for (int i = 1; i <= n; ++i) {
                if (i >= p[j]) {
                    (dp[i] += dp[i-p[j]]) %= mod;
                }
            }
        }
        return dp[n];
    }
};
```

### 数学法（c++）
```cpp
class Solution {
public:
    typedef long long ll;
    static const ll mod = 1e9 + 7;

    int waysToChange(int n) {
        ll res = 0;
        for (int i = 0; i <= n/25; ++i) {
            ll r = n - 25 * i;
            ll x = r / 10, y = r / 5;
            (res += (x + 1) * (y - x + 1)) %= mod;
        }
        return res;
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)