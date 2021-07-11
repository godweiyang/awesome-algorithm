---
title: 每日算法系列【LeetCode 124】二叉树中的最大路径和
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-13 22:48:39
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
给定一个非空二叉树，返回其最大路径和。

本题中，路径被定义为一条从树中任意节点出发，达到任意节点的序列。该路径至少包含一个节点，且不一定经过根节点。

**示例1**
```text
输入：
[1,2,3]

      1
     / \
    2   3
输出：
6
```

**示例2**
```text
输入：
[-10,9,20,null,null,15,7]

     -10
     / \
    9  20
      /  \
     15   7
输出：
42
```



## 题解
这是一道**树形 dp** 入门题，也就是树上的动态规划。

首先要理解它这个输入什么意思，虽然写代码的时候不用你管，已经给你处理成结构体了。
输入是一个数组，其实是二叉树的层次遍历，也就是从第一层（根结点）开始，往下一层一层遍历结点，同一层从左往右遍历。

这题要求的是一条路径，路径上的数字之和要最大。
我们采用递归来做这题，**假设`dfs(r)`表示以 r 为根结点的子树中最长路径的和，而左右子结点用 l 和 r 来表示**。

那么有人可能会说，这不是很简单了嘛。
一共就下面几种情况：
1. 只取根结点：`r->val`
2. 只取左子树：`dfs(l)`
3. 只取右子树：`dfs(r)`
4. 取根结点和左子树：`r->val + dfs(l)`
5. 取根结点和右子树：`r->val + dfs(r)`
6. 取根结点和左子树和右子树：`r->val + dfs(l) + dfs(r)`

最后的答案就是`dfs(root)`。

然而这样对吗？其实是错的，刚开始我也犯了这样的错误（好久没做树形 dp 了，见笑了）。
为什么是错的呢？试想这么一种情况，万一左子树的最优解是不经过左子结点的话，怎么与根结点连接起来呢？
这种情况下你的计算就有问题了，所以我们必须加强一下之前的假设。

这次我们假设`dfs(r)`表示以 r 为根结点的子树中**经过根结点 r** 的最长路径的和。
现在继续分成上面的几种情况讨论，然而最后的`dfs(root)`意思变了，指的是必须经过根结点 root 的最优路径之和。
那怎么办呢？很好办，只需要用一个全局变量，每次递归的时候都更新一下最大值就行了，因为总有一个结点是最优路径所在子树的根结点。

分析到这里，貌似都对了，但是还有问题吗？
注意看上面的第`2、3、6`三种情况，如果最优情况是这三种，然后用它们更新`dfs(r)`，会出现什么情况？
情况`2、3`会导致回溯之后，在根结点 r 处断开了，也就是不经过 r 了，那再高层也就没法求解了。
而情况`6`会导致路径出现左右分叉，这也是不允许的。
所以递归的最后更新时，只能用其他三种情况更新。

## 代码
### c++
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int res = INT_MIN;

    int maxPathSum(TreeNode* root) {
        dfs(root);
        return res;
    }

    int dfs(TreeNode* root) {
        if (root == NULL) return 0;
        int l_max_sum = dfs(root->left);
        int r_max_sum = dfs(root->right);
        int sum = root->val;
        sum = max(sum, sum + l_max_sum);
        sum = max(sum, sum + r_max_sum);
        res = max(res, sum);
        return max({0, l_max_sum, r_max_sum}) + root->val;
    }
};
```

### python
```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    res = -sys.maxsize

    def maxPathSum(self, root: TreeNode) -> int:
        self.dfs(root)
        return self.res

    def dfs(self, root: TreeNode) -> int:
        if root == None:
            return 0
        l_max_sum = self.dfs(root.left)
        r_max_sum = self.dfs(root.right)
        sum = root.val
        sum = max(sum, sum + l_max_sum)
        sum = max(sum, sum + r_max_sum)
        self.res = max(self.res, sum)
        return max(0, l_max_sum, r_max_sum) + root.val
```

## 后记
这题虽然是困难题，但是也是树形 dp 的入门题，思考起来和实现起来 trick 还是挺多的。