---
mathjax: true
title: 最小费用最大流
date: 2018-11-01 15:32:51
tags:
- Graph
- Network-Flow
categories:
- 图论
- 网络流
---

# 模板

```c++
const int maxn = 5000 + 5;
const int maxm = 100000 + 5;

int head[maxn], tot = 1;
struct edge{
    int to, nxt, flow, cost;
}g[maxm];
void add(int x, int y, int f, int c){
    g[++tot] = edge{y, head[x], f, c};
    head[x] = tot;
    g[++tot] = edge{x, head[y], 0, -c};
    head[y] = tot;
}
void init() {
    ms(head, 0); tot = 1;
}

int vis[maxn], cost[maxn], pre[maxn], flow[maxn], last[maxn], mf, mc;
bool spfa(int s, int t){
    ms(cost, 0x7f); ms(flow, 0x7f); ms(vis, 0);
    queue<int> q; q.push(s); vis[s] = 1; cost[s] = 0; pre[t] = -1;
    while (!q.empty()){
        int now = q.front(); q.pop(); vis[now] = 0;
        for (int i = head[now]; i; i = g[i].nxt){
            int v = g[i].to;
            if (g[i].flow > 0 && cost[v] > cost[now] + g[i].cost){
                cost[v] = cost[now] + g[i].cost;
                pre[v] = now; last[v] = i;
                flow[v] = min(flow[now], g[i].flow);
                if (!vis[v]){
                    vis[v] = 1; q.push(v);
                }
            }
        }
    }
    return pre[t] != -1;
}

int s, t;
void mcmf(){
    mc = mf = 0;
    while (spfa(s, t)){
        int now = t;
        mf += flow[t]; mc += flow[t] * cost[t];
        while (now != s){
            g[last[now]].flow -= flow[t];
            g[last[now] ^ 1].flow += flow[t];
            now = pre[now];
        }
    }
}
```

# dijkstra 优化

```c++
struct MCMF {
    struct edge {
        int to, flow, cost, rev;
        edge() { }
        edge(int to, int f, int cost, int r): to(to), flow(f), cost(cost), rev(r) { }
    };
    int V, H[maxn + 5], dis[maxn + 5], preV[maxn + 5], preE[maxn + 5];
    vector<edge> G[maxn + 5];
    void init(int n) {
        V = n;
        for (int i = 0; i <= V; ++i) G[i].clear();
    }
    void add(int from, int to, int cap, int cost) {
        G[from].push_back(edge(to, cap, cost, (int)G[to].size()));
        G[to].push_back(edge(from, 0, -cost, (int)G[from].size() - 1));
    }
    int getmin(int s, int t, int f, int& flow) {
        int ans = 0; 
        fill(H, H + 1 + V, 0);
        while (f) {
            priority_queue<PII,vector<PII>,greater<PII> > q;
            fill(dis, dis + 1 + V, inf);
            dis[s] = 0; q.push({0, s});
            while (!q.empty()) {
                PII now = q.top(); q.pop();
                int v = now.second;
                if (dis[v] < now.first) continue;
                for (int i = 0; i < G[v].size(); ++i) {
                    edge& e = G[v][i];
                    if (e.flow > 0 && dis[e.to] > dis[v] + e.cost + H[v] - H[e.to]) {
                        dis[e.to] = dis[v] + e.cost + H[v] - H[e.to];
                        preV[e.to] = v;
                        preE[e.to] = i;
                        q.push({dis[e.to], e.to});
                    }
                }
            }
            if (dis[t] == inf) break;
            for (int i = 0; i <= V; ++i) H[i] += dis[i];
            int d = f;
            for (int v = t; v != s; v = preV[v]) d = min(d, G[preV[v]][preE[v]].flow);
            f -= d; flow += d; ans += d * H[t];
            for (int v = t; v != s; v = preV[v]) {
                edge& e = G[preV[v]][preE[v]];
                e.flow -= d;
                G[v][e.rev].flow += d;
            }
        }
        return ans;
    }
} f;
```