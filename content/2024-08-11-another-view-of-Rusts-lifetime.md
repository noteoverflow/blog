---
tags: programming,rust
slug: another-view-of-rusts-lifetime
---

# Another view of Rust's lifetime

Compared with *Haskell*, Rust is different for its effect system and ownership system.
Inside ownership system, lifetime plays an important role on borrowing and safety.
Traditionally, people think about lifetime as some region of code, which is a little kind of vague.
Why not try to see lifetime as a kind of memory(dependency) reference?

## Lifetime as scope?
*The Rust book* suggests viewing lifetime as scopes. Consider a simple example:

```rust
fn main() {
    let r;                // ---------+-- 'a
                          //          |
    {                     //          |
        let x = 5;        // -+-- 'b  |
        r = &x;           //  |       |
    }                     // -+       |
                          //          |
    println!("r: {r}");   //          |
}                         // ---------+
```

Since `x`'s lifetime `'b` is shorter than `r`'s `'a`, we cannot assign `&x` to `r`.
However, this reason is rather imprecise.
If we simply remove the last `println!` line, this code just compile fine.:

```rust
fn main() {
    let r;                // ---------+-- 'a
                          //          |
    {                     //          |
        let x = 5;        // -+-- 'b  |
        r = &x;           //  |       |
    }                     // -+       |
}                         // ---------+
```

Why? 

Even worse, lifetime can contain *holes*, where it’s intermittently invalid between where it starts and where it ultimately ends. For example, let's change our previous code simply:

```rust
fn main() {
    let i = 42;
    let mut r;                
    {
        let x = 5;        
        r = &x;            // -------------+-- 'r
                           //              |
    }                      // -------------+

    r = &i;                // -------------+-- 'r
    println!("r: {}", *r); //              |
}                          // -------------+
```

This time the code compiles. Surprise!
Why? why this code compiles fine?? If r's lifetime coprresponds to `x`'s reference, why `r` can br printed outside `x`'s scope now? There may exist holes in lifetime.

Apparently *scope* view is not correct. We need something better.

## Lifetime as regions of code?
*Rustonomicon* the book suggests viewing lifetime as named regions of code.
Now our previous problems are solved, since `'r` spans only its valid data flow regions and the regions can contains arbitrary holes.

> Lifetime is named regions of code where the pointed data is valid.

What about lifetime constraints like `'a: 'b`?

## Lifetime subtyping
Subtyping is really just a spectial relation just as other *trait*s can represent.
The only subtyping in rust is lifetime subtyping.

> if `'b` outlives `'a`, then `'b: 'a` and `&'b T` is a subtype of `&'a T`.

Since `&'b T` is a subtype of `&'a T`, we can assign any `&'b T` to `&'a T`.
For example(again from *the rust book*):

```rust
fn longer<'a>(s1: &'a str, s2: &'a str) -> &'a str {
    if s1.len() > s2.len() {
        s1
    } else {
        s2
    }
}
```

Actually, this code cannot compile without lifetime subtyping. It's equivalent to this code:

```rust
fn longer<'a, 'b, 'c>(s1: &'a str, s2: &'b str) -> &'c str 
where
    'a: 'c,
    'b: 'c,
{
    if s1.len() > s2.len() {
        s1
    } else {
        s2
    }
}
```

Automatic subtyping is only a fancy and convenient way to expression this relationship.

## Lifetime as memory references
If you know *category theory*, you may heard of *duality*.
Just like *category theory*, the code region view also has its *DUAL* view:

> A lifetime refers to some memory or other resources and
> constraint `'a: 'b` can also be read `'a` refers to a subset of `'b` or, if you like, `'a` in `'b`.

In this dual view, if `'a` outlives `'b`, `'b` contains more resources than `'a` does.
I find this view is somehow complement to the region view.

Let's demonstrate this view with the following code:

```rust
fn use_longer() {
    let a: &str = "a";     // 'a
    let b: &str = "b";     // 'b
    let c: &str = "c";     // 'c

    let d = longer(a, b);  // 'd
    let d = longer(c, d);  // 'e
}
```

```
'e -> {
    'd -> {
        'a,
        'b,
    },
    'c,
}
```

where arrow `->` means point to some resource and `{}` means union.
From this graph, we can easily conclude:

```
'a: 'd
'b: 'd

'd: 'e
'c: 'e

'a: 'e
'b: 'e
```