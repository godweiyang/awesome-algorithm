---
title: 每日算法系列【LeetCode 329】矩阵中的最长递增路径
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-02 12:15:18
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
给定一个整数矩阵，找出最长递增路径的长度。

对于每个单元格，你可以往上，下，左，右四个方向移动。 你不能在对角线方向上移动或移动到边界外（即不允许环绕）。

**示例1**
```text
输入：
nums = 
[
  [9,9,4],
  [6,6,8],
  [2,1,1]
]
输出：
4
解释：
最长递增路径为 [1, 2, 6, 9]。
```

**示例2**
```text
输入：
nums = 
[
  [3,4,5],
  [3,2,6],
  [2,2,1]
]
输出：
4
解释：
最长递增路径是 [3, 4, 5, 6]。注意不允许在对角线方向上移动。
```

## 题解
### DFS+记忆化搜索
对于点 $[x, y]$ 来说，以它为终点的最长递增路径一定会经过上下左右四个点其一。所以如果它四周的点小于 $[x, y]$ ，就递归遍历四周的点，然后以 $[x, y]$ 为终点的最长递增路径长度就是以四周小于它的点为终点的最长递增路径长度加 $1$ ：
$$
dfs(x, y) = \max{\{dfs(x-1, y), dfs(x+1, y), dfs(x, y-1), dfs(x, y+1)\}} + 1
$$
注意这里四周的点首先不能超过边界，然后数值上必须小于 $[x, y]$ 。

但是直接这样会有很多重复计算，所以我们必须用记忆化搜索，用 $dp[x][y]$ 保存搜索结果。如果发现已经计算过了，就不再递归，直接返回结果。

最终每个格子最多遍历一遍，时间复杂度是 $O(nm)$ 。

### 拓扑排序
把每个格子当作一个点，然后从数值小的点向四周比它大的点连一条有向边，最终一定会形成一个有向无环图，问题就转变成了求有向无环图中的最长路径。

方法是先找到所有入度为 $0$ 的结点，然后放入一个队列，依次从队列里取出结点，从图中删除这些结点。然后图中就出现了新的入度为 $0$ 的结点了，它们路径长度加 $1$ 。接着重复上面的操作，直到最后没有结点。

## 代码
### DFS+记忆化搜索（c++）
```cpp
class Solution {
public:
    int dx[4] = {0, 0, 1, -1};
    int dy[4] = {1, -1, 0, 0};

    int longestIncreasingPath(vector<vector<int>>& matrix) {
        int n = matrix.size();
        if (!n) return 0;
        int m = matrix[0].size();
        vector<vector<int> > dp(n, vector<int>(m, 0));
        int res = 1;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                dp[i][j] = dfs(matrix, i, j, dp);
                res = max(res, dp[i][j]);
            }
        }
        return res;
    }

    int dfs(vector<vector<int>>& matrix, int x, int y, vector<vector<int>>& dp) {
        if (dp[x][y] > 0) return dp[x][y];
        int n = matrix.size(), m = matrix[0].size();
        dp[x][y] = 1;
        for (int i = 0; i < 4; ++i) {
            int nx = x + dx[i], ny = y + dy[i];
            if (inside(nx, ny, n, m) && matrix[nx][ny] < matrix[x][y]) {
                dp[x][y] = max(dp[x][y], dfs(matrix, nx, ny, dp)+1);
            }
        }
        return dp[x][y];
    }

    bool inside(int x, int y, int n, int m) {
        return 0 <= x && x < n && 0 <= y && y < m;
    }
};
```

### 拓扑排序（c++）
```cpp
class Solution {
public:
    int dx[4] = {0, 0, 1, -1};
    int dy[4] = {1, -1, 0, 0};

    int longestIncreasingPath(vector<vector<int>>& matrix) {
        int n = matrix.size();
        if (!n) return 0;
        int m = matrix[0].size();
        vector<vector<int> > dp(n, vector<int>(m, 0));
        vector<vector<int> > degree(n, vector<int>(m, 0));
        queue<pair<int, int> > Q;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < m; ++j) {
                for (int k = 0; k < 4; ++k) {
                    int nx = i + dx[k], ny = j + dy[k];
                    if (inside(nx, ny, n, m) && matrix[nx][ny] < matrix[i][j]) {
                        degree[i][j]++;
                    }
                }
                if (!degree[i][j]) {
                    Q.push({i, j});
                    dp[i][j] = 1;
                }
            }
        }
        int res = 1;
        while (!Q.empty()) {
            int x = Q.front().first, y = Q.front().second;
            Q.pop();
            res = max(res, dp[x][y]);
            for (int i = 0; i < 4; ++i) {
                int nx = x + dx[i], ny = y + dy[i];
                if (inside(nx, ny, n, m) && matrix[nx][ny] > matrix[x][y]) {
                    if (!(--degree[nx][ny])) {
                        Q.push({nx, ny});
                    }
                    dp[nx][ny] = max(dp[nx][ny], dp[x][y]+1);
                }
            }
        }
        return res;
    }

    bool inside(int x, int y, int n, int m) {
        return 0 <= x && x < n && 0 <= y && y < m;
    }
};
```