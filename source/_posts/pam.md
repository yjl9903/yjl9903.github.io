---
mathjax: true
title: 回文树
date: 2019-07-26 21:25:40
tags:
- String
- Suffix Automata
categories:
- 字符串
- 后缀自动机
---

# 模板

```c++
namespace pam {
    int sz, tot, last;
    int ch[maxn][26], len[maxn], fail[maxn];
    int cnt[maxn], dep[maxn];
    char s[maxn];
    int node(int l) {
        sz++; ms(ch[sz], 0);
        len[sz] = l; fail[sz] = cnt[sz] = dep[sz] = 0;
        return sz;
    }
    void clear() {
        sz = -1; last = 0;
        s[tot = 0] = '$';
        node(0); node(-1);
        fail[0] = 1;
    }
    int getfail(int x) {
        while (s[tot - len[x] - 1] != s[tot]) x = fail[x];
        return x;
    }
    void insert(char c) {
        s[++tot] = c;
        int now = getfail(last);
        if (!ch[now][c - 'a']) {
            int x = node(len[now] + 2);
            fail[x] = ch[getfail(fail[now])][c - 'a'];
            dep[x] = dep[fail[x]] + 1;
            ch[now][c - 'a'] = x;
        }
        last = ch[now][c - 'a'];
        cnt[last]++;
    }
    void count() {
        for (int i = sz; i >= 0; i--) {
            cnt[fail[i]] += cnt[i];
        }
    }
}
```

<!--more-->

# 定义

+ 奇数根结点 $1$，偶数根结点 $0$。

+ `fail[x]`：结点 $x$ 的最长回文后缀结点。

+ `cnt[x]`：结点 $x$ 的出现次数。

+ `dep[x]`：结点 $x$ 在 fail 树深度。

+ `half[x]`：结点 $x$ 的小于等于 `len[x] / 2` 的最长回文后缀。

# 维护 half 指针

```c++
void insert(char c) {
    s[++tot] = c;
    int now = getfail(last);
    if (!ch[now][c - 'a']) {
        int x = node(len[now] + 2);
        fail[x] = ch[getfail(fail[now])][c - 'a'];
        dep[x] = dep[fail[x]] + 1;
        ch[now][c - 'a'] = x;

        if (len[x] == 1) half[x] = 1;
        else {
            int pos = half[now];
            while (s[tot - len[pos] - 1] != s[tot] || (2 + len[pos]) * 2 > len[x]) pos = fail[pos];
            half[x] = ch[pos][c - 'a'];
        }
        val[x] = 1;
        if (len[x] / 2 == len[half[x]]) val[x] += val[half[x]];
    }
    last = ch[now][c - 'a'];
    cnt[last]++;
}
```

# 双向加字符

```c++
namespace pam {
    // last1: right, last2: left
    int sz, last1, last2, l, r;
    int ch[maxn][26], len[maxn], fail[maxn];
    int cnt[maxn], dep[maxn];
    char s[maxn];
    int node(int l) {
        sz++; ms(ch[sz], 0);
        len[sz] = l;
        fail[sz] = cnt[sz] = dep[sz] = 0;
        return sz;
    }
    void clear() {
        sz = -1; last1 = last2 = 0;
        l = 100002; r = l - 1;
        node(0); node(-1);
        fail[0] = 1;
        ms(s, -1);
    }
    int getfail1(int x) {
        while (s[r - len[x] - 1] != s[r]) x = fail[x];
        return x;
    }
    int getfail2(int x) {
        while (s[l + len[x] + 1] != s[l]) x = fail[x];
        return x;
    }
    int insertR(char c) {
        s[++r] = c;
        int now = getfail1(last1);
        if (!ch[now][c - 'a']) {
            int x = node(len[now] + 2);
            fail[x] = ch[getfail1(fail[now])][c - 'a'];
            dep[x] = dep[fail[x]] + 1;
            ch[now][c - 'a'] = x;
        }
        last1 = ch[now][c - 'a'];
        cnt[last1]++;
        if (len[last1] == r - l + 1) last2 = last1;
        return dep[last1];
    }
    int insertL(char c) {
        s[--l] = c;
        int now = getfail2(last2);
        if (!ch[now][c - 'a']) {
            int x = node(len[now] + 2);
            fail[x] = ch[getfail2(fail[now])][c - 'a'];
            dep[x] = dep[fail[x]] + 1;
            ch[now][c - 'a'] = x;
        }
        last2 = ch[now][c - 'a'];
        cnt[last2]++;
        if (len[last2] == r - l + 1) last1 = last2;
        return dep[last2];
    }
}
```