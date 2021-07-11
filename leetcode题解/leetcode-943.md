---
title: 每日算法系列【LeetCode 943】最短超级串
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-23 20:14:54
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
给定一个字符串数组 A，找到以 A 中每个字符串作为子字符串的最短字符串。

我们可以假设 A 中没有字符串是 A 中另一个字符串的子字符串。

**示例1**
```text
输入：
["alex","loves","leetcode"]
输出：
"alexlovesleetcode"
解释：
"alex"，"loves"，"leetcode" 的所有排列都会被接受。
```

**示例2**
```text
输入：
["catg","ctaagt","gcta","ttca","atgcatc"]
输出：
"gctaagttcatgcatc"
```

**提示**
* 1 <= A.length <= 12
* 1 <= A[i].length <= 20


## 题解
> 这是每日算法系列更新以来，做过最难的一道题目了，也是第一次涉及到字符串类型的题目。如果觉得难，可以忽略今天这题。

这题意思就是，给你 n 个字符串，任意两个字符串如果拼接在一起的话，首尾可能会有重合的部分，那么就按照最长的重合部分拼接上去。要求的是 n 个字符串怎么排列，然后依次拼接，得到的最终字符串长度最短？

最暴力的方法当然就是枚举所有排列，然后把他们拼起来看长度，这样的话光是阶乘的复杂度就不可接受了。

这题就要用到**状态压缩动态规划**了，按照字面意思理解就是动态规划的状态是经过压缩的，那具体什么意思呢？

首先我们用二进制来表示每个字符串选取状态， 1 表示选取， 0 表示没有选取。比如 4 个字符串 ，我们选取了第 0 个和第 2 个，那么我们就可以用 1010 来表示这个选取状态，而 1010 转化成 10 进制就是 10 ，所以我们就可以用 10 这个数字来表示这种选取状态。状态最小值是 0 ，表示一个都没有选取，最大值是 $2^n-1$ ，表示所有的字符串都选取了。

然后定义 $dp[s][i]$ 表示选取状态为 s 的情况下，最后一个字符串是第 i 个的情况下，最短的长度是多少。那么去掉第 i 个字符串，选取状态就变成了 $s \oplus 2^i$ ，其中 $\oplus$ 表示异或操作。然后遍历所有的 $dp[s \oplus 2^i][j]$ ，也就是看前一个状态以哪个字符串结尾长度最短。那么状态更新就是： 
$$
dp[s][i]=\min{\{dp[s \oplus 2^i][j]+A[i].length-o[j][i]\}}
$$
其中 $o[j][i]$ 表示 $A[j]$ 和 $A[i]$ 最大重合的长度，可以预处理出来。

与此同时，我们还需要一个数组 $path[s][i]$ ，用来保存 s 状态下结尾是 $A[i]$ 时，前一个字符串最优是哪个。在做完动态规划之后，再进行回溯，找出整个最优序列是什么。

## 代码
### c++
```cpp
class Solution {
public:
    string shortestSuperstring(vector<string>& A) {
        const int INF = 0x3f3f3f3f;
        int n = A.size(), M = (1<<n);
        int o[n][n];
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                o[i][j] = overlap(A[i], A[j]);
            }
        }
        int dp[M][n], path[M][n];
        memset(dp, INF, sizeof dp);
        memset(path, 0, sizeof path);
        for (int i = 0; i < n; ++i) {
            dp[1<<i][i] = A[i].size();
        }
        for (int s = 0; s < M; ++s) {
            for (int i = 0; i < n; ++i) {
                if ((s^(1<<i)) == 0) continue;
                for (int j = 0; j < n; ++j) {
                    if (i != j && ((s>>j)&1)) {
                        if (dp[s][i] > dp[s^(1<<i)][j]+A[i].size()-o[j][i]) {
                            dp[s][i] = dp[s^(1<<i)][j]+A[i].size()-o[j][i];
                            path[s][i] = j;
                        }
                    }
                }
            }
        }
        int last = 0;
        for (int i = 1; i < n; ++i) {
            if (dp[M-1][i] < dp[M-1][last]) {
                last = i;
            }
        }
        vector<int> seq = {last};
        int s = M - 1;
        for (int i = 0; i < n-1; ++i) {
            int tmp = last;
            last = path[s][last];
            seq.push_back(last);
            s = s^(1<<tmp);
        }
        reverse(seq.begin(), seq.end());
        string res = A[seq[0]];
        for (int i = 1; i < n; ++i) {
            res += A[seq[i]].substr(o[seq[i-1]][seq[i]]);
        }
        return res;
    }

    int overlap(const string& a, const string& b) {
        int na = a.size(), nb = b.size();
        for (int i = min(na, nb); i >= 1 ; --i) {
            if (a.substr(na-i) == b.substr(0, i)) return i;
        }
        return 0;
    }
};
```

### python
```python
class Solution:
    def overlap(self, a, b):
        na, nb = len(a), len(b)
        for i in range(min(na, nb), 0, -1):
            if a[na-i:] == b[0:i]:
                return i
        return 0

    def shortestSuperstring(self, A: List[str]) -> str:
        INF = 0x3f3f3f3f
        n= len(A)
        M = 1<<n
        o = [[0] * n for _ in range(n)]
        for i in range(n):
            for j in range(n):
                o[i][j] = self.overlap(A[i], A[j])
        dp = [[INF] * n for _ in range(M)]
        path = [[0] * n for _ in range(M)]
        for i in range(n):
            dp[1<<i][i] = len(A[i])
        for s in range(M):
            for i in range(n):
                if s^(1<<i) == 0:
                    continue
                for j in range(n):
                    if i != j and ((s>>j)&1):
                        if dp[s][i] > dp[s^(1<<i)][j]+len(A[i])-o[j][i]:
                            dp[s][i] = dp[s^(1<<i)][j]+len(A[i])-o[j][i]
                            path[s][i] = j
        last = 0
        for i in range(1, n):
            if dp[M-1][i] < dp[M-1][last]:
                last = i
        seq = [last]
        s = M - 1
        for _ in range(n-1):
            tmp = last
            last = path[s][last]
            seq.append(last)
            s = s^(1<<tmp)
        seq = seq[::-1]
        res = A[seq[0]]
        for i in range(1, n):
            res += A[seq[i]][o[seq[i-1]][seq[i]]:]
        return res
```

## 后记
这题还是有点难度的，我还是看了答案后才自己写出来的，如果实在不会，不要勉强。