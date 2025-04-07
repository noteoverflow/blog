---
tags: programming,golang
---

# Golang does have sum types, however...

> Golang has no sum type!

People like to say that golang lacks *sum types*.
For example, in Rust we can write a simple `Result` type:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

Here `Result` is a sum type which can only be one variant(`Ok` or `Err`) but not both.
Sum types are really useful to constraint values. In category theory, sum type is dual to product type, which is a universal type. If product types can be written as `a * b`, then sum types can be written as `a + b`.

Golang lacks direct support on sum types, but we can still simulate it:

```go
type result[T, E any] interface {
    isResult()
}

type Result[T, E any] struct {
    inner result[T, E]
}


type Ok[T, E any] struct {
    value T
}
type Err[T, E any] struct {
    err E
}

func (Ok[T, E]) isResult() {}
func (Err[T, E]) isResult() {}

func (res Result[T, E]) Switch() result[T, E] {
    return res.inner
}
```

To match variants:

```golang
func matching[T, E any](res Result[T, E]) {
    switch r := res.Switch().(type) {
    case Ok[T, E]:
        _ = r.value
    case Err[T, E]:
    default:
    }
}
```

However, since golang doest not have generic methods, its function is still limited(golang team is far too conservative)...

Although we can simulate `Result` sum type in Golang, we are still forced to use (T, error) and write `if err != nil` lol. What a great language!