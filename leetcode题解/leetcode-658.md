---
title: 每日算法系列【LeetCode 658】找到 K 个最接近的元素
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-15 18:13:29
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
给定一个排序好的数组，两个整数 k 和 x，从数组中找到最靠近 x（两数之差最小）的 k 个数。返回的结果必须要是按升序排好的。如果有两个数与 x 的差值一样，优先选择数值较小的那个数。

**示例1**
```text
输入：
[1,2,3,4,5], k=4, x=3
输出：
[1,2,3,4]
```

**示例2**
```text
输入：
[1,2,3,4,5], k=4, x=-1
输出：
[1,2,3,4]
```

**提示**
* k 的值为正数，且总是小于给定排序数组的长度
* 数组不为空，且长度不超过 10^4
* 数组里的每个元素与 x 的绝对值不超过 10^4

## 题解
### 滑动窗口
这题要找离 $x$ 最近的 $k$ 个元素，又因为数组是排好序的，所以离 $x$ 最远的元素一定在数组两端。

那么我们只需要用两个指针，一个指针 $l$ 指着第一个元素，一个指针 $r$ 指着最后一个元素。如果 $r - l \ge k$ ，那就说明窗口中元素个数大于 $k$ ，那么就要删除一个元素。删除哪个呢？就看 $arr[l]$ 和 $arr[r]$ 谁离 $x$ 更远，就删除谁。如果一样远，就删除大的元素 $arr[r]$ 。就这样删到窗口中只剩 $k$ 个元素为止。

这个方法时间复杂度是 $O(n)$ 。

### 二分+滑动窗口
如果 $n$ 太大，那么仅仅靠滑动窗口显然不行。注意观察答案所在的窗口可以发现，这个长度为 $k$ 的窗口一定是靠近 $x$ 的，也就是 $x$ 要么在窗口前一个位置，要么在窗口后一个位置，要么在窗口中间某个位置。 $x$ 和窗口中间绝对不可能有其他的数组元素。

那么我们可以二分找到第一个比 $x$ 大的元素（找第一个比它小的元素也行），然后左右各伸展出 $k$ 的长度，最终答案窗口一定就在这个范围之内。然后继续使用上面的滑动窗口来求解。

这个方法时间复杂度缩减到了 $O(\log n + k)$ 。

### 二分
如果 $k$ 太大，那么上面的方法又没有意义了，还是会退化到 $O(n)$ 。

上面两个方法都是先把窗口范围定到某一个区间里，然后一点一点的缩小窗口大小，最终得到答案的。那么能否直接判断出长度为 $k$ 的答案窗口位置在哪里呢？

按照上面的思路，长度为 $k$ 的窗口一定是通过长度为 $k+1$ 的窗口删除首尾之一元素得到的。那么我们观察某一个特定的长度为 $k+1$ 的窗口 $[l, l+k]$ ，如果 $arr[l]$ 离 $x$ 距离比 $arr[l+k]$ 离 $x$ 更远的话，那就要删除 $arr[l]$ ，同时说明 $l$ 以及它左边的所有元素都不可能是答案窗口的左边界。反之如果 $arr[l]$ 离 $x$ 距离小于等于 $arr[l+k]$ 离 $x$ 的距离，那么就要删除 $arr[l+k]$ 了，同时说明 $l$ 右边的元素都不可能是答案窗口的左边界。

综上，我们可以用二分直接寻找答案窗口的左边界。这样时间复杂度就降到了 $O(\log n)$ 。

## 代码
### 滑动窗口（c++）
```cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        int n = arr.size();
        int l = 0, r = n-1;
        while (r-l >= k) {
            if (x-arr[l] <= arr[r]-x) r--;
            else l++;
        }
        vector<int> res(k);
        copy(arr.begin()+l, arr.begin()+l+k, res.begin());
        return res;
    }
};
```

### 二分+滑动窗口（c++）
```cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        int n = arr.size();
        int l = 0, r = n-1;
        while (l < r) {
            int m = (l + r) / 2;
            if (arr[m] < x) l = m + 1;
            else r = m;
        }
        r = min(n-1, l+k-1);
        l = max(0, l-k);
        while (r-l >= k) {
            if (x-arr[l] <= arr[r]-x) r--;
            else l++;
        }
        vector<int> res(k);
        copy(arr.begin()+l, arr.begin()+l+k, res.begin());
        return res;
    }
};
```

### 二分（c++）
```cpp
class Solution {
public:
    vector<int> findClosestElements(vector<int>& arr, int k, int x) {
        int n = arr.size();
        int l = 0, r = n-k;
        while (l < r) {
            int m = (l + r) / 2;
            if (x-arr[m] > arr[m+k]-x) l = m + 1;
            else r = m;
        }
        vector<int> res(k);
        copy(arr.begin()+l, arr.begin()+l+k, res.begin());
        return res;
    }
};
```

### 滑动窗口（python）
```python
class Solution:
    def findClosestElements(self, arr: List[int], k: int, x: int) -> List[int]:
        n = len(arr)
        l, r = 0, n-1
        while r-l >= k:
            if x-arr[l] <= arr[r]-x:
                r -= 1
            else:
                l += 1
        return arr[l:l+k]
```

### 二分+滑动窗口（python）
```python
class Solution:
    def findClosestElements(self, arr: List[int], k: int, x: int) -> List[int]:
        n = len(arr)
        l, r = 0, n-1
        while l < r:
            m = (l + r) // 2
            if arr[m] < x:
                l = m + 1
            else:
                r = m
        r = min(n-1, l+k-1)
        l = max(0, l-k)
        while r-l >= k:
            if x-arr[l] <= arr[r]-x:
                r -= 1
            else:
                l += 1
        return arr[l:l+k]
```

### 二分（python）
```python
class Solution:
    def findClosestElements(self, arr: List[int], k: int, x: int) -> List[int]:
        n = len(arr)
        l, r = 0, n-k
        while l < r:
            m = (l + r) // 2
            if x-arr[m] > arr[m+k]-x:
                l = m + 1
            else:
                r = m
        return arr[l:l+k]
```