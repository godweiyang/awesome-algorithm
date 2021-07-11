---
title: 每日算法系列【LeetCode 825】适龄的朋友
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-25 15:20:56
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
人们会互相发送好友请求，现在给定一个包含有他们年龄的数组，ages[i] 表示第 i 个人的年龄。

当满足以下条件时，A 不能给 B（A、B不为同一人）发送好友请求：

* age[B] <= 0.5 * age[A] + 7
* age[B] > age[A]
* age[B] > 100 && age[A] < 100

否则，A 可以给 B 发送好友请求。

注意如果 A 向 B 发出了请求，不等于 B 也一定会向 A 发出请求。而且，人们不会给自己发送好友请求。 

求总共会发出多少份好友请求?

**示例1**
```text
输入：
[16,16]
输出：
2
解释：
二人可以互发好友申请。
```

**示例2**
```text
输入：
[16,17,18]
输出：
2
解释：
好友请求可产生于 17 -> 16, 18 -> 17.
```

**示例3**
```text
输入：
[20,30,100,110,120]
输出：
3
解释：
好友请求可产生于 110 -> 100, 120 -> 110, 120 -> 100.
```

**提示**
* 1 <= ages.length <= 20000.
* 1 <= ages[i] <= 120.

## 题解
仔细观察一下这三个条件，我们把 $age[A]$ 简写为 a ，把 $age[B]$ 简写为 b ，那么如果 a 可以向 b 发送邀请的话，必须同时满足下面三个条件：

* $a \le 2b-15$
* $a \ge b$
* $a \ge 100 || b \le 100$

可以发现，如果满足了条件 2 ，那么一定会满足条件 3 ，所以最终只需要满足 $b \le a \le 2b-15$ 就行了。
### 暴力法
这题最暴力的方法显然就是遍历任意数对 a 和 b ，看两个数是否符合这个条件，但是时间复杂度太高，不可行。

### 二分法
另一个方法是先对数组进行排序，然后遍历每一个数作为 b ，然后二分寻找 $2b-15$ 在哪就行了，中间的数字都可以作为 a ，这样最终的时间复杂度是 $O(n \log n)$ 。

### 计数法
有没有更好的办法呢？注意到年龄的范围最大只有 120 ，那么我们可以统计每个年龄出现的人数，用 $c[i]$ 来表示 i 岁的人数。那么 b 岁的人数就有 $c[b]$ 个，而符合条件的 a 在 $[b, 2b-15]$ 之间。其中 $a = b$ 需要单独讨论，因为包含了自己邀请自己的情况，这种情况邀请的数量是 $c[b] \cdot (c[b] - 1)$ 。而 a 在 $[b+1, 2b-15]$ 范围内的话，数量是 $\sum_{b+1 \le a \le 2b-15}{c[b] \cdot c[a]}$ 。所以最终的总数量就是：
$$
\begin{aligned}
S_b &= \sum_{b+1 \le a \le 2b-15}{c[b] \cdot c[a]} + c[b] \cdot (c[b] - 1)    \\\
&= c[b] \cdot (\sum_{b \le a \le 2b-15}{c[a]} - 1)
\end{aligned}
$$
如果用前缀和 $sum$ 来预处理 $c$ 数组的话，可以进一步简化为：
$$
S_b = c[b] \cdot (sum[2b-15] - sum[b-1] - 1)
$$
考虑到 $2b-15$ 可能会大于数组中年龄的最大值，所以我们设置一个阈值来截断它。最终的总数量就是 $\sum_{b}{S_b}$ ，这里 b 的取值范围是有讲究的。因为不等式需要满足 $b \le 2b-15$ ，所以 $b \ge 15$ 。这样最终的时间复杂度降到了 $O(MA)$ ，其中 MA 表示年龄的最大值。

## 代码
### c++
```cpp
class Solution {
public:
    int numFriendRequests(vector<int>& ages) {
        const int MA = 120;
        vector<int> count(MA+1, 0), sum(MA+1, 0);
        int n = ages.size(), res = 0;
        for (int i = 0; i < n; ++i) {
            count[ages[i]]++;
        }
        for (int i = 1; i <= MA; ++i) {
            sum[i] = sum[i-1] + count[i];
        }
        for (int b = 15; b <= MA; ++b) {
            res += count[b] * (sum[min(MA, 2*b-15)] - sum[b-1] - 1);
        }
        return res;
    }
};
```

### python
```python
class Solution:
    def numFriendRequests(self, ages: List[int]) -> int:
        MA = 120
        count, S = [0]*(MA+1), [0]*(MA+1)
        n, res = len(ages), 0
        for age in ages:
            count[age] += 1
        for i in range(1, MA+1):
            S[i] = S[i-1] + count[i]
        for b in range(15, MA+1):
            res += count[b] * (S[min(MA, 2*b-15)] - S[b-1] - 1)
        return res
```