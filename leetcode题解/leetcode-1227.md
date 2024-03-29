---
title: 【每日算法Day 68】脑筋急转弯：只要一行代码，但你会证吗？
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-13 11:16:29
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
[LeetCode 1227. 飞机座位分配概率](https://leetcode-cn.com/problems/airplane-seat-assignment-probability/ "LeetCode 1227. 飞机座位分配概率")

## 题目描述
有 $n$ 位乘客即将登机，飞机正好有 $n$ 个座位。第一位乘客的票丢了，他随便选了一个座位坐下。

剩下的乘客将会：
* 如果他们自己的座位还空着，就坐到自己的座位上。
* 当他们自己的座位被占用时，随机选择其他座位。

第 $n$ 位乘客坐在自己的座位上的概率是多少？


**示例1**
```text
输入：
n = 1
输出：
1.00000
解释：
第一个人只会坐在自己的位置上。
```

**示例2**
```text
输入：
n = 2
输出：
0.50000
解释：
在第一个人选好座位坐下后，第二个人坐在自己的座位上的概率是 0.5。
```

## 题解
这题呢代码相当之简单，但是我看了看题解区能真正理解的也不是很多，很多都是揣着糊涂装明白，稀里糊涂就当证过了。

首先题目并没有说第一个乘客座位号就是 $1$ 啊？也没说最后一个乘客座位号就是 $n$ 啊？所以大家的假设是怎么来的？这一点没有说清。其实很简单，不管每个乘客编号是多少，我们不用管，我们只要看他入场的次序就行了，所以我们就按照入场次序给他们重新编个号，这样的话就是按照 $1$ 到 $n$ 的编号入场了（也就是这里的编号代表的是入场的次序，而不是实际的座位号）。

然后就是 $1$ 号进场了，可以分为下面几种情况：
* 他有 $\frac{1}{n}$ 的概率选择坐在 $1$ 号座位上。这样 $2$ 到 $n$ 号位置都不会被占，那么 $n$ 号坐在自己座位的概率就是 $1.0$ 。
* 他有 $\frac{1}{n}$ 的概率选择坐在 $n$ 号座位上。这样 $2$ 到 $n-1$ 号位置都不会被占，而 $n$ 号只能坐在 $1$ 号座位上，那么概率就是 $0.0$ 。
* 他有 $\frac{1}{n}$ 的概率选择坐在 $i$ 号座位上，其中 $2 \le i \le n-1$。这样 $2$ 到 $i-1$ 号位置都不会被占，他们都坐在自己的的位置上。而 $i$ 号乘客就犯难了，他的座位被 $1$ 号占了，他不知道坐哪了。这时候，如果他选择坐 $1$ 号座位，那么 $i+1$ 到 $n$ 号乘客还是坐在自己位置，相安无事。而如果他选择坐在 $i+1$ 到 $n$ 号中的某个位置，那么必然又会产生新的冲突，这样就不好求解了啊！

对于第三种情况，我们可以换个角度看问题。现在面临的问题是，$i$ 号选择坐在哪？这时候还没入场的有 $i$ 到 $n$ 号乘客，而座位还剩 $1$ 和 $i+1$ 到 $n$ 号。那既然 $i$ 号乘客坐在 $1$ 号座位的话，后面的人都能坐回原位，那我们就把 $1$ 号座位当作是 $i$ 号乘客原本的座位就行了嘛，反正我最后又不要求 $i$ 号乘客坐回原位的概率，你坐哪都没事，只要别影响到其他人就行了。那么问题的规模就被缩小到了 $n-i+1$ ，我们递归求解就行了。

令 $f(n)$ 表示 $n$ 个人的情况下，最后一个人坐回原位的概率，按照上面的分析，我们可以列出递推式：
$$
f(n) = \frac{1}{n}\left(1 + \sum_{i=2}^{n-1}{f(n-i+1)}\right)
$$
这个递推式想必大家高中就会求了，令 $n = n-1$再写出一项：
$$
f(n-1) = \frac{1}{n-1}\left(1 + \sum_{i=2}^{n-2}{f(n-i)}\right)
$$
然后两式相减得到：
$$
nf(n) - (n-1)f(n-1) = f(n-1)
$$
即：
$$
f(n) = f(n-1) = \cdots = f(2)
$$
那么我们就可以得到最终的答案了，对任意的 $n \ge 2$ 都有 $f(n) = f(2) = 0.5$ 。

还有一个特例就是 $f(1) = 1.0$ ，这样这题就证好了。

这题最关键的一步就是 $1$ 号坐在了 $i$ 号座位后，$i$ 号何去何从？如果你能换个角度，把 $1$ 号座位给 $i$ 号（因为给他之后，对后面的乘客座位没有任何影响，那么就能把 $1$ 号座位看成就是 $i$ 号乘客的），那么问题就能递归下去了。题解区许多人这一步为什么能递归下去？根本没有讲清楚。

## 代码
### c++
```cpp
class Solution {
public:
    double nthPersonGetsNthSeat(int n) {
        return n==1 ? 1 : .5;
    }
};
```

### python
```py
class Solution:
    def nthPersonGetsNthSeat(self, n: int) -> float:
        return 1 if n==1 else .5
```