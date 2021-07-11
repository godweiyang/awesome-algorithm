---
title: 每日算法系列【LeetCode 503】下一个更大元素 II
top: false
cover: false
toc: true
mathjax: true
date: 2020-02-10 09:37:09
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
给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字 x 的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1。

**示例1**
```text
输入：
[1,2,1]
输出：
[2,-1,2]
解释：
第一个 1 的下一个更大的数是 2；
数字 2 找不到下一个更大的数； 
第二个 1 的下一个最大的数需要循环搜索，结果也是 2。
```

**提示**
输入数组的长度不会超过 10000。

## 题解
这题中循环数组其实不用管，只需要再复制一遍数组接在后面就行了，那么关键还是如何求每个数后面第一个比它大的数。

我们可以从右往左遍历数组，如果遍历到某个数，那么它右边所有比它小的数都不用再考虑了。因为再继续遍历下去的话，它右边比它还小的数是绝对不可能成为第一个大的数的。

这样我们只需要保存每个数右边比它大的数就行了，这就需要用到单调栈了。对于 $nums[i]$ 来说，我们只需要不断出栈，直到栈顶元素大于它就行了，那么这时候栈顶元素就是 $nums[i]$ 右边第一个比它大的数。如果栈是空的，就说明它右边没有更大的数了。

而在具体实现的时候，没必要真的把数组复制一遍接在后面，只需要用求余操作就能解决了。

最终每个元素出栈入栈一次，时间复杂度是 $O(n)$ ，空间复杂度是 $O(n)$ 。

## 代码
### c++
```cpp
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int n = nums.size();
        stack<int> st;
        vector<int> res(n, -1);
        for (int i = 2*n-2; i >= 0; --i) {
            while (!st.empty() && nums[i%n] >= st.top()) {
                st.pop();
            }
            res[i%n] = st.empty() ? -1 : st.top();
            st.push(nums[i%n]);
        }
        return res;
    }
};
```

### python
```python
class Solution:
    def nextGreaterElements(self, nums: List[int]) -> List[int]:
        n = len(nums)
        stack, res = [], [-1] * n
        for i in range(2*n-2, -1, -1):
            while len(stack) > 0 and nums[i%n] >= stack[-1]:
                stack.pop()
            res[i%n] = -1 if len(stack)==0 else stack[-1]
            stack.append(nums[i%n])
        return res
```
