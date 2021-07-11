---
title: 每日算法系列【LeetCode 927】三等分
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-11 09:34:21
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
给定一个由 0 和 1 组成的数组 A，将数组分成 3 个非空的部分，使得所有这些部分表示相同的二进制值。

如果可以做到，请返回任何 [i, j]，其中 i+1 < j，这样一来：

* A[0], A[1], ..., A[i] 组成第一部分；
* A[i+1], A[i+2], ..., A[j-1] 作为第二部分；
* A[j], A[j+1], ..., A[A.length - 1] 是第三部分。
* 这三个部分所表示的二进制值相等。

如果无法做到，就返回 [-1, -1]。

注意，在考虑每个部分所表示的二进制时，应当将其看作一个整体。例如，[1,1,0] 表示十进制中的 6，而不会是 3。此外，前导零也是被允许的，所以 [0,1,1] 和 [1,1] 表示相同的值。

**示例1**
```text
输入：
[1,0,1,0,1]
输出：
[0,3]
```

**示例2**
```text
输入：
[1,1,0,1,1]
输出：
[-1,-1]
```

**提示**
* 3 <= A.length <= 30000
* A[i] == 0 或 A[i] == 1

## 题解
这题虽然名义上是个难题，其实基本没有用到什么算法，只是代码实现上略微繁琐了一点。

想象如果这个数组能分成三个子数组，每个子数组表示的数字都相同，那么首先**每个子数组中 1 的数量一定要相等！**

所以我们先统计 1 的数量，如果它不是 3 的倍数，那么一定不存在划分方式，直接返回无解就行了。如果数量是 0 ，就说明数组全 0 ，那么随便划分都是合理的，任意返回就行了。

接下来将 1 的数量等分为 3 份 ，然后遍历数组，找出 3 个子数组的左右边界（注意这个边界表示的是每个子数组第一个 1 和最后一个 1 的位置）。这时候还没结束，因为最后一个子数组末尾会多出来很多 0 。所以我们需要在前两个子数组后面加上等量的 0 。

最后遍历一遍三个子数组，判断是否完全相等就行了。

听起来是很简单，代码实现的时候还是有几个小细节的。比如求边界的时候，可以利用求余操作，保存到两个数组里，这样写起来美观方便。

## 代码
### c++
```cpp
class Solution {
public:
    vector<int> threeEqualParts(vector<int>& A) {
        int n = A.size();
        int cnt1 = accumulate(A.begin(), A.end(), 0);
        if (cnt1 % 3) return {-1, -1};
        if (!cnt1) return {0, 2};
        cnt1 /= 3;
        int cnt = 0, l[3], r[3];
        for (int i = 0; i < n; ++i) {
            if (!A[i]) continue;
            cnt++;
            if ((cnt-1)%cnt1 == 0) l[(cnt-1)/cnt1] = i;
            if (cnt%cnt1 == 0) r[(cnt-1)/cnt1] = i;
        }
        for (int i = 0; i < 3; ++i) {
            r[i] += n - 1 - r[2];
        }
        for (int i = 0; i <= r[0]-l[0]; ++i) {
            if (A[l[1]+i] != A[l[0]+i] || A[l[2]+i] != A[l[0]+i])
                return {-1, -1};
        }
        return {r[0], r[1]+1};
    }
};
```

### python
```python
class Solution:
    def threeEqualParts(self, A: List[int]) -> List[int]:
        n = len(A)
        cnt1 = sum(A)
        if cnt1%3 != 0:
            return [-1, -1]
        if cnt1 == 0:
            return [0, 2]
        cnt1 //= 3
        cnt = 0
        l, r = [0]*3, [0]*3
        for i in range(n):
            if A[i] == 0:
                continue
            cnt += 1
            if (cnt-1)%cnt1 == 0:
                l[(cnt-1)//cnt1] = i
            if cnt%cnt1 == 0:
                r[(cnt-1)//cnt1] = i
        for i in range(3):
            r[i] += n - 1 - r[2]
        if A[l[1]:r[1]+1] != A[l[0]:r[0]+1] or A[l[2]:r[2]+1] != A[l[0]:r[0]+1]:
            return [-1, -1]
        return [r[0], r[1]+1]
```
