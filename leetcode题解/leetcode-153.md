---
title: 每日算法系列【LeetCode 153】寻找旋转排序数组中的最小值
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-26 18:46:02
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
假设按照升序排序的数组在预先未知的某个点上进行了旋转。

(例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2])。

请找出其中最小的元素。

你可以假设数组中不存在重复元素。

**示例1**
```text
输入：
[3,4,5,1,2]
输出：
1
```

**示例2**
```text
输入：
[4,5,6,7,0,1,2]
输出：
0
```

## 题解
这题如果直接遍历一遍的话，时间复杂度是 $O(n)$ ，也能过。但是这题显然想要你更快，也就是用 $O(\log n)$ 的时间复杂度来做出来，那我们只能选择用二分法。

因为序列从中间切开来，然后调换过顺序，所以是先上升，再下降一下，然后再上升。并且第二段上升的最大值 $nums[n-1]$ 是一定小于第一段上升的最小值 $nums[0]$ 的，所以最小值一定是第二段的第一个数。

假设我们二分的时候，左端点 l ，右端点 r ，中间点是 m 。

如果 $nums[l] > nums[r]$ ，那说明左端点在第一段，右端点在第二段。这时如果 $nums[m] \ge nums[l]$ ，那么 m 也在第一段，所以 l 需要右移；否则的话 m 在第二段， r 需要左移。

如果 $nums[l] \le nums[r]$ ，那么两个端点都在第二段，是单调上升的，那最小值一定就是 l 。

## 代码
### c++
```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int l = 0, r = nums.size() - 1;
        while (l < r) {
            int m = (l + r) / 2;
            if (nums[l] > nums[r]) {
                if (nums[m] >= nums[l]) {
                    l = m + 1;
                } else {
                    r = m;
                }
            } else {
                r = l;
            }
        }
        return nums[r];
    }
};
```

### python
```python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        l, r = 0, len(nums)-1
        while l < r:
            m = (l + r) // 2
            if nums[l] > nums[r]:
                if nums[m] >= nums[l]:
                    l = m + 1
                elif nums[m] < nums[r]:
                    r = m
            else:
                r = l
        return nums[r]
```
