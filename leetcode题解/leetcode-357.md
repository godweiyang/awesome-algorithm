---
title: 每日算法系列【LeetCode 357】计算各个位数不同的数字个数
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-12 16:42:08
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
给定一个非负整数 n ，计算各位数字都不同的数字 x 的个数，其中 $0 \le x < 10^n$ 。

**示例1**
```text
输入：
2
输出：
91
解释：
答案应为除去 11,22,33,44,55,66,77,88,99 外，在 [0,100) 区间内的所有数字。
```

## 题解
这题要求所有数位数字都不相同的数字个数，那么我们可以将答案分成不同长度。

### 方法1
对于长度为 n 的数字，第一位取值有 1~9 一共 9 种情况，而后面 n - 1 位可以从 0~9 中随机取出 n - 1 个不同数字，然后随机排列。因为要和第一位不同，所以后面的数字选择只有 9 种情况，所以方案数是排列数 $A_{9}^{n-1}$。

然后一直累和到长度为 1 的数字，最后还有个特例，就是 0 ，它是首位可以为 0 的唯一情况。

所以最终答案就是 $\sum_{i=0}^{n-1}{A_{9}^{i}} + 1$ 。具体实现的时候可以将阶乘预处理好，然后直接用就行了。

### 方法2
因为 n 不会太大，所以我们可以本地将每个 n 对应的答案算出来，然后保存到数组里，提交的时候直接取答案就行了。这种方法非常投机取巧，适合直接做会超时，但是答案数量又不是太多的情况。

## 代码
### 方法1（c++）
```cpp
class Solution {
public:
    int countNumbersWithUniqueDigits(int n) {
       int fact[10] = {1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880};
       int res = 0;
       for (int i = 10-n; i <= 9; ++i) {
           res += fact[9] / fact[i];
       }
       return res * 9 + 1;
    }
};
```

### 方法1（python）
```python
class Solution:
    def countNumbersWithUniqueDigits(self, n: int) -> int:
        fact = [1, 1, 2, 6, 24, 120, 720, 5040, 40320, 362880]
        res = 0
        for i in range(10-n, 10):
            res += int(fact[9] / fact[i])
        return res * 9 + 1
```

### 方法2（c++）
```cpp
class Solution {
public:
    int countNumbersWithUniqueDigits(int n) {
       int res[] = {1, 10, 91, 739, 5275, 32491, 168571, 712891, 2345851, 5611771};
       return res[n];
    }
};
```

### 方法2（python）
```python
class Solution:
    def countNumbersWithUniqueDigits(self, n: int) -> int:
        res = [1, 10, 91, 739, 5275, 32491, 168571, 712891, 2345851, 5611771]
        return res[n]
```

## 后记
这题还可以用递归、解出和式的通式等方法求解，本质上没有太大区别。