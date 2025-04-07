---
tags: programming,rust
---


# Polymorphism in Rust

Every language has polymorphism functions. Some utilize dynamic interfaces, some invent generics and typeclass to overload behavories.

Rust has *both*.

In Rust, we have type parameters and traits.
Rust really prefer generics over existential types. If one want to be `Debug`, just "generic" it:
```rust
fn use_debug<A: fmt::Debug>(a: A);
```

We also have *associated types*, *where clauses*, *impl types*, etc.
These all lift our type to polymorphism. However, there still exists two kinds of polymorphism which are somewhat hard to achieve in Rust now.

## Effect polymorphism
It's somewhat hard to explain what effect is because *side-effects* is nearly everywhere and we can't live without it!
Effects are everything except for function input/output. Think about errors, asynchrony, uncertainty. Think about it and try to guess what they correspond to in out daily programming.

Yes, they are `Result<_>`, `Future<Output=_>` and various kinds of containers. Now suppose you want to write some `fn` which performs some unknown effect:
```rust
fn func<F>(x: i32) -> F<i32>;
```

Congratulations! we discovers *Higher-Kinded Types(HKT)*. But you may also have noticed that this is not enough since `Future` is a trait and we have no way of abstract traits. Apparently Rust does not like *Monads*. In Rust, abstracting effects is really really hard right now. If you want to write one API for both sync and async codes, I just suggest you to write two separate traits.

## Ownership polymorphism
Another one is ownership, which is also ubiquitous in Rust.
Suppose you want write some AST like:

```rust
enum Expr {
    Var(String),
    Call { f: Expr, args: Vec<Expr> },
    Lambda { args: Vec<Expr>, body: Expr }
}
```

Apparently this does not compile since `Expr` is not `Sized`.
We have to add some ownership for every recurrent position. For example:
```rust
enum Expr {
    //...
    Call { f: Box<Expr>, /*..*/ }
    //...
}
```
However, `Box` has exclusive ownership, what about `Rc` or even `Arc`?
You can just abstract the whole recurrent type as:
```rust
enum Expr<This> {
    Call { f: This, args: Vec<This> }
}

type Exprs = Expr<Rc<Expr<???>>>
```

`Expr` is a fixpoint! This way we now have infinite type.

How about just abstract HKT?
```rust
pub trait Kind {
    type F<T>;
}

enum Expr<K: Kind> {
    Call { f: K::F<Self>, args: Vec<K::F<Self>> },
}
```

HKTs are really useful on abstracting `* -> *` types!

I believe in the future, we can have effect polymorphism with `Coroutine` one-shot algebraic effects and ownership polymorphism with *GAT* and *impl type* alias.