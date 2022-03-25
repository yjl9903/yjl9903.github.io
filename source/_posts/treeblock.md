---
mathjax: true
title: 树分块
date: 2019-05-27 18:57:33
tags:
- Tree
- Data Structure
categories:
- 树
---

# 王室联邦分块

```c++
int n, b;
vector<int> edge[maxn];

int stk[maxn], tot, bel[maxn], bcnt, key[maxn];
void dfs(int u, int f) {
    int bot = tot;
    for (int& v: edge[u]) {
        if (v == f) continue;
        dfs(v, u);
        if (tot - bot >= b) {
            bcnt++; key[bcnt] = u;
            while (tot != bot) {
                bel[stk[tot--]] = bcnt;
            }
        }
    }
    stk[++tot] = u;
} 

int main() {
    scanf("%d%d", &n, &b);
    for (int i = 2, u, v; i <= n; i++) {
        scanf("%d%d", &u, &v);
        edge[u].push_back(v);
        edge[v].push_back(u);
    }
    dfs(1, 0);
    while (tot) bel[stk[tot--]] = bcnt;
    printf("%d\n", bcnt);
    for (int i = 1; i <= n; i++) printf("%d%c", bel[i], " \n"[i == n]);
    for (int i = 1; i <= bcnt; i++) printf("%d%c", key[i], " \n"[i == bcnt]);
    return 0;
}
```

<!--more-->