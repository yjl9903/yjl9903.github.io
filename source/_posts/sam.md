---
mathjax: true
title: 后缀自动机
date: 2018-09-18 18:41:48
tags:
- String
- Suffix Automata
categories:
- 字符串
- 后缀自动机
---

# 后缀自动机

```c++
namespace sam {
    int tot, last, cnt[maxn << 1];
    int len[maxn << 1], link[maxn << 1], ch[maxn << 1][26];
    void clear() { 
        tot = last = 1; ms(ch[1], 0);
    }
    void insert(int c) {
        int cur = ++tot, p = last; 
        ms(ch[cur], 0);
        len[cur] = len[last] + 1; 
        cnt[cur] = 1;
        for (; p && !ch[p][c]; p = link[p]) ch[p][c] = cur;
        if (!p) link[cur] = 1;
        else {
            int q = ch[p][c];
            if (len[p] + 1 == len[q]) link[cur] = q;
            else {
                int nq = ++tot; 
                len[nq] = len[p] + 1; 
                cnt[nq] = 0;
                link[nq] = link[q]; 
                link[q] = link[cur] = nq;
                memcpy(ch[nq], ch[q], sizeof ch[q]);
                for (; ch[p][c] == q; p = link[p]) ch[p][c] = nq;
            }
        }
        last = cur;
    }
    int c[maxn << 1], a[maxn << 1];
    void rsort() {
        for (int i = 1; i <= tot; i++) c[i] = 0;
        for (int i = 1; i <= tot; i++) c[len[i]]++;
        for (int i = 1; i <= tot; i++) c[i] += c[i - 1];
        for (int i = 1; i <= tot; i++) a[c[len[i]]--] = i;
    }
}
```

# 广义后缀自动机

```c++
namespace gsam {
    int tot, last, cnt[maxn << 1];
    int len[maxn << 1], link[maxn << 1], ch[maxn << 1][2];
    int insert(int last, int c) {
        int cur = ++tot, p = last; 
        ms(ch[cur], 0);
        len[cur] = len[last] + 1; 
        cnt[cur] = 1;
        for (; p && !ch[p][c]; p = link[p]) ch[p][c] = cur;
        if (!p) link[cur] = 1;
        else {
            int q = ch[p][c];
            if (len[p] + 1 == len[q]) link[cur] = q;
            else {
                int nq = ++tot; 
                len[nq] = len[p] + 1; 
                cnt[nq] = 0;
                link[nq] = link[q]; 
                link[q] = link[cur] = nq;
                memcpy(ch[nq], ch[q], sizeof ch[q]);
                for (; ch[p][c] == q; p = link[p]) ch[p][c] = nq;
            }
        } 
        return cur;
    }
    namespace Trie {
        int tot, ch[maxn][2], pos[maxn];
        void clear() {
            tot = 1; ms(ch[1], 0);
        }
        void insert(char* s) {
            int u = 1;
            for (int i = 0; s[i]; i++) {
                int c = s[i] - '0';
                if (!ch[u][c]) {
                    ch[u][c] = ++tot;
                    ms(ch[tot], 0);
                }
                u = ch[u][c];
            }
        }
        void build() {
            queue<int> q; q.push(1);
            pos[1] = 1;
            while (!q.empty()) {
                int u = q.front(); q.pop();
                for (int i = 0; i < 2; i++) {
                    if (!ch[u][i]) continue;
                    int v = ch[u][i];
                    pos[v] = gsam::insert(pos[u], i);
                    q.push(v);
                }
            }
        }
    }
    using Trie::insert;
    using Trie::build;
    void clear() { 
        Trie::clear();
        tot = last = 1;
        ms(ch[1], 0);
    }
}
```

<!--more-->

# 不同子串个数

## 方法一

不同子串数目即 $sam$ 上的不同路径的数目，记忆化搜索。

```c++
ll dfs(int u) {
    if (ans[u] != -1) return ans[u];
    ll x = 1;
    for (int i = 0; i < 26; i++) {
        if (!ch[u][i]) continue;
        x += dfs(ch[u][i]);
    }
    return ans[u] = x;
}

dfs(1);
printf("%lld", ans[1] - 1);
```

## 方法二

不同子串数目为 $sam$ 上每个 $endpos$ 状态上的子串数目,  即 $\sum_{i = 1}^{tot} len(i)-len(link(i))$。

```c++
ll cal(){  
  ll ans = 0;
  for (int i = 1; i <= tot; i++) ans += len[i] - len[link[i]];
  return ans;
}
```

### 增量维护前缀不同子串数

参考 [HDu4622](http://acm.hdu.edu.cn/viewcode.php?rid=26316331) 和 [SDOI2016 生成魔咒](https://www.luogu.org/problemnew/show/P4070)。

```c++
ll insert(int c) {
    // ...
    return cnt = cnt + len[cur] - len[link[cur]];
}
```

# 子串出现次数

按长度对状态基数排序可以得到 $sam$ 的拓扑排序序列。

```c++
int c[maxn], a[maxn];
void rsort() {
    for (int i = 1; i <= tot; i++) c[i] = 0;
    for (int i = 1; i <= tot; i++) c[len[i]]++;
    for (int i = 1; i <= tot; i++) c[i] += c[i - 1];
    for (int i = 1; i <= tot; i++) a[c[len[i]]--] = i;
    for (int i = tot; i; i--) {
        int p = a[i];
        cnt[link[p]] += cnt[p];
    }
}
```

# 最长公共子串

对第一个串建立一个 $sam$ ，维护第二个串的每一个前缀的最长公共子串，即每一个前缀在前一个串中出现过的最长后缀。

```c++
int ans = 0, now = 1, l = 0;
for (int i = 0; s[i]; i++) {
    if (ch[now][s[i] - 'a']) l++, now = ch[now][s[i] - 'a'];
    else {
        while (now && !ch[now][s[i] - 'a']) now = link[now];
        if (!now) now = 1, l = 0;
        else l = len[now] + 1, now = ch[now][s[i] - 'a'];
    }
}
printf("%d", ans);
```

# 多个串的最长公共子串

```c++
for (int i = 1; i <= tot; i++) ans[i] = len[i];
while (scanf("%s", s + 1) == 1) {
    ms(dep, 0);
    int u = 1, l = 0;
    for (int i = 1; s[i]; i++) {
        int c = s[i] - 'a';
        if (ch[u][c]) u = ch[u][c], l++;
        else {
            while (u && !ch[u][c]) u = link[u];
            if (!u) u = 1, l = 0;
            else l = len[u] + 1, u = ch[u][c];
        }
        dep[u] = max(dep[u], l);
    }
    for (int i = tot; i >= 1; i--) {
        int u = sam::a[i];
        dep[link[u]] = max(dep[u], dep[link[u]]);
    }
    for (int i = tot; i >= 1; i--) ans[i] = min(ans[i], dep[i]);
}
int res = 0;
for (int i = 1; i <= tot; i++) res = max(res, ans[i]);
printf("%d", res);
```

# 字典序第 $k$ 大子串

$t=0$，重复子串只算一个；$t=1$，重复子串算多个。

```c++
void rsort(int t) {
    for (int i = 1; i <= sz; i++) c[len[i]]++;
    for (int i = 1; i <= sz; i++) c[i] += c[i - 1];
    for (int i = 1; i <= sz; i++) a[c[len[i]]--] = i;
    for (int i = sz; i >= 1; i--) {
        int p = a[i];
        if (t) cnt[link[p]] += cnt[p];
        else cnt[p] = 1;
    }
    cnt[1] = 0;
    for (int i = sz; i >= 1; i--) {
        int p = a[i];
        sum[p] = cnt[p];
        for (int j = 0; j < 26; j++) {
            if (ch[p][j])
                sum[p] += sum[ch[p][j]];
        }
    }
}
string query(int k) {
    if (k > sum[1]) return string("-1");
    int u = 1; string ans; k -= cnt[1];
    while (k > 0) {
        int c = 0;
        while (k > sum[ch[u][c]]) {
            k -= sum[ch[u][c]]; c++;
        }
        u = ch[u][c]; ans += ('a' + c); k -= cnt[u];
    }
    return ans;
}
```

# 线段树合并

[Codeforces666E Forensic Examination](https://codeforces.com/contest/666/problem/E)

```c++
namespace SegTrees {
    const int maxm = maxn * 32;
    int tot, root[maxn << 1], ls[maxm], rs[maxm];
    PII val[maxm];
    PII cal(PII a, PII b) {
        if (a.first > b.first) return a;
        else if (a.first == b.first && a.second < b.second) return a;
        else return b;
    }
    void update(int i, int l, int r, int& rt) {
        if (!rt) rt = ++tot;
        if (l == r) {
            val[rt].first++;
            val[rt].second = i;
            return ;
        }
        int m = (l + r) / 2;
        if (i <= m) update(i, l, m, ls[rt]);
        else update(i, m + 1, r, rs[rt]);
        val[rt] = cal(val[ls[rt]], val[rs[rt]]);
    }
    int join(int x, int y, int l, int r) {
        if (!x || !y) return x + y;
        int u = ++tot;
        if (l == r) {
            val[u].first = val[x].first + val[y].first;
            val[u].second = l;
        } else {
            int m = (l + r) / 2;
            ls[u] = join(ls[x], ls[y], l, m);
            rs[u] = join(rs[x], rs[y], m + 1, r);
            val[u] = cal(val[ls[u]], val[rs[u]]);
        }
        return u;
    }
    PII query(int L, int R, int l, int r, int rt) {
        if (!rt) return { -1, inf };
        if (L <= l && r <= R) return val[rt];
        int m = (l + r) / 2;
        if (R <= m) return query(L, R, l, m, ls[rt]);
        else if (L > m) return query(L, R, m + 1, r, rs[rt]);
        else return cal(query(L, R, l, m, ls[rt]), query(L, R, m + 1, r, rs[rt]));
    }
}
```