---
title: 【每日算法Day 78】面试经典题：能说出全部四种方法，不录用你都不可能！
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-23 10:05:48
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

## 题目链接
[LeetCode 55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/ "LeetCode 55. 跳跃游戏")

## 题目描述
给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。


**示例1**
```text
输入：
[2,3,1,1,4]
输出：
true
解释：
我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。
```

**示例2**
```text
输入：
[3,2,1,0,4]
输出：
false
解释：
无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。
```

## 题解
### 动态规划+正推
用 $dp[i]$ 表示位置 $i$ 是否可达，初始的时候都是 $0$ ，只有 $dp[0] = 1$ ，因为起点一定是可达的。

然后从位置 $0$ 开始遍历。对于位置 $i$ ，如果发现 $dp[i] = 0$，那么从前面的位置无法到达它，那么就更无法到达后面的位置了，所以直接返回 `false` 。

否则的话，它能到达的范围是 $i+1$ 到 $i+nums[i]$ ，所以把这部分的 $dp$ 值都标记为 $1$ 。

如果发现 $i+nums[i] \ge n-1$ ，就说明当前位置直接就能跳到终点了，直接返回 `true` 。

时间复杂度 $O(n^2)$，空间复杂度 $O(n)$ 。

### 动态规划+倒推
用 $dp[i]$ 表示从位置 $i$ 能否到达终点，初始的时候都是 $0$ ，只有 $dp[n-1] = 1$ ，因为从终点一定是可到达终点的。

然后从位置 $n-2$ 开始往前遍历。对于位置 $i$ ，如果 $i+nums[i] \ge n-1$，那就说明当前位置直接就可以到达终点，那么就令 $dp[i] = 1$ 。

否则的话遍历所有的 $dp[i+1]$ 到 $dp[i+nums[i]]$ ，如果其中有等于 $1$ 的，那就说明先跳到那个位置，就能再跳到终点了。一个都没有的话 $dp[i] = 0$ 。

最后看 $dp[0]$ 是否为 $1$ 就行了。

时间复杂度 $O(n^2)$，空间复杂度 $O(n)$ 。

### 贪心+正推
在上面的动态规划方法中，对于位置 $i$ ，我们需要把他能到达的位置全部做上标记。

但是其实没有必要这么做，只需要记录一下能到的最远的那个位置 $maxx$ 就行了。如果遍历之后的位置 $j$ 时，发现 $maxx < j$ ，那就说明之前的所有位置最远都无法到达 $j$ ，那就直接返回 `false` 。否则的话，比较一下当前能到达的最远位置，更新一下 $maxx$ 的值。

时间复杂度 $O(n)$，空间复杂度 $O(1)$ 。

### 贪心+倒推
还是从上面的动态规划方法改变来的，上面动态规划在位置 $i$ ，需要遍历所有它能到达的位置，然后看有没有位置能够到达终点。

其实只需要看能到的最远的那个位置就行了，我们用 $minn$ 表示后面的位置中最靠前的那个能够到达终点的位置。如果最远到达位置满足 $i+nums[i] \ge minn$，那就说明位置 $i$ 可以直接跳到 $minn$ ，那么就更新 $minn = i$ 。否则的话怎么跳都跳不到终点，因为 $i$ 和 $minn$ 之间的位置都是无法到达终点的。

需要注意的是，这里最远的位置 $i+nums[i]$ 不一定能到达终点哦，但是中间的某个位置可能能够达到。

时间复杂度 $O(n)$，空间复杂度 $O(1)$ 。

## 代码
### 动态规划+正推（c++）
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n, 0);
        dp[0] = 1;
        for (int i = 0; i < n; ++i) {
            if (!dp[i]) return false;
            if (i+nums[i] >= n-1) return true;
            for (int j = i+1; j <= i+nums[i]; ++j) {
                dp[j] = 1;
            }
        }
        return false;
    }
};
```

### 动态规划+倒推（c++）
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n, 0);
        dp[n-1] = 1;
        for (int i = n-2; i >= 0; --i) {
            if (i+nums[i] >= n-1) {
                dp[i] = 1;
                continue;
            }
            for (int j = i+1; j <= i+nums[i]; ++j) {
                dp[i] |= dp[j];
                if (dp[i]) break;
            }
        }
        return dp[0];
    }
};
```

### 贪心+正推（c++）
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size(), maxx = 0;
        for (int i = 0; i < n; ++i) {
            if (i > maxx) return false;
            maxx = max(maxx, i+nums[i]);
        }
        return maxx >= n-1;
    }
};
```

### 贪心+倒推（c++）
```cpp
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int n = nums.size(), minn = n-1;
        for (int i = n-2; i >= 0; --i) {
            if (i+nums[i] >= minn) minn = i;
        }
        return !minn;
    }
};
```