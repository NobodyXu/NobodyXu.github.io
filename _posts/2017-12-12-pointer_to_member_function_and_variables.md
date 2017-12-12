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
3. When a virtual member function is stored in it, it actually holds 0x1, 0, which can't be the address of the function.
4. So I guess that, the it might be stored as if it's a member variable pointer, it holds the offset of it, not the absolute
   address. I defined a derived class from it and overload the virtual function, and what it holds is 0x0, 0, just as the Base
   class.
[The source file for testing the features above](https://nobodyxu.github.com/code)
