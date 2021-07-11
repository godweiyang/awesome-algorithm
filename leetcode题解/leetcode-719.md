---
title: 每日算法系列【LeetCode 719】找出第 k 小的距离对
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-08 09:48:26
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
给定一个整数数组，返回所有数对之间的第 k 个最小距离。一对 (A, B) 的距离被定义为 A 和 B 之间的绝对差值。

**示例1**
```text
输入：
nums = [1,3,1]
k = 1
输出：
0
解释：
所有数对如下：
(1,3) -> 2
(1,1) -> 0
(3,1) -> 2
因此第 1 个最小距离的数对是 (1,1)，它们之间的距离为 0。
```

**提示**
* 2 <= len(nums) <= 10000.
* 0 <= nums[i] < 1000000.
* 1 <= k <= len(nums) * (len(nums) - 1) / 2.

## 题解
注意到这题 $n$ 比较大，所以不能算出所有的数对差值，然后排序，这样时间复杂度就是 $O(n^2 \log {n^2})$ 了。

直觉上应该先给数组从小到大排个序，那么差值最小值就是 0 ，最大值就是 $nums[n-1] - nums[0]$ ，答案肯定也就在这个区间里了。

那么我们可以从最大的差值开始，看是否有数对满足这个差值，并且这个差值还得是第 $k$ 小的。对于差值 $m$ ，如果它是第 $k$ 小的，说明所有数对中差值小于等于 $m$ 的数对个数 $c$ 一定大于等于 $k$ 。因为如果第 $k$ 小的差值只有一个数对满足，那么 $c = k$ ，否则的话就有多个数对差值都是 $m$ ，那就有 $c > k$ 。于是我们找到第一个满足 $c < k$ 的差值就行了，再加 1 就是最终的答案。

那么怎么求小于等于 $m$ 的数对个数 $c$ 呢？因为排过序了，所以可以采用双指针的方法，初始时 $l = r = 0$ ，对于每个右指针 $r$ ，我们移动左指针 $l$ ，直到 $nums[r] - nums[l] \le m$ 。那么最终 $r - l$ 就是以 $nums[r]$ 作为较大数的数对个数。因为两个指针都是向右移动的，所以每次计算个数只需要 $O(n)$ 的时间复杂度。

所以这个方法最终总的时间复杂度为 $O(Wn + n \log n)$ ，因为最大差值 $W$ 还是太大，所以还是没有办法直接遍历。

又注意到随着 $m$ 的减小， $c$ 也是单调减小的，所以可以二分寻找 $m$ 。如果 $c \ge k$ ，那么说明答案小于等于 $m$ ，于是令 $r = m$ 。如果 $c < k$ ，那么说明答案一定大于 $m$ ，于是令 $l = m + 1$ ，最终答案就是 $l$ 。

这样二分优化之后，最终的时间复杂度为 $O(n\log W + n\log n)$ ，可以接受。

## 代码
### c++
```cpp
class Solution {
public:
    int smallestDistancePair(vector<int>& nums, int k) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        int l = 0, r = nums[n-1]-nums[0];
        while (l < r) {
            int m = (l + r) / 2;
            if (count(m, nums) >= k) r = m;
            else l = m + 1;
        }
        return l;
    }

    int count(int K, vector<int>& nums) {
        int cnt = 0, l = 0, n = nums.size();
        for (int r = 0; r < n; ++r) {
            while (l < r && nums[r]-nums[l] > K) {
                l++;
            }
            cnt += r - l;
        }
        return cnt;
    }
};
```

### python
```python
class Solution:
    def smallestDistancePair(self, nums: List[int], k: int) -> int:
        n = len(nums)
        nums.sort()

        def count(K: int) -> int:
            cnt, l = 0, 0
            for r in range(n):
                while l < r and nums[r]-nums[l] > K:
                    l += 1
                cnt += r - l
            return cnt

        l, r = 0, nums[-1]-nums[0]
        while l < r:
            m = (l + r) // 2
            if count(m) >= k:
                r = m
            else:
                l = m + 1
        return l
```