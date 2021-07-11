---
title: 【每日算法Day 94】经典面试题：机器人的运动范围
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-08 17:31:22
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 最近在忙面试，本来今天都不想更了，但是看基础知识看的太累了，于是写道题排解一下压力。

## 题目链接
[LeetCode 面试题13. 机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/ "LeetCode 面试题13. 机器人的运动范围")

## 题目描述
地上有一个 `m` 行 `n` 列的方格，从坐标 `[0, 0]` 到坐标 `[m-1, n-1]` 。一个机器人从坐标 `[0, 0]` 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于 `k` 的格子。例如，当 `k` 为 `18` 时，机器人能够进入方格 `[35, 37]` ，因为 `3+5+3+7=18`。但它不能进入方格 `[35, 38]`，因为 `3+5+3+8=19`。请问该机器人能够到达多少个格子？


**示例1**
```text
输入：
m = 2, n = 3, k = 1
输出：
3
```

**示例2**
```text
输入：
m = 3, n = 1, k = 0
输出：
1
```

**说明：**
* `1 <= n,m <= 100`
* `0 <= k <= 20`

## 题解
这道题没有什么算法，比较简单，主要考察你的代码实现能力，这里我写了两个方法，一个 BFS，一个 DFS。

### BFS
BFS 的思路就是用一个队列来保存即将要访问的结点，然后不断出队，将当前结点的四周的结点满足要求的入队。为了避免重复访问，可以用一个 `vis` 数组来标记已经访问过的结点位置。

### DFS
DFS 思路就更加清晰简单了，对于一个结点来说，从它出发可以访问到的结点总数就等于从它四周的结点出发可以访问到的结点总数加一。同样需要用一个 `vis` 数组来标记已经访问过的结点位置。

## 代码
### BFS（c++）
```cpp
class Solution {
public:
    int countDigit(int x, int y) {
        int sum = 0;
        while (x > 0) {
            sum += x % 10;
            x /= 10;
        }
        while (y > 0) {
            sum += y % 10;
            y /= 10;
        }
        return sum;
    }

    int movingCount(int m, int n, int k) {
        int dx[4] = {0, 0, 1, -1};
        int dy[4] = {1, -1, 0, 0};
        int res = 0;
        vector<vector<int> > vis(m, vector<int>(n, 0));
        queue<pair<int, int> > Q;
        Q.push({0, 0});
        vis[0][0] = 1;
        while (!Q.empty()) {
            pair<int, int> p = Q.front();
            Q.pop();
            res++;
            int nx = p.first, ny = p.second;
            for (int i = 0; i < 4; ++i) {
                int x = nx + dx[i], y = ny + dy[i];
                if (0 <= x && x < m && 0 <= y && y < n && !vis[x][y] && countDigit(x, y) <= k) {
                    vis[x][y] = 1;
                    Q.push({x, y});
                }
            }
        }
        return res;
    }
};
```

### DFS（c++）
```cpp
class Solution {
public:
    int dx[4] = {0, 0, 1, -1};
    int dy[4] = {1, -1, 0, 0};

    int countDigit(int x, int y) {
        int sum = 0;
        while (x > 0) {
            sum += x % 10;
            x /= 10;
        }
        while (y > 0) {
            sum += y % 10;
            y /= 10;
        }
        return sum;
    }

    int dfs(int nx, int ny, vector<vector<int> >& vis, int& m, int& n, int& k) {
        int res = 1;
        for (int i = 0; i < 4; ++i) {
            int x = nx + dx[i], y = ny + dy[i];
            if (0 <= x && x < m && 0 <= y && y < n && !vis[x][y] && countDigit(x, y) <= k) {
                vis[x][y] = 1;
                res += dfs(x, y, vis, m, n, k);
            }
        }
        return res;
    }

    int movingCount(int m, int n, int k) {
        vector<vector<int> > vis(m, vector<int>(n, 0));
        vis[0][0] = 1;
        int res = dfs(0, 0, vis, m, n, k);
        return res;
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)