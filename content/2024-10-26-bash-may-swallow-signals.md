---
tags: programming
---

# Bash may swallow signals

Recently I encountered a confusing bug:

> A simple `eprintln!` in Rust may cause panic!

That sounds weird, right?
Let's check the `eprintln!`'s doc:

> Prints to the standard error, with a newline.
> 
> Equivalent to the [`println!`] macro, except that output goes to
> [`io::stderr`] instead of [`io::stdout`]. See [`println!`] for
> example usage.
> 
> Use `eprintln!` only for error and progress messages. Use `println!`
> instead for the primary output of your program.
> 
> See [the formatting documentation in `std::fmt`](../std/fmt/index.html)
> for details of the macro argument syntax.
> 
> [`io::stderr`]: crate::io::stderr
> [`io::stdout`]: crate::io::stdout
> [`println!`]: crate::println
> 
> # Panics
> Panics if writing to `io::stderr` fails.
> 
> Writing to non-blocking stderr can cause an error, which will lead
> this macro to panic.
> 
> # Examples
> ```
> eprintln!("Error: Could not complete task");
> ```

Therefore, at least we can conclude that print will only panic when the stderr cannot be written, but how?

## Redirected stderr
I checked the original stderr and It's normal. But quickly I noticed that the rust program's stderr was been redirected to another process's input.

Now the problem seems to be clear:
> The process which redireced Rust's program's stderr exits, then `eprintln!` writes to broken pipe and panic!

But why? Why still write to stderr when something is broken?

## Bash launched bash
I noticed that the launch shell did not directly `exec` Rust program.
In stead, it call bash on another bash script which `exec` the real program.
For a minimal example:

```sh
# launch.sh
bash run.sh

# run.sh
exec program
```

When we run the `launch.sh`:
```sh
bash launch.sh
```
We create 2 processed: `bash lanuch.sh` and `program`.

It seems still fine. But if you try to `kill` the bash process, you can observe that, although the `bash` process is killed, the `program` process is still alive!

> BASH SWALLOWS SIGNALS!

Now we completely unravel the reason:

> When the pod try to exit and kill process, it failed to kill the real process and leave it writing to broken pipes which leads to panic and coredumps.