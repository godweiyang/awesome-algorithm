---
title: 【每日算法Day 93】不用额外空间，你会旋转一个矩阵吗？
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-07 10:33:44
password:
summary:
tags:
- leetcode
- 算法
categories:
- 编程算法
---

> 第 100 天时，我可能会对这 100 天以来的算法题进行一个总结，然后暂时停止更新 LeetCode 题解了。
下一步可能更新 NLP 相关算法了，非常感谢大家每天的支持。

## 题目链接
[LeetCode 面试题 01.07. 旋转矩阵](https://leetcode-cn.com/problems/rotate-matrix-lcci/ "LeetCode 面试题 01.07. 旋转矩阵")

## 题目描述
给你一幅由 `N × N` 矩阵表示的图像，其中每个像素的大小为 `4` 字节。请你设计一种算法，将图像旋转 `90` 度。

不占用额外内存空间能否做到？

**示例1**
```text
给定 matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],
原地旋转输入矩阵，使其变为:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```

**示例2**
```text
给定 matrix =
[
  [ 5, 1, 9,11],
  [ 2, 4, 8,10],
  [13, 3, 6, 7],
  [15,14,12,16]
], 
原地旋转输入矩阵，使其变为:
[
  [15,13, 2, 5],
  [14, 3, 4, 1],
  [12, 6, 8, 9],
  [16, 7,10,11]
]
```

## 题解
### 旋转
可以发现，每个格子旋转四次之后都会回到原位。所以对于每个格子，我们只需要交换和它相关的一共四个格子的位置就行了。

对于格子 `(i, j)` ，我们可以推算出它旋转后的三个新位置是 `(j, n-1-i), (n-1-i, n-1-j), (n-1-j, i)` 。所以只需要一个临时变量保存其中一个位置的值，然后按顺序交换位置就行了。

当然为了避免重复旋转，我们只能枚举四分之一的格子，如果 `n` 是偶数，如下图所示，我们可以这么划分：

![](3.jpg)

如果 `n` 是奇数，可以如下图这么划分：

![](2.jpg)

当然你也可以不规则的划分，如下图这样，只是代码写起来有点费劲：

![](1.jpg)

### 翻转
这题还可以不通过模拟旋转来实现旋转。

上面说过了，格子 `(i, j)` 旋转后的新位置是 `(j, n-1-i)` 。

那么我们可以先沿着主对角线翻转矩阵，这样格子 `(i, j)` 位置就换到了 `(j, i)` 。

然后再左右翻转一下矩阵，格子 `(j, i)` 就换到了 `(j, n-1-i)` ，正好等价于旋转后的位置！

因为翻转每次只需要交换两个格子的位置，所以不需要任何额外变量。

再提一个交换两个元素的小 trick ，如代码里注释的那样，可以采用异或操作来规避额外变量。

## 代码
### 旋转（c++）
```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        for (int i = 0; i < n/2; ++i) {
            for (int j = 0; j < (n+1)/2; ++j) {
                int tmp = matrix[n-1-j][i];
                matrix[n-1-j][i] = matrix[n-1-i][n-1-j];
                matrix[n-1-i][n-1-j] = matrix[j][n-1-i];
                matrix[j][n-1-i] = matrix[i][j];
                matrix[i][j] = tmp;
            }
        }
    }
};
```

### 翻转（c++）
```cpp
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        for (int i = 0; i < n; ++i) {
            for (int j = i+1; j < n; ++j) {
                matrix[i][j] ^= matrix[j][i];
                matrix[j][i] ^= matrix[i][j];
                matrix[i][j] ^= matrix[j][i];
                // swap(matrix[i][j], matrix[j][i]);
            }
        }
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n/2; ++j) {
                matrix[i][j] ^= matrix[i][n-1-j];
                matrix[i][n-1-j] ^= matrix[i][j];
                matrix[i][j] ^= matrix[i][n-1-j];
                // swap(matrix[i][j], matrix[i][n-1-j]);
            }
        }
    }
};
```

### 旋转（python）
```python
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        n = len(matrix)
        for i in range(n//2):
            for j in range((n+1)//2):
                tmp = matrix[n-1-j][i]
                matrix[n-1-j][i] = matrix[n-1-i][n-1-j]
                matrix[n-1-i][n-1-j] = matrix[j][n-1-i]
                matrix[j][n-1-i] = matrix[i][j]
                matrix[i][j] = tmp
```

### 翻转（python）
```python
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        n = len(matrix)
        for i in range(n):
            for j in range(i+1, n):
                matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
        for i in range(n):
            for j in range(n//2):
                matrix[i][j], matrix[i][n-1-j] = matrix[i][n-1-j], matrix[i][j]
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)