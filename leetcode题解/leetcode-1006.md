---
title: 每日算法系列【LeetCode 1006】笨阶乘
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-20 10:27:55
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
通常，正整数 n 的阶乘是所有小于或等于 n 的正整数的乘积。例如，`factorial(10) = 10 * 9 * 8 * 7 * 6 * 5 * 4 * 3 * 2 * 1`。

相反，我们设计了一个笨阶乘 `clumsy`：在整数的递减序列中，我们以一个固定顺序的操作符序列来依次替换原有的乘法操作符：`乘法(*)，除法(/)，加法(+)和减法(-)`。

例如，`clumsy(10) = 10 * 9 / 8 + 7 - 6 * 5 / 4 + 3 - 2 * 1`。然而，这些运算仍然使用通常的算术运算顺序：我们在任何加、减步骤之前执行所有的乘法和除法步骤，并且按从左到右处理乘法和除法步骤。

另外，我们使用的除法是地板除法（floor division），所以 `10 * 9 / 8` 等于 11。这保证结果是一个整数。

实现上面定义的笨函数：给定一个整数 N，它返回 N 的笨阶乘。

**示例1**
```text
输入：
4
输出：
7
解释：
7 = 4 * 3 / 2 + 1
```

**示例2**
```text
输入：
10
输出：
12
解释：
12 = 10 * 9 / 8 + 7 - 6 * 5 / 4 + 3 - 2 * 1
```

**提示**
* $1 \le N \le 10000$
* $-2^{31} \le answer \le 2^{31} - 1$  （答案保证符合 32 位整数。）

## 题解
笨方法我就不写了，按照运算顺序模拟一遍，数量比较少的话，应该也不会超时。

这道题可以用简单的数学计算来优化一下，首先笨阶乘数学形式是下面这样的：
$$
clumsy(n) = \left\lfloor\frac{n(n-1)}{n-2}\right\rfloor + (n-3) - \left\lfloor\frac{(n-4)(n-5)}{n-6}\right\rfloor + \cdots
$$

好像看不出什么东西，那么我们先去掉取整符号看看：
$$
\left\lfloor\frac{n(n-1)}{n-2}\right\rfloor = \left\lfloor\frac{(n-2)(n-2)+3(n-2)+2}{n-2}\right\rfloor = \left\lfloor n+1+\frac{2}{n-2}\right\rfloor
$$

所以当 $n \ge 5$ 时，取整符号可以直接去掉：
$$
\left\lfloor\frac{n(n-1)}{n-2}\right\rfloor = n + 1
$$

所以我们就讨论 $n \ge 5$ 时的情况，笨函数可以写成：
$$
clumsy(n) = n+1 + n-3 - (n-3) + \cdots = n+1 + d(n)
$$

也就是第一个取整（$3$ 项）结果是 $n+1$ ，而后面每 $4$ 项结果是 0 ，最后还剩余不足 $4$ 项的结果 $d(n)$ 。

那么 $d(n)$ 的取值有 $4$ 种情况：
* $n \% 4 = 0$ ，也就是最后剩余 $1$ 项时，$d(n) = 1$ 。
* $n \% 4 = 1$ ，也就是最后剩余 $2$ 项时，$d(n) = 2 - 1 = 1$ 。
* $n \% 4 = 2$ ，也就是最后剩余 $3$ 项时，$d(n) = 3 - 2 \times 1 = 1$ 。
* $n \% 4 = 3$ ，也就是最后剩余 $0$ 项时，$d(n) = 0$ 。

但是考虑到取整函数只有在 $n \ge 5$ 时才等于 $n+1$ ，也就是最后剩余的项数大于 $2$ 时才行。那么上面的 $4$ 种情况的 $1、4$ 两种，就要特殊处理一下前一个取整结果：
* $n \% 4 = 0$ ，也就是最后剩余 $1$ 项时，$d(n) = 5 - 4 \times 3 / 2 + 1 = 0$ 。
* $n \% 4 = 1$ ，也就是最后剩余 $2$ 项时，$d(n) = 2 - 1 = 1$ 。
* $n \% 4 = 2$ ，也就是最后剩余 $3$ 项时，$d(n) = 3 - 2 \times 1 = 1$ 。
* $n \% 4 = 3$ ，也就是最后剩余 $0$ 项时，$d(n) = 4 - 3 \times 2 / 1 = -2$ 。

综上，$n < 5$ 时的结果直接算出来就行了，$n \ge 5$ 时结果就是 $n+1+d(n)$ 。

## 代码
### c++
```cpp
class Solution {
public:
    int clumsy(int N) {
        int q = N & 3;
        int a[5] = {0, 1, 2, 6, 7};
        int d[4] = {1, 2, 2, -1};
        return N>=5 ? N+d[q] : a[N];
    }
};
```

### python
```python
class Solution:
    def clumsy(self, N: int) -> int:
        q = N & 3
        a = [0, 1, 2, 6, 7]
        d = [1, 2, 2, -1]
        return N+d[q] if N>=5 else a[N]
```