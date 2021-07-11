---
title: 每日算法系列【LeetCode 424】替换后的最长重复字符
top: false
cover: false
toc: true
mathjax: true
date: 2020-01-29 14:18:15
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
给你一个仅由大写英文字母组成的字符串，你可以将任意位置上的字符替换成另外的字符，总共可最多替换 k 次。在执行上述操作后，找到包含重复字母的最长子串的长度。

**示例1**
```text
输入：
s = "ABAB", k = 2
输出：
4
解释：
用两个'A'替换为两个'B',反之亦然。
```

**示例2**
```text
输入：
s = "AABABBA", k = 1
输出：
4
解释：
将中间的一个'A'替换为'B',字符串变为 "AABBBBA"。
子串 "BBBB" 有最长重复字母, 答案为 4。
```

**提示**
字符串长度和 k 不会超过 10^4。

## 题解
这题和之前做过的一题非常类似：[每日算法系列【LeetCode 1004】最大连续1的个数 III](https://godweiyang.com/2020/01/09/leetcode-1004/)，只不过这题字符数量变成了 26 个。

方法和那题类似，都是用滑动窗口。用数组 count 记录每个字母出现的次数，并且用变量 cmax 记录窗口中出现次数最多的字母数量。

当前窗口是 [l, r] ，如果保留窗口中出现次数最多的字母，将其他字母全部替换为这个字母，那么替换次数就是 $r - l + 1 - cmax$ 。如果它大于 k ，那就说明不能继续向右扩展，而是需要左端点右移，缩小窗口了。缩小的过程中时刻更新 cmax 的值就行了，直到 $r - l + 1 - cmax$ 再次小于等于 k ，然后继续右移 r 。

## 代码
### c++
```cpp
class Solution {
public:
    int characterReplacement(string s, int k) {
        int n = s.size();
        vector<int> count(26, 0);
        int l = 0, r = 0, cmax = 0, res = 0;
        while (r < n) {
            cmax = max(cmax, ++count[s[r]-'A']);
            while (r - l + 1 - cmax > k)
                count[s[l++]-'A']--;
            res = max(res, r - l + 1);
            r++;
        }
        return res;
    }
};
```

### python
```python
class Solution:
    def characterReplacement(self, s: str, k: int) -> int:
        n = len(s)
        count = [0] * 26
        l, r, cmax, res = 0, 0, 0, 0
        while r < n:
            count[ord(s[r])-ord('A')] += 1
            cmax = max(cmax, count[ord(s[r])-ord('A')])
            while r - l + 1 - cmax > k:
                count[ord(s[l])-ord('A')] -= 1
                l += 1
            res = max(res, r - l + 1)
            r += 1
        return res
```

## 后记
注意这里代码实现上面有个很大的问题，就是右移左端点缩小窗口的时候， cmax 并没有跟着减小，这样为什么还是对的呢？这种情况下， cmax保存的其实是历史出现次数最多的字母的次数。而你不改变 cmax ，就会导致中间过程中出现很多不符合题意的窗口，也就是实际要修改的数量大于 k 的窗口，但是因为你 cmax 偏大，算下来修改数量偏小，它又是符合题意的。不过不影响，这些错误的窗口的长度一定是小于你之前算到的正确窗口的长度的（如果大于了，那么 cmax 一定会被更新）。

**下面解释来自于algsCG：**
> 因为我们只对最长有效的子字符串感兴趣，所以我们的滑动窗口不需要收缩，即使窗口可能覆盖无效的子字符串。我们可以通过在右边添加一个字符来扩展窗口，或者将整个窗口向右边移动一个字符。而且我们只在新字符的计数超过历史最大计数（来自覆盖有效子字符串的前一个窗口）时才增长窗口。也就是说，我们不需要精确的当前窗口的最大计数;我们只关心最大计数是否超过历史最大计数;这只会因为新字符而发生。

