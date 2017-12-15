---
layout: post
title: extern template
disqus: True
---

When you are compiling a header full of template in different places and they share the same instantation parameters, you'll waste
a lot of time compiling. Luckily, c++11 introduce "extern template" to solve this problem.
<p>First, you need to add the following code to every compilation unit to force the compiler to not instantiate the template.
```c++
//it's just like the specialization of a function but with no template paramter list
extern template ret_t optional classname:: func_name<specific_type>(parms ...);
```
<p>Then, you need to create a .cc file to store the instantation of these functions.
```c++
//some-name.cc
#include <the-template-header.h>//it can be included in other #include stype\
template ret_t optional classname:: func_name<specific_type>(parms ...);
```
Then use ```compiler-name -std=c++11 -c -probably-other-parms some-name.cc```, you will get the PID code of these templates, 
<p>and now you can use the samecommand (-probably-other-parms doesn't need to be the ssame) to compile other .cc files that use
<p>these templates and link them togther. Bingo, they work!
