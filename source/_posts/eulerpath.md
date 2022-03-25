---
title: 欧拉回路
date: 2018-09-27 18:44:29
tags:
- Graph
categories:
- 图论
---

# 判断条件

## 无向图

因为欧拉路径中，起点与终点度数为奇数，其它点的度数均为偶数。

如果是欧拉回路，奇点的个数应该为 0。

## 有向图

欧拉路径中，最多只有两个点的入度不等于出度。起点出度比入度大 1，终点入度比出度大 1。

如果是欧拉回路，所有点的 入度 = 出度 。

# Hierholzer算法

```c++
void dfs(int u){
    for (int i = 0; i < edge[u].size(); i++){
        int v = edge[u][i];
        if (vis[u][i]) continue;
        vis[u][i] = 1;
        dfs(v);
    }
    ans.push_back(u);
}
```

<!--more-->

ans 逆序存放着遍历的答案。

> 参考blog: https://www.cnblogs.com/acxblog/p/7390301.html