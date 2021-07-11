---
title: 【每日算法Day 66】经典面试题：不用四则运算如何做加法？
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-11 23:07:21
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
[LeetCode 面试题65. 不用加减乘除做加法](https://leetcode-cn.com/problems/bu-yong-jia-jian-cheng-chu-zuo-jia-fa-lcof/ "LeetCode 面试题65. 不用加减乘除做加法")

## 题目描述
写一个函数，求两个整数之和，要求在函数体内不得使用 $+,-,*,/$ 四则运算符号。

**示例1**
```text
输入：
a = 1, b = 1
输出：
2
```

**提示**
* $a, b$ 均可能是负数或 $0$
* 结果不会溢出 $32$ 位整数

## 题解
因为不允许采用四则运算，所以只能考虑位运算了。

其实就是用二进制来模拟加法操作。首先将两个数最低位相加，如果都是 $1$ ，那么就得到 $0$ ，并且进位 $1$ ，然后接着算下一位。

但是这样一位一位模拟不方便实现，更简单的实现方法是直接把两个数对应位相加，不管进位。然后进位单独计算，如果某一位两个数都是 $1$ ，那么进位就会对下一位产生影响。然后接着算不进位求和加上进位的值，再计算新的进位，依次重复下去，直到进位为 $0$ 为止。

用一个实际的例子来演示一下，计算 $3+7$ 的值，其中 $s$ 表示每一步不考虑进位的求和，$c$ 表示每一步的进位，最后得到结果 $1010$ ，也就是十进制的 $10$ ：
![](1.jpg)

但是这里还是用到了加法怎么办呢？因为是二进制，所以不考虑进位求和的话，可以直接采用**异或**运算。而计算进位的话，直接用**位与**和**左移一位**就行了。

在 c++ 和 python 具体实现中，还有几个注意事项：
* LeetCode c++ 不允许负数左移操作，所以要转换成无符号整数。
* python 因为位数没有限制，所以负数补码会很长，所以要位与 0xffffffff 处理成 $32$ 位整型数。
* c++ 还可以写成递归形式，也就是 $a+b$ 可以递归成 $s+c$ ，其中 $s$ 表示不进位求和结果，$c$ 表示进位的值。


## 代码
### 非递归（c++）
```cpp
class Solution {
public:
    int add(int a, int b) {
        while (b) {
            int carry = (unsigned int)(a & b) << 1;
            a ^= b;
            b = carry;
        }
        return a;
    }
};
```

### 递归（c++）
```cpp
class Solution {
public:
    int add(int a, int b) {
        return b ? add(a^b, (unsigned int)(a&b)<<1) : a;
    }
};
```

### 非递归（python）
```python
class Solution:
    def add(self, a: int, b: int) -> int:
        a &= 0xffffffff
        b &= 0xffffffff
        while b != 0:
            carry = ((a & b) << 1) & 0xffffffff
            a ^= b
            b = carry
        return a if a < 0x80000000 else ~(a^0xffffffff)
```

### 投机取巧（python）
```python
class Solution:
    def add(self, a: int, b: int) -> int:
        return sum([a, b])
```