# RISC-V Operating System

A small kernel for RISC-V that runs in QEMU. It supports threads, semaphores, sleeping, a memory allocator and console I/O. This was my project for the Operating Systems 1 course at the School of Electrical Engineering in Belgrade (2023). The assignment text, in Serbian, is in `Projektni_zadatak_2023._v1.0.pdf`.

## What it does

- **Memory allocator**: first-fit over a free list, working in fixed-size blocks.
- **Threads**: each thread has its own stack and context. Switching happens either when a thread yields (`thread_dispatch`) or when its time slice runs out on a timer interrupt.
- **Semaphores**: `sem_open`, `sem_close`, `sem_wait`, `sem_signal`.
- **Sleep**: `time_sleep` puts a thread on a list ordered by wake-up time.
- **Console**: `getc` and `putc` go through buffers that two kernel threads fill and drain, driven by console interrupts.
- **System calls**: user code runs in user mode and enters the kernel with `ecall`. The trap handler works out which call was made and dispatches it.

On top of the C calls there is a C++ wrapper with `Thread`, `Semaphore`, `PeriodicThread` and `Console` classes.

## Layout

Everything is under `project-base-v1.1/`:

```
h/      kernel headers
src/    kernel sources (C++ and a bit of assembly for the trap and context switch)
lib/    hardware and console libraries that came with the assignment
test/   test programs provided by the course
```

The public interface is in `h/syscall_c.hpp` and `h/syscall_cpp.hpp`.

## Build and run

You need a RISC-V cross compiler (`riscv64-unknown-elf-gcc` or `riscv64-linux-gnu-gcc`) and `qemu-system-riscv64`.

```
cd project-base-v1.1
make qemu
```

After boot it asks for a test number from 1 to 7 (the prompt is in Serbian). The tests cover threads through both APIs, producer and consumer with semaphores, sleeping, and a check that user code really runs in user mode.

To leave QEMU press `Ctrl+A`, then `X`. `make qemu-gdb` starts it waiting for a debugger.
