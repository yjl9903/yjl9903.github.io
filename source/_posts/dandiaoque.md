---
mathjax: true
title: 单调队列
date: 2018-11-27 21:25:05
tags:
- Data Structure
categories:
- 数据结构
---

# 滑动窗口求极值。

```c++
int l = 0, r = 0;
for (int i = 1; i < k; i++){
    while (l <= r && a[q[r]] <= a[i]) r--;
    q[++r] = i;
}
for (int i = k; i <= n; i++){
    while (l <= r && a[q[r]] <= a[i]) r--;
    q[++r] = i;
    while (i - q[l] >= k) l++;
    ans[i] = a[q[l]];
}
```

# 有长度限制的最大连续子序列和

单调队列维护前缀和的不减单调队列，队首元素为最小值标号，则此时的答案最大。

```c++
for (int i = 1; i <= n; i++) pre[i] = pre[i - 1] + a[i];
int l = 1, r = 0, ansl = 0, ansr = 0; ll ans = -inf;
for (int i = 1; i <= n; i++) {
    while (l <= r && pre[i - 1] < pre[q[r]]) r--;
    q[++r] = i - 1;
    while (i - q[l] > k) l++;
    if (pre[i] - pre[q[l]] > ans){
        ans = pre[i] - pre[q[l]];
        ansl = q[l] + 1; ansr = i;
    }
}
```

<!--more-->

为啥每次写单调队列都是该到死也不知道哪里挂了啊？？？