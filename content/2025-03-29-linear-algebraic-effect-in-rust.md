---
tags: rust,programming,effect-system
---

# Linear algebraic effects in Rust

Recently I successfully implement *Linear Algebraic Effect(Linear AE)* in Rust!
You may ask, what exactly is *Linear AE*?
Well, honestly, the word 'linear' is used by myself to mean *one-shot*, which implies the effect cannot be cloned casually. *AE* is a special kind of structure like "exceptions can return".
I'm not going to explain *AE* in detail in this post. Let's jump into rust's implementation!

## The experimental `Coroutine` trait
Yes rust already has a trait called `Coroutine` whose definition is:

```rust
pub trait Coroutine<R = ()> {
    type Yield;
    type Return;

    fn resume(self: Pin<&mut Self>, arg: R) -> CoroutineState<Self::Yield, Self::Return>;
}
```

The associated type `Coroutine::Yield` means the yielded type of the coroutine and the type `Coroutine::Return` refers to the type of returned value. For example:
```rust
let generator = #[coroutine] || {
    for i in 0..10 {
        yield i;
    }
}
```
The generated coroutine `generator` acts like a simple iterator which *yields* integers from 0 to 10.
Therefore the `Yield` type of `generator` is `i32` and type `Return` is `()`.

Now let's focus on the method `Corouine::resume`.
`resume` takes a *pinned self* since the coroutine itself may contain self-referential pointers just like those in `Future`. Every time one resume with a parameter, the coroutine returns a `CoroutineState` to indicate whether it should return or yield. For instance:

```rust
let mut coroutine = #[coroutine] || {
    yield 1;
    "foo"
};

match Pin::new(&mut coroutine).resume(()) {
    CoroutineState::Yielded(1) => {}
    _ => panic!("unexpected return from resume"),
}
match Pin::new(&mut coroutine).resume(()) {
    CoroutineState::Complete("foo") => {}
    _ => panic!("unexpected return from resume"),
}
```

## `Yield` as effects
Since rust's compiler can automatically transform our coroutine into a stackless state machine, we can directly utilize it's `Yield` type to implement effects!

Consider a common *logging* effect, let's define it as a common data strucutre:
```rust
struct Log(String);
```

The we can create a coroutine to yield `Log`:
```rust
let may_log = #[coroutine] |i: usize| {
    if i == 0 {
        yield Log("i == 0".to_string());
    }
}
```

If you try to write `may_log`'s type, You will find that the `Yield` type can indicate that, while executing the coroutine, it may yield *logging*:
```rust
may_log: usize -> () yields Log
```
Notably, `yield` is a expression which can return value which will be filled as parameters while resuming.

Let's abstract this pattern a little.
Firstly, we can model effects as various kinds of data such as
```rust
enum Effect {
    Log(String),
    State(State),
    Async,
    Injection,
    //...
}
```

Then we write our program logic as a coroutine which yields its requiring effect:
```rust
#[coroutine] |arg: I| -> R {
    let a = yield Effect::Log("a".to_string());
    let b = yield Effect::State(State::Get);
    yield Effect::State(State::Set(0));
    let c = yield Effect::Async,
    //...
}
```

Finally, we write a data structure to resume the coroutine until it returns.


## Composing effectful programs
We can now create effectful programs. But how can we compose them?
Coroutines are not like normal functions, they will not return until it yields all effects.
If we write:
```rust
let a = coroutine(arg);
```
We are expecting `a` as the returned result of calling `coroutine`.
Therefore we need to comsume all its effects:
```rust
let a = {
    let mut pinned = pin!(coroutine);
    let mut arg = arg;
    loop {
        let res = pinned.as_mut().resume(arg);
        match res {
            CoroutineState::Yielded(eff) => {
                arg = yield eff;
            }
            CoroutineState::Complete(v) => break v,
        }
    }
}
```
This is also a common pattern, so let's write a macro:
```rust
macro_rules! run {
    ($f:expr, $arg:expr) => {{
        let mut pinned = pin!($f);
        let mut arg = $arg;
        loop {
            let res = pinned.as_mut().resume(arg);
            match res {
                CoroutineState::Yielded(eff) => {
                    arg = yield eff;
                }
                CoroutineState::Complete(v) => break v,
            }
        }
    }};
}
```
Now we can simply write to compose effectful programs:
```rust
let a = run!(coroutine, arg);
```

## Epilogue
This a demo implementation of algebraic effect in Rust.
There is a better version with *Handlers* in [crates.io/algoroutine](https://crates.io/crates/algoroutine).