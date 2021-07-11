---
title: 【每日算法Day 108】一道简单的二叉树题目，写法还是挺多的。
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-22 12:03:45
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

## 题目链接
[LeetCode 199. 二叉树的右视图](https://leetcode-cn.com/problems/binary-tree-right-side-view/ "LeetCode 199. 二叉树的右视图")

## 题目描述
给定一棵二叉树，想象自己站在它的右侧，按照从顶部到底部的顺序，返回从右侧所能看到的节点值。

**示例1**
```text
输入：
[1,2,3,null,5,null,4]
输出:
[1,3,4]
解释：
   1            <---
 /   \
2     3         <---
 \     \
  5     4       <---
```

## 题解
### dfs
dfs 的思路就是直接递归求解左右子树各自能看到的右视图是什么，然后判断两个视图长度。

如果右子树右视图长度大于等于左子树右视图长度，那左子树完全被挡住。不用管左子树了，直接返回根结点加上右子树右视图就行了。

否则的话，左子树中超出右子树深度的部分不会被挡住，也会被看到，所以得拼接在右子树右视图后面。

### bfs
bfs 的思路就是层次遍历了。对二叉树的每一层，只取最后一个结点就行了。

bfs 的话就得用一个队列来维护结点值了，那么怎么知道哪些结点是同一层的呢？最初的想法是用一个 `pair` 再保存一个深度值，但是这样有点多余了。

我们只需要每次队列中只保存同一层的结点，然后记录下队列大小。然后依次出队，直到出队个数达到之前记录的大小。并且同时将所有的下一层结点入队。这样就能保证这一层的结点全部出队之后，队列中只剩下了下一层的结点。

## 代码
### dfs（c++）
```cpp
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        if (!root) return {};
        vector<int> left = rightSideView(root->left);
        vector<int> right = rightSideView(root->right);
        vector<int> res = {root->val};
        for (auto x : right) res.push_back(x);
        for (int i = right.size(), sz = left.size(); i < sz; ++i) {
            res.push_back(left[i]);
        }
        return res;
    }
};
```

### bfs（c++）
```cpp
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> res;
        queue<TreeNode*> Q;
        if (root) Q.push(root);
        while (!Q.empty()) {
            int sz = Q.size();
            while (sz--) {
                TreeNode* node = Q.front();
                Q.pop();
                if (!sz) res.push_back(node->val);
                if (node->left) Q.push(node->left);
                if (node->right) Q.push(node->right);
            }
        }
        return res;
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)