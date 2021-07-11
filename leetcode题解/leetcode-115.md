---
title: 每日算法系列【LeetCode 115】不同的子序列
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-01 11:15:18
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
给定一个字符串 S 和一个字符串 T，计算在 S 的子序列中 T 出现的个数。

一个字符串的一个子序列是指，通过删除一些（也可以不删除）字符且不干扰剩余字符相对位置所组成的新字符串。（例如，"ACE" 是 "ABCDE" 的一个子序列，而 "AEC" 不是）

**示例1**
```text
输入：
S = "rabbbit", T = "rabbit"
输出：
3
解释：
如下图所示, 有 3 种可以从 S 中得到 "rabbit" 的方案。
(上箭头符号 ^ 表示选取的字母)

rabbbit
^^^^ ^^
rabbbit
^^ ^^^^
rabbbit
^^^ ^^^
```

**示例2**
```text
输入：
S = "babgbag", T = "bag"
输出：
5
解释：
如下图所示, 有 5 种可以从 S 中得到 "bag" 的方案。 
(上箭头符号 ^ 表示选取的字母)

babgbag
^^ ^
babgbag
^^    ^
babgbag
^    ^^
babgbag
  ^  ^^
babgbag
    ^^^
```

## 题解
### dfs+记忆化搜索
这题要求字符串 s 中有多少子序列正好等于字符串 t ，那么我们不如从最后一个字符看起，假设 s 和 t 的长度分别为 n 和 m 。

如果 s[n-1] 和 t[m-1] 不相等，那么显然只能在 s[0] 到 s[n-2] 之中寻找 t 。

如果 s[n-1] 和 t[m-1] 相等，那么有两种选择。一种是这两个字符对应上，然后在 s[0] 到 s[n-2] 之中寻找 t[0] 到 t[m-2] 。另一种是不用 s[n-1] ，还是和上一种情况一样，在 s[0] 到 s[n-2] 之中寻找 t 。两种选择的方案数加起来就是答案了。

那么递归终止条件是什么呢？如果发现 s 已经空了，但是 t 还有字符没有对应上，那么方案数一定为 0 。如果 t 空了，那么不管 s 还剩多少字符，都说明 t 已经找到对应的子序列了，方案数加 1 。

为了防止重复计算，还要加上记忆化搜索，用数组记录一下每个状态的方案数。状态 (i, j) 就表示在 s[0] 到 s[i] 中寻找 t[0] 到 t[j] 的方案数。

### 动态规划
当然一般上面那种 dfs 都可以转化成动态规划的写法。

这里动态规划就是从长度比较短的字符串开始求解。初始状态是 dp[i][0] ，表示 s[0] 到 s[i] 之中有多少个 t[0] ，这很容易求出来。

然后两层循环遍历两个的字符串的结尾，跟上面 dfs 方法一样，如果 s[i] 和 t[j] 不相等，那么 dp[i][j] = dp[i-1][j] ；否则的话再加上一个 dp[i-1][j-1] 就行了。

### 动态规划+空间优化
上面动态规划方法有个问题就是字符串如果太长的话，空间会吃不消。而仔细观察就会发现，当循环到 i ，然后遍历 j 的时候，其实只会用到 i-1 的状态。那么我们就可以去掉第一维，只保存 j 的所有状态就行了。

但是有个注意的点是，第二层循环 j 的顺序要变一下，要从大往小遍历。因为 j 需要用到 (i-1, j-1) 时刻的状态值，如果你从小到大遍历，那么 (i, j-1) 的方案数就会把 (i-1, j-1) 的方案数覆盖掉，之后你获取到的就不是 i-1 时刻的方案数了。

另一个小区别是 dp[i][0] 的计算被移到了第一层循环的最后，而不是初始化就计算好了。这是因为第一维 i 被去掉了，所以只能在用到的时候再更新计算。

### 动态规划+空间优化+时间优化
其实上面一个方法速度已经不错了，但是时间上还是有优化的余地的。

可以发现，只有当 s[i] = t[j] 的时候，才需要更新 (i, j) 的方案数。所以我们只需要提前预处理出来每个字母 s[i] 在 字符串 t 中的哪些位置出现过就行了。然后遍历的时候只需要直接取出这些位置来更新就行了。

而实际运行中也会发现， dfs 要比动态规划快很多！这是因为 dfs 只会遍历合法的那些状态，而动态规划会把所有状态都计算出来，不管对最后的答案有没有帮助。

举个例子，s = "abcbbbb" ， t = "abc" ，因为 t 只在 s 的前三个字母中出现了，所以如果我们寻找 t 的子串 "ab" 在 s 中出现次数的时候，从第二个 b 开始都是没有任何意义的，因为后面都没有 c 给你匹配了。

## 代码
### dfs+记忆化搜索（c++）
```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size(), m = t.size();
        if (n < m) return 0;
        vector<vector<int> > dp(n, vector<int>(m, -1));
        return dfs(n-1, m-1, s, t, dp);
    }

    int dfs(int i, int j, string& s, string& t, vector<vector<int> >& dp) {
        if (j < 0) return 1;
        if (i < 0) return 0;
        if (dp[i][j] >= 0) return dp[i][j];
        int res = dfs(i-1, j, s, t, dp);
        if (s[i] == t[j]) res += dfs(i-1, j-1, s, t, dp);
        return dp[i][j]=res;
    }
};
```

### 动态规划（c++）
```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size(), m = t.size();
        if (n < m) return 0;
        vector<vector<long> > dp(n, vector<long>(m, 0));
        dp[0][0] = (s[0] == t[0]);
        for (int i = 1; i < n; ++i) {
            dp[i][0] = dp[i-1][0] + (s[i] == t[0]);
        }
        for (int i = 1; i < n; ++i) {
            for (int j = 1; j < m; ++j) {
                dp[i][j] = dp[i-1][j];
                if (s[i] == t[j]) {
                    dp[i][j] += dp[i-1][j-1];
                }
            }
        }
        return dp[n-1][m-1];
    }
};
```

### 动态规划+空间优化（c++）
```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size(), m = t.size();
        if (n < m) return 0;
        vector<long> dp(m, 0);
        dp[0] = (s[0] == t[0]);
        for (int i = 1; i < n; ++i) {
            for (int j = m-1; j >= 1; --j) {
                if (s[i] == t[j]) {
                    dp[j] += dp[j-1];
                }
            }
            if (s[i] == t[0]) dp[0]++;
        }
        return dp[m-1];
    }
};
```

### 动态规划+空间优化+时间优化（c++）
```cpp
class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size(), m = t.size();
        if (n < m) return 0;
        vector<long> dp(m, 0);
        vector<vector<int> > pos(300);
        for (int j = 1; j < m; ++j) {
            pos[t[j]].push_back(j);
        }
        dp[0] = (s[0] == t[0]);
        for (int i = 1; i < n; ++i) {
            int sz = pos[s[i]].size();
            for (int j = sz-1; j >= 0; --j) {
                int p = pos[s[i]][j];
                dp[p] += dp[p-1];
            }
            if (s[i] == t[0]) dp[0]++;
        }
        return dp[m-1];
    }
};
```

### dfs+记忆化搜索（python）
```python
class Solution:
    def numDistinct(self, s: str, t: str) -> int:
        n, m = len(s), len(t)
        if n < m:
            return 0
        self.dp = [[-1]*m for _ in range(n)]
        return self.dfs(n-1, m-1, s, t)
    
    def dfs(self, i, j, s, t):
        if j < 0:
            return 1
        if i < 0:
            return 0
        if self.dp[i][j] >= 0:
            return self.dp[i][j]
        res = self.dfs(i-1, j, s, t)
        if s[i] == t[j]:
            res += self.dfs(i-1, j-1, s, t)
        self.dp[i][j] = res
        return res
```

### 动态规划（python）
```python
class Solution:
    def numDistinct(self, s: str, t: str) -> int:
        n, m = len(s), len(t)
        if n < m:
            return 0
        dp = [[0]*m for _ in range(n)]
        if s[0] == t[0]:
            dp[0][0] = 1
        for i in range(1, n):
            dp[i][0] = dp[i-1][0] + (1 if s[i] == t[0] else 0)
        for i in range(1, n):
            for j in range(1, m):
                dp[i][j] = dp[i-1][j]
                if s[i] == t[j]:
                    dp[i][j] += dp[i-1][j-1]
        return dp[n-1][m-1]
```

### 动态规划+空间优化（python）
```python
class Solution:
    def numDistinct(self, s: str, t: str) -> int:
        n, m = len(s), len(t)
        if n < m:
            return 0
        dp = [0] * m
        if s[0] == t[0]:
            dp[0] = 1
        for i in range(1, n):
            for j in range(m-1, 0, -1):
                if s[i] == t[j]:
                    dp[j] += dp[j-1]
            if s[i] == t[0]:
                dp[0] += 1
        return dp[m-1]
```

### 动态规划+空间优化+时间优化（python）
```python
class Solution:
    def numDistinct(self, s: str, t: str) -> int:
        n, m = len(s), len(t)
        if n < m:
            return 0
        dp = [0] * m
        pos = [[] for _ in range(300)]
        for j in range(1, m):
            pos[ord(t[j])].append(j)
        if s[0] == t[0]:
            dp[0] = 1
        for i in range(1, n):
            for j in pos[ord(s[i])][::-1]:
                if s[i] == t[j]:
                    dp[j] += dp[j-1]
            if s[i] == t[0]:
                dp[0] += 1
        return dp[m-1]
```

### 后记
这题还有个坑爹的地方，就是用动态规划写的时候，数组数据类型必须定义成 long 类型，否则会爆 int 范围，但是最终的答案又在 int 范围内。这其实就是因为动态规划计算了很多无用的状态，这些状态里有超出 int 范围的。而 dfs 用 int 就完全没有问题。

本题还是非常不错的，带你一步步从最好写的 dfs ，然后转化成动态规划，然后优化空间，减少数组维度，最后优化时间。优化时间在 c++ 上面提升不明显，但是 python 提升非常大，直接超过了 100% 的方法。

时间上还有一些小 trick ，我这里没有考虑，留给大家思考。比如对于状态 (i, j) ，如果 n-i < m-j ，那么就没必要更新了，因为 s 中剩下来的字符串都不够 t 剩下来的去匹配的。