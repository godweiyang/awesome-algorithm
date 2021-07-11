---
title: 【每日算法Day 103】老题新做，几乎不会有人想到的解法，它来了
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-17 11:30:53
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 前两天 Day 99 的时候，做过一道顺子的题目，当时有一个网友的妙解有点没看懂，今天我来给大家详细讲解一下。

## 题目链接
[LeetCode 846. 一手顺子](https://leetcode-cn.com/problems/hand-of-straights/ "LeetCode 846. 一手顺子")

**往期回顾：**  
[【每日算法Day 99】你们可能不知道只用20万赢到578万是什么概念](https://godweiyang.com/2020/04/13/leetcode-846/ "【每日算法Day 99】你们可能不知道只用20万赢到578万是什么概念")

## 题目描述
卢本伟有一手（hand）由整数数组给定的牌。 

现在她想把牌重新排列成组，使得每个组的大小都是 `W`，且由 `W` 张连续的牌组成。

如果她可以完成分组就返回 `true`，否则返回 `false`。

**说明：**
* `1 <= hand.length <= 10000`
* `0 <= hand[i] <= 10^9`
* `1 <= W <= hand.length`

**示例1**
```text
输入：
hand = [1,2,3,6,2,3,4,7,8], W = 3
输出:
true
解释：
卢本伟的手牌可以被重新排列为 [1,2,3]，[2,3,4]，[6,7,8]。
```

**示例2**
```text
输入：
hand = [1,2,3,4,5], W = 4
输出:
false
解释：
卢本伟的手牌无法被重新排列成几个大小为 4 的组。
```

## 题解
这题的妙解来自于题解区网友 `zhanzq`，当时没怎么看懂，现在我来给大家讲解一下。

[网友题解地址](https://leetcode-cn.com/problems/hand-of-straights/solution/onlognsuan-fa-by-zhanzq/ "网友题解地址")

我们用一个例子来讲解：
![](1.jpg)

假设 `W = 3`，给定的手牌正好是三个顺子：`[1,2,3], [2,3,4], [6,7,8]` 。

那么我们统计出每张牌的数量，并且从小到大排序，记为 `count` ，这里就是 `[1,2,2,1,0,1,1,1,0]` ，并且在数字不连续处和末尾补 `0` （作用后面会详细说）。

* 然后从小到大遍历每一张牌，首先 `1` 只有一张，那么如果它和后面牌能构成顺子，那么 `2, 3` 至少要有一张才行，于是 `total` 数组后面两个位置都加上 `1` 。
* 然后遍历到 `2` ，因为 `2` 的数量是大于该位置处的 `total` 值的，所以 `2` 的数量足够满足前面的牌顺子要求。此外 `2` 还会多出一张，那么后面两个位置至少要有一张牌才行，于是 `total` 后面两个位置再加上 `1` 。
* 然后遍历 `3, 4` ，发现数量正好都等于 `total` ，那说明它俩正好和前面的牌构成顺子，一点都不会多余。
* 然后遍历到 `0` 了，这就说明和前面的牌断开了。如果这时候 `total` 不为 `0` ，就说明中间缺失了一些牌，前面存在顺子没法补足结尾。而如果最开始没有填充 `0` 的话，就没有办法判断这里的牌是否和前面连续的，你就有可能把 `6` 这张牌直接接到 `4` 后面组成顺子了。
* 然后遍历 `6, 7, 8` 同理，在对应位置处更新 `total` 就行了。
* 最后遍历 `0` ，发现 `total` 也是 `0` ，那就说明整副牌可以构成顺子，完美！

时间复杂度是 $O(n \log n + nW)$ ，这题数据不强也可以过的。

有没有办法优化呢？其实更新 `total` 这一步可以优化掉 $O(W)$ 这个复杂度，直接 $O(1)$ 更新 `total` 。

* 首先遍历 `1` ，因为 `1` 只有一张，那么如果它和后面牌能构成顺子，那么 `2, 3` 至少要有一张才行。但是这里我们不对这几张牌的 `total` 加上一，而是在这个顺子结尾的下一张牌处的 `deltas` 减去 `1` 。
* 然后遍历 `2` ，那么这时候没有 `total` 了，怎么计算应该扣除多少前面顺子需要的 `2` 呢？其实只需要用前一张牌的牌数加上当前的 `deltas` 值就行了。为什么呢？前面一张牌有多少张，你当前这张就得至少有那么多去构成顺子，但是如果前面一张牌是某些顺子的结尾，你还得扣掉一些，而扣掉的数值正好就是当前的 `deltas` ，这在前面顺子的开头处已经记录过了。
* 后面操作类似，就不详细阐述了。

这种方法精髓就在于，不需要直接更新所有的 `total` 值，只需要在顺子结尾下一个元素处更新一下 `deltas` 就行了，每次的 `total` 可以通过上一张牌的 `count` 和当前的 `deltas` 推算出来。

这样总的时间复杂度就降到了 $O(n \log n + n)$，近似 $O(n \log n)$ 。

不得不说，这个方法还是非常妙的，反正我是一下子想不到的，看了代码都想了很久才想通。

## 代码
### 暴力更新（c++）
```cpp
class Solution {
public:
    bool valid(vector<int> &count, int W) {
        int n = count.size();
        vector<int> total(n, 0);
        for (int i = 0; i < n; ++i) {
            if (count[i] > total[i]) {
                int delta = count[i] - total[i];
                for (int j = i; j < i+W && j < n; ++j) total[j] += delta;
            } else if (count[i] < total[i]) {
                return false;
            }
        }
        return true;
    }

    bool isNStraightHand(vector<int>& hand, int W) {
        int n = hand.size();
        if (W == 1) return true;
        if (n%W) return false;
        sort(hand.begin(), hand.end());
        vector<int> count;
        int i = 0, j = 0;
        while (i < n) {
            while (j < n && hand[i] == hand[j]) j++;
            count.push_back(j-i);
            if (j >= n) break;
            else if (hand[j] != hand[j-1]+1) count.push_back(0);
            i = j;
        }
        count.push_back(0);
        return valid(count, W);
    }
};
```

### 优化（c++）
```cpp
class Solution {
public:
    bool valid(vector<int> &count, int W) {
        int n = count.size(), pre = 0;
        vector<int> deltas(n, 0);
        for (int i = 0; i < n; ++i) {
            pre += deltas[i];
            if (pre < count[i]) {
                int delta = count[i] - pre;
                pre = count[i];
                if (i + W < n) deltas[i+W] -= delta;
            } else if (pre > count[i]) {
                return false;
            }
        }
        return true;
    }

    bool isNStraightHand(vector<int>& hand, int W) {
        int n = hand.size();
        if (W == 1) return true;
        if (n%W) return false;
        sort(hand.begin(), hand.end());
        vector<int> count;
        int i = 0, j = 0;
        while (i < n) {
            while (j < n && hand[i] == hand[j]) j++;
            count.push_back(j-i);
            if (j >= n) break;
            else if (hand[j] != hand[j-1]+1) count.push_back(0);
            i = j;
        }
        count.push_back(0);
        return valid(count, W);
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)