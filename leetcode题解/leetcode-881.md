---
title: 每日算法系列【LeetCode 881】救生艇
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-22 16:17:34
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
第 i 个人的体重为 people[i]，每艘船可以承载的最大重量为 limit。

每艘船最多可同时载两人，但条件是这些人的重量之和最多为 limit。

返回载到每一个人所需的最小船数。(保证每个人都能被船载)。


**示例1**
```text
输入：
people = [1,2], limit = 3
输出：
1
解释：
1 艘船载 (1, 2)
```

**示例2**
```text
输入：
people = [3,2,2,1], limit = 3
输出：
3
解释：
3 艘船分别载 (1, 2), (2) 和 (3)
```

**示例3**
```text
输入：
people = [3,5,3,4], limit = 5
输出：
4
解释：
4 艘船分别载 (3), (3), (4), (5)
```

**提示**
* 1 <= people.length <= 50000
* 1 <= people[i] <= limit <= 30000


## 题解
这题第一直觉就是，对于一个人 people[i] 来说，装了他之后船上还剩 limit-people[i] 的空间，所以第二个人要尽量装能装得下的人中最重的那个。

那如果直接这么遍历显然不行，时间复杂度是 $O(n^2)$ 的。所以我们先对重量进行排序，然后从最轻的人开始看，如果他能和最重的坐一个船，那就去掉他们俩，在剩下的人里继续找；如果不能的话，那么最重的人只能单独坐船，然后看能不能和第二重的人坐一个船，依次下去。

具体实现上，只需要用两个指针，一个指着最小的数，一个指着最大的数就行了，时间复杂度是 $O(n)$ ，但是由于还得调用一次快速排序，所以最终时间复杂度是 $O(n \log n)$ 。

当然，因为这题重量范围比较小，所以如果采用计数排序，可以进一步降低时间复杂度。

## 代码
### 双指针（c++）
```cpp
class Solution {
public:
    int numRescueBoats(vector<int>& people, int limit) {
        sort(people.begin(), people.end());
        int l = 0, r = people.size() - 1, res = 0;
        while (l <= r) {
            res++;
            if (people[l] + people[r] <= limit) l++;
            r--;
        }
        return res;
    }
};
```

### 双指针（python）
```python
class Solution:
    def numRescueBoats(self, people: List[int], limit: int) -> int:
        people.sort()
        res, l, r = 0, 0, len(people) - 1
        while l <= r:
            res += 1
            if people[l] + people[r] <= limit:
                l += 1
            r -= 1
        return res
```

### 计数排序（c++）
```cpp
class Solution {
public:
    int numRescueBoats(vector<int>& people, int limit) {
        int n = people.size();
        vector<int> sp(n, 0);
        vector<int> count(limit+1, 0);
        for (int i = 0; i < n; ++i) {
            count[people[i]]++;
        }
        for (int i = 1; i <= limit; ++i) {
            count[i] += count[i-1];
        }
        for (int i = 0; i < n; ++i) {
            sp[count[people[i]]-1] = people[i];
            count[people[i]]--;
        }
        int l = 0, r = n - 1, res = 0;
        while (l <= r) {
            res++;
            if (sp[l] + sp[r] <= limit) l++;
            r--;
        }
        return res;
    }
};
```

### 计数排序（python）
```python
class Solution:
    def numRescueBoats(self, people: List[int], limit: int) -> int:
        sp = [0] * len(people)
        count = [0] * (limit + 1)
        for i in people:
            count[i] += 1
        for i in range(1, limit + 1):
            count[i] += count[i-1]
        for i in people:
            sp[count[i]-1] = i
            count[i] -= 1
        res, l, r = 0, 0, len(people) - 1
        while l <= r:
            res += 1
            if sp[l] + sp[r] <= limit:
                l += 1
            r -= 1
        return res
```

## 后记
这题注意写循环的时候也有技巧的，我们实现的时候，对于最大值 people[r] ，先给他分配一条船，再看最小的人能否和他一条船，如果可以，那就顺便带上（ l 加 1 ），如果不可以的话，那就 r 减 1 ，继续看下一个更重的人。