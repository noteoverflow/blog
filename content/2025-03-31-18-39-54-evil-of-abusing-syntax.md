---
tags: math,plt
extra:
    math: true
---
# The evil of abusing syntax

It took me about one week until I realized that I misunderstood the meaning of the expression syntax in the paper.
Operator priorities are rediculous!

Let's have a look at the formal expression:
$$
?x:a \rightarrow b.x\ e
$$

At first look, my eyes parsed the expression as:
$$
(?x:a) \rightarrow (b.x) e
$$
which is totally WRONG!
The correct grouping is
$$
(?x:(a \rightarrow b).x)\ e
$$

I really appreciate the great work of the author, but
> *Please use PARENTHESIS when necessary!*