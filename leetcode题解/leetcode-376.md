---
title: 每日算法系列【LeetCode 376】摆动序列
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-24 12:24:23
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
如果连续数字之间的差严格地在正数和负数之间交替，则数字序列称为摆动序列。第一个差（如果存在的话）可能是正数或负数。少于两个元素的序列也是摆动序列。

例如， [1,7,4,9,2,5] 是一个摆动序列，因为差值 [6,-3,5,-7,3] 是正负交替出现的。相反, [1,4,7,2,5] 和 [1,7,4,5,5] 不是摆动序列，第一个序列是因为它的前两个差值都是正数，第二个序列是因为它的最后一个差值为零。

给定一个整数序列，返回作为摆动序列的最长子序列的长度。 通过从原始序列中删除一些（也可以不删除）元素来获得子序列，剩下的元素保持其原始顺序。


**示例1**
```text
输入：
[1,7,4,9,2,5]
输出：
6
解释：
整个序列均为摆动序列。
```

**示例2**
```text
输入：
[1,17,5,10,13,15,10,5,16,8]
输出：
7
解释：
这个序列包含几个长度为 7 摆动序列，其中一个可为[1,17,10,13,10,16,8]。
```

**示例3**
```text
输入：
[1,2,3,4,5,6,7,8,9]
输出：
2
```

## 题解
这题题面说的啰里啰唆的，其实就一句话：给你一个序列，找出最长的一个子序列，其中子序列相邻两个数的大小是波形的（也就是大小大小大等等这样的）。

### 暴力法
用 dfs 枚举所有可能的子序列，然后看最长的是多少，这种方法显然会超时。

### 动态规划
其实看到这道题，我第一个想到了最长上升子序列，这不就变了个形式嘛，于是动态规划解法直接就有了。

用 $dp[i][s]$ 表示以 $nums[i]$ 结尾的符合条件的最长子序列长度，其中 s 取 1 表示在 $nums[i]$ 处子序列上升，取 0 表示下降。那么我们只需要遍历之前的所有 j ，如果 $nums[j] < nums[i]$ ，那么在 j 处必须是要下降的，更新：
$$
dp[i][1] = \max_j{\{dp[j][0]\}}
$$
如果 $nums[j] > nums[i]$ ，那么在 j 处必须是要上升的，更新：
$$
dp[i][0] = \max_j{\{dp[j][1]\}}
$$
然后取数组中最大值就是答案了，时间复杂度 $O(n^2)$ 。

### 动态规划+时间优化
换个定义，用 $dp[i][s]$ 表示 $nums[i]$ 之前的最长子序列，注意和上面的区别就是不一定以 $nums[i]$ 结尾了。 s 取 1 表示最后两个数是上升的，取 0 表示最后两个数是下降的。

这里分为几种情况：
* $nums[i-1] < nums[i]$ :
    * 考虑 $dp[i][0]$ ，也就是最后两个数下降的，那肯定不能取 $nums[i]$ ，因为 $nums[i-1]$ 比它更小、更优，所以直接更新为 $dp[i-1][0]$ 。
    * 考虑 $dp[i][1]$ ，也就是最后两个数上升的，那如果不取 $nums[i]$ ，那更新为 $dp[i-1][1]$ ；如果取的话，我们就要保证 i-1 之前最后两个数是下降的，并且之前的最后一个数小于 $nums[i]$ 。我们可以证明， i-1 之前的最后两个下降的数一定满足：第二个数 $nums[j]$ 是小于 $nums[i]$ 的，因为如果 $nums[j] >= nums[i]$ ，那么 j 到 i 之间的数一定是单调下降的，否则存在更长的子序列，那么就和 $nums[i-1] < nums[i]$ 矛盾了。综上，取的话 $dp[i][1]$ 更新为 $dp[i-1][0]+1$ 。
* $nums[i-1] > nums[i]$ :
    同样考虑最后两个数上升还是下降，分析和上面一样。

综上考虑，时间复杂度可以降为 $O(n)$ ，空间复杂度是 $O(n)$ 。

### 动态规划+空间优化
在上面优化的基础上，我们还可以观察到，每一次 $dp[i][s]$ 其实只会用到 $dp[i-1][s]$ ，所以我们只需要保存当前和前一时刻的状态就行了，空间复杂度可以降为 $O(1)$ 。

### 贪心法
其实这题还可以直接贪心做，考虑一段连续的上升序列，最优子序列一定是包括了首尾两个数的，因为首是最小的数，选了它才能给前一个数留出更大的上升空间，而尾是最大的数，选了它才能给下一个数留出更多的下降空间。

所以我们贪心的扫描一遍数组，遇到上升或者下降的转折点就选取这个数。而如果数组不升不降，也就是不变的话，就不用管它，因为这些相同的数里面只需要选取一个就行了。

时间复杂度是 $O(n)$ ，空间复杂度是 $O(1)$ 。

## 代码
### 动态规划（c++）
```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return n;
        int dp[n][2], res = 1;
        memset(dp, 0, sizeof dp);
        dp[0][0] = dp[0][1] = 1;
        for (int i = 1; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                if (nums[j] != nums[i]) {
                    int s = nums[j] < nums[i];
                    dp[i][s] = max(dp[i][s], dp[j][s^1]+1);
                }
            }
            res = max(res, dp[i][0]);
            res = max(res, dp[i][1]);
        }
        return res;
    }
};
```

### 动态规划+时间优化（c++）
```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return n;
        int dp[n][2];
        memset(dp, 0, sizeof dp);
        dp[0][0] = dp[0][1] = 1;
        for (int i = 1; i < n; ++i) {
            if (nums[i] == nums[i-1]) {
                dp[i][0] = dp[i-1][0];
                dp[i][1] = dp[i-1][1];
            } else {
                int s = nums[i] > nums[i-1];
                dp[i][s] = max(dp[i-1][s], dp[i-1][s^1] + 1);
                dp[i][s^1] = dp[i-1][s^1];
            }
        }
        return max(dp[n-1][0], dp[n-1][1]);
    }
};
```

### 动态规划+空间优化（c++）
```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return n;
        int dp[2][2];
        memset(dp, 0, sizeof dp);
        dp[0][0] = dp[0][1] = 1;
        for (int i = 1; i < n; ++i) {
            if (nums[i] == nums[i-1]) {
                dp[1][0] = dp[0][0];
                dp[1][1] = dp[0][1];
            } else {
                int s = nums[i] > nums[i-1];
                dp[1][s] = max(dp[0][s], dp[0][s^1]+1);
                dp[1][s^1] = dp[0][s^1];
                swap(dp[0][s], dp[1][s]);
                swap(dp[0][s^1], dp[1][s^1]);
            }
        }
        return max(dp[0][0], dp[0][1]);
    }
};
```

### 贪心（c++）
```cpp
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return n;
        int res = 1, pre_ord = -1;
        for (int i = 1; i < n; ++i) {
            if (nums[i] == nums[i-1]) continue;
            int ord = nums[i] > nums[i-1];
            if (ord != pre_ord) res++;
            pre_ord = ord;
        }
        return res;
    }
};
```

## 后记
鼠年快乐，新年献给大家的第一道题，尽量写的详细一点。

官方题解没有严谨的证明，虽然方法也是这 5 种，但是没有说清楚，不能令人信服。