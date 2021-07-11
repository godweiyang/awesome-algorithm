---
title: 每日算法系列【LeetCode 689】三个无重叠子数组的最大和
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-07 14:21:35
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
给定数组 $nums$ 由正整数组成，找到三个互不重叠的子数组的最大和。
每个子数组的长度为 $k$ ，我们要使这 $3k$ 个项的和最大化。
返回每个区间起始索引的列表（索引从 0 开始）。如果有多个结果，返回字典序最小的一个。

**示例1**
```text
输入：
[1,2,1,2,6,7,5,1], 2
输出：
[0, 3, 5]
解释：
子数组 [1, 2], [2, 6], [7, 5] 对应的起始索引为 [0, 3, 5]。
我们也可以取 [2, 1], 但是结果 [1, 3, 5] 在字典序上更大。
```

**提示**
* $nums.length$ 的范围在[1, 20000]之间。
* $nums[i]$ 的范围在[1, 65535]之间。
* $k$ 的范围在 [1, floor(nums.legth / 3)]之间。

## 题解
首先看数据范围，这题不能使用暴力，暴力时间复杂度是 $O(n^3)$ ，一定会超时，所以考虑使用动态规划求解。

下面考虑一般情况，也就是求解划分成 $N$ 个不重叠数组的最大和。

假设到第 $i$ 个元素为止，一共已经产生了 $j$ 个不重叠数组，那么令 $dp[i][j]$ 表示这$j$个不重叠数组的最大和。

然后就要寻找状态转移方程。对于第 $i$ 个元素，分为两种情况，可取可不取。

如果取，那就说明 $nums[i]$ 是第 $j$ 个子数组的最后一个元素，那么转移方程为：

$$
    dp[i][j] = dp[i-k][j-1] + nums_{i-k+1:i}
$$

也就是说，从 $i-k+1$ 到 $i$ ，这 $k$ 个元素构成了第 $j$ 个子数组，那我们只需要求到第 $i-k$ 个元素为止，产生 $j-1$ 个不重叠数组的最大和即可。

如果不取，那问题就变成了求到第 $i-1$ 个元素为止，产生 $j$ 个不重叠数组的最大和，那么转移方程为：

$$
    dp[i][j] = dp[i-1][j]  
$$

当然这题还需要你还原出最大和的情况下，所有子数组的起始元素下标，所以需要另外用一个数组保存一下每一步的最优下标。

同样，假设到第 $i$ 个元素为止，一共已经产生了 $j$ 个不重叠数组，用 $path[i][j]$ 表示第 $j$ 个子数组的末尾元素下标。

那么按照上面的推断，如果取第 $i$ 个元素，那么 $path[i][j]=i$ ；否则的话 $path[i][j]=path[i-1][j]$ 。

最后就是根据 $path$ 数组还原答案了。

首先最后一个子数组的末尾元素下标一定是 $idx=path[len-1][N]$ ，那么它的起始元素下标就是 $idx-k+1$ ，然后前一个子数组末尾元素下标就是 $idx=path[idx-k][N-1]$ ，依次下去，直到第一个子数组被求解完毕。

## 代码
```cpp
class Solution {
public:
    vector<int> maxSumOfThreeSubarrays(vector<int>& nums, int k) {
        int len = nums.size(), N = 3;
        int sum[len], s = 0;
        for (int i = 0; i < k; ++i) {
            s += nums[i];
            sum[i] = 0;
        }
        sum[k-1] = s;
        for (int i = k; i < len; ++i) {
            s += nums[i] - nums[i - k];
            sum[i] = s;
        }

        int dp[len][N+1], path[len][N+1];
        memset(dp, 0, sizeof dp);
        dp[k-1][1] = sum[k-1];
        path[k-1][1] = k - 1;
        for (int i = k; i < len; ++i) {
            for (int j = 1; j <= N; ++j) {
                dp[i][j] = dp[i-1][j];
                path[i][j] = path[i-1][j];
                if (dp[i][j] < dp[i-k][j-1] + sum[i]) {
                    dp[i][j] = dp[i-k][j-1] + sum[i];
                    path[i][j] = i;
                }
            }
        }
        
        vector<int> res;
        int idx = path[len-1][N];
        res.push_back(idx - k + 1);
        for (int i = N - 1; i > 0; --i) {
            idx = path[idx-k][i];
            res.push_back(idx - k + 1);
        }
        reverse(res.begin(), res.end());
        return res;
    }
};
```

## 后记
![](1.jpg)

可以看到，时间和空间还有提升的余地。想到的可能优化方法是类似于0-1背包那样，去掉动态规划数组的第二个维度，来优化空间复杂度。

但是这是有些问题的，暂时并没有想到不增加时间复杂度下减少空间开销的方法，欢迎大家提出自己的想法。