---
title: Prim
date: 2018-08-18 11:46:53
tags: [Graph]
categories: 图论
comments: false
---

{% codeblock lang:cpp %}
struct edge{
    int to, nxt, d;
    bool operator<(const edge& b)const {
        return d > b.d;
    }
}f[20 * maxn];
int head[maxn], tot = 0;
void add(int x, int y, int d){
    f[++tot].to = y; f[tot].d = d; f[tot].nxt = head[x]; head[x] = tot;
}

int p, r, vis[maxn];
int prim(){
    ms(vis, 0); vis[1] = 1;
    priority_queue<edge> q;
    int ans = 0;
    for (int i = head[1]; i; i = f[i].nxt){
        q.push(f[i]);
    }
    while (!q.empty()){
        edge t = q.top(); q.pop();
        if (vis[t.to]) continue;
        vis[t.to] = 1;
        ans += t.d;
        for (int i = head[t.to]; i; i = f[i].nxt){
            q.push(f[i]);
        }
    }
    return ans;
}
{% endcodeblock %}