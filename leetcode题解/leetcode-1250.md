---
title: 每日算法系列【LeetCode 1250】检查「好数组」
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-10 23:58:57
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 关注公众号【算法码上来】，每日算法干货马上就来！

![](https://godweiyang.com/medias/contact.jpg)

## 题目描述
给你一个正整数数组 nums ，你需要从中任选一些子集，然后将子集中每一个数乘以一个任意整数，并求出他们的和。

假如该和结果为 1 ，那么原数组就是一个「好数组」，则返回 True ；否则请返回 False 。

**示例1**
```text
输入：
nums = [12,5,7,23]
输出：
true
解释：
挑选数字 5 和 7 。
5*3 + 7*(-2) = 1
```

**示例2**
```text
输入：
nums = [29,6,10]
输出：
true
解释： 
挑选数字 29 , 6 和 10 。
29*1 + 6*(-3) + 10*(-1) = 1
```

**示例3**
```text
输入：
nums = [3,6]
输出：
false
```

**提示**

* 1 <= nums.length <= 10^5
* 1 <= nums[i] <= 10^9

## 题解
这题名义上是困难难度，实际上只要知道一些数学知识，就非常的简单。

首先题目中要求挑选出一些数，然后给每个数分配整数系数，加权求和等于 1 。
仔细想一想就不对劲，全选不是一样嘛？有些数系数分配 0 就行了。

假设系数分别是 $x_1, x_2, \ldots, x_n$ ，那么问题就变成了求解下面的多元一次方程有整数解的条件：
$$
    a_1 x_1 + a_2 x_2 + \dots + a_n x_n = 1
$$
如果你数学基础不错的话，一眼就会发现条件就是**所有非零数的最大公约数为 1** ：
$$
    gcd(a_1, a_2, \ldots, a_n) = 1, a_i \neq 0
$$

证明参见 n 个数的[裴蜀定理](https://en.wikipedia.org/wiki/B%C3%A9zout%27s_identity, "维基百科：裴蜀定理")

## 代码
```cpp
class Solution {
public:
    bool isGoodArray(vector<int>& nums) {
        int x = nums[0], n = nums.size();
        for (int i = 1; i < n; ++i) {
            if (nums[i]) x = gcd(x, nums[i]);
        }
        return x == 1;
    }

    int gcd(int x, int y) {
        return x%y ? gcd(y, x%y) : y;
    }
};
```

## 后记
最后不管是用时还是空间消耗都超越了100%的用户。