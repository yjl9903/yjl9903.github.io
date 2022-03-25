---
mathjax: true
title: Tarjan 强联通分量
date: 2018-10-10 21:36:51
tags:
- Graph
categories:
- 图论
- 连通性
---

# 模板

```c++
vector<int> edge[maxn], st;
int n, id, dfn[maxn], low[maxn], vis[maxn];
int cnt, bel[maxn], ind[maxn], oud[maxn];

void dfs(int u){
    dfn[u] = low[u] = ++id;
    vis[u] = 1; st.push_back(u);
    for (int i = 0; i < edge[u].size(); i++){
        int v = edge[u][i];
        if (!dfn[v]){
            dfs(v); low[u] = min(low[u], low[v]);
        }
        else if (vis[v]) low[u] = min(low[u], dfn[v]);
    }
    if (dfn[u] == low[u]){
        cnt++; int t = 0;
        do{
            t = st.back(); st.pop_back();
            bel[t] = cnt;
            vis[t] = 0;
        } while(t != u);
    }
}

void init(){ ms(dfn, 0); ms(vis, 0); st.clear(); cnt = id = 0; }
```