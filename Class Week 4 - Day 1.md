---
date: 2026-10-13
tags:
  - scheduling
  - priority
---
### Before
- Process
- Thread
- Scheduling overview

We did not get into the details of context switching.

### Today
- More scheduling theory

# Scheduling
[[Class Week 3 - Day 2#Scheduling]]
- A conceptual abstraction, not a separate mechanism
- We need to identify whether we are switching to a new thread in a new process or to a new thread in the same process.
- Threads are the scheduled entities, not processes.
- Each thread runs on a hart.
- xv6 has one kernel thread per process. Its scheduler therefore looks like it schedules processes, because switching threads also switches processes.

Three issues in scheduling:
- **When?**
  - Selection can happen after any of these events:
    - A new task is created.
    - A blocked task is awakened.
    - A task is terminated.
    - An I/O-completion interrupt occurs and may wake a waiting task.
    - A timer interrupt occurs (the time slice expires).
- **How?**
  - How to bind work to a CPU
  - Context switch
- **Who?**
  - Which new thread to select

### What needs to happen?
- The currently running process is interrupted.
- Control switches to the kernel.
- The kernel handles the event.
- The process status changes.

Generally, there is a queue of threads that are ready to run.

Save the outgoing thread's context:
- CPU registers, including the **PC** and **SP**
- Kernel-stack pointer/context
- Scheduling state and status

> [!info] Clarification
> The kernel stack itself is not copied during a context switch; each thread's kernel stack remains in memory, and the saved stack pointer lets execution resume on it.

- If the new thread belongs to the **same** process, switch the thread's register and kernel-stack context.
- If the new thread belongs to **another** process, also switch to that process's virtual address space (for example, its page table/root-table base) and other process-specific execution state.

### PCB components
- Process ID
- CPU state/context
- Process state and scheduling information (for example, priority)
- Memory-management information, including the page-table base
- Kernel stack
- References to resources such as open files

### xv6 Scheduling
`Runnable? → Yes → Run next`

In xv6, we use a round-robin policy. But we need to determine:
- How long it should run
- Whether fairness matters
- Whether cache locality matters

### Priority and Queue
Priority is basically a number given to a process. It can be static or dynamic.
- **Static priority:** remains the same for the lifespan of the process.
- **Dynamic priority:** is recomputed every time the scheduler is invoked.

Typically, priority is a positive integer, with a lower number representing a higher priority. Another consideration is whether scheduling is preemptive or non-preemptive: whether a process can be taken off the CPU before it finishes or blocks.

- **Voluntary blocking/yielding:** the process chooses when to give up the hart.
- **Involuntary preemption:** the process is forced to give up the hart, usually when its time slice expires.
  - A time slice is a way to stop and later resume processes.

Round-robin must be preemptive. Each process runs for a fixed time quantum before the scheduler moves to the next ready process.

### Priority Queue Implementation
We usually use a binary-heap data structure to quickly pick the highest-priority process. The implementation is a design choice for the OS.

### Basic Scheduling Algorithms
- **First In, First Out (FIFO)**
  - Runs tasks in order of arrival.
  - This is normally non-preemptive.
- **Shortest Job First (SJF)**
  - Average turnaround time is shorter when jobs are ordered by burst time from shortest to longest.
  - Select the job with the shortest estimated CPU burst.
  - This is usually non-preemptive.
- **Shortest Remaining Time First (SRTF)**
  - The preemptive version of SJF: run the job with the least remaining CPU time.
  - A newly arrived job with a shorter remaining time can preempt the current job.
- **Earliest Deadline First (EDF)**
  - Select the runnable task with the earliest absolute deadline.
  - It is commonly preemptive: a newly runnable task with an earlier deadline can preempt the current task.
- **Round Robin**
  - Cycle through the ready queue, giving each task a fixed time quantum.
