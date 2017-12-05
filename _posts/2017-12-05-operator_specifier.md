---
layout: post
title: operator specifer, not just a name.
disqus: True
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
    std::cout << (Foo<int>{0} + Foo<int>{10}) << std::endl;
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
> 1 errors generated.
which is quite confusing to me.
So I do some searching on Stackoverflow, and I found this [Why string concat macro doesn't work for this “+” case?](https://stackoverflow.com/questions/25072193/why-string-concat-macro-doesnt-work-for-this-case), it mainly tells me 2things: 
  1. operator is not a name when defining or declaraing a function, it is a specifier.
     So, instead of writing `operator+`, we should write `operator +`.
     And that explain why I can write `operator Type`, `operator auto`, `template <class T> operatpr T` and sth. like:
     ```c++
     template <class T>
     void destroy_at(const T *p) { p->~T(); }
     ``` 
     and
     ```c++
     #include <iostream>
     #define OUTPUT() std::cout << __PRETTY_FUNCTION__ << std::endl
     struct Foo {
         ~ Foo() { OUTPUT(); }
     };
     template <class T>
     void destroy_at(const T *p) { p-> ~ T(); }
     int main() {
         Foo *p = new Foo;
         destroy_at(p);//Foo::~Foo()
         return 0;
     }
     ```
     So here I should replace the macro with the following code:
     ```c++
     #define DEF_B_OP_TP(OP) \
     template <class T1, class T2>\
     auto operator OP (const Foo<T1> &t1, const Foo<T2> &t2) noexcept -> Foo<decltype(t1.obj + t2.obj)>\
     { return {t1.obj + t2.obj}; }
     ```
     And it works.
  2. [## cannot concat anything](https://nobodyxu.github.io/cannot-concat-anything/)
