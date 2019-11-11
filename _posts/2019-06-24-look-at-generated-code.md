---
layout: post
title: Be careful with compiler optimizations
---

{{ page.title }}
================

<p class="meta">06 Jun 2019</p>

Why should one be careful when using compiler optimizations?

Just look at the below snippet. Does it print negative numbers? Anybody with basic understanding of computer programming can argue that it doesn't print negative numbers because number gets printed on standard output only if `t >= 0` 
```cpp
#include <bits/stdc++.h>

const int d = 1e9;

int main() {
    int t = 10;
    for (; t >= 0; t++) {
        if (t%d == 0) {
            if (t >= 0) {
                std::cout << t << std::endl;
            }
        }
    }
    return 0;
}
```
But things go crazy when the above code is compiled with optimization flag set i.e g++ -O2 tmp.cpp. There are many flags that are used for optimization other than O2, more at GCC Documentation.

Here is how compiler tries to optimize the above snippet, t is set to positive integer and it is only increasing. Given that infomation compiler can safely assume that t will always be positive integer and removes the redundant checks t >= 0. Now the generated code doesn't have the check whether t is non negative number. Not only does it print the negative numbers because of integer overflow but also it gets into infinite loop. Undefined behaviour in C, C++ languages can be very frustrating in situations like this. 