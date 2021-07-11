---
title: 【每日算法Day 61】LeetCode 672. 灯泡开关 Ⅱ
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-06 18:13:29
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
现有一个房间，墙上挂有 $n$ 只已经打开的灯泡和 $4$ 个按钮。在进行了 $m$ 次未知操作后，你需要返回这 $n$ 只灯泡可能有多少种不同的状态。

假设这 $n$ 只灯泡被编号为 $[1, 2, 3 ..., n]$，这 $4$ 个按钮的功能如下：

* 将所有灯泡的状态反转（即开变为关，关变为开）
* 将编号为偶数的灯泡的状态反转
* 将编号为奇数的灯泡的状态反转
* 将编号为 $3k+1$ 的灯泡的状态反转（$k = 0, 1, 2, \ldots$)


**示例1**
```text
输入：
n = 1, m = 1.
输出：
2
解释：
状态为: [开], [关]
```

**示例2**
```text
输入：
n = 2, m = 1.
输出：
3
解释：
状态为: [开, 关], [关, 开], [关, 关]
```

**示例3**
```text
输入：
n = 3, m = 1.
输出：
4
解释：
状态为: [关, 开, 关], [开, 关, 开], [关, 关, 关], [关, 开, 开].
```

**提示**
* $n$ 和 $m$ 都属于 $[0, 1000]$.

## 题解
首先我们要知道，**一个操作做两次就等于没做**，所以一个操作只有**没做**和**做了**两种状态，也就是说有效操作数量最多 $4$ 次：$m = \min{\{m, 4\}}$。

然后我们观察每一个操作对灯状态（初始都开着，状态都为 $1$）的影响：
* 操作 $1$ 每 $1$ 个灯状态就要反转一次，也就是灯的状态按照周期 $1$ 重复（与 $(1,\ldots)$ 异或）。
* 操作 $2$ 每 $2$ 个灯状态就要反转一次，也就是灯的状态按照周期 $2$ 重复（与 $(0,1,\ldots)$ 异或）。
* 操作 $3$ 每 $2$ 个灯状态就要反转一次，也就是灯的状态按照周期 $2$ 重复（与 $(1,0,\ldots)$ 异或）。
* 操作 $4$ 每 $3$ 个灯状态就要反转一次，也就是灯的状态按照周期 $3$ 重复（与 $(1,0,0,\ldots)$ 异或）。

综上，我们只需要取周期的最小公倍数 $6$ 就行了。也就是**只需要看前 $6$ 盏灯的最终状态，就能唯一确定后面所有灯的最终状态**。

形式化表示，用 $a_i = \{0, 1\}$ 表示第 $i$ 个操作是否用过。那么对于第 $i$ 盏灯来说，它的最终状态可以表示为：
$$
s_i = 1 \oplus a_1 \oplus a_2 [i \% 2 = 0] \oplus a_3 [i \% 2 = 1] \oplus a_4 [i \% 3 = 1]
$$
由此可以推出： $s_i = s_{i+6}$，也就是灯的最终状态以 $6$ 为周期。

到此其实可以直接暴力枚举 $1 \le n \le 6, 1 \le m \le 4$ 的所有状态了，但是还是有优化空间的。

如果我们列出前 $6$ 盏灯的状态：
* $s_1 = 1 \oplus a_1 \oplus a_3 \oplus a_4$
* $s_2 = 1 \oplus a_1 \oplus a_2$
* $s_3 = 1 \oplus a_1 \oplus a_3$
* $s_4 = 1 \oplus a_1 \oplus a_2 \oplus a_4$
* $s_5 = 1 \oplus a_1 \oplus a_3$
* $s_6 = 1 \oplus a_1 \oplus a_2$

我们可以看出，**如果前 $3$ 盏灯状态确定了，可以唯一确定出后 $3$ 盏灯状态**。因此，我们只需要计算前 $3$ 盏灯有多少种状态就行了。

最终经过枚举计算（$1 \le n \le 3, 0 \le m \le 4$）：
* 如果 $m = 0$ ，那么就只有 $1$ 种状态（灯都开着）。
* 否则如果 $n = 1$ ，那么有 $2$ 种状态。
* 否则如果 $n = 2$ ，若 $m = 1$，就有 $3$ 种状态；若 $m \ge 2$ ，就有 $4$ 种状态。
* 否则如果 $n \ge 3$ ，若 $m = 1$，就有 $4$ 种状态；若 $m = 2$ ，就有 $7$ 种状态；若 $m \ge 3$ ，就有 $8$ 种状态。

如果你实在不想手动计算，那你可以枚举所有的 $16$ 种操作状态，然后保存前三盏灯的状态到一个集合中，最终输出集合大小就行了。

## 代码
### c++
```cpp
class Solution {
public:
    int flipLights(int n, int m) {
        if (m == 0) return 1;
        if (n == 1) return 2;
        m = min(m, 3);
        if (n == 2) return vector<int>{3, 4, 4}[m-1];
        return vector<int>{4, 7, 8}[m-1];
    }
};
```

### python
```python
class Solution:
    def flipLights(self, n: int, m: int) -> int:
        if m == 0: return 1
        if n == 1: return 2
        m = min(m, 3)
        if n == 2: return [3, 4, 4][m-1]
        return [4, 7, 8][m-1]
```

### python（枚举）
```python
class Solution:
    def flipLights(self, n, m):
        seen = set()
        for cand in itertools.product((0, 1), repeat = 4):
            if sum(cand) % 2 == m % 2 and sum(cand) <= m:
                A = []
                for i in range(min(n, 3)):
                    light = 1
                    light ^= cand[0]
                    light ^= cand[1] and i % 2
                    light ^= cand[2] and i % 2 == 0
                    light ^= cand[3] and i % 3 == 0
                    A.append(light)
                seen.add(tuple(A))
        return len(seen)
```