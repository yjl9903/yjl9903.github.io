---
mathjax: true
title: Manacher 算法
date: 2018-10-22 14:23:50
tags:
- String
- Palindrome
categories:
- 字符串
- 回文串
---

# 模板

```c++
namespace manacher{
    char s[maxn << 1] = "##";
    int n, hw[maxn << 1];
    void init(){
        int len = strlen(a);
        for (int i = 0; i < len; i++){
            s[i * 2 + 2] = a[i];
            s[i * 2 + 3] = '#';
        }
        n = len * 2 + 2; s[n] = 0;
    }
    void manacher(){
        int maxr = 0, m = 0;
        for (int i = 1; i < n; i++){
            if (i < maxr) hw[i] = min(hw[m * 2 - i], hw[m] + m - i);
            else hw[i] = 1;
            while (s[i + hw[i]] == s[i - hw[i]]) hw[i]++;
            if (hw[i] + i > maxr){
                m = i; maxr = hw[i] + i;
            }
        }
    }
    int getMax(){
        init(); manacher(); int ans = 1;
        for (int i = 0; i < n; i++) ans = max(ans, hw[i]);
        return ans - 1;
    }
}
```

<!--more-->

# 理解

$hw[i]=\min(hw[m \times 2 - i],hw[m]+m-i)$.

关于最长回文串的对称位置处回文串长度，是否在最长的回文串当中，决定有多少重复信息可以利用。