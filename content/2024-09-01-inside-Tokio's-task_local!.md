---
tags: programming,rust
---

# Inside Tokio's task_local!

Recently I'm curious about Tokio runtime's `task_local` and `LocalKey`.
How can this macro ensure task-level global variable?

## `task_local!` is based on `thread_local!`
Let's unravel `task_local` the macro's definition:

```rust
#[macro_export]
#[cfg_attr(docsrs, doc(cfg(feature = "rt")))]
macro_rules! task_local {
     // empty (base case for the recursion)
    () => {};

    ($(#[$attr:meta])* $vis:vis static $name:ident: $t:ty; $($rest:tt)*) => {
        $crate::__task_local_inner!($(#[$attr])* $vis $name, $t);
        $crate::task_local!($($rest)*);
    };

    ($(#[$attr:meta])* $vis:vis static $name:ident: $t:ty) => {
        $crate::__task_local_inner!($(#[$attr])* $vis $name, $t);
    }
}

#[doc(hidden)]
#[macro_export]
macro_rules! __task_local_inner {
    ($(#[$attr:meta])* $vis:vis $name:ident, $t:ty) => {
        $(#[$attr])*
        $vis static $name: $crate::task::LocalKey<$t> = {
            std::thread_local! {
                static __KEY: std::cell::RefCell<Option<$t>> = const { std::cell::RefCell::new(None) };
            }

            $crate::task::LocalKey { inner: __KEY }
        };
    };
}
```

where we can clearly conclude that `task_local!` is based on `thread_local!`.
However, threads are reused by multiple different tasks asynchronously.
If task 1 yields, the thread may be scheduled to another task 2.
When task 1 is ready to run again, it may select another thread 2 to poll.
Therefore, `thread_local` values may be overwritten or lost.

## Swap!
So, how does `task_local` ensure thread_local values not be overwritten or lost?
The secret is under the implementation of `Future` for `task::LocalKey`'s wrapper.

Firstly, to use a LocalKey, one need to `scope` it and produce a `TaskLocalFuture<T, F>`
which binds `T` and the corresponding task's future `F`:

```rust
impl<T> LocalKey<T> {
    pub fn scope<F>(&'static self, value: T, f: F) -> TaskLocalFuture<T, F>
    where
        F: Future,
    {
        TaskLocalFuture {
            local: self,
            slot: Some(value),
            future: Some(f),
            _pinned: PhantomPinned,
        }
    }

    fn scope_inner<F, R>(&'static self, slot: &mut Option<T>, f: F) -> Result<R, ScopeInnerErr>
    where
        F: FnOnce() -> R,
    {
        struct Guard<'a, T: 'static> {
            local: &'static LocalKey<T>,
            slot: &'a mut Option<T>,
        }

        impl<'a, T: 'static> Drop for Guard<'a, T> {
            fn drop(&mut self) {
                // This should not panic.
                //
                // We know that the RefCell was not borrowed before the call to
                // `scope_inner`, so the only way for this to panic is if the
                // closure has created but not destroyed a RefCell guard.
                // However, we never give user-code access to the guards, so
                // there's no way for user-code to forget to destroy a guard.
                //
                // The call to `with` also should not panic, since the
                // thread-local wasn't destroyed when we first called
                // `scope_inner`, and it shouldn't have gotten destroyed since
                // then.
                self.local.inner.with(|inner| {
                    let mut ref_mut = inner.borrow_mut();
                    mem::swap(self.slot, &mut *ref_mut);
                });
            }
        }

        self.inner.try_with(|inner| {
            inner
                .try_borrow_mut()
                .map(|mut ref_mut| mem::swap(slot, &mut *ref_mut))
        })??;

        let guard = Guard { local: self, slot };

        let res = f();

        drop(guard);

        Ok(res)
    }
}
```

The value `T` is initialized when polled the first time.
The future impl is as follows:

```rust
impl<T: 'static, F: Future> Future for TaskLocalFuture<T, F> {
    type Output = F::Output;

    #[track_caller]
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output> {
        let this = self.project();
        let mut future_opt = this.future;

        let res = this
            .local
            .scope_inner(this.slot, || match future_opt.as_mut().as_pin_mut() {
                Some(fut) => {
                    let res = fut.poll(cx);
                    if res.is_ready() {
                        future_opt.set(None);
                    }
                    Some(res)
                }
                None => None,
            });

        match res {
            Ok(Some(res)) => res,
            Ok(None) => panic!("`TaskLocalFuture` polled after completion"),
            Err(err) => err.panic(),
        }
    }
}
```

Whenever the future is used to poll again, it just *SWAP* the global `thread_local` slot and the value inside `TaskLocalFuture` and *SWAP* back when finished.

As easy as pie ;)