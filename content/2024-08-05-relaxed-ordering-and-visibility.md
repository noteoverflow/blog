---
date: 2024-08-05
tags: programming,cpp
---

# Relaxed ordering and visibility

I'm really curious that whether the *relaxed* memory ordering can ensure to see
the last recent value in the total modification order. It appears that the C++ memory model alone is not enough.

The C++ memory model only states that

> Implementations should make atomic stores visible to atomic loads within a reasonable amount of time.

which means in the code
```c++
std::atomic<int> g{0};

// thread 1
g.store(42);

// thread 2
int a = g.load();
// do stuff with a
int b = g.load();
```
if thread 1 has executed the storing, thread 2 is not guaranteed to load 42 immediately.

However, C++ standard does ensure the visibility of **RMW(Read-Modify-Write)** operations as the standard says:

> Atomic read-modify-write operations shall always read the last value (in the modification order) written before the write associated with the read-modify-write operation.

which means you don't need to worry about operations like `fetch_xx` and `compare_exchange`.