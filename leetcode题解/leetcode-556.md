---
title: 每日算法系列【LeetCode 556】下一个更大元素 III
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-06 08:23:38
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
给定一个32位正整数 n，你需要找到最小的32位整数，其与 n 中存在的位数完全相同，并且其值大于n。如果不存在这样的32位整数，则返回-1。

**示例1**
```text
输入：
12
输出：
21
```

**示例2**
```text
输入：
21
输出：
-1
```

## 题解
首先要发现一个性质，如果调换两个数位之后，整个数字变大了，那说明第一个数位的数字小于第二个数位的数。所以我们只需要找到一个顺序对，调换它俩顺序就行了。

但是如果存在两个顺序对 $(i_1, j_1)$ 和 $(i_2, j_2)$ ，那我们就要找 $i$ 更大的那个，因为前面的尽量不动才会使调换后的数字最小。

如果 $i$ 相同的话，就要在 $i$ 右边找最小的使得 $nums[j] > nums[i]$ 的数，这样 $i$ 处的数字才是最小的，同时整体数字还会变大。

最后因为 $i$ 处已经变大了，所以 $i$ 后面的数字全部都要升序排列，这样整体数字才是最小的。

所以整体算法就有了，我们从右往左找，找到第一个上升的位置 $i$ ，也就是 $nums[i] < nums[j]$ 。这样 $i$ 右边就是降序了，不存在顺序对。然后在 $i$ 右边的数字中二分查找最小的大于 $nums[i]$ 的数 $nums[j]$ ，调换它俩位置。最后把 $i$ 右边的数字变成升序即可。

## 代码
### c++
```cpp
class Solution {
public:
    int nextGreaterElement(int n) {
        int a[11], len = 0;
        while (n > 0) {
            a[++len] = n % 10;
            n /= 10;
        }
        a[0] = INT_MIN;
        int ok = 0;
        for (int i = 1; i <= len; ++i) {
            if (a[i] < a[i-1]) {
                int idx = upper_bound(a+1, a+i, a[i]) - a;
                swap(a[idx], a[i]);
                for (int j = 1; j <= (i-1)/2; ++j) {
                    swap(a[j], a[i-j]);
                }
                ok = 1;
                break;
            }
        }
        if (!ok) return -1;
        long res = 0;
        for (int i = len; i > 0; --i) {
            res = res * 10 + a[i];
        }
        return res > INT_MAX ? -1 : res;
    }
};
```

### python
```python
class Solution:
    def nextGreaterElement(self, n: int) -> int:
        s = str(n)
        n = len(s)
        res = ''
        for i in range(n-1, -1, -1):
            res = s[i] + res
            if i < n-1 and res[0] < res[1]:
                res = ''.join(sorted(res))
                for j in range(n-i):
                    if res[j] > s[i]:
                        res = res[j] + res[:j] + res[j+1:]
                        break
                s = s[:i] + res
                return int(s) if int(s) < pow(2, 31) else -1
        return -1
```

## 后记
这题还可以直接用 c++ 标准库函数 `next_permutation` 直接生成下一个更大的字符串排列，然后转换成整数就行了，代码如下：

```cpp
class Solution {
public:
    int nextGreaterElement(int n) {
        string s = to_string(n);
        next_permutation(s.begin(), s.end());
        long res = stoll(s);
        return res > INT_MAX || res <= n ? -1 : res;
    }
};
```