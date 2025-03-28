---
tags: programming,rust
---

# Rusty docker setting

Recently I'm learning debugging and profiling tools of *Rust* on *Linux*.
I utilize docker to build a debian container and install various kinds of tools(e.g., LLDB, perf).
However, to use LLDB in docker container, there're some notices which one may need to care.

## LLDB python3 lib path problem
I install LLDB ion the official Rust image via:
```sh
apt-get install lldb python3-lldb
```

But when I typed `rust-lldb`, I encountered the following error messages:
```
ModuleNotFoundError: No module named ‘lldb.embedded_interpreter’ · Issue #55575 · llvm/llvm-project;
```

After quite some googling, I found this script useful:
```sh
ln -s /usr/lib/llvm-14/lib/python3.11/dist-packages/lldb/* /usr/lib/python3/dist-packages/lldb/
```
Still, the version of python may vary on different lldb versions. Replace the actual version number if necessary.

## Container addtional options
When I thought "I have fixed the python3 problem and here we go!", another problem arises.
I just cannot use LLDB commands!

It turns out that I have to include some addtional options to enable debugging:
```sh
docker run -dit --cap-add=SYS_PTRACE --security-opt seccomp=unconfined rust
```

Now LLDB is ready for debugging rust programs!