---
title: 【每日算法Day 87】今天我脱单了，所以大家不用做题了！
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-01 00:33:29
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

<div align="center"><audio controls="controls" height="100" width="100" autoplay="autoplay" loop="loop"><source src="zhejiushiaima.mp3" type="audio/mp3" /></audio></div>

> 想啥呢，我哪来的女朋友？今天是愚人节，还是给我老老实实做题吧。

## 题目链接
[LeetCode 1111. 有效括号的嵌套深度](https://leetcode-cn.com/problems/maximum-nesting-depth-of-two-valid-parentheses-strings/ "LeetCode 1111. 有效括号的嵌套深度")

## 题目描述
> 题面太长晦涩不想看，请直接跳到最后一段。

**有效括号字符串** 仅由 `"("` 和 `")"` 构成，并符合下述几个条件之一：
* 空字符串
* 连接，可以记作 `AB`（`A` 与 `B` 连接），其中 `A` 和 `B` 都是有效括号字符串
* 嵌套，可以记作 `(A)`，其中 A 是有效括号字符串

类似地，我们可以定义任意有效括号字符串 `s` 的 **嵌套深度** `depth(s)`：
* `s` 为空时，`depth("") = 0`
* `s` 为 `A` 与 `B` 连接时，`depth(A + B) = max(depth(A), depth(B))`，其中 `A` 和 `B` 都是有效括号字符串
* `s` 为嵌套情况，`depth("(" + A + ")") = 1 + depth(A)`，其中 `A` 是有效括号字符串

例如：`""`，`"()()"`，和 `"()(()())"` 都是有效括号字符串，嵌套深度分别为 `0`，`1`，`2`，而 `")("` 和 `"(()"` 都不是有效括号字符串。

给你一个有效括号字符串 `seq`，将其分成两个不相交的子序列 `A` 和 `B`，且 `A` 和 `B` 满足有效括号字符串的定义（注意：`A.length + B.length = seq.length`）。

现在，你需要从中选出 **任意** 一组有效括号字符串 `A` 和 `B`，使 `max(depth(A), depth(B))` 的可能取值最小。

返回长度为 `seq.length` 答案数组 `answer` ，选择 `A` 还是 `B` 的编码规则是：如果 `seq[i]` 是 `A` 的一部分，那么 `answer[i] = 0`。否则，`answer[i] = 1`。即便有多个满足要求的答案存在，你也只需返回 **一个**。

> 不是我吹牛，我估计一大部分人看完都不知道题目什么意思。一句话概括就是，给你一个合法的括号序列，你需要将其拆分成两个合法的子序列（不连续），使得两个子序列的括号嵌套深度较大者尽量的小。

**示例1**
```text
输入：
seq = "(()())"
输出:
[0,1,1,1,1,0]
解释：
拆成 "()" 和 "()()" ，最大嵌套深度为 1
```

**示例2**
```text
输入：
seq = "()(())()"
输出:
[0,0,0,1,1,0,1,1]
解释：
拆成 "()()" 和 "()()" ，最大嵌套深度为 1
```

**说明：**
* `1 <= text.size <= 10000`

## 题解
既然想要两个子序列的嵌套深度中较大者尽量小，那么我们最好能够让两个子序列的嵌套深度相同。

再考虑任意一个原序列中嵌套深度为 $d$ 的合法子序列，我们要想办法把它拆成两半。那么最优的方法肯定是一半嵌套深度为 $\lfloor d/2 \rfloor$ ，一半是 $d - \lfloor d/2 \rfloor$ 。这样两个子序列中嵌套深度较大值就是 $\lceil d/2 \rceil$，而其它任何分法都会导致较大值大于它。

那么怎么样才能对半分呢？这个其实随意了，但是最为方便的方法就是，嵌套深度为奇数的作为一个子序列，偶数的作为另一个子序列，这样就对半分了，代码还好写。

具体实现上，我们用一个变量 $cnt$ 来表示当前括号的嵌套深度，那么遇到左括号就深度加一，遇到右括号嵌套深度就是当前的 $cnt$ ，但是遍历完这个括号之后，深度要减一，然后嵌套深度为奇数的括号位置处标记为 1 就行了。

伪代码也就是：
```text
if c = '('
    cnt := cnt + 1
    mask := cnt&1
else
    mask := cnt&1
    cnt := cnt - 1
```

### 简化
其实我们可以注意到，不管是加一还是减一，奇偶性的变化都是一致的，也就是减一之后的奇偶性和加一之后是相同的。

所以我们把减一也变成加一，那么不管遇到什么括号，都是 $cnt$ 加一了，那不就变成了下标 $i$ 了吗？

我们把上面的伪代码按照这种思路改变一下：
```text
if c = '('
    cnt := cnt + 1
    mask := cnt&1
else
    mask := cnt&1
    cnt := cnt + 1
```

然后用下标 $i$ 替换掉 $cnt$ :
```text
if c = '('
    mask := (i+1)&1
else
    mask := i&1
```

继续改写一下，让形式统一一点：
```text
if c = '('
    mask := ~(i&1)
else
    mask := i&1
```

那么最后就可以把这两种情况合并了，也就是标记值直接就等于 `(i&1)^(c='(')` 。

当然我是从代码的角度，从奇偶性推过来的，官方题解是直接严格证明了正确性：

[官方题解：LeetCode 1111. 有效括号的嵌套深度](https://leetcode-cn.com/problems/maximum-nesting-depth-of-two-valid-parentheses-strings/solution/you-xiao-gua-hao-de-qian-tao-shen-du-by-leetcode-s/ "官方题解：LeetCode 1111. 有效括号的嵌套深度")

![](1.jpg)

## 代码
### c++
```cpp
class Solution {
public:
    vector<int> maxDepthAfterSplit(string seq) {
        int cnt = 0;
        vector<int> res;
        for (auto c : seq) {
            if (c == '(') {
                res.push_back((++cnt)&1);
            } else {
                res.push_back((cnt--)&1);
            }
        }
        return res;
    }
};
```

### python
```python
class Solution:
    def maxDepthAfterSplit(self, seq: str) -> List[int]:
        cnt = 0
        res = []
        for c in seq:
            if c == '(':
                cnt += 1
                res.append(cnt&1)
            else:
                res.append(cnt&1)
                cnt -= 1
        return res
```

### 简化（c++）
```cpp
class Solution {
public:
    vector<int> maxDepthAfterSplit(string seq) {
        vector<int> res;
        for (int i = 0, sz = seq.size(); i < sz; ++i) {
            res.push_back((i&1)^(seq[i]=='('));
        }
        return res;
    }
};
```

### 简化（python）
```python
class Solution:
    def maxDepthAfterSplit(self, seq: str) -> List[int]:
        res = []
        for i, c in enumerate(seq):
            res.append((i&1)^(c=='('))
        return res
```