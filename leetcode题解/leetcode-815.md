---
title: 【每日算法Day 62】LeetCode 815. 公交路线
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-07 18:13:29
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
我们有一系列公交路线。每一条路线 $routes[i]$ 上都有一辆公交车在上面循环行驶。例如，有一条路线 $routes[0] = [1, 5, 7]$，表示第一辆（下标为 $0$）公交车会一直按照 $1 \to 5 \to 7 \to 1 \to 5 \to 7 \to 1 \to \ldots$ 的车站路线行驶。

假设我们从 $S$ 车站开始（初始时不在公交车上），要去往 $T$ 站。 期间仅可乘坐公交车，求出最少乘坐的公交车数量。返回 $-1$ 表示不可能到达终点车站。

**示例1**
```text
输入：
routes = [[1, 2, 7], [3, 6, 7]]
S = 1
T = 6
输出：
2
解释：
最优策略是先乘坐第一辆公交车到达车站 7, 然后换乘第二辆公交车到车站 6。
```

**提示**
* $1 \le routes.length \le 500$.
* $1 \le routes[i].length \le 500$.
* $0 \le routes[i][j] < 10 ^ 6$.

## 题解
我们可以将每一条线路视作一个点，对于任意两条线路，如果它们经过的车站有交集，那么就在两点之间连一条边，这样就构成了一张图。

图中有些点（路线）是包含起点 $S$ 的，我们把它们都作为起点。而有些点（路线）是包含终点 $T$ 的，我们把它们都作为终点。

那么问题就转化为了求起点到终点的最短路径。因为起点和终点数量可能有多个，所以我们新建两个结点，一个起点用来指向所有包含 $S$ 的点，一个终点用来指向所有包含 $T$ 的点。接下来问题就变成了单源最短路径问题了。

因为本题中边并没有权值（或者说都是 $1$），那么我们就可以直接用 BFS 来进行求解最短路。

建图的时候，对于任意两条路线，我们要判断它们车站是否存在交集。可以事先对每条线路的车站编号进行排序，然后用双指针法判断。最好排完序还要去重，防止数据有重复。不过实际运行中，就算不排序也能通过，说明数据给的就是有序的了。

最终时间复杂度由几部分决定。假设路线数量是 $N$，每条路线最多有 $M$ 个车站。那么排序复杂度为 $O(NM \log M)$，建图复杂度为 $O(N^2M)$，BFS 复杂度为 $O(N^2)$。因此总的时间复杂度忽略低阶项之后为 $O(N^2M)$。看起来貌似还是有点高，但其实建图的时候，大多数情况下双指针法并不会遍历完所有的车站，所以达不到 $O(M)$ 。

## 代码
### c++
```cpp
class Solution {
public:
    int numBusesToDestination(vector<vector<int>>& routes, int S, int T) {
        if (S == T) return 0;
        int n = routes.size();
        for (int i = 0; i < n; ++i) {
            sort(routes[i].begin(), routes[i].end());
            routes[i].erase(unique(routes[i].begin(), routes[i].end()), routes[i].end());
        }
        routes.push_back({S});
        routes.push_back({T});
        vector<vector<int>> G = buildGraph(routes, S, T);
        return BFS(G);
    }

    vector<vector<int>> buildGraph(vector<vector<int>>& routes, int S, int T) {
        int n = routes.size();
        vector<vector<int>> G(n);
        for (int i = 0; i < n; ++i) {
            for (int j = i+1; j < n; ++j) {
                int su = routes[i].size(), sv = routes[j].size();
                int u = 0, v = 0;
                while (u < su && v < sv) {
                    if (routes[i][u] < routes[j][v]) ++u;
                    else if (routes[i][u] > routes[j][v]) ++v;
                    else {
                        G[i].push_back(j);
                        G[j].push_back(i);
                        break;
                    }
                }
            }
        }
        return G;
    }

    int BFS(vector<vector<int>>& G) {
        int n = G.size();
        int S = n - 2;
        int T = n - 1;
        vector<int> dis(n, -1);
        queue<int> Q;
        Q.push(S);
        dis[S] = 0;
        while (!Q.empty()) {
            int u = Q.front();
            Q.pop();
            for (int i = 0, sz = G[u].size(); i < sz; ++i) {
                int v = G[u][i];
                if (dis[v] == -1) {
                    Q.push(v);
                    dis[v] = dis[u] + 1;
                    if (v == T) return dis[v]-1;
                }
            }
        }
        return -1;
    }
};
```
