# Concurrency, Locks, and IPC

Processes may **compete** for shared resources or **collaborate** toward a result. Concurrent execution can interleave operations even on one CPU; multiple harts can also execute simultaneously.

## Races and critical sections

A **race condition** occurs when a result depends on an uncontrolled ordering of concurrent operations. Suppose `count = 5` and two threads increment it:

```text
A reads 5 → B reads 5 → A writes 6 → B writes 6
```

One update is lost; the intended result is 7. In C or C++, unsynchronized conflicting non-atomic accesses also constitute a data race and yield undefined behavior. A **critical section** is code that accesses shared state under a coordination rule.

Three goals:

1. **Mutual exclusion:** at most one participant enters the protected critical section at a time.
2. **Progress:** when the section is free and participants want to enter, selection is not postponed forever.
3. **Bounded waiting / no starvation:** a participant is not indefinitely bypassed.

**Deadlock** means progress stops because participants wait on one another; **starvation** means one waits indefinitely while others may continue.

## Why locks need atomic operations

Checking `if (lock == 0)` and then setting `lock = 1` separately is unsafe: both threads can observe 0 before either writes 1. **Test-and-set** atomically sets and returns the old value; **compare-and-swap** atomically changes a value only if it matches the expected value. These require appropriate memory ordering as well as atomicity.

```text
acquire: while atomic_test_and_set(lock) was already set: keep trying
critical section
release: atomic clear with release ordering
```

This is explanatory pseudocode, not a working implementation using ordinary C variables.

| Lock | Waiting behavior | Suitable context |
| --- | --- | --- |
| Spinlock | Repeatedly checks while using CPU time | Short waits; contexts that cannot sleep. |
| Blocking mutex | Waiter sleeps until it can proceed | Longer waits in a context allowed to sleep; normally has an owner. |

Interrupt handlers cannot sleep on a normal blocking mutex. When interrupt code and normal kernel code share a spinlock, normal code may need to disable local interrupts while holding it to avoid self-deadlock. Disabling interrupts locally does not stop other harts; a multiprocessor still needs inter-hart exclusion. Always restore the previous interrupt state.

## Classical software algorithms

**Dekker** and **Peterson** use shared intent flags and a turn variable for two participants. Peterson's outline for A: set A's intent, give B the turn, wait while B is interested *and* it is B's turn, enter, then clear A's intent. B acts symmetrically. Strict turn-taking alone can block a willing process when the other is uninterested. These textbook algorithms assume atomic accesses and suitable memory ordering; ordinary C variables are not sufficient.

## Interprocess communication

| Mechanism | Use |
| --- | --- |
| Pipe | Byte stream through file descriptors; unnamed pipes commonly connect related processes. |
| FIFO | Named pipe in the filesystem; can connect unrelated processes. |
| Shared memory | Processes map common memory; they must coordinate concurrent access. |
| Message queue | Kernel-managed messages exchanged by processes. |
| Signal | Notify a process of an event. |
| Shared file | Exchange data via filesystem storage. |

A pipe's `fd[0]` is the read end and `fd[1]` the write end. An empty pipe with an open writer normally blocks a blocking read; when all writers close, a reader sees end-of-file after draining it. Semaphores are primarily coordination primitives and are covered in [[08 - Semaphores and Coordination]].

**Course sources:** Professor Slides Lecture5-1-1process-relation-overall and Lecture5-1-2process-relation-competition; Professor Notes Lecture6-1 and Lecture7-1; [[Class Week 5 - Day 1]], [[Class Week 5 - Day 2]], [[Class Week 7 - Day 1]].
