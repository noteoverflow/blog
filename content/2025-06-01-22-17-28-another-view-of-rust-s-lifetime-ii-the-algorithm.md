---
tags: rust,programming
extra:
    math: true
---
# Another view of Rust's lifetime II - The algorithm

Previously, I described another view of Rust's lifetime which is dual to the traditional one in
> [Another view of Rust's lifetime](another-view-of-Rusts-lifetime.md)

Today, let's try to make it a concrete algorithm demo based on [Polonius](https://rust-lang.github.io/polonius/).
To formally and clearly describe the core rules of the algorithm, I want to use a special DSL which is drived from Datalog:


## Meta-language
The lanugage we use to describe rules has only 2 basic components:

### Atomic entity
If a entity derives itself, we call it atomic entity (usually they are the input of the algorithm). For instance:
```
reachable (a : A) (b : B)
```
We can also view this entity as a type:
```
reachable : A -> B -> Input
```
We use a spectial kind `Input` to imply that the entity is atomic.

### Derived entity
This kind of entities can be derived from others (even recursively!):
```
path : P -> P -> Type

trans : path a b -> path b c -> path a c
```
Here `trans` serves as a deriving rule.


## Materials
Before demonstrating the whole algorithm, we firstly prepare some materials to cook.

### $G$ the CFG
As in the traditional algorithm, we need the Control Flow Graph(CFG), denoted $G$, to help us trace program and memory flow.

The CFG comprises set of MIR Point, denoted $P$ and control flow edges between points $E$.
To get the source and destination of one edge, we have two functions:
```
source : E -> P
dest : E -> P
```
Since we are working on the meta-language, we say edge `e` exists by:
```
edge : (source e) -> (dest e) -> Input
```
Normally, we denote one edge as `edge a b` by its end points.

### $R$ the Regions
Region is the core concept of the borrow checker.
Then what do we mean by a **region**?
Let's consider how to assemble references and memory locations in Rust!
Basically, we use product type, or, if you like, `struct`s:
```rust
struct Region<'a, 'b> {
    a: &'a A,
    b: &'b B,
}
```
Similarly, We just define regions as products of references. 
However, as we want to be more specific here, we call referred references **loans**:

> Regions are sets of loans.

Moreover, since regions can be viewed as sets, we then have subset relations which is explored in the next section.

### $L$ the Loans
In the previous part, we used loans to define regions.
Loans can be viewed as any borrow sites.

For example:
```rust
let x = vec![1, 2];

let p: &'a i32 = if random() {
    &x[0] // Loan L0
} else {
    &x[1] // Loan L1
};
```
Here, the region 'a would correspond to the set {L0, L1}, since it may refer to data produced by the loan L0, but it may also refer to data from the loan L1.


## Relations

### Injection
We mentioned that regions can form subset relations.
But we can say more general here by defining relation structures between regions at the same point.

```
in : P -> R -> R -> Type
```
To simplify the notation a little, we denote `a in b at p` just as `in p a b`.

```
trans       : a in b at p -> b in c at p -> a in c at p
follow_edge : a in b at p -> edge p q -> a in b at q
```

Also, we have some input injection relations:
```
base_in : P -> R -> R -> Input
```

and one more rule:
```
atom : base_in p a b -> a in b at p
```


### Liveness
a region `'a` is live at some point P if some reference with type `&'a i32` may be dereferenced later:
```
region_live_at : R -> P -> Input
```

We also talk about liveness of loans:
```
loan_live_at : L -> P -> Type

derive_loan_live : region_live_at r p -> require r l p -> loan_live_at l p
```
What's `require` here?


### Requirement
`require R L P` means:

> If the terms of the loan L are violated at the point P, then the region R is invalidated.

Formally:
```
require : R -> L -> P -> Type
```

and rules:
```
from_loan     : loan r l p -> require r l p
follow_subset : require r' l p -> r' in r at p -> require r l p
follow_cfg    : require r l p -> edge p q -> require r l q
```

### Invalidation
And now finally we can define what a borrow check error is. 
Before that, We need to define an input invalidates(P, L), 
which indicates that some access or action at the point P invalidates the terms of the loan L:

```
invalidate : P -> L -> Input
```

### Errors
Finally

```
error : P -> Type

detect_error : exists l. invalidate p l -> loan_live_at l p -> error p 
```

## Refinement
The core algorithm is rather coarse.
We need refinements to handle reassignment and reborrow problems.