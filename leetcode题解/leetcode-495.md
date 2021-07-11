---
title: 每日算法系列【LeetCode 495】提莫攻击
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-04 08:21:27
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
在《英雄联盟》的世界中，有一个叫 “提莫” 的英雄，他的攻击可以让敌方英雄艾希（编者注：寒冰射手）进入中毒状态。

![兔宝宝提莫](1.jpg)

现在，给出提莫对艾希的攻击时间序列和提莫攻击的中毒持续时间，你需要输出艾希的中毒状态总时长。

你可以认为提莫在给定的时间点进行攻击，并立即使艾希处于中毒状态。

**示例1**
```text
输入：
[1,4], 2
输出：
4
解释：
在第 1 秒开始时，提莫开始对艾希进行攻击并使其立即中毒。中毒状态会维持 2 秒钟，直到第 2 秒钟结束。
在第 4 秒开始时，提莫再次攻击艾希，使得艾希获得另外 2 秒的中毒时间。
所以最终输出 4 秒。
```

**示例2**
```text
输入：
[1,2], 2
输出：
3
解释：
在第 1 秒开始时，提莫开始对艾希进行攻击并使其立即中毒。中毒状态会维持 2 秒钟，直到第 2 秒钟结束。
但是在第 2 秒开始时，提莫再次攻击了已经处于中毒状态的艾希。
由于中毒状态不可叠加，提莫在第 2 秒开始时的这次攻击会在第 3 秒钟结束。
所以最终输出 3。
```

## 题解
因为数组是时间序列，所以是给你排好序的，不需要你自己排序。

那么对于 $t[i]$ 来说，如果 $t[i-1]+d \le t[i]$ 的话，就说明前一时刻的中毒效果已经过去了，那么当前时刻的中毒效果持续时间都是属于当前时刻的，答案加上 $d$ 就行了。但是如果 $t[i-1]+d > t[i]$ ，那么前一时刻的中毒效果还在，等前一时刻的中毒效果过去了，剩下的中毒时间才能算是当前时刻的，而扣除掉上一时刻剩余的时间为 $t[i] - t[i-1]$ 。所以最后属于当前时刻的中毒时间只要取 $t[i] - t[i-1]$ 和 $d$ 的最小值就行了。

最终时间复杂度为 $O(n)$ ，空间复杂度为 $O(1)$ 。

## 代码
### c++
```cpp
class Solution {
public:
    int findPoisonedDuration(vector<int>& timeSeries, int duration) {
        int n = timeSeries.size();
        int res = n ? duration : 0;
        for (int i = 1; i < n; ++i) {
            res += min(timeSeries[i]-timeSeries[i-1], duration);
        }
        return res;
    }
};
```

### python
```python
class Solution:
    def findPoisonedDuration(self, timeSeries: List[int], duration: int) -> int:
        n = len(timeSeries)
        res = 0 if n==0 else duration
        for i in range(1, n):
            res += min(timeSeries[i]-timeSeries[i-1], duration)
        return res
```

### 后记
这题难度其实称不上中等，选取这道题完全是因为有我最爱的小提莫！

如果有同样喜爱英雄联盟的召唤师（小姐姐最棒了），可以加我好友一起开黑呀。本人**艾欧尼亚**（**ID：godweiyang**）黄金水平，虽然很菜，但是打字速度快！