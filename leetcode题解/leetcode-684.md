---
title: 每日算法系列【LeetCode 684】冗余连接
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-21 12:26:26
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
在本问题中, 树指的是一个连通且无环的无向图。

输入一个图，该图由一个有着 $N$ 个节点（节点值不重复 $1, 2, ..., N$）的树及一条附加的边构成。附加的边的两个顶点包含在 $1$ 到 $N$ 中间，这条附加的边不属于树中已存在的边。

结果图是一个以边组成的二维数组。每一个边的元素是一对 $[u, v]$ ，满足 $u < v$，表示连接顶点 $u$ 和 $v$ 的无向图的边。

返回一条可以删去的边，使得结果图是一个有着 $N$ 个节点的树。如果有多个答案，则返回二维数组中最后出现的边。答案边 $[u, v]$ 应满足相同的格式 $u < v$。

**示例1**
```text
输入：
[[1,2], [1,3], [2,3]]
输出：
[2,3]
解释：
  1
 / \
2 - 3
```

**示例2**
```text
输入：
[[1,2], [2,3], [3,4], [1,4], [1,5]]
输出：
[1,4]
解释：
5 - 1 - 2
    |   |
    4 - 3
```

**提示**
* 输入的二维数组大小在 $3$ 到 $1000$。
* 二维数组中的整数在 $1$ 到 $N$ 之间，其中 $N$ 是输入数组的大小。

## 题解
首先因为这是一个无向图，所以不需要考虑谁是树根。

那么我们一条条边加入到图里去，直到出现了环为止，那么这条边就是冲突的边，需要删除掉。

那么怎么判断是否出现了环呢？如果加入一条边 $[u, v]$ 的时候，两个结点所在的连通块不是同一个，那么一定没有环。否则的话，两个结点连在了同一棵子树上，那么一定会产生一个环。

如何高效的判断两个结点是否在同一棵子树上呢？这就需要用到一个数据结构——**并查集**。

并查集采用一个数组 $f[i]$ 来表示结点 $i$ 的父结点。那么初始的时候没有任何边，定义所有结点的父结点等于它自身： $f[i] = i$ 。

当加入一条边 $[u, v]$ 的时候，可以沿着 $u \to f[u] \to f[f[u]] \to \cdots$ 的路径递归找到 $u$ 所在子树的根结点 $ru$（$v$ 同理得到 $rv$），然后只需要判断两个根结点是否相同就行了。如果根结点相同，那么就产生环了，直接输出这个冲突边就行。否则的话就要把这两棵子树连到一起，最简单的做法就是直接把 $ru$ 连到 $rv$ 下面，当作它的子结点，那么就需要更新 $f[ru] = rv$ 。

下面讲两个常用的并查集优化。

**路径压缩：**
因为我们无需关注每一棵子树结构是什么样的，我们只关注它的根结点是谁。所以为了减小查找根结点的时间，每个结点离根结点要尽量近。

那么我们定义查找根结点函数 $find(u)$ ，如果 $u = f[u]$ ，那么不用找了，它自己就是根结点。否则的话调用 $find(f[u])$ 递归寻找子树的根结点。最后做一步路径压缩的优化，把根结点当作 $u$ 的父结点：$f[u] = find(f[u])$ 。这样下次再查找的时候，路径长度就变为了 $1$ ，一步就能找到根结点了。

**按秩合并：**
合并两棵子树的时候，为了使得合并后的子树高度尽量小，我们需要把高度小的那棵子树接在高度高的那棵下面，当作儿子。

所以我们定义一个 $rank[i]$ 数组，用来记录 $i$ 这个结点作为根结点的子树高度，初始时全都是 $1$ 。那么在合并的时候，把 $rank$ 值小的接到大的下面去，如果一样怎么办呢？随便接，然后把合并后的根结点 $rank$ 值加 $1$ 就行了。


## 代码
### c++
```cpp
class Solution {
public:
    static const int N = 1010;
    int f[N], rank[N];

    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        init();
        for (auto e : edges) {
            int u = e[0], v = e[1];
            if (same(u, v)) return {u, v};
            else join(u, v);
        }
        return {-1, -1};
    }

    void init() {
        for (int i = 0; i < N; ++i) {
            f[i] = i;
            rank[i] = 1;
        }
    }

    int find(int u) {
        return u==f[u] ? u : f[u]=find(f[u]);
    }

    void join(int u, int v) {
        u = find(u);
        v = find(v);
        if (u == v) return;
        if (rank[u] < rank[v]) {
            f[u] = v;
        } else {
            f[v] = u;
            if (rank[u] == rank[v]) {
                rank[u]++;
            }
        }
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
    def findRedundantConnection(self, edges: List[List[int]]) -> List[int]:
        n = len(edges)
        self.f = [i for i in range(n+1)]
        self.rank = [1] * (n+1)
        for [u, v] in edges:
            if self.same(u, v):
                return [u, v]
            else:
                self.join(u, v)
    
    def find(self, u):
        if u == self.f[u]:
            return u
        self.f[u] = self.find(self.f[u])
        return self.f[u]

    def join(self, u, v):
        u, v = self.find(u), self.find(v)
        if u == v:
            return
        if self.rank[u] < self.rank[v]:
            self.f[u] = v
        else:
            self.f[v] = u
            if self.rank[u] == self.rank[v]:
                self.rank[u] += 1
    
    def same(self, u, v):
        u, v = self.find(u), self.find(v)
        return u == v
```