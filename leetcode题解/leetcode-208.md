---
title: 【每日算法Day 84】面试必考题：Trie（字典树/前缀树）的实现
top: false
cover: false
toc: true
mathjax: true
date: 2020-03-29 12:42:43
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

## 题目链接
[LeetCode 208. 实现 Trie (前缀树)](https://leetcode-cn.com/problems/implement-trie-prefix-tree/ "LeetCode 208. 实现 Trie (前缀树)")

## 题目描述
实现一个 Trie （前缀树），包含 `insert`, `search`, 和 `startsWith` 这三个操作。

**示例1**
```text
Trie trie = new Trie();

trie.insert("apple");
trie.search("apple");   // 返回 true
trie.search("app");     // 返回 false
trie.startsWith("app"); // 返回 true
trie.insert("app");   
trie.search("app");     // 返回 true
```

**说明：**
* 你可以假设所有的输入都是由小写字母 $a-z$ 构成的。
* 保证所有输入均为非空字符串。

## 题解
字典树主要支持**插入字符串、查询字符串是否在字典树中、查询字典树中是否存在某个前缀**等操作，我这里还额外实现了一下 c++ 版本的**删除字符串**操作。

### 初始化字典树
初始化的时候，根结点为空，不用来放任何字符，所有字符串都是从下一层子结点开始存储。

每个结点有 26 个指针，指向下一层子结点，每个指针代表着下一个不同的字母。

每个结点还保存了一个变量 `isEnd` ，用来表示该结点是不是某个字符串结束的位置。

### 插入字符串
从根结点往下递归，如果字符串中下一个字母对应的子结点为空，那就新建一个结点再递归，否则的话就直接递归下去。

最后把最后一个结点的 `isEnd` 设置为 1，表示这个结点是字符串的结束位置。

### 查询字符串
从根结点往下递归查找，如果字符串还没遍历结束，但是结点已经空了，说明字符串不在字典树中。否则的话一直查找到最后一个字符，然后看对应结点的 `isEnd` 是 1 还是 0，如果是 1 ，就存在字符串，否则不存在。

### 查询字符串前缀
和查询字符串过程一模一样，唯一的区别就是最后不用看最后一个结点的 `isEnd` 了，直接返回 `true` 。因为既然都查询到了最后一个字符了，说明这个前缀一定存在。

### 删除字符串
这个是我自己实现的，一般来说字典树很少用到删除操作。

首先整体框架是和查询字符串类似的，从根结点往下递归查询，然后用一个栈保存查询到的结点。

如果查询过程中直接遇到了空结点，就直接返回，因为都不存在字符串，就不用删除了。然后判断最后一个结点的类型。

如果它的 `isEnd` 是 0，说明字符串不存在，那就直接返回不用删了。

如果它不是叶子结点，说明后面还接着字符串呢，那也不用删了，只要把该结点的 `isEnd` 设置为 0 就行了。

否则的话它就是叶子结点，那么就直接删除这个结点，并且从栈里出栈。

然后从栈里最后一个结点开始删除，直到栈顶的结点不是叶子结点（表示字典树中存在删除字符串的相同前缀字符串）或者 `isEnd` 是 1（表示字典树中存在删除字符串的前缀子串）。

## 代码
具体实现上面，c++ 我采用的结构体指针来构建出了一颗树。而 python 我直接用的嵌套的字典，并没有真正的构建出树，只有一个类，这样还挺方便的，但是删除操作有点麻烦，暂时就不写了。

### c++
```cpp
class Trie {
public:
    bool isEnd;
    vector<Trie*> next;

    Trie() {
        isEnd = false;
        next = vector<Trie*>(26, 0);
    }

    ~Trie() {
        for (auto p : next) delete p;
    }
    
    void insert(string word) {
        Trie* node = this;
        for (auto c : word) {
            if (node->next[c-'a'] == NULL) {
                node->next[c-'a'] = new Trie();
            }
            node = node->next[c-'a'];
        }
        node->isEnd = true;
    }

    void del(string word) {
        stack<Trie*> st;
        Trie* node = this;
        for (auto c : word) {
            node = node->next[c-'a'];
            st.push(node);
            if (node == NULL) return;
        }
        if (!(node->isEnd)) return;
        if (!isLeaf(node)) {
            node->isEnd = false;
            return;
        }
        delete node;
        st.pop();
        while (!st.empty()) {
            node = st.top();
            st.pop();
            if (isLeaf(node) && !(node->isEnd)) delete node;
            else break;
        }
    }
    
    bool search(string word) {
        Trie* node = this;
        for (auto c : word) {
            node = node->next[c-'a'];
            if (node == NULL) return false;
        }
        return node->isEnd;
    }
    
    bool startsWith(string prefix) {
        Trie* node = this;
        for (auto c : prefix) {
            node = node->next[c-'a'];
            if (node == NULL) return false;
        }
        return true;
    }

    bool isLeaf(Trie* node) {
        for (auto p : next) {
            if (p) return false;
        }
        return true;
    }
};
```

### python
```python
class Trie:
    def __init__(self):
        self.nxt = {}

    def insert(self, word: str) -> None:
        node = self.nxt
        for c in word:
            if c not in node:
                node[c] = {}
            node = node[c]
        node['#'] = True

    def search(self, word: str) -> bool:
        node = self.nxt
        for c in word:
            if c not in node:
                return False
            node = node[c]
        return '#' in node

    def startsWith(self, prefix: str) -> bool:
        node = self.nxt
        for c in prefix:
            if c not in node:
                return False
            node = node[c]
        return True
```