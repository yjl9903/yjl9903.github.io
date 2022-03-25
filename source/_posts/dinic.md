---
title: Dinic算法
date: 2018-09-16 09:44:17
tags: 
- Graph
- Network-Flow
categories:
- 图论
- 网络流
---

```c++
struct Edge {
    int from, to; ll cap, flow;
    Edge(int u, int v, ll c, ll f): from(u), to(v), cap(c), flow(f) {}
};

struct Dinic {
    int n, m, s, t;
    vector<Edge> edges;
    vector<int> G[maxn];
    bool vis[maxn];
    int dep[maxn], cur[maxn];
    void init(int n) {
        this->n = n;
        for (int i = 0; i <= n; i++) G[i].clear();
        edges.clear();
    }
    void adde(int from, int to, ll cap) {
        edges.emplace_back(from, to, cap, 0);
        edges.emplace_back(to, from, 0, 0);
        m = edges.size();
        G[from].push_back(m - 2);
        G[to].push_back(m - 1);
    }
    bool bfs() {
        ms(vis, 0); ms(dep, 0);
        queue<int> q; q.push(s);
        dep[s] = 0; vis[s] = 1;
        while (!q.empty()) {
            int x = q.front(); q.pop();
            for (int i = 0; i < G[x].size(); i++) {
                Edge& e = edges[G[x][i]];
                if (!vis[e.to] && e.cap > e.flow) {
                    vis[e.to] = 1;
                    dep[e.to] = dep[x] + 1;
                    q.push(e.to);
                }
            }
        }
        return vis[t];
    }
    ll dfs(int x, ll a) {
        if (x == t || a == 0) return a;
        ll flow = 0, f;
        for (int& i = cur[x]; i < G[x].size(); i++) { 
            //从上次考虑的弧
            Edge& e = edges[G[x][i]];
            if (dep[x] + 1 == dep[e.to] && (f = dfs(e.to, min(a, e.cap - e.flow))) > 0) {
                e.flow += f;
                edges[G[x][i] ^ 1].flow -= f;
                flow += f;
                a -= f;
                if (a == 0) break;
            }
        }
        return flow;
    }
    ll get(int s, int t) {
        this->s = s, this->t = t;
        ll flow = 0;
        while (bfs()) {
            ms(cur, 0);
            flow += dfs(s, inf);
        }
        return flow;
    }
} f;
```

<!--more-->

```c++
int to[maxn * 2], nxt[maxn * 2], flow[maxn * 2], head[maxn], tot = 1;
void add(int x, int y, int w){
    to[++tot] = y; nxt[tot] = head[x]; flow[tot] = w;
    head[x] = tot;
    to[++tot] = x; nxt[tot] = head[y]; flow[tot] = 0;
    head[y] = tot;
}
void init() {
    ms(head, 0); tot = 1;
}

int dep[maxn], tag;
int bfs(int s, int t){
    queue<int> q;
    ms(dep, 0);
    dep[s] = 1; q.push(s);
    while (!q.empty()){
        int u = q.front(); q.pop();
        for (int i = head[u]; i; i = nxt[i]){
            int v = to[i];
            if (flow[i] && dep[v] == 0){
                dep[v] = dep[u] + 1;
                q.push(v);
            }
        }
    }
    return dep[t];
}
int dfs(int u, int fl){
    if (u == tag) return fl;
    int f = 0;
    for (int i = head[u]; i && fl; i = nxt[i]){
        int v = to[i];
        if (dep[v] == dep[u] + 1 && flow[i]){
            int x = dfs(v, min(fl, flow[i]));
            flow[i] -= x; flow[i ^ 1] += x;
            fl -= x; f += x;
        }
    }
    if (!f) dep[u] = -1;
    return f;
}
int dinic(int s, int t){
    int ans = 0; tag = t;
    while (bfs(s, t)){
        ans += dfs(s, inf);
    }
    return ans;
}
```

# 注意

+ 前向星存图时, 记得初始化 head 数组, tot 置为 1。

+ 加边的时候，记得存一个对应的反边。

