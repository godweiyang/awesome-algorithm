## 题目链接
[LeetCode 字符串转换整数（atoi）](https://leetcode-cn.com/problems/string-to-integer-atoi/ "LeetCode 字符串转换整数（atoi）")

## 题目描述
请你来实现一个 `atoi` 函数，使其能将字符串转换成整数。

首先，该函数会根据需要丢弃无用的开头空格字符，直到寻找到第一个非空格的字符为止。接下来的转化规则如下：
* 如果第一个非空字符为正或者负号时，则将该符号与之后面尽可能多的连续数字字符组合起来，形成一个有符号整数。
* 假如第一个非空字符是数字，则直接将其与之后连续的数字字符组合起来，形成一个整数。
* 该字符串在有效的整数部分之后也可能会存在多余的字符，那么这些字符可以被忽略，它们对函数不应该造成影响。

注意：假如该字符串中的第一个非空格字符不是一个有效整数字符、字符串为空或字符串仅包含空白字符时，则你的函数不需要进行转换，即无法进行有效转换。

在任何情况下，若函数不能进行有效的转换时，请返回 `0` 。

**说明：**
* 本题中的空白字符只包括空格字符 ' ' 。
* 假设我们的环境只能存储 `32` 位大小的有符号整数，那么其数值范围为 $[−2^{31},  2^{31} − 1]$。如果数值超过这个范围，请返回 `INT_MAX` （$2^{31} − 1$） 或 `INT_MIN` （$−2^{31}$） 。

**示例1**
```text
输入：
"42"
输出:
42
```

**示例2**
```text
输入：
"   -42"
输出:
-42
解释：
第一个非空白字符为 '-', 它是一个负号。
我们尽可能将负号与后面所有连续出现的数字组合起来，最后得到 -42 。
```

**示例3**
```text
输入：
"4193 with words"
输出:
4193
解释：
转换截止于数字 '3' ，因为它的下一个字符不为数字。
```

**示例4**
```text
输入：
"words and 987"
输出:
0
解释：
第一个非空字符是 'w', 但它不是数字或正、负号。
因此无法执行有效的转换。
```

**示例5**
```text
输入：
"-91283472332"
输出:
-2147483648
解释：
数字 "-91283472332" 超过 32 位有符号整数范围。 
因此返回 INT_MIN (−231) 。
```

## 题解
官方题解用的有限状态自动机，理解起来也是比较轻松的，写起来也避免了一堆臃肿的 `if-else` 。但是我这里就不用自动机了，直接模拟就行了，写起来也很轻松好理解！

* 首先用一个变量 `idx` 标记当前遍历到的位置，用 `sign = 1, -1` 表示整数的正负性。
* 然后从 `idx = 0` 开始，将开头的空格全部去掉。

* 然后判断当前的位置 `idx` 处的字符是不是 `+, -` 或者数字，并且 `idx` 要小于字符串长度。如果一个都没满足，那么说明是非法字符串，直接返回 `0` 。

* 然后如果当前的位置 `idx` 处的字符是 `-` ，那么就令 `sign = -1` 。否则如果是 `+` ，就令 `sign = 1` 。然后遍历后一个字符。

* 最后遍历一段连续的数字，把它转换成整数，注意转换的时候要乘上符号位 `sign` 。如果发现数字超过了 `[INT_MIN, INT_MAX]` 范围，就直接返回最大最小值就行了。

* 否则的话最后就是合法整数，直接返回答案。

## 代码
### c++
```cpp
class Solution {
public:
    int myAtoi(string str) {
        int idx = 0, n = str.size();
        long res = 0, sign = 1;
        while (idx < n && str[idx] == ' ') idx++;
        if (idx >= n || (str[idx] != '+' && str[idx] != '-' && !isdigit(str[idx]))) return 0;
        if (str[idx] == '-') {
            sign = -1;
            idx++;
        } else if (str[idx] == '+') {
            idx++;
        }
        while (idx < n && isdigit(str[idx])) {
            res = res*10+sign*(str[idx++]-'0');
            if (res > INT_MAX) return INT_MAX;
            if (res < INT_MIN) return INT_MIN;
        }
        return res;
    }
};
```

### python
```python
class Solution:
    def myAtoi(self, str: str) -> int:
        str = str.lstrip()
        n, idx = len(str), 0
        res, sign = 0, 1
        if idx >= n or (str[idx] != '+' and str[idx] != '-' and not str[idx].isdigit()): return 0
        if str[idx] == '-':
            sign = -1
            idx += 1
        elif str[idx] == '+':
            idx += 1
        for c in str[idx:]:
            if not c.isdigit(): break
            res = res*10+sign*(int(c))
            if res > 2**31-1: return 2**31-1
            if res < -2**31: return -2**31
        return res
```

### 正则表达式（python）
```python
class Solution:
    def myAtoi(self, str: str) -> int:
        return max(min(int(*re.findall('^[\+\-]?\d+', str.lstrip())), 2**31-1), -2**31)
```

> 关注公众号【算法码上来】，每日算法干货马上就来！
![](/medias/contact.jpg)