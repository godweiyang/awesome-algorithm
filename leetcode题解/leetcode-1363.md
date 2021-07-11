---
title: 每日算法系列【LeetCode 1363】形成三的最大倍数
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-04 12:17:07
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
给你一个整数数组 digits，你可以通过按任意顺序连接其中某些数字来形成 3 的倍数，请你返回所能得到的最大的 3 的倍数。

由于答案可能不在整数数据类型范围内，请以字符串形式返回答案。

如果无法得到答案，请返回一个空字符串。

**示例1**
```text
输入：
digits = [8,1,9]
输出：
"981"
```

**示例2**
```text
输入：
digits = [8,6,7,1,0]
输出：
"8760"
```

**示例3**
```text
输入：
digits = [1]
输出：
""
```

**示例4**
```text
输入：
digits = [0,0,0,0,0,0]
输出：
"0"
```

**提示**
* 1 <= digits.length <= 10^4
* 0 <= digits[i] <= 9
* 返回的结果不应包含不必要的前导零。

## 题解
首先要知道一个小学生都知道的定理：**如果一个数可以被 $3$ 整除，那么它的每一位上的数之和也可以被 $3$ 整除，反之也成立。**

那么问题就转化为了挑选出最多的数，使得和是 $3$ 的倍数。我们可以先求出所有数之和，记为 $sum$ ，然后有如下三种情况：
* 如果 $sum \% 3 = 0$ ，那么所有数都选中就行了。
* 如果 $sum \% 3 = 1$ ，那么必须删掉一个模 $3$ 余 $1$ 的数（按照从小到大顺序删除 1、4、7）。如果这三个数都没有，那就要删除两个模 $3$ 余 $2$ 的数（按照从小到大顺序删除 2、5、8，删除两次）。
* 如果 $sum \% 3 = 2$ ，那么必须删掉一个模 $3$ 余 $2$ 的数（按照从小到大顺序删除 2、5、8）。如果这三个数都没有，那就要删除两个模 $3$ 余 $1$ 的数（按照从小到大顺序删除 1、4、7，删除两次）。

最终将剩下的数按照从小到大顺序排序，拼接在一起就行了。

注意如果有前导 $0$ ，就说明答案就是 $0$ 。

时间复杂度为 $O(n)$ 。

## 代码
### c++
```cpp
class Solution {
public:
    int del(vector<int>& cnt, int q) {
        for (int i = 0; i <= 9; ++i) {
            if (i%3 == q && cnt[i]) {
                return --cnt[i];
            }
        }
        return -1;
    }

    string largestMultipleOfThree(vector<int>& digits) {
        vector<int> cnt(10, 0);
        int sum = 0;
        for (auto x : digits) {
            cnt[x]++;
            sum += x;
        }
        int q = sum % 3;
        if (q && del(cnt, q) < 0) {
            del(cnt, 3-q);
            del(cnt, 3-q);
        }
        string res = "";
        for (int i = 9; i >= 0; --i) {
            while (cnt[i]--) {
                res += i+'0';
            }
        }
        if (res.size() && res[0] == '0') return "0";
        return res;
    }
};
```