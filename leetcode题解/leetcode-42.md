## 题目描述
给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

![](1.png)

上面是由数组 [0,1,0,2,1,0,1,3,2,1,2,1] 表示的高度图，在这种情况下，可以接 6 个单位的雨水（蓝色部分表示雨水）。感谢 Marcos 贡献此图。

**示例1**
```text
输入：
[0,1,0,2,1,0,1,3,2,1,2,1]
输出：
6
```

## 题解
### 方法1（按列算）
这也是最容易理解的一种方法，我们计算每一个柱子上方的水最多有多高就行了，而这个高度取决于它的左右两边最高的柱子分别是多高。

当然可以暴力求左右两端最高的高度了，不过其实只需要预处理一下，用数组保存一下每个位置左右两端最高的高度就行了。

最后答案的话就是用左右两边最高高度的较小值，减去这根柱子的高度。

时间复杂度 $O(n)$ ，空间复杂度 $O(n)$ ，需要扫描两遍数组。

### 方法2（按行算）
我们可以发现，每一行水左右肯定都会被柱子卡住（显然是废话）。那么从左向右遍历柱子，如果高度在下降，那么显然不会蓄水。如果高度上升了，那就说明中间是个低点，这之间可以蓄水。而这个下降的高度用单调栈来维护就行了，栈里我们只放下标。

那到底蓄水多少呢？假设 q 是栈顶下标，p 是栈顶第二个元素下标，那么一定有 $h[p] \ge h[q]$ 。现在进来一个 $h[i] > h[q]$ ，那么 q 就是一个低点，而 p ， q ， i 之间的蓄水（比 $h[q]$ 高，比 $h[p]$ 和 $h[i]$ 都低的部分）可以计算为 $(\min(h[p], h[i]) - h[q])\cdot(i - p - 1)$ 。然后把 q 出栈，继续用栈顶两个元素计算。

那么为什么这里只需要计算 p 和 i 之间比 $h[q]$ 高的那部分矩形就行了呢？因为比它低的部分在之前都已经算过了，而比它高的部分在之后还会计算到。

用下面这张示意图可以看的更清楚一点：

![](1.jpg)

红色表示栈里的元素，白色表示已经出栈了，绿色表示当前准备进栈的元素。那么这时候我们上面求的就是 3 号水块的面积，而 1 和 2 水块在之前进栈操作中就已经求出来了， 4 水块的话在之后（q 出栈，p 和 i 进行比较）也会被计算到。

时间复杂度 $O(n)$ ，空间复杂度 $O(n)$ ，需要扫描一遍数组，但是每个元素会入栈再出栈，所以操作次数和方法1其实是一样的。

### 方法3（双指针优化方法1）
方法 1 中，我们需要用到一个额外数组来保存左右两边的最大值，其实我们可以用双指针法来规避这个问题。

考虑用两个指针 l 和 r 分别从最左和最右端往中间靠拢，同时用 lmax 记录 l 左边的最高高度，用 rmax 记录 r 右边的最高高度。

此时如果 $h[l] < h[r]$ ，那么我们计算 l 处能蓄水多高，如果 $h[l] \ge h[r]$ ，我们计算 r 处蓄水多高。这样我们时刻只计算低的那边的答案，就能保证 l 两边的最高处较小值一定是 lmax ，r 两边同理。为什么呢？你模拟一遍左右切换的过程就会发现，当 $h[l] > h[r]$ 的时候，切换到计算 r 那边去了，再继续等到 $h[r] > h[l]$ 的时候，又切回计算 l 这边了，所以两端 l 和 r 的值始终保证：当它固定不动，计算另一端高度时，它一定是这一边最高的。

那么如果 $h[l] < h[r]$ ，我们怎么计算 $h[l]$ 处蓄水多高呢？如果 $h[l] \ge lmax$ ，那么 l 处根本就没法蓄水，因为它是最高的，所以更新 lmax 就行了。否则的话 l 两边最大高度较小值一定是 lmax ，还是按照方法 1 那样计算就行了。

这样的话，就不需要额外维护一个高度数组了。时间复杂度 $O(n)$ ，空间复杂度 $O(1)$。

## 代码
### 方法1（c++）
```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();
        vector<int> lmax(n+1, 0);
        for (int i = 0; i < n; ++i) {
            lmax[i+1] = max(height[i], lmax[i]);
        }
        int rmax = 0, res = 0;
        for (int i = n-1; i >= 0; --i) {
            rmax = max(rmax, height[i]);
            res += min(lmax[i+1], rmax) - height[i];
        }
        return res;
    }
};
```

### 方法2（c++）
```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size(), res = 0;
        stack<int> st;
        for (int i = 0; i < n; ++i) {
            while (!st.empty() && height[i] > height[st.top()]) {
                int mid = st.top();
                st.pop();
                if (st.empty()) break;
                res += (min(height[st.top()], height[i]) - height[mid]) * (i - st.top() - 1);
            }
            st.push(i);
        }
        return res;
    }
};
```

### 方法3（c++）
```cpp
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size(), res = 0;
        int l = 0, r = n-1, lmax = 0, rmax = 0;
        while (l < r) {
            if (height[l] < height[r]) {
                if (height[l] >= lmax) lmax = height[l];
                else res += lmax - height[l];
                l++;
            } else {
                if (height[r] >= rmax) rmax = height[r];
                else res += rmax - height[r];
                r--;
            }
        }
        return res;
    }
};
```

### 方法1（python）
```python
class Solution:
    def trap(self, height: List[int]) -> int:
        n = len(height)
        lmax = [0] * (n+1)
        for i in range(n):
            lmax[i+1] = max(height[i], lmax[i])
        rmax, res = 0, 0
        for i in range(n-1, -1, -1):
            rmax = max(rmax, height[i])
            res += min(lmax[i+1], rmax) - height[i]
        return res
```

### 方法2（python）
```python
class Solution:
    def trap(self, height: List[int]) -> int:
        n, res = len(height), 0
        st = []
        for i in range(n):
            while len(st) != 0 and height[i] > height[st[-1]]:
                mid = st[-1]
                st.pop()
                if len(st) == 0:
                    break
                res += (min(height[st[-1]], height[i]) - height[mid]) * (i - st[-1] - 1)
            st.append(i)
        return res
```

### 方法3（python）
```python
class Solution:
    def trap(self, height: List[int]) -> int:
        n, res = len(height), 0
        l, r, lmax, rmax = 0, n-1, 0, 0
        while l < r:
            if height[l] < height[r]:
                if height[l] >= lmax:
                    lmax = height[l]
                else:
                    res += lmax - height[l]
                l += 1
            else:
                if height[r] >= rmax:
                    rmax = height[r]
                else:
                    res += rmax - height[r]
                r -= 1
        return res
```

## 后记
这题方法还是很多的，最好需要画个示意图，模拟一下运行过程。