---
layout: post
title: ## cannot concat anything
disqus: True
---
related post: [operator_specifier, not just a name](https://nobodyxu.github.io/operator_specifier/), this post is about a question 
raised from it.

<p>From [Why string concat macro doesn't work for this “+” case?](https://stackoverflow.com/questions/25072193/why-string-concat-macro-doesnt-work-for-this-case), I learnt that the preprocessing operator `##` can only produce valid preprocessing tokens from other ones.
> The following tokens are considered as valid preprocessing tokens(N3797):

>     header-name

>     identifier

>     pp-number

>     character-literal

>     user-defined-character-literal

>     string-literal

>     user-defined-string-literal

>     preprocessing-op-or-punc

>     each non-white-space character that cannot be one of the above

(The quote above is from [Why string concat macro doesn't work for this “+” case?](https://stackoverflow.com/questions/25072193/why-string-concat-macro-doesnt-work-for-this-case))
<p>  The reason that `#define DEF(OP) #operator##OP` can't work is because operator and OP(which can be +, - ...) are 2 different
tokens, so thye can't be concatenated into one valid preprocessing token.
