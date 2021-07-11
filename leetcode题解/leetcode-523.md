---
title: 每日算法系列【LeetCode 523】连续的子数组和
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-02 11:53:29
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
给定一个包含非负数的数组和一个目标整数 k，编写一个函数来判断该数组是否含有连续的子数组，其大小至少为 2，总和为 k 的倍数，即总和为 n*k，其中 n 也是一个整数。

**示例1**
```text
输入：
[23,2,4,6,7], k = 6
输出：
True
解释：
[2,4] 是一个大小为 2 的子数组，并且和为 6。
```

**示例2**
```text
输入：
[23,2,6,4,7], k = 6
输出：
True
解释：
[23,2,6,4,7]是大小为 5 的子数组，并且和为 42。
```

**提示**
* 数组的长度不会超过 10000 。
* 你可以认为所有数字总和在 32 位有符号整数范围内。

## 题解
### 暴力法
直接枚举所有的区间，然后求出每个区间的和，看是不是 k 的整数倍就行了。这种方法时间复杂度是 $O(n^3)$ ，一定过不了的。

### 前缀和优化
还是枚举所有区间，但是预处理的时候把所有的前缀和保存到数组里，这样区间求和就可以直接计算出来了。最后时间复杂度是 $O(n^2)$ ，理论上应该还是没法通过，但是这题数据太弱，竟然勉强通过了。

### 求余优化
假设前缀和为 sum ，那么区间 [i, j] 的和就可以表示为 sum[j]-sum[i-1] ，如果它是 k 的倍数，就说明了 sum[j] 和 sum[i-1] 模 k 的余数是相同的。

那么我们就可以提前把 sum 数组里的每个数都对 k 求余，然后看有没有两个余数是相同的，并且距离大于等于 2 就行了。

这只需要用一个哈希表就可以判断一个数有没有在之前出现过了。如果一个数没有出现过，就把它的下标放进哈希表。否则的话就判断当前下标和哈希表中的下标差值，如果大于等于 2 ，就找到合法区间了，直接返回 true 。


## 代码
### c++
```cpp
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        if (n < 2) return false;
        if (!k) {
            for (int i = 1; i < n; ++i) {
                if (!nums[i] && !nums[i-1]) {
                    return true;
                }
            }
            return false;
        }
        unordered_map<int, int> mp;
        mp[0] = 0;
        int sum = 0;
        for (int i = 0; i < n; ++i) {
            (sum += nums[i]) %= k;
            if (mp.find(sum) == mp.end()) {
                mp[sum] = i + 1;
            } else if (i + 1 - mp[sum] >= 2) {
                return true;
            }
        }
        return false;
    }
};
```

### python
```python
class Solution:
    def checkSubarraySum(self, nums: List[int], k: int) -> bool:
        n = len(nums)
        if k == 0:
            for i in range(1, n):
                if nums[i] == 0 and nums[i-1] == 0:
                    return True
            return False
        mp = {}
        mp[0] = 0
        sum = 0
        for i in range(n):
            sum += nums[i]
            sum %= k
            if sum not in mp:
                mp[sum] = i + 1
            elif i + 1 - mp[sum] >= 2:
                return True
        return False
```

### 后记
c++ 有多种实现方法，可以用 map 、hash_map 、unordered_map 等多种数据结构。其中 hash_map 不在标准库里，这里没法使用。理论上 unordered_map 比 map 会快一点，但是实际运行中没有发现差别。