---
layout: post
title: pointer to member functions and variables
disqus: True
---

# pointer to member functions
## memory layout
The memory layout of member function pointers is actually a bit different from an ordinary pointer :
1. The size of it is twice as an ordinary function pointer. 
2. And what it stores is quite different, too. When I store a normal member function in it, the first 8 bytes(a 64-bit machine) 
seems to be the address of that member function. 
3. - When a virtual member function is stored in it, it actually holds 0x1, 0, which can't be the address of the function.
   -  So I guess that, the it might be stored as if it's a member variable pointer, it holds the offset of it, not the absolute
   - address. I defined a derived class from it and overload the virtual function, and what the pointer of it holds 0x0, 0.
### The source file for testing the features above
```c++
#include <iostream>

struct Foo {
    int i;
    void A() {}
    virtual void B() {}
};
struct DerivedFoo: Foo {
    void B() {}
};

template <class T>
using memFuncPtr = void (T::*)();
using FooFuncPtr = memFuncPtr<Foo>;
using DerivedFooFuncPtr = memFuncPtr<DerivedFoo>;

template <class T>
union Arg {
    memFuncPtr<T> funcptr;
    void *ptr[2];
};

template <class Stream>
Stream& operator << (Stream &s, void* x[2]) { return (s << x[0] << ", " << x[1]); }
template <class Stream, class T>
Stream& operator << (Stream &s, const memFuncPtr<T> &funcptr) { return (s << Arg<T>{.funcptr = funcptr}.ptr); }
 
int main() {
    FooFuncPtr A{&Foo::A};
    //The next line output 2 on my machine, and if that's not your case, I welcome anyone to dicuss it with me.
    std::cout << sizeof(A) / sizeof(char*) << std::endl;
    //Next, I try to figure out what is exactly stored in it.
    //output:0x2011e0, 0
    //I found 0x2011e0 is actually the address of Foo::A in my assembly code dumped by objdump -d.
    std::cout << A << std::endl;
    
    FooFuncPtr B{&Foo::B};
    std::cout << B << std::endl;//0x1, 0, I guess this is a offset rather an absolute address, so I write class DerivedFoo
    
    DerivedFooFuncPtr derivedB{&DerivedFoo::B};
    std::cout << derivedB << std::endl;//gives 0x1, 0, same as Foo::B
}
```
