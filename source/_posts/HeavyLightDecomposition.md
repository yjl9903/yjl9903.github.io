---
title: 树链剖分
date: 2018-08-14 13:06:15
tags: [Tree, Data Structure]
categories: 树
comments: false
---

树链剖分：树链更新和查询 + 子树更新和查询

{% codeblock lang:cpp %}
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
using namespace std;
typedef long long ll;
const int maxn = 100000 + 5;

int to[maxn << 1], nxt[maxn << 1], head[maxn], tot = 0;
void add(int x, int y){
    to[++tot] = y; nxt[tot] = head[x]; head[x] = tot;
}

int n, m, a[maxn];

namespace hld{
    ll wt[maxn], tree[maxn << 2], laz[maxn << 2];
    void pushup(int rt){tree[rt] = tree[rt << 1] + tree[rt << 1 | 1];}
    void pushdown(int rt, int ln, int rn){
        if (!laz[rt]) return;
        tree[rt << 1] += 1ll * laz[rt] * ln; tree[rt << 1 | 1] += 1ll * laz[rt] * rn;
        laz[rt << 1] += 1ll * laz[rt]; laz[rt << 1 | 1] += 1ll * laz[rt];
        laz[rt] = 0;
    }
    void build(int l, int r, int rt){
        if (l == r){
            laz[rt] = 0; tree[rt] = wt[l];
            return;
        }
        int m = (l + r) >> 1;
        build(lson); build(rson);
        pushup(rt);
    }
    void update(int i, int x, int l, int r, int rt){
        if (l == r){
            tree[rt] += 1ll * x;
            return;
        }
        int m = (l + r) >> 1; pushdown(rt, m - l + 1, r - m);
        if (i <= m) update(i, x, lson);
        else update(i, x, rson);
        pushup(rt);
    }
    void update(int L, int R, int x, int l, int r, int rt){
        if (L <= l && r <= R){
            tree[rt] += 1ll * x * (r - l + 1); laz[rt] += 1ll * x;
            return;
        }
        int m = (l + r) >> 1; pushdown(rt, m - l + 1, r - m);
        if (L <= m) update(L, R, x, lson);
        if (R > m) update(L, R, x, rson);
        pushup(rt);
    }
    ll query(int L, int R, int l, int r, int rt){
        if (L <= l && r <= R) return tree[rt];
        int m = (l + r) >> 1; pushdown(rt, m - l + 1, r - m);
        ll s = 0;
        if (L <= m) s += query(L, R, lson);
        if (R > m) s += query(L, R, rson);
        return s;
    }

    int siz[maxn], dep[maxn], fa[maxn], son[maxn], top[maxn], id[maxn], cnt = 0;
    void dfs(int p, int d, int old){
        dep[p] = d; fa[p] = old; siz[p] = 1;
        int m = -1;
        for (int i = head[p]; i; i = nxt[i]){
            int v = to[i];
            if (v == fa[p]) continue;
            dfs(v, d + 1, p);
            siz[p] += siz[v];
            if (siz[v] > m) son[p] = v, m = siz[v];
        }
    }
    void dfs(int p, int tp){
        id[p] = ++cnt; top[p] = tp; wt[cnt] = a[p];
        if (!son[p]) return;
        dfs(son[p], tp);
        for (int i = head[p]; i; i = nxt[i]){
            int v = to[i];
            if (v == fa[p] || v == son[p]) continue;
            dfs(v, v);
        }
    }
    void init(){
        cnt = 0; dfs(1, 1, 0); dfs(1, 1); build(1, n, 1);
    }

    int qpath(int x, int y){
        int ans = 0;
        while (top[x] != top[y]){
            if (dep[top[x]] < dep[top[y]]) swap(x, y);
            ans = (ans + query(id[top[x]], id[x], 1, n, 1)) % p;
            x = fa[top[x]];
        }
        if (dep[x] > dep[y]) swap(x, y);
        ans = (ans + query(id[x], id[y], 1, n, 1)) % p;
        return ans;
    }
    int qson(int x){
        return query(id[x], id[x] + siz[x] - 1, 1, n, 1);
    }
    void upath(int x, int y, int k){
        k %= p;
        while (top[x] != top[y]){
            if (dep[top[x]] < dep[top[y]]) swap(x, y);
            update(id[top[x]], id[x], k, 1, n, 1);
            x = fa[top[x]];
        }
        if (dep[x] > dep[y]) swap(x, y);
        update(id[x], id[y], k, 1, n, 1);
    }
    void uson(int x, int k){
        update(id[x], id[x] + siz[x] - 1, k % p, 1, n, 1);
    }
}
{% endcodeblock %}