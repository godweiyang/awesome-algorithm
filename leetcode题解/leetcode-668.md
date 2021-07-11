---
title: 【每日算法Day 83】邻居小孩一年级就会的乘法表，你会吗？
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-28 13:06:56
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
[LeetCode 668. 乘法表中第k小的数](https://leetcode-cn.com/problems/kth-smallest-number-in-multiplication-table/ "LeetCode 668. 乘法表中第k小的数")

## 题目描述
几乎每一个人都用乘法表。但是你能在乘法表中快速找到第 $k$ 小的数字吗？

给定高度 $m$、宽度 $n$ 的一张 $m \times n$ 的乘法表，以及正整数 $k$，你需要返回表中第 $k$ 小的数字。

**示例1**
```text
输入：
m = 3, n = 3, k = 5
输出：
3
解释：
乘法表:
1	2	3
2	4	6
3	6	9
第5小的数字是 3 (1, 2, 2, 3, 3).
```

**示例2**
```text
输入：
m = 2, n = 3, k = 6
输出：
6
解释：
乘法表:
1	2	3
2	4	6
第6小的数字是 6 (1, 2, 2, 3, 4, 6).
```

**说明：**
* $m$ 和 $n$ 的范围在 $[1, 30000]$ 之间。
* $k$ 的范围在 $[1, mn]$ 之间。

## 题解
### 二分法
因为 $mn$ 数量级是 $9 \times 10^8$ 级别的，所以显然不能直接枚举，要想一个对数级别的算法。

对数级别首先想到的肯定是二分了，我们二分第 $k$ 小的数 $mid$ ，然后求出乘法表中小于等于 $mid$ 的数的数量 $cnt$ 。如果发现 $cnt \le mid$ ，那就说明这个答案太大了，还可以继续缩小。否则的话答案太小了，得增大一点。

那么对于枚举的答案 $mid$ 来说，如何找到乘法表中有多少小于等于它的数呢？我们可以直接从 $1$ 开始枚举，和 $1$ 相乘并且结果小于等于 $mid$ 的数有 $mid$ 个，当然还有个 $n$ 的限制，所以是 $\min{(mid, n)}$ 个。然后和 $2$ 相乘并且结果小于等于 $mid$ 的数有 $\min{(\left\lfloor\frac{mid}{2}\right\rfloor, n)}$ 个。依此类推下去，最终和 $m$ 相乘并且结果小于等于 $mid$ 的数有 $\min{(\left\lfloor\frac{mid}{m}\right\rfloor, n)}$ 个。

所以最终小于等于 $mid$ 的个数 $cnt$ 就可以计算为：
$$
\sum_{i=1}^{m}{\min{\left(\left\lfloor\frac{mid}{i}\right\rfloor, n\right)}}
$$

### 二分法+优化
当然这题计算还可以进行一些优化。

首先第 $k$ 小的数是一定小于等于 $k$ 的，所以我们的二分上界可以定为 $k$ 。

其次注意到当 $i > mid$ 之后，个数一定是 $0$，所以 $i$ 只需要枚举到 $\min{(mid, m)}$ 就行了。

然后当 $i \le \left\lfloor\frac{mid}{n}\right\rfloor$ 时，有 $\min{\left(\left\lfloor\frac{mid}{i}\right\rfloor, n\right)} = n$，所以这部分的求和结果就是 $n\left\lfloor\frac{mid}{n}\right\rfloor$ 。所以 $cnt$ 又可以写为：
$$
n\left\lfloor\frac{mid}{n}\right\rfloor + \sum_{i=\left\lfloor\frac{mid}{n}\right\rfloor+1}^{\min{(mid, m)}}{\left\lfloor\frac{mid}{i}\right\rfloor}
$$

最后，对于某个 $i = t$ ，我们会发现如果 $i$ 慢慢增大，某一段连续区间内 $\left\lfloor\frac{mid}{i}\right\rfloor$ 的值都是不会变的。而 $i$ 最大可以增大到 $\left\lfloor\frac{mid}{\left\lfloor\frac{mid}{t}\right\rfloor}\right\rfloor$，那么这一段区间内的求和就可以直接算出来：
$$
\left\lfloor\frac{mid}{t}\right\rfloor \left(\left\lfloor\frac{mid}{\left\lfloor\frac{mid}{t}\right\rfloor}\right\rfloor-t+1\right)
$$
接着令 $i$ 直接跳转到 $\left\lfloor\frac{mid}{\left\lfloor\frac{mid}{t}\right\rfloor}\right\rfloor + 1$ 就可以了，这样就不用慢慢加 $1$ 计算了。要特别注意的是最后不能超过 $m$ 。

理论上这样的计算复杂度是更低的，但是实际运行中速度还不如不加最后一步优化，可能原因是除法操作次数太多了，反而总的操作次数超过了直接遍历计算。

## 代码
### 二分法（c++）
```cpp
class Solution {
public:
    int findKthNumber(int m, int n, int k) {
        int l = 1, r = m*n;
        while (l < r) {
            int mid = l+((r-l)>>1);
            if (enough(mid, m, n, k)) r = mid;
            else l = mid+1;
        }
        return l;
    }

    bool enough(int x, int m, int n, int k) {
        int cnt = 0;
        for (int i = 1; i <= m; ++i) {
            cnt += x/i<n?x/i:n;
        }
        return cnt >= k;
    }
};
```

### 二分法+优化（c++）
```cpp
class Solution {
public:
    int findKthNumber(int m, int n, int k) {
        int l = 1, r = k;
        while (l < r) {
            int mid = l+((r-l)>>1);
            if (enough(mid, m<mid?m:mid, n<mid?n:mid, k)) r = mid;
            else l = mid+1;
        }
        return l;
    }

    bool enough(int x, int m, int n, int k) {
        int cnt = n*(x/n), d = 0;
        for (int i = (x/n)+1; i <= m; i = d+1) {
            d = x/(x/i);
            cnt += (x/i)*((d<m?d:m)-i+1);
        }
        return cnt >= k;
    }
};
```

### 二分法（python）
```python
class Solution:
    def findKthNumber(self, m: int, n: int, k: int) -> int:
        def enough(x, m, n, k):
            cnt = 0
            for i in range(1, m+1):
                cnt += x//i if x//i<n else n
            return cnt >= k

        l, r = 1, m*n
        while l < r:
            mid = l+((r-l)>>1)
            if enough(mid, m, n, k): r = mid
            else: l = mid+1
        return l
```

### 二分法+优化（python）
```python
class Solution:
    def findKthNumber(self, m: int, n: int, k: int) -> int:
        def enough(x, m, n, k):
            cnt, i, d = n*(x//n), x//n+1, 0
            while i <= m:
                d = x//(x//i)
                cnt += (x//i)*((d if d<m else m)-i+1)
                i = d+1
            return cnt >= k

        l, r = 1, k
        while l < r:
            mid = l+((r-l)>>1)
            if enough(mid, m if m<mid else mid, n if n<mid else mid, k): r = mid
            else: l = mid+1
        return l
```
