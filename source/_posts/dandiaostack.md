---
mathjax: true
title: 单调栈
date: 2019-05-09 15:18:23
tags:
- Data Structure
categories:
- 数据结构
---

# 模板

```c++
int main() {
    scanf("%d", &n);
    vector<int> st;
    ll ans = 0;
    for (int i = 1; i <= n; i++) {
        scanf("%d", h + i);
        while (!st.empty() && h[st.back()] > h[i]) {
            r[st.back()] = i - 1;
            st.pop_back();
        }
        st.push_back(i);
    }
    st.clear();
    for (int i = n; i >= 1; i--) {
        while (!st.empty() && h[st.back()] > h[i]) {
            l[st.back()] = i + 1; st.pop_back();
        }
        st.push_back(i);
    }
    for (int i = 1; i <= n; i++) {
        if (!r[i]) r[i] = n;
        if (!l[i]) l[i] = 1;
    }
    for (int i = 1; i <= n; i++) {
        ans = max(ans, 1ll * h[i] * (r[i] - l[i] + 1));
    }
    printf("%lld\n", ans);
    return 0;
}
```