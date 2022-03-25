---
mathjax: true
title: 字符串最小表示法
date: 2018-11-01 16:41:44
tags:
- String
categories:
- 字符串
---

# 模板

```c++
int getmin(char* s){
    int n = strlen(s), i = 0, j = 1, k = 0;
    while (i < n && j < n && k < n){
        int t = s[(i + k) % n] - s[(j + k) % n];
        if (!t) k++;
        else {
            if (t > 0) i += k + 1;
            else j += k + 1;
            if (i == j) j++;
            k = 0;
        }
    }
    return min(i, j) + 1;
}
```
