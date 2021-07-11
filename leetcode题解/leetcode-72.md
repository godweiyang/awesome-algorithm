---
title: 【每日算法Day 92】经典面试题：编辑距离
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-06 13:45:09
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

## 题目链接
[LeetCode 72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/ "LeetCode 72. 编辑距离")

## 题目描述
给你两个单词 `word1` 和 `word2`，请你计算出将 `word1` 转换成 `word2` 所使用的最少操作数 。

你可以对一个单词进行如下三种操作：
* 插入一个字符
* 删除一个字符
* 替换一个字符

**示例1**
```text
输入：
word1 = "horse", word2 = "ros"
输出:
3
解释：
horse -> rorse (将 'h' 替换为 'r')
rorse -> rose (删除 'r')
rose -> ros (删除 'e')
```

**示例2**
```text
输入：
word1 = "intention", word2 = "execution"
输出:
5
解释：
intention -> inention (删除 't')
inention -> enention (将 'i' 替换为 'e')
enention -> exention (将 'n' 替换为 'x')
exention -> exection (将 'n' 替换为 'c')
exection -> execution (插入 'u')
```

## 题解
这是一道典型的动态规划题目，我们用 $dp[i][j]$ 表示 $word1_{1:i}$ 转换成 $word2_{1:j}$ 所需要的最小步数。那么对于最后一个字符来说，有如下四种情况：
* 如果 $word1_{i} = word2_{j}$ ，那么最后一个字符不需要操作，答案就是 $dp[i-1][j-1]$ 。
* 如果 最后一步操作是插入得到的，那么问题就转化为了 $word1_{1:i}$ 转换成 $word2_{1:j-1}$ 所需要的最小步数。最后再插入 $word2_{j}$ 就行了，答案就是 $dp[i][j-1] + 1$ 。
* 如果 最后一步操作是删除得到的，那么问题就转化为了 $word1_{1:i-1}$ 转换成 $word2_{1:j}$ 所需要的最小步数。最后再删除 $word1_{i}$ 就行了，答案就是 $dp[i-1][j] + 1$ 。
* 如果 最后一步操作是替换得到的，那么问题就转化为了 $word1_{1:i-1}$ 转换成 $word2_{1:j-1}$ 所需要的最小步数。最后再将 $word1_{i}$ 替换为 $word2_{j}$ 就行了，答案就是 $dp[i-1][j-1] + 1$ 。

综上，如果 $word1_{i} = word2_{j}$ ，那么 $dp[i][j] = dp[i-1][j-1]$ 。否则的话：
$$
dp[i][j] = \min{\{dp[i][j-1], dp[i-1][j], dp[i-1][j-1]\}} + 1
$$

初始化就是，所有的 $dp[0][i] = i$ ，因为需要插入 $i$ 次。所有的 $dp[i][0] = i$ ，因为需要删除 $i$ 次。

总的时间复杂度就是 $O(nm)$ 。

## 代码
### c++
```cpp
class Solution {
public:
    int minDistance(string word1, string word2) {
        int n = word1.size(), m = word2.size();
        vector<vector<int> > dp(n+1, vector<int>(m+1, INT_MAX));
        dp[0][0] = 0;
        for (int i = 0; i < m; ++i) dp[0][i+1] = i + 1;
        for (int i = 0; i < n; ++i) dp[i+1][0] = i + 1;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                if (word1[i] == word2[j]) {
                    dp[i+1][j+1] = dp[i][j];
                    continue;
                }
                // 插入
                dp[i+1][j+1] = min(dp[i+1][j+1], dp[i+1][j]+1);
                // 删除
                dp[i+1][j+1] = min(dp[i+1][j+1], dp[i][j+1]+1);
                // 替换
                dp[i+1][j+1] = min(dp[i+1][j+1], dp[i][j]+1);
            }
        }
        return dp[n][m];
    }
};
```

### python
```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        n, m = len(word1), len(word2)
        dp = [[0]*(m+1) for _ in range(n+1)]
        dp[0] = [i for i in range(m+1)]
        for i in range(n+1): dp[i][0] = i
        for i in range(n):
            for j in range(m):
                if word1[i] == word2[j]:
                    dp[i+1][j+1] = dp[i][j]
                    continue
                dp[i+1][j+1] = min(dp[i+1][j], dp[i][j+1], dp[i][j]) + 1
        return dp[n][m]
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)