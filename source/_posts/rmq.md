---
title: 倍增RMQ
date: 2018-09-21 16:14:39
tags:
- RMQ
- Data Structure
categories:
- 数据结构
---

# 询问最值

```c++
int dp[20][maxn];
void build(){
    for (int i = 1; i <= n; i++) dp[0][i] = s[i];
    for (int j = 1; j < 20; j++)
        for (int i = 1; i + (1 << j) <= n + 1; i++)
            dp[j][i] = max(dp[j - 1][i], dp[j - 1][i + (1 << (j - 1))]);
}
int rmq(int l, int r){
    // int k = 0; while ((1 << (k + 1)) <= r - l + 1) k++;
    int k = __lg(r - l + 1);
    return max(dp[k][l], dp[k][r - (1 << k) + 1]);
}
```

# 询问最值下标

```c++
int dp[20][maxn];
void build() {
    for (int i = 1; i <= n; i++) dp[0][i] = i;
    for (int j = 1; j < 20; j++)
        for (int i = 1; i + (1 << j) <= n + 1; i++) {
            int l = dp[j - 1][i];
            int r = dp[j - 1][i + (1 << (j - 1))];
            if (a[l] > a[r]) dp[j][i] = l;
            else dp[j][i] = r;
        }
}
int qmax(int l, int r) {
    int k = __lg(r - l + 1);
    int x = dp[k][l], y = dp[k][r - (1 << k) + 1];
    if (a[x] > a[y]) return x;
    else return y;
}
```