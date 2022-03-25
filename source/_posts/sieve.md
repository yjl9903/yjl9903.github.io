---
title: 线性筛法
date: 2018-09-22 01:13:21
tags:
- Number Theory
categories:
- 数论
---

```c++
namespace sieve{
    const int maxp = 1000000 + 5;
    int vis[maxp], prime[maxp], tot;
    void init(){
        ms(vis, 0);
        for (int i = 2; i < maxp; i++){
            if (!vis[i]) prime[tot++] = i;
            for (int j = 0; j < tot && prime[j] * i < maxp; j++){
                vis[i * prime[j]] = 1;
                if (i % prime[j] == 0) break;
            }
        }
    }
}
```