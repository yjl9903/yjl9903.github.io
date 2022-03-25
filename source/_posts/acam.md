---
mathjax: true
title: AC 自动机
date: 2019-06-30 20:52:42
tags:
- String
categories:
- 字符串
- KMP
---

# 模板

```c++
namespace ACAM {
    static const int maxp = 100000 + 5;
    int sz, ch[maxp][26], fail[maxp];
    int match[maxp], val[maxp];
    int node() {
        ms(ch[++sz], 0); fail[sz] = 0; val[sz] = 0;
        return sz;
    }
    void clear() {
        sz = 0; node();
        for (int i = 0; i < 26; i++) ch[0][i] = 1;
    }
    void insert(char* s, int i) {
        int u = 1;
        for (int i = 0; s[i]; i++) {
            int v = s[i] - 'a';
            if (!ch[u][v]) ch[u][v] = node();
            u = ch[u][v];
        }
        val[u]++; match[i] = u;
    }
    void build() {
        queue<int> q; q.push(1);
        while (!q.empty()) {
            int t = q.front(); q.pop();
            for (int i = 0; i < 26; i++) {
                if (ch[t][i]) {
                    fail[ch[t][i]] = ch[fail[t]][i];
                    q.push(ch[t][i]);
                } else {
                    ch[t][i] = ch[fail[t]][i];
                }
            }
        }
    }
}
```

<!--more-->

# 注意

+ $1$ 号结点为空结点。

+ 字母大小写和字母值域。

# 定义

+ fail 指针: 当前结点对应串的最长后缀，使得他也是 Trie 树上的结点，类似于 KMP。

+ Trie 图: 建完 AC 自动机后，Trie 树变成了 Trie 图，Trie 树上不存在的出边补成 fail 指针指向结点的对应出边。

# 应用

## 危险结点

串的终止结点是危险结点，fail 指向危险结点的是危险结点。

```c++
int node() {
    ms(ch[++sz], 0); 
    fail[sz] = 0; danger[sz] = 0;
    return sz;
}
void build() {
    queue<int> q; q.push(1);
    while (!q.empty()) {
        int t = q.front(); q.pop();
        for (int i = 0; i < 2; i++) {
            if (ch[t][i]) {
                fail[ch[t][i]] = ch[fail[t]][i];
                danger[ch[t][i]] |= danger[fail[ch[t][i]]];
                // pushdown danger
                q.push(ch[t][i]);
            } else {
                ch[t][i] = ch[fail[t]][i];
            }
        }
    }
}
```

## 求所有模式串出现次数

类似于 KMP，在 Trie 上的每个点打标记，然后将标记从底往上传递，其实就是将 fail 树建初来，求子树大小。

也可以对 fail 树跑拓扑排序。

```c++
vector<int> edge[maxp];
int siz[maxp];
void dfs(int u) {
    for (int& v: edge[u]) {
        dfs(v); siz[u] += siz[v];
    }
}
vector<int> query(int n, char* s) {
    // n: 文本串个数, s: 询问串
    for (int i = 0; i <= sz; i++) edge[i].clear(), siz[i] = 0;
    int now = 1;
    for (int i = 0; s[i]; i++) {
        now = ch[now][s[i] - 'a'];
        siz[now]++;
    }
    for (int i = 2; i <= sz; i++) edge[fail[i]].push_back(i);
    dfs(1);
    vector<int> ans(1, 0);
    for (int i = 1; i <= n; i++) ans.push_back(siz[match[i]]);
    return ans;
}
```

## 询问串包含另一个串的个数

枚举第一个串的每个前缀，即跳 Trie 上的父亲，再每次跳 fail 指针，看能否跳到另外一个结点上。

## AC 自动机上 DP

令 $dp[i][j]$ 表示询问串长度为 $i$，处于结点 $j$ 的不包含任何一个 Trie 树结点的情况数。

枚举每个状态 $j$ 的儿子 $v$，且 $ch[j][v]$ 不是危险结点，因为到该状态不包含任何一个模板串，加入一个新的字母后，只存在当前某个后缀可能会变成模板串，如果该节点是危险结点，意味着他含有一个后缀是模板串，否则不就一定存在模板串，此时有转移方程 $dp[i][ch[j][v]]+=dp[i-1][j]$。

优化：显然每次转移的转移矩阵相同，矩阵快速幂加速。