---
title: 每日算法系列【LeetCode 685】冗余连接 II
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-22 16:47:17
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
在本问题中，有根树指满足以下条件的有向图。该树只有一个根节点，所有其他节点都是该根节点的后继。每一个节点只有一个父节点，除了根节点没有父节点。

输入一个有向图，该图由一个有着 $N$ 个节点（节点值不重复 $1, 2, ..., N$）的树及一条附加的边构成。附加的边的两个顶点包含在1到N中间，这条附加的边不属于树中已存在的边。

结果图是一个以边组成的二维数组。 每一个边 的元素是一对 $[u, v]$，用以表示有向图中连接顶点 $u$ 和 $v$ 和顶点的边，其中父节点 $u$ 是子节点 $v$ 的一个父节点。

返回一条能删除的边，使得剩下的图是有 $N$ 个节点的有根树。若有多个答案，返回最后出现在给定二维数组的答案。

**示例1**
```text
输入：
[[1,2], [1,3], [2,3]]
输出：
[2,3]
解释：
  1
 / \
v   v
2-->3
```

**示例2**
```text
输入：
[[1,2], [2,3], [3,4], [4,1], [1,5]]
输出：
[4,1]
解释：
5 <- 1 -> 2
     ^    |
     |    v
     4 <- 3
```

**提示**
* 输入的二维数组大小在 $3$ 到 $1000$。
* 二维数组中的整数在 $1$ 到 $N$ 之间，其中 $N$ 是输入数组的大小。

## 题解
这题是上一道题[LeetCode 685. 冗余连接](https://zhuanlan.zhihu.com/p/108220276)的进阶版，区别就是无向图变成了有向图。

上一道题解说过，无向图能构成一棵树的条件是没有环，那么有向图的条件是什么呢？

首先还是得没有环，其次因为是边是有向的，所以一个结点只能有一个父结点（也就是入度为 $1$）。那么这题解法就有了。

* 首先判断有没有入度为 $2$ 的结点，如果有的话，那两条边一定有一条得删掉。
* 按照出现时间从后到前看那两条边，删掉其中一条之后是否能构成一棵树（也就是无环）。如果删掉了无环，那就直接返回这条边作为答案。
* 如果入度全是 $1$ ，那就直接按照出现时间从前到后看添加了哪一条边之后开始出现了环，那么那条边就是答案。

判断能否构成一棵树的话还是用并查集，唯一区别就是不需要用**按秩合并**的优化了，而且给定有向边 $[u, v]$，只能把 $v$ 接在 $u$ 下面。


## 代码
### c++
```cpp
class Solution {
public:
    static const int N = 1010;
    int f[N], degree[N];
    int n;

    vector<int> findRedundantDirectedConnection(vector<vector<int>>& edges) {
        n = edges.size();
        memset(degree, 0, sizeof degree);
        for (auto e : edges) ++degree[e[1]];
        for (int i = n-1; i >= 0; --i) {
            if (degree[edges[i][1]] == 2 && !wrongEdge(edges, i).size()) {
                return edges[i];
            }
        }
        return wrongEdge(edges, n);
    }

    vector<int> wrongEdge(vector<vector<int>>& edges, int except) {
        init();
        for (int i = 0; i < n; ++i) {
            if (i == except) continue;
            int u = edges[i][0], v = edges[i][1];
            if (same(u, v)) return edges[i];
            join(u, v);
        }
        return {};
    }

    void init() {
        for (int i = 1; i <= n; ++i) {
            f[i] = i;
        }
    }

    int find(int u) {
        return u==f[u] ? u : f[u]=find(f[u]);
    }

    void join(int u, int v) {
        u = find(u);
        v = find(v);
        if (u == v) return;
        f[v] = u;
    }

    bool same(int u, int v) {
        u = find(u);
        v = find(v);
        return u == v;
    }
};
```

### python
```python
class Solution:
    def findRedundantDirectedConnection(self, edges: List[List[int]]) -> List[int]:
        self.n = len(edges)
        degree = [0] * (self.n+1)
        for u, v in edges:
            degree[v] += 1
        for u, v in edges[::-1]:
            if degree[v] == 2 and len(self.wrongEdge(edges, [u, v])) == 0:
                return [u, v]
        return self.wrongEdge(edges, [])
        

    def wrongEdge(self, edges, ex):
        self.f = [i for i in range(self.n+1)]
        for u, v in edges:
            if [u, v] == ex:
                continue
            if self.same(u, v):
                return [u, v]
            self.join(u, v)
        return []
    
    def find(self, u):
        if u == self.f[u]:
            return u
        self.f[u] = self.find(self.f[u])
        return self.f[u]

    def join(self, u, v):
        u, v = self.find(u), self.find(v)
        if u == v:
            return
        self.f[v] = u
    
    def same(self, u, v):
        u, v = self.find(u), self.find(v)
        return u == v
```