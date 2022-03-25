---
title: debug宏定义
date: 2018-09-22 01:21:39
tags:
- other
categories:
- 乱七八糟
---

```c++
#ifdef XLor
  #define dbg(args...) do {cout << "debug -> "; err(args);} while (0)
#else
  #define dbg(...)
#endif
void err() {std::cout << std::endl;}
template<typename T, typename...Args>
void err(T a, Args...args){std::cout << a << ' '; err(args...);}
```