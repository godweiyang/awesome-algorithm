---
title: 每日算法系列【LeetCode 面试题 17.05】字母与数字
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-22 23:07:21
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
给定一个放有字符和数字的数组，找到最长的子数组，且包含的字符和数字的个数相同。

返回该子数组，若不存在这样的数组，返回一个空数组。

**示例1**
```text
输入：
["A","1","B","C","D","2","3","4","E","5","F","G","6","7","H","I","J","K","L","M"]
输出：
["A","1","B","C","D","2","3","4","E","5","F","G","6","7"]
```

**示例2**
```text
输入：
["A","A"]
输出：
[]
```

**提示**
* array.length <= 100000

## 题解
回忆之前做过的一道题目：[每日算法系列【LeetCode 523】连续的子数组和](https://godweiyang.com/2020/02/02/leetcode-523/)，是不是有点相似？那道题是要求是否含有总和为 $k$ 的整数倍的连续子数组。而本题要求的是最长的子数组，满足数字和字母个数相等。

我们可以把字母抽象成 $-1$ ，数字抽象成 $1$ ，那么子数组的总和含义就是数字个数与字母个数的差值。假设子数组 $[l, r]$ 是满足字母个数和数字个数相等的，那么这段子数组总和必然是 $0$ 。如果我们用前缀和来进行优化的话，$sum[r] - sum[l-1] = 0$ ，也就是说 $sum[r]$ 和 $sum[l-1]$ 的值是相等的。

所以我们只需要求一遍前缀和，如果 $sum[i]$ 没有在之前出现过，那就保存一下 $sum[i]$ 最开始出现的下标是 $i$ 。否则的话就看一下当前子数组长度 $i - sum[i]$ 和历史最优的长度 $r - l$ 谁大，然后决定更不更新。

实际实现中 c++ 还是可以采用 `unordered_map` 来当作哈希表保存下标。

时间复杂度是 $O(n)$ 。


## 代码
### c++
```cpp
class Solution {
public:
    vector<string> findLongestSubarray(vector<string>& array) {
        int n = array.size();
        unordered_map<int, int> mp;
        mp[0] = -1;
        int cnt = 0, l = 0, r = 0;
        for (int i = 0; i < n; ++i) {
            cnt += isdigit(array[i][0]) ? 1 : -1;
            if (mp.find(cnt) != mp.end()) {
                if (r-l < i-mp[cnt]) {
                    l = mp[cnt] + 1;
                    r = i + 1;
                }
            } else {
                mp[cnt] = i;
            }
        }
        return vector<string>(array.begin()+l, array.begin()+r);
    }
};
```

### python
```python
class Solution:
    def findLongestSubarray(self, array: List[str]) -> List[str]:
        mp = {0: -1}
        cnt, l, r = 0, 0, 0
        for i, s in enumerate(array):
            cnt += 1 if s[:1].isdigit() else -1
            if cnt in mp:
                if r-l < i-mp[cnt]:
                    l = mp[cnt] + 1
                    r = i + 1
            else:
                mp[cnt] = i
        return array[l:r]
```