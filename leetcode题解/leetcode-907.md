---
title: 每日算法系列【LeetCode 907】子数组的最小值之和
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-20 11:43:33
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
给定一个整数数组 A，找到 min(B) 的总和，其中 B 的范围为 A 的每个（连续）子数组。

由于答案可能很大，因此返回答案模 10^9 + 7。

**示例1**
```text
输入：
[3,1,2,4]
输出：
17
解释：
子数组为 [3]，[1]，[2]，[4]，[3,1]，[1,2]，[2,4]，[3,1,2]，[1,2,4]，[3,1,2,4]。 
最小值为 3，1，2，4，1，1，2，1，1，1，和为 17。
```

**提示**
* 1 <= A.length <= 30000
* 1 <= A[i] <= 30000


## 题解
这题意思是，遍历所有的连续子数组，然后求所有子数组中最小值之和。

### 暴力法
遍历所有区间，然后对于每个区间找出最小值求和。这种方法时间复杂度是 $O(n^3)$ ，显然不可行。

### 暴力法优化
对于区间左端点 i ，遍历所有的右端点 j ，然后维护最小值，时间复杂度可以降到 $O(n^2)$ ，但还是太高了。

### 单调栈
既然我们不能先遍历区间，然后找最小值，那么我们不如顺序倒过来，对于每个值，我们找有多少区间里面，它是最小值。

对于一个数字 A[i] 来说，如果在某个区间 [j, k] 里面它是最小值，那么 [j, k] 包含 A[i] 的子数组的最小值也一定是 A[i] 。所以我们只需要找出最大的那个区间，使得 A[i] 是最小值就行了。

另一个性质是，左右端点 j 和 k 是相互独立的，不会影响，因为 [i, k] 的改变并不会改变 [j, i] 的最小值。所以我们只需要分别求出 A[i] 往左和往右的最远距离就行了。

因为往左和往右求解方法是类似的，所以我们只需要看一个方向就行了。同样不能遍历一遍，不然就和暴力法没区别了嘛。这时候就要介绍神器了——**单调栈**。

单调栈是一个栈，后进先出，里面的元素是单调递增或递减的。而在这题里面，我们要求的是 A[i] 左边最远的距离，等价于求左边第一个比它小的数字 A[j] 。而 A[j+1], ..., A[i] 都大于等于 A[i] ，所以都可以作为符合要求区间的左端点。

这里单调栈只需要维护一个单调上升的子序列就行了，遍历到一个数 A[i] 的时候，如果栈顶的元素大于等于 A[i] ，那么就出栈，直到第一个小于 A[i] 的数 A[j] 为止，那么 A[i] 为最小值的区间左端点可选择数量为 j - i。为什么这样是对的呢？因为 A[j] 是栈里面第一个小于 A[i] 的数，而 A[j] 后面的数都大于 A[j] ，这样才不会把 A[j] 顶出栈。而如果栈是空的，就说明 A[i] 前面的所有元素都大于等于它，那么所有区间都符合条件了。

而右边最大的范围同理可以求得，但是这里有个需要注意的地方！如果存在两个相同的数，这么算不是会导致同一个区间在两个数的位置处计算两次吗？所以要稍稍改进一下，既然向左计算的时候，已经包含了相等的值了，那么向右计算就要排除掉了。也就是从右往左计算右边最远范围的时候，只能计算右边第一个小于等于它的位置，而向左是计算第一个小于它的位置。这样就不会重复计算了。

### 单调栈+动态规划
上面的方法不仅要考虑两端的范围，还得考虑去重，真是麻烦又容易写错。下面介绍一种更加好写又不容易写错的方法，只是不那么容易想到。

我们定义 sum[i] 为所有以 i 为右端点的区间的最小值之和，同样用单调队列的方法来寻找左边最远的距离，使得区间内 A[i] 是最小值。假设用单调队列找到了左边第一个比 A[i] 小的数是 A[j] ，那么 sum[i] 就可以加上 (i - j) * A[i] ，因为 A[j] 往右都是 A[i] 最小。而 A[j] 再往左呢？这些区间最小值等价于直接以 A[j] 为右端点的最小值，因为 A[j] 往右的数都比它大，没有影响，所以 sum[i] 再加上 sum[j] 就行了。

上面两种方法时间复杂度都是 $O(n)$ 的，因为进栈出栈最多也只需要 2n 次。


## 代码
### 单调栈（c++）
```cpp
class Solution {
public:
    static const int MOD = 1e9 + 7;

    int sumSubarrayMins(vector<int>& A) {
        int n = A.size();
        vector<int> pre(n, 0), nxt(n, 0);
        stack<int> sp, sn;
        for (int i = 0; i < n; ++i) {
            while (!sp.empty() && A[sp.top()] >= A[i]) sp.pop();
            pre[i] = sp.empty() ? i + 1 : i - sp.top();
            sp.push(i);
        }
        for (int i = n-1; i >= 0; --i) {
            while (!sn.empty() && A[sn.top()] > A[i]) sn.pop();
            nxt[i] = sn.empty() ? n - i : sn.top() - i;
            sn.push(i);
        }
        int res = 0;
        for (int i = 0; i < n; ++i) {
            (res += ((pre[i] * nxt[i]) % MOD * A[i]) % MOD) %= MOD;
        }
        return res;
    }
};
```

### 单调栈（python）
```python
class Solution:
    def sumSubarrayMins(self, A: List[int]) -> int:
        MOD = 1e9 + 7
        n = len(A)
        pre = [0] * n
        nxt = [0] * n
        sp = []
        sn = []
        for i in range(n):
            while len(sp) != 0 and A[sp[-1]] >= A[i]:
                sp.pop()
            pre[i] = i + 1 if len(sp) == 0 else i - sp[-1]
            sp.append(i)
        
        for i in range(n-1, -1, -1):
            while len(sn) != 0 and A[sn[-1]] > A[i]:
                sn.pop()
            nxt[i] = n - i if len(sn) == 0 else sn[-1] - i
            sn.append(i)
        
        res = sum([pre[i] * nxt[i] * A[i] for i in range(n)]) % MOD
        return int(res)
```

### 单调栈+动态规划（c++）
```cpp
class Solution {
public:
    static const int MOD = 1e9 + 7;

    int sumSubarrayMins(vector<int>& A) {
        int n = A.size();
        vector<int> sum(n, 0);
        stack<int> sp;
        for (int i = 0; i < n; ++i) {
            while (!sp.empty() && A[sp.top()] >= A[i]) sp.pop();
            sum[i] = sp.empty() ? (i + 1) *  A[i] : (i - sp.top()) * A[i] + sum[sp.top()];
            sum[i] %= MOD;
            sp.push(i);
        }
        int res = 0;
        for (int i = 0; i < n; ++i) {
            (res += sum[i]) %= MOD;
        }
        return res;
    }
};
```

### 单调栈+动态规划（python）
```python
class Solution:
    def sumSubarrayMins(self, A: List[int]) -> int:
        MOD = 1e9 + 7
        n = len(A)
        summ = [0] * n
        sp = []
        for i in range(n):
            while len(sp) != 0 and A[sp[-1]] >= A[i]:
                sp.pop()
            summ[i] = (i + 1) * A[i] if len(sp) == 0 else (i - sp[-1]) * A[i] + summ[sp[-1]]
            sp.append(i)

        res = sum(summ) % MOD
        return int(res)
```

## 后记
这题虽然是个中等难度的题，但是还是比一些难题难做一些的，通过这题主要去学会单调栈的使用。