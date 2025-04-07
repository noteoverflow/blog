---
date: 2024-08-05
tags: programming,golang
---

# Golang is terrible as a general purpose language

Golang is terrible at several things:

1. Try to mix up *defaults*, *empty value*, *empty slice*, *null pointer* and *optional value*.
Function callers have to check all these things to avoid **PANIC**!
Also comparing nil values with poor type inference is really dirty.

2. Try to mix up sum type and product type.
Golang likes to simulate sum type with product types (e.g., return extra error), which is error-prone for several things:
  - Cannot ensure exclusive variant. You may receive both a return value and non-nil err.
  - You may also try to simulate sum type with interfaces, but interface is implicit and not sealed.

3. Try to mix up dynamic and compile-time polymorphism.
Many APIs have to use `interface{}` and methods on receivers cannot have generic parameters.

4. Try to mix up *SHOULD implement* and *HAPPEN TO implement*.
Due to poor expressiveness of golang's type system, you may happens to implement some interfaces with wrong semantic.

5. Try to mix up directory hierarchy and module system.
You have to `mkdir` to create inner modules.

Golang pros:

1. Fast development with bugs.
2. Fast compiling time with little static check.