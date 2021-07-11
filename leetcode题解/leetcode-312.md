---
title: 每日算法系列【LeetCode 312】戳气球
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-30 13:32:15
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
有 n 个气球，编号为0 到 n-1，每个气球上都标有一个数字，这些数字存在数组 nums 中。

现在要求你戳破所有的气球。每当你戳破一个气球 i 时，你可以获得 nums[left] * nums[i] * nums[right] 个硬币。 这里的 left 和 right 代表和 i 相邻的两个气球的序号。注意当你戳破了气球 i 后，气球 left 和气球 right 就变成了相邻的气球。

求所能获得硬币的最大数量。

**示例1**
```text
输入：
[3,1,5,8]
输出：
167
解释：
nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
coins =  3*1*5      +  3*5*8    +  1*3*8      + 1*8*1   = 167
```

**提示**
* 你可以假设 nums[-1] = nums[n] = 1，但注意它们不是真实存在的所以并不能被戳破。
* 0 ≤ n ≤ 500, 0 ≤ nums[i] ≤ 100

## 题解
### dfs+记忆化搜索
对于区间 [l, r] ，我们考虑最后一个被戳破的气球 k ，那么之前的步骤我们可以分为两步，也就是求 [l, k-1] 和 [k+1, r] 之间的最大分数。

那么为什么不考虑先戳破 k 呢？因为这样的话 [l, k-1] 和 [k+1, r] 就会连接在一起，两个子状态就不能独立计算了，互相会产生影响。

两个子区间的最大的分算完之后，最后 k 的得分就是 nums[l-1] * nums[k] * nums[r+1] ，取使得总得分最高的 k 就行了。

有一个小技巧就是，提示里也说了，就是刚开始的时候在首尾各添加一个分数为 1 的虚拟气球。

但是直接这样递归会超时，因为有很多的子状态都重复计算了，所以可以用一个全局的数组保存每个状态的分数。初始化为 -1 ，如果某个状态计算过了，就直接返回它的值就行了，不然就递归计算。

### 动态规划
上面的方法是自顶向下的，其实也可以转化成自底向上的，也就是从小的区间开始算起，最后算最大的，这就是动态规划的方法，具体的实现细节和上面是一模一样的。

## 代码
### dfs+记忆化搜索（c++）
```cpp
class Solution {
public:
    static const int N = 510;
    int dp[N][N];

    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        nums.insert(nums.begin(), 1);
        nums.push_back(1);
        memset(dp, -1, sizeof dp);
        int res = dfs(1, n, nums);
        return res;
    }

    int dfs(int l, int r, vector<int>& nums) {
        if (dp[l][r] != -1) return dp[l][r];
        if (l > r) return 0;
        int res = 0;
        for (int k = l; k <= r; ++k) {
            res = max(res, nums[l-1]*nums[k]*nums[r+1]+dfs(l, k-1, nums)+dfs(k+1, r, nums));
        }
        return dp[l][r] = res;
    }
};
```

### 动态规划（c++）
```cpp
class Solution {
public:
    static const int N = 510;
    int dp[N][N];

    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        nums.insert(nums.begin(), 1);
        nums.push_back(1);
        memset(dp, 0, sizeof dp);
        for (int len = 1; len <= n; ++len) {
            for (int i = 1; i+len-1 <= n; ++i) {
                int j = i + len - 1;
                for (int k = i; k <= j; ++k) {
                    dp[i][j] = max(dp[i][j], nums[i-1]*nums[k]*nums[j+1] + dp[i][k-1] + dp[k+1][j]);
                }
            }
        }
        return dp[1][n];
    }
};
```

### dfs+记忆化搜索（python）
```python
class Solution:
    def maxCoins(self, nums: List[int]) -> int:
        n = len(nums)
        nums = [1] + nums + [1]
        self.dp = [[-1]*(n+2) for _ in range(n+2)]
        res = self.dfs(1, n, nums)
        return res
    
    def dfs(self, l, r, nums):
        if self.dp[l][r] != -1:
            return self.dp[l][r]
        if l > r:
            return 0
        res = 0
        for k in range(l, r+1):
            res = max(res, nums[l-1]*nums[k]*nums[r+1]+self.dfs(l, k-1, nums)+self.dfs(k+1, r, nums))
        self.dp[l][r] = res
        return res

```

### 动态规划（python）
```python
class Solution:
    def maxCoins(self, nums: List[int]) -> int:
        n = len(nums)
        nums = [1] + nums + [1]
        dp = [[0]*(n+2) for _ in range(n+2)]
        for l in range(1, n+1):
            for i in range(1, n-l+2):
                j = i + l - 1
                for k in range(i, j+1):
                    dp[i][j] = max(dp[i][j], nums[i-1]*nums[k]*nums[j+1] + dp[i][k-1] + dp[k+1][j])
        return dp[1][n]
```
