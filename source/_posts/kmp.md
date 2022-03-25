---
mathjax: true
title: KMP 算法
date: 2018-10-23 01:54:18
tags:
- String
categories:
- 字符串
- KMP
---

# KMP

## 模板1

```c++
char s[maxn], p[maxn];
int nxt[maxn];

void getnxt(char *p){
    int len = strlen(p), k = -1, i = 0; nxt[0] = -1;
    while (i < len){
        if (k == -1 || p[k] == p[i]) i++, k++, nxt[i] = k;
        else k = nxt[k];
    }
}
int kmp(char *s, char *p){
    getnxt(p);
    int slen = strlen(s), plen = strlen(p), i = 0, j = 0;
    while (i < slen && j < plen){
        if (j == -1 || s[i] == p[j]) i++, j++;
        else j = nxt[j];
    }
    if (j == plen) return i - j;
    return -1;
}
```

## 模板2

```c++
void getfail(int len, char* s, int fail[]) {
    fail[1] = 0;
    for (int i = 2; i <= len; i++) {
        int cur = fail[i - 1];
        while (cur > 0 && s[cur + 1] != s[i])
            cur = fail[cur];
        if (s[cur + 1] == s[i])
            ++cur;
        fail[i] = cur;
    }
}
void kmp(char *s, char *p) {
    int slen = strlen(s + 1), plen = strlen(p + 1), cur = 0;
    getfail(plen, p, nxt);
    for (int i = 1; i <= slen; i++) {
        while (cur > 0 && s[i] != p[cur + 1]) cur = nxt[cur];
        if (p[cur + 1] == s[i]) cur++;
        if (cur == plen) {
            printf("%d\n", i - cur + 1);
            cur = nxt[cur];
        }
    }
}
```

# 扩展KMP

```c++
char s[maxn], t[maxn];
int nxt[maxn], extend[maxn];

void getnxt(char *s){
    int n = strlen(s), p = 0, k = 1; nxt[0] = n;
    while (p + 1 < n && s[p] == s[p + 1]) p++;
    nxt[1] = p;
    for (int i = 2; i < n; i++){
        p = k + nxt[k] - 1;
        if (i + nxt[i - k] <= p) nxt[i] = nxt[i - k];
        else {
            int j = max(p - i + 1, 0);
            while (i + j < n && s[i + j] == s[j]) j++;
            nxt[i] = j; k = i;
        }
    }
}
void exkmp(char *t, char *s){ 
    getnxt(s); int tlen = strlen(t), slen = strlen(s), p = 0, k = 0;
    while (p < tlen && p < slen && t[p] == s[p]) p++;
    extend[0] = p;
    for (int i = 1; i < tlen; i++){
        p = k + extend[k] - 1;
        if (i + nxt[i - k] <= p) extend[i] = nxt[i - k];
        else {
            int j = max(p - i + 1, 0);
            while (i + j < tlen && j < slen && t[i + j] == s[j]) j++;
            extend[i] = j; k = i;
        }
    }
}
```

<!--more-->

# 注意

参数均为前一个是文本串，后一个为模板串。

# 应用

## 求 $p$ 串在 $t$ 串中的位置可重复的出现次数

传送门：[子串查找](https://loj.ac/problem/103)

$kmp$ 时，$j=plen$ 时更新答案，并将 $j$ 转移到对应的 $nxt$ 值。

## 求 $s$ 串的最短循环节

传送门：[Power Strings](https://loj.ac/problem/10035)

$getnxt()$ 后，$nxt[len]$ 表示 $s$ 串前缀和后缀匹配的最大长度。

如果 $len-nxt[len]$ 整除 $len$，则这个串有循环节，循环节长度就是 $len-nxt[len]$。 

## 求每个前缀的出现次数

每经过模板串上的一个位置，这个前缀和他的前缀函数等等出现次数都 $+1$。

匹配时更新一下每个位置的经过次数，最后倒序根据前缀函数更新次数。

### 代码

```c++
getfail(strlen(t + 1), t, fail);
for (int i = 1; i <= n; i++) {
    while (cur && t[cur + 1] != s[i]) cur = fail[cur];
    if (t[cur + 1] == s[i]) cur++;
    ans[cur]++;
    if (cur == m) cur = fail[cur];
}
for (int i = m; i >= 1; i--) ans[fail[i]] += ans[i];
```