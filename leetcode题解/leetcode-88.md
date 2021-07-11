---
title: 【每日算法Day 96】腾讯面试题：合并两个有序数组
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-10 20:24:17
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 昨天腾讯一面上来就给我整的这道 easy 难度的题，然后我太紧张了还想了一会儿，差点炸裂。

## 题目链接
[LeetCode 88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/ "LeetCode 88. 合并两个有序数组")

## 题目描述
给你两个有序整数数组 `nums1` 和 `nums2`，请你将 `nums2` 合并到 `nums1` 中，使 `nums1` 成为一个有序数组。

**说明：**
* 初始化 `nums1` 和 `nums2` 的元素数量分别为 `m` 和 `n` 。
* 你可以假设 `nums1` 有足够的空间（空间大小大于或等于 `m + n`）来保存 `nums2` 中的元素。

**示例1**
```text
输入：
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3
输出:
[1,2,2,3,5,6]
```

## 题解
看到这道题，脑海里应该第一时间想到的是归并排序，但是归并排序需要一个额外的数组用来保存排序后的数组，这里不允许使用额外空间。

那么我们还是用归并排序的思路来做，试一下两个指针 `i = 0` 和 `j = 0` ，初始的时候分别指着 `nums1[0]` 和 `nums2[0]` 。然后比较 `nums1[i]` 和 `nums2[j]` 大小，如果 `nums1[i]` 更小，那么就放在原位不动它，然后 `i += 1`。如果 `nums2[j]` 更小，那么就交换 `nums1[i]` 和 `nums2[j]` ，然后还是 `i += 1`。这么看貌似可行哦？但是最终一定会先遍历完 `nums1`，然后 `j` 还是停留在 `0` ，然后你会发现 `nums2` 中的数字还是乱序的，根本没法处理。

那么怎么利用上 `nums1` 后面多出的那么多空位呢？我们可以换个思路，从最大的开始遍历。两个指针初始的时候 `i = m-1` 和 `j = n-1` ，然后将较大值填充到 `nums1` 的最后面。最后如果 `nums2` 中还有剩余，就依次填充到 `nums1` 最前面就行了。

这样为什么就可以了呢？因为如果从小到大遍历的话，元素会覆盖掉 `nums1` 中还没遍历的元素。但是从大到小是填充到尾部，就不会产生覆盖。就算极限情况下 `nums2` 中元素全部大于 `nums1` 中元素，也不会覆盖到 `nums1` 的最后一个元素。

面试官最后还会问你有啥优化，我当时图省事，最后还把 `nums1` 中剩下元素填充到 `nums1` 最前面了，其实完全没有必要，本来就是有序的，等于没有做事。

## 代码
### c++
```cpp
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int i = m-1, j = n-1, k = m+n-1;
        while (i >= 0 && j >= 0) {
            if (nums1[i] > nums2[j]) nums1[k--] = nums1[i--];
            else nums1[k--] = nums2[j--];
        }
        while (j >= 0) nums1[k--] = nums2[j--];
    }
};
```

### python
```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        i, j = m-1, n-1
        while i >= 0 and j >= 0:
            if nums1[i] > nums2[j]:
                nums1[i+j+1] = nums1[i]
                i -= 1
            else:
                nums1[i+j+1] = nums2[j]
                j -= 1
        while j >= 0:
            nums1[j] = nums2[j]
            j -= 1
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)