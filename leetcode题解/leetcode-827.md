---
title: 每日算法系列【LeetCode 827】最大人工岛
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-17 16:07:47
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
在二维地图上， 0 代表海洋， 1 代表陆地，我们最多只能将一格 0 海洋变成 1 变成陆地。

进行填海之后，地图上最大的岛屿面积是多少？（上、下、左、右四个方向相连的 1 可形成岛屿）

**示例1**
```text
输入：
[[1, 0], [0, 1]]
输出：
3
解释：
将一格 0 变成 1 ，最终连通两个小岛得到面积为 3 的岛屿。
```

**示例2**
```text
输入：
[[1, 1], [1, 0]]
输出：
4
解释：
将一格 0 变成 1 ，岛屿的面积扩大为 4 。
```

**示例3**
```text
输入：
[[1, 1], [1, 1]]
输出：
4
解释：
没有 0 可以让我们变成 1 ，面积依然为 4 。
```

**提示**
* 1 <= grid.length = grid[0].length <= 50
* 0 <= grid[i][j] <= 1

## 题解
**没有做过这种类型题目的强烈建议自己动手实现一遍，对提升代码能力有很大帮助。**

这题虽然是 hard 难度，但其实没有什么算法，只用到了 dfs 求连通块而已。我们只需要知道填充了一个 0 之后，它上下左右跟它相邻的 4 块连通块是多大。

首先普及一下什么是连通块，就是一块由 1 组成的区域，其中任意两块 1 都可以由一条 1 组成的路径走过去（只能上下左右走）。那么这题我们遍历所有的 0 ，把它四周的连通块大小加起来就是填充了这块 0 之后的连通块大小了。那么问题是怎么知道它周围连通块的大小呢？

首先我们得求出所有连通块的大小，然后存下来，这就得用到 dfs 了。

用二维数组 index （初始化为 -1）记录方块是否被搜索过。然后遍历所有的方块，如果是 1 ，并且 index 为 -1 （也就是没被搜索过），那么它的 index 设为 1 ，表示被搜索过了。然后从它开始向四周进行 dfs ，直到跟它相连的所有 1 的连通块都被搜索完毕。这时候整块连通块里面的 1 方块的 index 都被设成了 1 ，就算以后被遍历到了也不会被搜索了。那么大小怎么记录呢？只需要设置一个变量 cnt 初始为 1 ，然后 dfs 的时候遇到一个未被搜索的块就加 1 ，最后 cnt 就是当前连通块的大小了。

光知道了每个连通块的大小还不行，对于一个 0 块，四周的 1 方块万一属于同一个连通块怎么办？得区分它们，不然就会被重复计算。所以在上面的 dfs 之前，给那块连通块一个唯一的编号（从 0 开始），遇到下一个连通块就加 1 。这样 index 数组就可以用来存连通块的编号了，同时还能表示方块是否被搜索过。

具体的细节还得看代码，有很多实现的技巧，当然我写的还有一些优化余地。

## 代码
### c++
```cpp
class Solution {
public:
    static const int N = 55;
    int dx[4] = {0, 0, 1, -1};
    int dy[4] = {1, -1, 0, 0};
    int index[N][N], area[N*N], flag[N*N];
    int m, n;

    int largestIsland(vector<vector<int>>& grid) {
        m = grid.size();
        n = grid[0].size();
        memset(index, -1, sizeof index);
        memset(area, 0, sizeof area);
        memset(flag, 0, sizeof flag);
        
        int res = 0, idx = -1;
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (grid[i][j] == 1 && index[i][j] == -1) {
                    int cnt = 1;
                    index[i][j] = ++idx;
                    dfs(i, j, idx, cnt, grid);
                    area[idx] = cnt;
                    res = max(res, cnt);
                }
            }
        }
        
        for (int x = 0; x < m; ++x) {
            for (int y = 0; y < n; ++y) {
                if (grid[x][y] == 0) {
                    int tmp = 1;
                    for (int i = 0; i < 4; ++i) {
                        int nx = x + dx[i], ny = y + dy[i];
                        if (inside(nx, ny) && grid[nx][ny] == 1 && !flag[index[nx][ny]]) {
                            flag[index[nx][ny]] = 1;
                            tmp += area[index[nx][ny]];
                        }
                    }
                    for (int i = 0; i < 4; ++i) {
                        int nx = x + dx[i], ny = y + dy[i];
                        if (inside(nx, ny) && grid[nx][ny] == 1 && flag[index[nx][ny]]) {
                            flag[index[nx][ny]] = 0;
                        }
                    }
                    res = max(res, tmp);
                }
            }
        }
        return res;
    }

    bool inside(int x, int y) {
        return 0 <= x && x < m && 0 <= y && y < n;
    }

    void dfs(int x, int y, int idx, int& cnt, vector<vector<int>>& grid) {
        for (int i = 0; i < 4; ++i) {
            int nx = x + dx[i], ny = y + dy[i];
            if (inside(nx, ny) && grid[nx][ny] == 1 && index[nx][ny] == -1) {
                index[nx][ny] = idx;
                dfs(nx, ny, idx, ++cnt, grid);
            }
        }
    }
};
```

## 后记
dfs 求连通块是常规操作，必须要学会，很考验代码功底，要细心，不然很容易写错。 