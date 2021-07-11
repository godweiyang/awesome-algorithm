---
title: 【每日算法Day 106】打家劫舍系列最后一弹，撑住你就赢了！
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-20 10:46:49
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

## 题目链接
[LeetCode 337. 打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/ "LeetCode 337. 打家劫舍 III")

**往期回顾：打家劫舍 I ：**  
[【每日算法Day 104】偷电瓶的周某今天放出来了，还不赶紧做这道题防范一下！](https://godweiyang.com/2020/04/18/leetcode-198/ "【每日算法Day 104】偷电瓶的周某今天放出来了，还不赶紧做这道题防范一下！")

**往期回顾：打家劫舍 II ：**  
[【每日算法Day 105】打家劫舍第二弹：看好你的电瓶车！](https://godweiyang.com/2020/04/19/leetcode-213/ "【每日算法Day 105】打家劫舍第二弹：看好你的电瓶车！")

## 题目描述
在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为**根**。 除了**根**之外，每栋房子有且只有一个**父**房子与之相连。一番侦察之后，聪明的小偷意识到**这个地方的所有房屋的排列类似于一棵二叉树**。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

**示例1**
```text
输入：
[3,2,3,null,3,null,1]
     3
    / \
   2   3
    \   \ 
     3   1
输出:
7
解释：
小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.
```

**示例2**
```text
输入：
[3,4,5,1,3,null,1]
     3
    / \
   4   5
  / \   \ 
 1   3   1
输出:
9
解释：
小偷一晚能够盗取的最高金额 = 4 + 5 = 9.
```

## 题解
这次是在一棵树上偷窃了，做法还是一样。对于结点 `r` 来说，我们还是分为偷和不偷两种情况。

如果偷的话，它的左右儿子就不能偷了，所以最大价值就是左儿子**不偷**的最大价值，加上右儿子**不偷**的最大价值，再加上 `r` 的价值。

而如果不偷的话，最大价值就是左儿子**偷或不偷**的最大价值，加上右儿子**偷或不偷**的最大价值。

因为需要用到儿子结点偷和不偷两个价值，所以需要在 `dfs` 时返回两个值，用来表示偷和不偷两个最大价值，具体实现时用 `pair` 来表示。

可能有人会用另一种实现方式，用 `dfs0` 表示不偷的最大价值，`dfs1` 表示偷的最大价值，然后两个函数互相调用。注意这样理论上是可行的，但是会产生很多重复计算，最终会超时。所以这种方法需要记忆化搜索，比较麻烦，需要用 `map<TreeNode*, int>` 来保存每个结点的答案。

## 代码
### 复杂实现方式（c++）
```cpp
class Solution {
public:
    unordered_map<TreeNode*, int> dp0, dp1;

    int dfs0(TreeNode* root) {
        if (!root) return 0;
        if (dp0.find(root) != dp0.end()) return dp0[root];
        int res = 0;
        res += max(dfs0(root->left), dfs1(root->left));
        res += max(dfs0(root->right), dfs1(root->right));
        return dp0[root]=res;
    }

    int dfs1(TreeNode* root) {
        if (!root) return 0;
        if (dp1.find(root) != dp1.end()) return dp1[root];
        int res = root->val;
        res += dfs0(root->left);
        res += dfs0(root->right);
        return dp1[root]=res;
    }

    int rob(TreeNode* root) {
        if (!root) return 0;
        dp0.clear();
        dp1.clear();
        return max(dfs0(root), dfs1(root));
    }
};
```

### 精简实现方式（c++）
```cpp
class Solution {
public:
    typedef pair<int, int> pii;

    pii dfs(TreeNode* root) {
        if (!root) return {0, 0};
        pii l = dfs(root->left);
        pii r = dfs(root->right);
        int r0 = max(l.first, l.second) + max(r.first, r.second);
        int r1 = l.first + r.first + root->val;
        return {r0, r1};
    }

    int rob(TreeNode* root) {
        pii res = dfs(root);
        return max(res.first, res.second);
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)