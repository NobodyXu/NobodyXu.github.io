---
layout: post
title: Redefinition of macro
disqus: True
---

When I was writing macros, I wondered whether I can overload macros. According to my C++ knowledge, you can't. But I still hope
I can do so, so I give it a try:

```c++
#include <iostream>
#define A()
#define A(ARG) std::cout << __PRETTY_FUNCTION__ << std::endl
int main() {
    A(1);
#define A 2
    std::cout << A << std::endl;
}
```

```
overload_macro.cc:3:9: warning: 'A' macro redefined [-Wmacro-redefined]
#define A(ARG) std::cout << __PRETTY_FUNCTION__ << std::endl
        ^
overload_macro.cc:2:9: note: previous definition is here
#define A()
        ^
overload_macro.cc:6:9: warning: 'A' macro redefined [-Wmacro-redefined]
#define A 2
        ^
overload_macro.cc:3:9: note: previous definition is here
#define A(ARG) std::cout << __PRETTY_FUNCTION__ << std::endl
        ^
2 warnings generated.
```

The output of this programme:

```
int main()
2
```

THis meant when I'm using others library, if my macro has the same name with the one defined in it, as long as I define the
<p>macro after the #include of these library, my macro will be the valid one.
