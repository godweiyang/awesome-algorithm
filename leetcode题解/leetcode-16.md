---
title: 每日算法系列【LeetCode 16】最接近的三数之和
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-09 10:52:54
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
给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在唯一答案。

**示例1**
```text
输入：
nums = [-1，2，1，-4], target = 1.
输出：
2
解释：
与 target 最接近的三个数的和为 2. (-1 + 2 + 1 = 2).
```

## 题解
最暴力的方法就是直接枚举三个不同的数，然后求出差值最小的和，但是这样时间复杂度是 $O(n^3)$ ，太高了。

那么我们先枚举一个数试试，并且假设它是最小的数，然后寻找比它大的两个数就行了，这就需要我们先对数组进行排序（假设排序后数组是 $a$ ）。

如果枚举的数是 $a[i]$ ，那么我们只需要寻找和 $t - a[i]$ 差值最小的两个数之和就行了。

如果用双指针的方法，初始时令 $l = i + 1$ ，同时 $r = n - 1$ 。那么如果 $a[i] + a[l] + a[r] > t$ ，就说明 $r$ 太大了，需要左移。否则的话如果 $a[i] + a[l] + a[r] < t$ ，就说明 $l$ 太小了，需要右移。在不断移动的过程中更新最小差值就行了，因为 $l$ 和 $r$ 最终一共只移动了 $O(n)$ 步，所以总的时间复杂度只有 $O(n \log n + n^2)$ ，忽略低阶项之后只有 $O(n^2)$ ，还是可以接受的。

## 代码
### c++
```cpp
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        int n = nums.size(), res = 10000000;
        sort(nums.begin(), nums.end());
        for (int i = 0; i < n - 2; ++i) {
            int l = i + 1, r = n - 1;
            while (l < r) {
                int sum = nums[i] + nums[l] + nums[r];
                if (sum == target) return sum;
                if (abs(sum-target) < abs(res-target)) res = sum;
                if (sum > target) r--;
                else l++;
            }
        }
        return res;
    }
};
```

### python
```python
class Solution:
    def threeSumClosest(self, nums: List[int], target: int) -> int:
        n, res = len(nums), 10000000
        nums.sort()
        for i in range(n-2):
            l, r = i+1, n-1
            while l < r:
                s = nums[i] + nums[l] + nums[r]
                if s == target:
                    return s
                if abs(s-target) < abs(res-target):
                    res = s
                if s > target:
                    r -= 1
                else:
                    l += 1
        return res
```
