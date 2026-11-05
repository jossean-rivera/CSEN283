---
date: 2026-10-20
tags:
---

*Continuing scheduling policies. So far, we have seen:*
- FCFS
- SJF
- SRTF
- HRRN
- RR
- Priority Scheduling
- MLFQ (Multi-Level Feedback Queue)

In MLFQ, processes can "fool" the scheduler by voluntarily giving up the CPU (for example, by calling `sleep()`) just before their time slice ends. The scheduler may treat them as interactive and keep them at a higher priority instead of demoting them. A common defense is to track total CPU time used at a priority level and demote a process once it uses its full allotment, even if it yields frequently.

# Finish Scheduling Policies

## Completely Fair Scheduler (CFS)

Round-Robin is fair in the sense that each process receives a time slice. CFS redefines fairness by **considering the weight of a program**.

For example, let us say we have the following processes:

| Process | Weight | Fair share |
| ------- | ------ | ---------- |
| P1      | 1024   | 25%        |
| P2      | 2048   | 50%        |
| P3      | 1024   | 25%        |

If the weight is the same for all processes, then CFS gives them approximately equal CPU shares, though it does not use fixed Round-Robin time slices.

**Virtual runtime:** `virtual_runtime_increment = real_runtime_increment × (1024 / weight)`

The larger the weight, the more slowly a process's virtual runtime increases. Thus, the scheduler considers a high-weight process to have run for less time, even when its actual (`real_runtime`) running time is longer.

For example, assume `delta_time = 6 ms`:

| Process | Weight | Virtual-runtime increment |
| ------- | ------ | ------------------------- |
| P1      | 1024   | `6 ms`                    |
| P2      | 2048   | `3 ms`                    |
| P3      | 1024   | `6 ms`                    |

### Nice value

In Linux, a **nice value** ranges from `-20` to `+19` and is translated into a scheduling weight. For example: `-20 → 88761`, `0 → 1024`, and `+19 → 15`.

The scheduler selects the runnable process with the smallest virtual runtime.

#### How to set a nice value

Use the `nice` command to set the nice value of a program when starting it, or use `renice` for an existing process.

- An unprivileged user can only lower a process's priority (increase its numeric nice value), meaning the process becomes “nicer.”
- Raising a process's priority requires appropriate privileges.

## EEVDF — Earliest Eligible Virtual Deadline First

EEVDF is used by modern Linux kernels for fair scheduling; it began replacing CFS in Linux 6.6.

The keyword is **eligible**, which determines fairness. EEVDF uses a process's **lag** to determine whether it is owed CPU time:

`lag = ideal_cpu_time − actual_cpu_time`

Only processes with nonnegative lag are eligible to run. Among eligible processes, the one with the earliest virtual deadline runs first. Shorter requested time slices can therefore improve responsiveness.

For example, we have two processes with different actual running times:

| Process | Ideal CPU time | Actual CPU time | Lag    |
| ------- | -------------- | --------------- | ------ |
| P1      | `10 ms`        | `14 ms`         | `-4 ms` |
| P2      | `10 ms`        | `6 ms`          | `4 ms` |

*Eligible = nonnegative lag, meaning the process is owed CPU time.*

A virtual deadline is based on the current virtual runtime plus a weighted virtual time slice:

`virtual_deadline = virtual_runtime + slice × (1024 / weight)`

## EDF — Earliest Deadline First

EDF schedules the ready process with the earliest deadline. It requires knowing or estimating deadlines, so it is most useful for real-time workloads.

On a single processor, preemptive EDF is optimal for independent jobs when preemption has no cost: if any schedule can meet all deadlines, EDF can.

# Process Relationships

*Or thread relationships—we use “process” and “thread” loosely here.*

There are many processes at any moment. There are two types:

- **Competition**
  - Processes compete for shared resources.
  - We need synchronization to avoid conflicts.
- **Collaboration**
  - **Loose collaboration:** independent jobs must be synchronized to run in the correct order.
  - **Tight collaboration:** for example, the producer-consumer pattern processes large amounts of data.

## Competition

Without synchronization, there can be many issues. For example, two processes might write at the same time—like two cars entering an intersection at the same time and crashing.

Processes do not need to be aware of the existence of the others. It is inevitable that multiple processes will compete for limited resources in the computer.

For example:

```c
int count = 5;

void process1() // runs in a given thread
{
    count++;
}

void process2() // runs in another thread
{
    count--;
}
```

Even though each operation is one statement in C, it can require three assembly-level operations:

```c
process1:
    MOV count, R0
    ADDI R0, R0, 1
    MOV R0, count

process2:
    MOV count, R0
    ADDI R0, R0, -1
    MOV R0, count
```

An interleaving of these read-modify-write operations can cause a **race condition**, leaving `count` with an unexpected value. In C, unsynchronized concurrent access also creates a data race and undefined behavior.

#### Problems with bad synchronization

- **Deadlock:** a process waits for a resource held by another process, while that other process is waiting for a resource held by the first.
- **Starvation:** a process is indefinitely delayed because other processes continually acquire the resource.

### Synchronization

- Some processes require a collaborative effort to accomplish the same task.
- The execution of one process may rely on another.
- Since each process is carried out independently and at an unpredictable pace, collaborating processes need to coordinate their work.
- One process may need to **block** until another is done with a shared resource.

Solutions:

- **Software:**
  - Dekker's algorithm
  - Peterson's algorithm
- **Hardware:**
  - Disabling interrupts (in limited kernel contexts)
  - Atomic machine instructions

### Interprocess Communication (IPC)

In many cases, there is a need to exchange large amounts of data between processes. Threads in the same process already share an address space.

For example, sending a batch of messages or an entire file can be accomplished through a communication mechanism. Common IPC methods include:

- Signals
- Shared memory
- Message passing
- Shared files

There are many ways to communicate between processes:

- **Pipeline (pipe)**
  - The oldest IPC mechanism in Unix.
  - A pipe is a connection between two processes: the standard output of one becomes the standard input of the other.
  - Unix shell pipe example: `cat mytextfile | sed 's/hate/love/g'`
    - `cat` displays the file.
    - `sed` (*stream editor*) replaces every occurrence of `hate` with `love`.
  - Traditional unnamed pipes are typically used between related processes, such as processes created by the same shell.
  - If a process tries to read from an empty pipe while a writer is still open, it blocks until data is written.
  - Call `pipe()` to get two file descriptors in an array: `fd[0]` for reading and `fd[1]` for writing.
- **FIFO**
  - A FIFO is a named pipe.
  - A traditional pipe is unnamed and lasts only as long as the processes using it.
  - A named pipe has a filesystem name and persists until it is removed.
  - Create one with `mkfifo()`.
  - Since a named pipe is a kind of file, we can use `open()`, `read()`, `write()`, and `close()`.
  - With a FIFO, unrelated processes can exchange data.
- **Shared memory**
  - Two or more processes use a shared memory region to communicate.
  - The data structure is defined by the processes, not controlled by the system.
  - A typical example is the producer-consumer problem.
  - In System V IPC, `shmget()` creates or obtains a segment, `shmat()` attaches it, and `shmctl()` controls it.
- **Message queue**
  - A queue of messages stored in the kernel.
  - Managed by the kernel through system calls.
  - Use `msgsnd()` and `msgrcv()` to send and receive messages.
- **Semaphore**
  - A synchronization primitive that uses an integer counter.
  - We will look at it in more depth later.
