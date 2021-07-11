---
title: 【每日算法Day 91】求解数组中出现次数超过1/3的那个数
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-05 00:53:11
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

## 题目链接
[LeetCode 229. 求众数 II](https://leetcode-cn.com/problems/majority-element-ii/ "LeetCode 229. 求众数 II")

## 题目描述
给定一个大小为 $n$ 的数组，找出其中所有出现超过 $\lfloor n/3 \rfloor$ 次的元素。

**说明：**
* 要求算法的时间复杂度为 $O(n)$，空间复杂度为 $O(1)$。

**示例1**
```text
输入：
[3,2,3]
输出:
3
```

**示例2**
```text
输入：
[1,1,1,3,3,2,2,2]
输出:
[1,2]
```

## 题解
这是求解众数的升级版：   
[【每日算法Day 90】5种方法：求解数组中出现次数超过一半的那个数](https://godweiyang.com/2020/04/04/leetcode-inteview-39/ "【每日算法Day 90】5种方法：求解数组中出现次数超过一半的那个数")
但是这题没有保证一定存在满足条件的数，不过不要紧。

首先可以肯定最多有两个数的数量超过 `1/3` ，这个就不用我过多解释了吧。然后我们只需要找出出现次数最多的两个数，看它俩次数是否超过 `1/3` 就行了。

那么怎么找呢？如果存在某个数超过 `1/3` ，那我们每次删掉三个不同的数，直到最后没法删，最后剩下的数一定有这个超过 `1/3` 的数。原因很简单，因为每删一次最多删掉一个这个数，而删除最多 `1/3` 数组长度次之后所有数都被删光了，但是这个数还剩下一点。

所以我们用两个变量 `cand1` 和 `cand2` 表示两个候选人，`cnt1` 和 `cnt2` 表示两个候选人数量。那么如果两个候选人有一个和当前数 `x` 相同，对应的数量就加一。否则的话如果如果有某个候选人为空，就让 `x` 顶替成为新的候选人。否则的话就说明两个候选人都有，并且 `x` 和它俩都不相同，那么就同时删除三个不同的数，也就是两个候选人数量各减一，同时删去 `x` 。

最后判断两个候选人数量是否超过了 `1/3` 就行了。

这里关键点就在于，**每次删除三个不同的数，判断最后剩下的数是否符合题意就行了**。

## 代码
### c++
```cpp
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        int n = nums.size();
        int cand1 = 0, cand2 = 0, cnt1 = 0, cnt2 = 0;
        for (auto x : nums) {
            if (cand1 == x) {
                cnt1++;
            } else if (cand2 == x) {
                cnt2++;
            } else if (!cnt1) {
                cand1 = x;
                cnt1++;
            } else if (!cnt2) {
                cand2 = x;
                cnt2++;
            } else {
                cnt1--;
                cnt2--;
            }
        }
        cnt1 = cnt2 = 0;
        for (auto x : nums) {
            if (x == cand1) cnt1++;
            else if (x == cand2) cnt2++;
        }
        vector<int> res;
        if (cnt1 > n/3) res.push_back(cand1);
        if (cnt2 > n/3) res.push_back(cand2);
        return res;
    }
};
```

### python
```python
class Solution:
    def majorityElement(self, nums: List[int]) -> List[int]:
        n = len(nums)
        cand1, cand2, cnt1, cnt2 = 0, 0, 0, 0
        for x in nums:
            if cand1 == x:
                cnt1 += 1
            elif cand2 == x:
                cnt2 += 1
            elif cnt1 == 0:
                cand1 = x
                cnt1 = 1
            elif cnt2 == 0:
                cand2 = x
                cnt2 = 1
            else:
                cnt1 -= 1
                cnt2 -= 1
        cnt1 = cnt2 = 0
        for x in nums:
            if x == cand1:
                cnt1 += 1
            elif x == cand2:
                cnt2 += 1
        res = []
        if cnt1 > n//3:
            res.append(cand1)
        if cnt2 > n//3:
            res.append(cand2)
        return res
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)