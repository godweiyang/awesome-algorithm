---
title: 【每日算法Day 107】面试必考：良心推荐，一题三解，不看后悔一辈子
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-21 11:14:02
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 可能有些同学只会写 `python` ，看不懂 `c++`。但是一个是因为我懒，多解时不想再写一遍 `python` 了，一个是理解算法最重要，语言不重要。今天学妹发来一张图，我觉得说的很好。

![院长大大如是说](1.jpg)

## 题目链接
[LeetCode 1248. 统计「优美子数组」](https://leetcode-cn.com/problems/count-number-of-nice-subarrays/ "LeetCode 1248. 统计「优美子数组」")

## 题目描述
给你一个整数数组 `nums` 和一个整数 `k`。

如果某个**连续**子数组中恰好有 `k` 个奇数数字，我们就认为这个子数组是「优美子数组」。

请返回这个数组中「优美子数组」的数目。

**示例1**
```text
输入：
nums = [1,1,2,1,1], k = 3
输出:
2
解释：
包含 3 个奇数的子数组是 [1,1,2,1] 和 [1,2,1,1] 。
```

**示例2**
```text
输入：
nums = [2,4,6], k = 1
输出:
0
解释：
数列中不包含任何奇数，所以不存在优美子数组。
```

**示例3**
```text
输入：
nums = [2,2,2,1,2,2,1,2,2,2], k = 2
输出:
16
```

## 题解
**为了方便表示，我们下面统一将奇数变为 `1` ，偶数变为 `0` ，不难发现这是等价于原来题意的。**

### 统计奇数位置
我们发现，如果两个 `1` 之间（包含自身）一共包含了 `k` 个 `1` ，那么这 `k` 个 `1` 可以构成的连续子数组个数就是 **左边 `0` 的个数加一** 乘上 **右边 `0` 的个数加一**。

那么如何统计每个 `1` 前后 `0` 的个数呢？其实只需要记录一下每个 `1` 的位置，然后直接用相邻两个 `1` 的位置相减就可以得到中间 `0` 的个数加一了。

所以直接记录每个 `1` 的位置，为了处理边界，我们还需要在最开始添加上虚拟位置 `-1` ，在最后添加虚拟位置 `n` 。

然后对于第 $i$ 个 `1` 来说，如果将它作为子数组第一个 `1` ，那么最后一个 `1` 应该是第 $i+k-1$ 个 `1` 。所以直接计算两边 `0` 的数量，再加一相乘就行了：
$$
(pos[i] - pos[i-1])\cdot(pos[i+k] - pos[i+k-1])
$$

最后遍历所有的 `i` ，将第 $i$ 个 `1` 作为起点，然后累加答案就行了。

时间复杂度 $O(n)$ ，空间复杂度 $O(n)$ 。

### 双指针
主要思想还是跟上面方法一样，但是不用直接计算 **左边 `0` 的个数加一** 乘上 **右边 `0` 的个数加一**。只需要计算 **左边 `0` 的个数加一** ，然后右指针如果指着 `0` ，就加上这个值，一直加到右指针为 `1` 为止。

所以只需要用常数个变量就行了，`even` 记录子数组前面有多少个 `0` ，`cnt` 记录当前子数组有多少个 `1` 。用 `l` 指向子数组开头，`r` 指向子数组结尾。

如果 `cnt = k` ，那就说明子数组中正好有 `k` 个 `1` 。那就右移 `l` ，直到遇到 `1` 为止，这样就能统计出左边有多少个 `0` ，记录在 `even` 中。然后 `l` 右移跳过这个 `1` ，同时 `cnt` 减一。

如果 `cnt < k` ，那就说明 `1` 的数量不够，`r` 继续右移就行了。同时每移动一次，答案都要加上 `even` 值，因为你之前 `cnt = k` 时记录了一下左边 `0` 的数量，现在右边每一个 `0` 都得加上它。其实除了初始阶段，其余时候 `cnt` 都是等于 `k-1` 的。而初始阶段 `even = 0` ，所以加上也没事，可以合并写。

时间复杂度 $O(n)$ ，空间复杂度 $O(1)$ 。

### 前缀和

遍历原数组中每个位置 $i$，如果 $i$ 之前（包含自身） `1` 的个数一共 `odd` 个（也就是前缀和），那么我们只需要看有多少个位置 $j < i$ 满足 `1` 的前缀和等于 `odd-k` ，那么 $[j+1, i]$ 就是正好包含 `k` 个 `1` 的子数组。

所以我们只需要用一个计数数组来记录一下前缀和对应的出现次数就行了，然后每次取出 `odd-k` 的次数加到答案里就行了。

时间复杂度 $O(n)$ ，空间复杂度 $O(n)$ 。

## 代码
### 统计奇数位置（c++）
```cpp
class Solution {
public:
    int numberOfSubarrays(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> pos;
        pos.push_back(-1);
        for (int i = 0; i < n; ++i) {
            if (nums[i]&1) pos.push_back(i);
        }
        pos.push_back(n);
        int res = 0, sz = pos.size();
        for (int i = 1; i+k < sz; ++i) {
            res += (pos[i] - pos[i-1]) * (pos[i+k] - pos[i+k-1]);
        }
        return res;
    }
};
```

### 双指针（c++）
```cpp
class Solution {
public:
    int numberOfSubarrays(vector<int>& nums, int k) {
        int n = nums.size();
        int res = 0, cnt = 0, even = 0;
        int l = 0, r = 0;
        while (r < n) {
            if (cnt < k && (nums[r++]&1)) cnt++;
            if (cnt == k) {
                even = 1;
                while (!(nums[l++]&1)) even++;
                cnt--;
            }
            res += even;
        }
        return res;
    }
};
```

### 前缀和（c++）
```cpp
class Solution {
public:
    int numberOfSubarrays(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> count(n+1, 0);
        count[0] = 1;
        int res = 0, odd = 0;
        for (int i = 0; i < n; ++i) {
            odd += nums[i]&1;
            if (odd >= k) res += count[odd-k];
            count[odd]++;
        }
        return res;
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)