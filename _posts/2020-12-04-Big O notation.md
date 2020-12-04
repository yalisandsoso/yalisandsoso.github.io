---
bg: "tools.jpg"
layout: post
title:  Big O notation
crawlertitle: algorithm asymptotic analysis
summary: Algorithms
date:   2020-12-04
categories: 算法
tags: ['Gengzi 12']
author: hekun
bg: "african-penguins.jpg"
---

Big O notation, also called Landau's symbol, is a symbolism used in complexity theory and so on.

## theorem

```mathematica
order: the rate of growth of function

e^x = 1 + x + x^2/2! + O(x^3)  for x->0
O(x^3)接近0，如果x无限接近0

<1>
define:
  f(x) = O(g(x)) for x->infinite
  
if:
   |f(x)| <= C*|g(x)|   for all x>N,exist C&N
   C is a constants
我们可以理解为 f(x)增长速度小于g(x)

<2>
f(x) = O(g(x))  for x->a  a是具体实数
Big O 可以写成表达式
|f(x)| <= C|g(x)| 
for all x with |x-a|<d,  d>0, exist C&d

比如 f(x) = O(g(x)), g(x) = 1+2x+x^2
那么 |f(x)| = O(g(x)) = x^2
exist C,N
有： |f(x)| <= C*|g(x)|,  for x>N

c is arbitrary constant!
notation    name  (slower first)
O(1)        constant
O(log(n))   logarithmic
O((log(n))^c) polylogarithmic    多项对数
O(n)         linear
O(n^2)       quadratic
O(n^c)       polynomial     多项式
O(c^n)       exponential    

O(n^2) < O(2^n) < O(n!)

Note, O(log(n)) = O(log(n^c))

100! = 9.332622e+157
2^100 = 1.2676506e+30
100^2 = 10000
```

```c
如果f(n)是上面函数之和，我们只考虑增长最快的term
e：math form
f(n) = 10*log(n) + 5(log(n))^3 + 7n + 3n^2 + 6*n^3 
then f(n) = O(n^3)

由上面<1>,<2>对Big-O的数学描述，可以推广到multiple variables
f(n,m) = n^2 + m^3 + O(n+m)
=》
exit C,N
f(n,m) <= n^2 + m^3 + C(n+m) for n>N,m>N
```

```c++
算法的性能指标
CPU(time) usage
memory usage
disk usage
network usage
```

```c++
How to Determine Complexities!!!
depends on what kinds of statements are used.
意思就系话 time complexities的计算多决定于你的算法代码结构

以下讨论常用结构的time complexities分析
    
1. Sequence of statements
    statement 1;
    statement 2;
    statement 3;
    ...
    statement k;
total t = t(s1) + t(s2) + ... + t(sk)
    if each statement is "simple" then the time for each statement is constant
        and the total time is also constant: O(1)
```



# code
## support

 