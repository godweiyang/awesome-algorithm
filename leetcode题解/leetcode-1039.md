---
title: 每日算法系列【LeetCode 1039】多边形三角剖分的最低得分
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-05 08:34:18
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
给定 N，想象一个凸 N 边多边形，其顶点按顺时针顺序依次标记为 A[0], A[i], ..., A[N-1]。

假设您将多边形剖分为 N-2 个三角形。对于每个三角形，该三角形的值是顶点标记的乘积，三角剖分的分数是进行三角剖分后所有 N-2 个三角形的值之和。

返回多边形进行三角剖分后可以得到的最低分。

**示例1**
```text
输入：
[1,2,3]
输出：
6
解释：
多边形已经三角化，唯一三角形的分数为 6。
```

**示例2**
![](1.png)
```text
输入：
[3,7,4,5]
输出：
144
解释：
有两种三角剖分，可能得分分别为：3*7*5 + 4*5*7 = 245，或 3*4*5 + 3*4*7 = 144。最低分数为 144。
```

**示例3**
```text
输入：
[1,3,1,4,1,5]
输出：
13
解释：
最低分数三角剖分的得分情况为 1*1*3 + 1*1*4 + 1*1*5 + 1*1*1 = 13。
```

**提示**
* 3 <= A.length <= 50
* 1 <= A[i] <= 100

## 题解
一个凸 $n$ 边多边形，不停切割下去，最终一定是能切割成 $n-2$ 个三角形。那么按照什么顺序切割才能方便求解呢？

可以发现，一刀下去，两个多边形只有一条边是在内部，其他边都是连续的外围的边，如下图所示：

![](2.jpg)

所以右边的多边形我们可以用 $(i, j)$ 二维状态来表示。

那么继续切割下去，例如切割左边那块多边形，我们应该先把 $(i, j)$ 这条边对应的三角形给找出来，那就是在 $(i, j)$ 之间找到第三个点 $k$ ，如下图所示：

![](3.jpg)

这样右边多边形就被划分为了 3 块，其中除了 $(i, j, k)$ 这个三角形外，两外两块多边形仍然满足只有一条内边的性质，所以可以继续用二位状态表示为 $(i, k)$ 和 $(k, j)$。

那如果不先找三角形 $(i, j, k)$ 会怎么样呢。如下图所示：

![](4.jpg)

这样的话，多边形 $(i, k_1, k_2, j)$ 就会出现两条内边，那么这种多边形就很难用简单的二维状态来表示了，程序中很难实现。

最后就能用二维动态规划来递归求解了。用 $(i, j)$ 表示多边形 $i \to i+1 \to \cdots j$ ，其中只有 $j \to i$ 是内边。设 $dp[i][j]$ 表示多边形 $(i, j)$ 切割后最小得分，那么只需要找到上面所说的切割点 $k$ 就行了，转移方程为：
$$
dp[i][j] = \min(dp[i][k] + dp[k][j] + A[i] \cdot A[j] \cdot A[k])
$$

## 代码
### c++
```cpp
class Solution {
public:
    static const int N = 55;
    int dp[N][N];

    int minScoreTriangulation(vector<int>& A) {
        int n = A.size();
        memset(dp, 0, sizeof dp);
        for (int len = 3; len <= n; ++len) {
            for (int i = 0; i < n; ++i) {
                int j = i + len - 1;
                int tmp = INT_MAX;
                for (int k = i+1; k < j; ++k) {
                    tmp = min(tmp, A[i]*A[j%n]*A[k%n]+dp[i][k%n]+dp[k%n][j%n]);
                }
                dp[i][j%n] = tmp;
            }
        }
        return dp[0][n-1];
    }
};
```

### python
```python
class Solution:
    def minScoreTriangulation(self, A: List[int]) -> int:
        n = len(A)
        dp = [[0]*n for _ in range(n)]
        for l in range(3, n+1):
            for i in range(n):
                j = i + l - 1
                tmp = 0x3f3f3f3f
                for k in range(i+1, j):
                    tmp = min(tmp, A[i]*A[j%n]*A[k%n]+dp[i][k%n]+dp[k%n][j%n])
                dp[i][j%n] = tmp
        return dp[0][n-1]
```