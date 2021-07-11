---
title: 每日算法系列【LeetCode 926】将字符串翻转到单调递增
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-13 12:08:25
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
如果一个由 '0' 和 '1' 组成的字符串，是以一些 '0'（可能没有 '0'）后面跟着一些 '1'（也可能没有 '1'）的形式组成的，那么该字符串是单调递增的。

我们给出一个由字符 '0' 和 '1' 组成的字符串 S，我们可以将任何 '0' 翻转为 '1' 或者将 '1' 翻转为 '0'。

返回使 S 单调递增的最小翻转次数。

**示例1**
```text
输入：
"00110"
输出：
1
解释：
我们翻转最后一位得到 00111.
```

**示例2**
```text
输入：
"010110"
输出：
2
解释：
我们翻转得到 011111，或者是 000111。
```

**示例3**
```text
输入：
"00011000"
输出：
2
解释：
我们翻转得到 00000000。
```

**提示**
* 1 <= S.length <= 20000
* S 中只包含字符 '0' 和 '1'

## 题解
要想把字符串变成递增的，只有两种可能，一种就是从某一处开始全是 1 ，之前都是 0 或者没有，另一种就是全 0 。那么我们只需要遍历这个 1 开始的位置就行了。

对于位置 i ，我们假设从它开始后面都是 1 ，前面都是 0 ，那么需要修改的的次数就是它后面 0 的数量减去它前面 1 的数量。

如果我们用数组预处理出来位置 i 开始到最后 1 的数量，记为 $dp[i]$ 。那么它后面 0 的数量就可以表示为 $n - i - dp[i]$ ，也就是后面的长度减去 1 的数量。而它前面 1 的数量可以表示为 $dp[0] - dp[i]$ ，也就是 1 的总数量减去 i 后面 1 的数量。

那么总的修改次数就是 $n - i - dp[i] + dp[0] - dp[i]$ ，我们只需要遍历所有的 i ，找出最小值就行了。

另外还需要比较一下 $dp[0]$ 的大小，也就是把所有的 1 都修改为 0 。

最终时间复杂度是 $O(n)$ ，空间复杂度也是 $O(n)$ 。

## 代码
### c++
```cpp
class Solution {
public:
    int minFlipsMonoIncr(string S) {
        int n = S.size();
        int dp[n+1];
        dp[n] = 0;
        for (int i = n-1; i >= 0; --i) {
            dp[i] = dp[i+1] + (S[i] == '1');
        }
        int res = dp[0];
        for (int i = 0; i < n; ++i) {
            res = min(res, dp[0]-dp[i]+n-i-dp[i]);
        }
        return res;
    }
};
```

### python
```python
class Solution:
    def minFlipsMonoIncr(self, S: str) -> int:
        n = len(S)
        dp = [0] * (n+1)
        for i in range(n-1, -1, -1):
            dp[i] = dp[i+1] + (1 if S[i]=='1' else 0)
        res = dp[0]
        for i in range(n):
            res = min(res, dp[0]-dp[i]+n-i-dp[i])
        return res
```
