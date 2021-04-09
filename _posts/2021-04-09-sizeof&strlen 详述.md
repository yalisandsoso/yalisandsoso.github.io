---
bg: "tools.jpg"
layout: post
title:  C++ 语法
crawlertitle: C++ 语法
summary: Language
date:   2021-04-09
categories: C++
tags: ['Niu 04']
author: hekun
bg: "african-penguins.jpg"
---

 sizeof vs strlen。

## 用于字符串引发的bug

```cpp
char text[] = "abcdef";
size_t s1 = sizeof(text);   // is 6 
size_t s2 = strlen(text);   // is 7

const char* text2 = "abcdef";
size_t s3 = sizeof(text2); // is platform-dependent
size_t s4 = strlen(text2); // is 6
```

## sizeof() 是个什么瓜皮

> sizeof() is a unary operator or compiletime expression that calculates the amount of the memory occupied by a variables. Return unsigned int.

### 用法1(C)

```c
#include <stdio.h> // C language
#define my_sizeof(type) (char*)(&type+1)-(char*)(&type)
int main()
{
    double x;
    printf("%ld", my_sizeof(x));  // is 8
    getchar();
    return 0;
}
```

### 用法2

```c
sizeof(variable-name)
sizeof(expression)
sizeof(type)
```

## strlen()又是什么玩意

> strlen is a predefined function, which is defined in a header file known as string.h

### 用法1

```c
strlen(const char* str)
```
