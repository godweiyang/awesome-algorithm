---
title: 【每日算法Day 99】你们可能不知道只用20万赢到578万是什么概念
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-13 12:23:00
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 你们可能不知道只用 20 万赢到 578 万是什么概念。
我们一般只会用两个字来形容这种人：赌怪！
我经常说一句话，当年陈刀仔他能用 20 块赢到 3700 万，我 LBW 用 20 万赢到 500 万不是问题。
埋伏他一手，这个牌不能抢，这个牌不用抢，他死定了。
反手给一个超级加倍，闷声发大财。他也超级加倍？但是不用怕，他的牌赢不了我。
五六七八九十，两个炸，很牛逼这个牌，如果把这个 K 换成 J ，我这个牌将绝杀，但是换不得。
单走一个六，SB，直接把 K 走了。
走他一张 2 顶他。阿姨快点，阿姨，阿姨你 K 都不要吗？阿姨你快点啊！阿姨别磨磨蹭蹭的。
五六七八九十。打错了，应该打 2 的。
给阿姨倒杯茶好吧，阿姨给你倒一杯卡布奇诺。
给阿姨倒一杯卡布奇诺！开始你的炸弹秀，炸他炸他。漂亮！
十七张牌你能秒我？你能秒杀我？！你今天能十七张牌把卢本伟秒了，我！当！场！就把这个电脑屏幕吃掉！！！

## 题目链接
[LeetCode 846. 一手顺子](https://leetcode-cn.com/problems/hand-of-straights/ "LeetCode 846. 一手顺子")

## 题目描述
爱丽丝有一手（hand）由整数数组给定的牌。 

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
爱丽丝的手牌可以被重新排列为 [1,2,3]，[2,3,4]，[6,7,8]。
```

**示例2**
```text
输入：
hand = [1,2,3,4,5], W = 4
输出:
false
解释：
爱丽丝的手牌无法被重新排列成几个大小为 4 的组。
```

## 题解
## 巧用 map
这也是最直观的一个方法，用 `map` 来保存每个数出现的次数。

然后从最小的数开始，以它作为顺子的开头，然后看顺子里的数在不在 `map` 里，在就次数减一，不在就直接返回 `false` 。

接着重复上面步骤，最后直到 `map` 为空，最后返回 `true`。

`map` 的特性就是你取它的第一个键值对，它的 `key` 就是最小的，这就很方便了。

## 排序统计
首先对手牌从小到大进行排序，然后从最小的开始，作为顺子开头，遍历之后的数。如果在数组里，并且没有被访问过，那么就标记为访问过了。

注意可以提前终止遍历，也就是如果发现某一个顺子还没遍历完，但是访问到的元素已经超过接在顺子后的数了，那就直接返回 `false` 。

## 排序统计2
> 这题还有个解法，来自于题解区网友 `zhanzq`，感觉挺不错的。但是我没怎么看懂，如果谁看懂了请教教我。

[网友题解](https://leetcode-cn.com/problems/hand-of-straights/solution/onlognsuan-fa-by-zhanzq/ "网友题解")

![](1.jpg)

## 代码
### 巧用 map（c++）
```cpp
class Solution {
public:
    bool isNStraightHand(vector<int>& hand, int W) {
        int n = hand.size();
        if (n%W) return false;
        map<int, int> count;
        for (auto x : hand) count[x]++;
        while (count.size()) {
            int start = count.begin()->first;
            for (int i = start; i < start+W; ++i) {
                if (count.find(i) == count.end()) return false;
                if (!--count[i]) count.erase(i);
            }
        }
        return true;
    }
};
```

### 排序统计（c++）
```cpp
class Solution {
public:
    bool isNStraightHand(vector<int>& hand, int W) {
        int n = hand.size();
        if (n%W) return false;
        if (W==1) return true;
        sort(hand.begin(), hand.end());
        vector<int> vis(n, 0);
        for (int i = 0; i < n; ++i) {
            if (vis[i]) continue;
            int cnt = 1;
            vis[i] = 1;
            for (int j = i+1; j < n; ++j) {
                if (hand[j]>hand[i]+cnt) break;
                if (!vis[j] && hand[j]==hand[i]+cnt) {
                    vis[j] = 1;
                    cnt++;
                    if (cnt == W) break;
                }
            }
            if (cnt != W) return false;
        }
        return true;
    }
};
```

### 排序统计2，来自于网友zhanzq（c++）
```cpp
class Solution {
public:
    bool valid(vector<int> &lst, int W){
        lst.push_back(0);
        int sz = lst.size();
        int pre = 0;
        vector<int> deltas(sz, 0);
        for(int i = 0; i < sz; i++){
            pre += deltas[i];
            if(pre < lst[i]){
                int delta = lst[i] - pre;
                pre = lst[i];
                if(i + W < sz){
                    deltas[i+W] -= delta;
                }
            }else if(pre > lst[i]){
                return false;
            }
        }
        return true;
    }

    bool isNStraightHand(vector<int>& hand, int W) {
        int sz = hand.size();
        if(sz%W){
            return false;
        }else{
            sort(hand.begin(), hand.end());
            vector<int> lst;
            int i = 0, j = 0;
            while(i < sz){
                while(j < sz && hand[i] == hand[j]){
                    j++;
                }
                lst.push_back(j - i);
                if(j >= sz){
                    break;
                }else if(hand[j] != hand[j-1] + 1){
                    lst.push_back(0);
                }
                i = j;
            }
            return valid(lst, W);
        }
    }
};
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)