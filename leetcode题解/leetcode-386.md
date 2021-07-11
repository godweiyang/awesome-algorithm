---
title: 每日算法系列【LeetCode 386】字典序排数
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-26 16:53:46
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
给定一个整数 n, 返回从 1 到 n 的字典顺序。

例如，给定 n = 13，返回 [1,10,11,12,13,2,3,4,5,6,7,8,9] 。

请尽可能的优化算法的时间复杂度和空间复杂度。 输入的数据 n 小于等于 5,000,000。

## 题解
### 排序法
首先把 1 到 n 所有整数的字符串形式放进数组，然后对这个字符串数组进行排序，最后把所有字符串转换成对应的整数就行了。

时间复杂度是 $O(n \log n)$ ，空间复杂度是 $O(n)$。

### 字典树法
还可以按从小到大顺序直接生成所有整数，首先观察如下的字典树：

![字典树](1.jpg)

可以看出来，这是一棵 10 叉的字典树，第一层根节点，第二层没有 0 （因为不能有前导 0 ），后面的每一层都是在上一层的基础上添加一位 0 到 9 。

而如果按照前序遍历的顺序遍历这棵树，得到的整数序列就是字典序从小到大的。但是这棵树深度是没有限制的啊，所以如果遍历到的数字 x 大于 n 的话，就要结束遍历，回溯到上一层。

时间复杂度是 $O(n)$ ，空间复杂度是 $O(n)$。

## 代码
### 排序法（c++）
```cpp
class Solution {
public:
    vector<int> lexicalOrder(int n) {
        vector<string> s;
        vector<int> res;
        for (int i = 1; i <= n; ++i)
            s.push_back(to_string(i));
        sort(s.begin(), s.end());
        for (int i = 0; i < n; ++i) {
            res.push_back(atoi(s[i].c_str()));
        }
        return res;
    }
};
```

### 排序法（python）
```python
class Solution:
    def lexicalOrder(self, n: int) -> List[int]:
        res = []
        for i in range(1, n+1):
            res.append(str(i))
        res.sort()
        res = [int(x) for x in res]
        return res
```

### 字典树法（c++）
```cpp
class Solution {
public:
    vector<int> lexicalOrder(int n) {
        vector<int> res;
        for (int i = 1; i <= 9; ++i)
            dfs(i, n, res);
        return res;
    }

    void dfs(int x, int n, vector<int>& res) {
        if (x > n) return;
        res.push_back(x);
        for (int i = 0; i <= 9; ++i) {
            dfs(x*10+i, n, res);
        }
    }
};
```

### 字典树法（python）
```python
class Solution:
    def lexicalOrder(self, n: int) -> List[int]:
        res = []
        for i in range(1, 10):
            self.dfs(i, n, res)
        return res

    def dfs(self, x, n, res):
        if x > n:
            return
        res.append(x)
        for i in range(10):
            self.dfs(x*10+i, n, res)
```

## 后记
字典序法的递归需要耗费更大的空间，而在实际运行中， python 代码排序法的运行速度甚至比字典序法更快，这说明了 python 递归是真的慢。