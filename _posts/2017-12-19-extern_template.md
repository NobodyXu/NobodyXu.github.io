---
layout: post
title: extern template
disqus: True
---

When you are compiling a header full of template in different places and they share the same instantation parameters, you'll 
waste a lot of time compiling them. Luckily, c++11 introduce "extern template" to solve this problem.
<p>First, you need to add the following code to every compilation unit to force the compiler to not instantiate the template 
  immediately.
  
```c++
//it's just like the specialization of a function but with no template paramter list
extern template ret_t optional classname:: func_name<specific_type>(parms ...);
```

Then, you need to create a .cc file to store the instantation of these functions.
  
```c++
//some-name.cc
#include <the-template-header.h>//it can be included in other #include stype\
template ret_t optional classname:: func_name<specific_type>(parms ...);
```

Then use ```compiler-name -std=c++11 -c -probably-other-parms some-name.cc``` , you will get the PID code of these templates, 
<p>and now you can use the samecommand (-probably-other-parms doesn't need to be the ssame) to compile other .cc files that use
<p>these templates and link them togther. Bingo, they work!
  
But how about writing a template header and then put some rather frequent used instantiation into a .cc file?
<p>I write the following test code to demonstrate how to use it:
  
```c++
//extern_template.h
template <class A>
struct wrapper {
    A object;
    A& getA() noexcept { return object; }
};
extern template int& wrapper<int>::getA() noexcept;

template <class T> void print(const T &val) { std::cout << val << std::endl; }
extern template void print<int>(const int&);
```

```c++
//extern_template.cc
#include "extern_template.h"
template int& wrapper<int>::getA() noexcept;
template void print<int>(const int&);
```

```c++
//main.cc
#include "extern_template.h"
int main() {
    wrapper<int> A{1};
    std::cout << A.getA() << wrapper<char>{'s'}.getA() << std::endl;
    print(int{10});
}
```

```Assembly
Disassembly of section .text._ZN7wrapperIiE4getAEv:                                                                                                                                                                                                                            
                                                                                                                                                                                                                                                                               
0000000000000000 <_ZN7wrapperIiE4getAEv>:                                                                                                                                                                                                                                      
   0:   55                      push   %rbp                                                                                                                                                                                                                                    
   1:   48 89 e5                mov    %rsp,%rbp                                                                                                                                                                                                                               
   4:   48 89 7d f8             mov    %rdi,-0x8(%rbp)                                                                                                                                                                                                                         
   8:   48 8b 45 f8             mov    -0x8(%rbp),%rax                                                                                                                                                                                                                         
   c:   5d                      pop    %rbp                                                                                                                                                                                                                                    
   d:   c3                      retq                                                                                                                                                                                                                                           
                                                                                                                                                                                                                                                                               
Disassembly of section .text._Z5printIiEvRKT_:                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                                               
0000000000000000 <_Z5printIiEvRKT_>:                                                                                                                                                                                                                                           
   0:   55                      push   %rbp
   1:   48 89 e5                mov    %rsp,%rbp
   4:   48 83 ec 10             sub    $0x10,%rsp
   8:   48 b8 00 00 00 00 00    movabs $0x0,%rax
   f:   00 00 00 
  12:   48 89 7d f8             mov    %rdi,-0x8(%rbp)
  16:   48 8b 7d f8             mov    -0x8(%rbp),%rdi
  1a:   8b 37                   mov    (%rdi),%esi
  1c:   48 89 c7                mov    %rax,%rdi
  1f:   e8 00 00 00 00          callq  24 <_Z5printIiEvRKT_+0x24>
  24:   48 be 00 00 00 00 00    movabs $0x0,%rsi
  2b:   00 00 00 
  2e:   48 89 c7                mov    %rax,%rdi
  31:   e8 00 00 00 00          callq  36 <_Z5printIiEvRKT_+0x36>
  36:   48 89 45 f0             mov    %rax,-0x10(%rbp)
  3a:   48 83 c4 10             add    $0x10,%rsp
  3e:   5d                      pop    %rbp
  3f:   c3                      retq   

```

It works!
And the a.out compiled from main.cc can output:
```
1s
10
```
