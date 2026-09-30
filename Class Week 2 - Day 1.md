---
date: 2026-11-29
course: COEN 283
tags:
  - rvos
  - xv6
  - threads
  - harts
---

## Review

Last time we discussed:

- The boot sequence
- `entry.S` (in M-mode) starts the kernel.
- The kernel runs in S-mode.
- To move from M-mode to S-mode, set the `mstatus.MPP` field before calling `start_kernel()` in `entry.S`.
  - `MPP` occupies bits 12:11; S-mode is encoded as `01`.
  - `1 << 11` sets `MPP` to S-mode. `3 << 11` encodes `11`, which is M-mode.
  - See [[Class Week 1 - Day 2#RISC-V Privilege-Mode Encoding]].

The professor began a more complicated `entry.S` example that does more work in M-mode before entering S-mode and calling `start_kernel()`. It uses **RVOS**, a simpler version of `xv6`.

> [!info] Clarification
> `__asm__ volatile("ecall")` executes the RISC-V **environment-call** instruction. It causes a trap, which the kernel’s trap handler can handle as a system call; it does not directly call an assembly function from C.

- `mret` returns from an M-mode trap: it sets the PC to `mepc` and restores the target privilege mode from `mstatus`.
- `sret` returns from an S-mode trap: it sets the PC to `sepc` and restores the target privilege mode from `sstatus`.

A **trap** is an event that transfers control to a privileged trap handler. Traps include exceptions (such as an `ecall`), interrupts (such as timer or software interrupts), and faults.

# Hart — Hardware Thread

Moving on to the next topic, the professor clarified hardware concepts before examining the software side.

In a RISC-V implementation and execution pipeline, we have different components:

- **IF/ID pipeline register**: holds the fetched instruction (and typically its PC) between the instruction-fetch and instruction-decode stages.
- **Instruction decoder**
- **Register file**
- **CSR**: control and status registers

This is the pipeline:

`PC → IF → ID → EXE → MEM → WB`

- **PC**: program counter
- **IF**: instruction fetch
- **ID**: instruction decode
- **EXE**: execution
- **MEM**: memory access
- **WB**: write back

This is what we call a **hart**, or logical CPU.

A system can have more than one hart at a time. In the example, there are four: `Hart0`, `Hart1`, `Hart2`, and `Hart3`. Each hart has its own private general-purpose registers, PC, and CSRs (architectural state).

Although diagrams often show harts as entirely separate components, that is only one implementation. Harts may share implementation resources, such as a pipeline, as long as they can execute independently.

A hart is an architectural concept, not necessarily a physical component. Each hart has its own execution state; implementations may use separate or shared pipelines.

### What the OS sees

The OS sees the harts available to run work and a separate virtual address space for each process. It generally does not directly manage hardware details such as L1/L2/L3 caches, cache coherence, or cache lines. The OS manages:

- Which virtual addresses map to which physical pages
- Which hart runs which process
- Context switching
- Permissions
- TLB-related page-table updates and invalidation

The **central job of an OS** is to manage sharing of limited hardware resources, such as:

- Harts
- Memory
- Devices

Each hart can run its own scheduler loop, which selects a runnable process for that hart.

# Process and Thread

## Process

Processes and programs are different concepts.

- **Process**
  - It is not a program.
  - It is an executing instance of a program that has entered the system.
  - It has an execution environment and resources: code, data, an address space (page table and memory), a heap, and file descriptors.
- **Program**
  - Static code: a file on disk containing instructions, data, and symbols before it is executed.

Example: Microsoft Word is a program on disk. When you double-click Word, the OS creates a process that runs in RAM; you can see it in Task Manager.

When processes exchange data, they use **IPC (interprocess communication)**, such as pipes, shared memory, sockets, or message passing.

## Threads

Every process has at least one thread. Execution is performed by a process’s thread(s), not by the process itself.

- A thread runs on a hart when scheduled by the kernel’s scheduler.

The professor discussed a C `main` function that creates three threads that increment a shared global variable named `counter`. `main` calls `pthread_create` and passes a function for the new thread to execute. It calls `pthread_join` to wait for all threads to finish.

A program has at least `thread0`, called the **main thread**. If it calls `pthread_create`, the process has the main thread and `thread1` created by that call.

### Resource hierarchy

- **Process 1**
  - Shared code
  - Shared global variables
  - Shared heap
  - **Thread 0**
    - Private registers
    - Private stack: stores call frames, local variables, and return addresses
  - **Thread 1**
    - Private registers
    - Private stack: stores call frames, local variables, and return addresses

Threads share memory only when they belong to the same process. Threads in different processes need an explicit sharing mechanism, such as shared memory or IPC.

### Summary

- **Harts** = hardware execution engines
- **Threads** = what runs on harts
- **Processes** = what own memory and shared resources

# Scheduler

Looking at the `xv6` example, which is more complicated than RVOS:

> [!info] Clarification
> In the basic `xv6` implementation, each process has one kernel thread. It does not provide a general user-level `pthreads` interface.

~~~asm
_entry:
  # ...

  # Call start() in start.c
  call start

spin:
  j spin # Infinite loop
~~~

The `start` function prepares machine-mode settings and calls the kernel’s `main` function in `kernel/main.c`. In this setup, `main` does not return; after initialization, it enters the scheduler.

Each hart follows:

`entry.S → start.c → main.c`

The last thing `main.c` does is call `scheduler()`. The scheduler is an infinite loop that finds runnable processes and calls `swtch()`. Once `swtch()` transfers control to a process, the scheduler does not run again until that process switches back to its scheduler context.

- `swtch()` performs a context switch. In `swtch.S`, it saves the current context’s callee-saved registers to the structure passed in `a0`, then restores the next context’s registers from the structure passed in `a1`.

Each hart has its own scheduler context; there is no single central scheduler. The scheduler chooses a runnable process for the current hart.

# Next time

We will look at page tables and the process data structure: code, global variables, and page tables.
