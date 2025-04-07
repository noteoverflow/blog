---
extra:
    math: true
---

# Test markdown

> 这群在连云港过冬的蛎鹬本来依赖两个因放水而露出底部的鱼塘作为高潮栖息地，但是前段时间鱼塘又开始蓄水，让它们失去了合适的高潮地，潮水上涨时候只能像这样挤在鱼塘边的土质堤坝上，休息环境更差而且容易受人惊扰，实在是很可怜

## H2

### H3

- 1

1. 2

## font

testing: `code`

testing: *italic*

testing: **bold**

## mathjax

\\( \int x dx = \frac{x^2}{2} + C \\)

\\[ \mu = \frac{1}{N} \sum_{i=0} x_i \\]

## katex

testing: inline formula: $\int_a^b f(x) dx = F(b) - F(a)$

testing: block formula: $$f g = g f g$$

$$
\int_0^1x^4 \sqrt{\frac {1+x} {1-x}}dx
$$

$$
\lim f(x) - g(x) = g(x) (\frac {f} {g} - 1)
$$

## code

```cpp
#include __FILE__

template <typename T, class C, int R>
using T = C<R>;

static inline register void main(){
    auto f = main();
    int a = (long) (void*) &f;
    f(a);

    return 1;
}
```