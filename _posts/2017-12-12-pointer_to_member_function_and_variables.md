---
layout: post
title: pointer to member functions and variables
disqus: True
---
#pointer to member functions
##memory layout
  The memory layout of member function pointers is actually a bit different from an ordinary pointer. The size of it is twice 
as an ordinary function pointer. 
  And what it stores is quite different, too. When I store a normal member function in it, the first 8 bytes(a 64-bit machine) 
seems to be the address of that member function. 
  But when I store a virtual member function in it, things begin to be quite confusing for actually holds 0x1, 0, 
it can't be the address of the function. So I guess that, the it might be stored as if it's a member variable pointer, it holds 
the offset of it, not the absolute address.
[The source file for testing the features above](https://nobodyxu.github.com/code)
