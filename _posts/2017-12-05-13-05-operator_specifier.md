---
layout: post
title: operator specifer,not just a name.
disqus: False
---

  When I was trying to write the following code:
```c++
#include <iostream>
template <class T>
struct Foo {
    T obj;
};
#define DEF_B_OP_TP(OP) \
template <class T1, class T2>\
auto operator##OP (const Foo<T1> &t1, const Foo<T2> &t2) noexcept -> Foo<decltype(t1.obj + t2.obj)>\
{ return {t1.obj + t2.obj}; }
DEF_B_OP_TP(+)
template <class Stream, class T>
Stream&& operator<<(Stream &&s, const Foo<T> &t) { return static_cast<Stream&&>(s << t.obj); }
int main() {
    std::cout << (Foo<int>{0} + Foo<int>{10}) << std::endl
    return 0;
}
```
After compiling with clang++-3.5 -std=c++14(same output when using clang++-5.0 -std=c++14),it gives the following messages from
clang:
> operator_specifer.cc:10:1: error: pasting formed 'operator+', an invalid preprocessing token
> DEF_B_OP_TP(+)
> ^
> operator_specifer.cc:8:14: note: expanded from macro 'DEF_B_OP_TP'
> auto operator##OP (const Foo<T1> &t1, const Foo<T2> &t2) noexcept -> Foo<decltype(t1.obj + t2.obj)>\
>              ^
> operator_specifer.cc:14:59: error: expected ';' after expression
>     std::cout << (Foo<int>{0} + Foo<int>{10}) << std::endl
>                                                           ^
>                                                           ;
> 2 errors generated.
which is quite confusing to me.
