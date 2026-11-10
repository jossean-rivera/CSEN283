# Exam and Assignment Practice

Use this as a closed-note check. Explain the reasoning before expanding each answer.

## Foundations and memory

> [!question]- Why are user mode and kernel mode needed?
> User programs cannot directly perform privileged operations. A controlled trap gives the kernel a chance to validate and perform a requested service.

> [!question]- A system has 4 KiB pages and 32-bit virtual addresses. How many offset bits and virtual pages?
> 4 KiB is `2^12` bytes, so the offset is 12 bits. The remaining 20 bits select `2^20` virtual pages. The page table maps pages to frames; the offset remains unchanged. This is an address-space calculation, not a claim that every page is mapped.

> [!question]- What do sibling threads share, and what does each keep?
> They share their process's address space and resources, including globals and heap; each has its own registers, program counter, and stack.

## Processes and xv6

> [!question]- What does `fork()` return and what does `exec()` do?
> On success, fork returns 0 in the child and the child's PID in the parent. Exec replaces the current program image; successful exec does not return to the old program.

> [!question]- Distinguish an orphan and a zombie.
> An orphan is still alive after its parent exits; a zombie has exited but awaits collection of its status. `wait` reaps a terminated child.

> [!question]- Trace a timer interrupt in the course xv6.
> User trap entry saves user registers, switches to the kernel page table, and enters `usertrap`; timer handling can call `yield`, which makes the process runnable and reaches `scheduler` through `sched` and `swtch`. The scheduler selects a runnable process, switches to its kernel context, and it eventually returns to user mode. See [[04 - Process Creation and xv6]] and [[05 - Scheduling Mechanics]].

## Scheduling

> [!question]- A job arrives at 2, first runs at 5, completes at 12, and needs 6 CPU units with no blocking. Find response, turnaround, waiting.
> Response = `5 − 2 = 3`; turnaround = `12 − 2 = 10`; waiting = `10 − 6 = 4`.

> [!question]- What's the difference between SJF and SRTF?
> Both favor short CPU work. Non-preemptive SJF chooses a shortest ready burst at dispatch; SRTF compares remaining times and can preempt when a shorter job arrives.

> [!question]- A job waits 6 ms and needs an estimated 3 ms. Its HRRN ratio?
> `(6 + 3) / 3 = 3`. The ratio is not response time.

> [!question]- A task of weight 2048 runs 6 ms. How much virtual runtime in the lecture's CFS model?
> `6 × 1024 / 2048 = 3` virtual ms. This is an increment; absolute virtual runtime depends on its starting value.

> [!question]- Can an EEVDF task with an earlier virtual deadline always run?
> No. It must also be eligible: in the lecture's model its lag must be nonnegative. Compare deadlines among eligible tasks.

## Synchronization

> [!question]- Why is `if (lock == 0) lock = 1` unsafe?
> The check and update are separate. Two threads can both observe 0 and enter. An atomic read-modify-write primitive and correct memory ordering are required.

> [!question]- How do spinlocks and blocking mutexes differ?
> A spinning waiter consumes CPU while retrying; a blocked waiter gives up the CPU. A normal blocking mutex cannot be used where sleeping is forbidden, such as an interrupt handler.

> [!question]- Why can opposite lock ordering deadlock?
> A can hold M1 while waiting for M2, while B holds M2 and waits for M1. Both acquiring M1 before M2 prevents that cycle.

> [!question]- How do you initialize the semaphores for a buffer with 8 slots?
> `space = 8`, `items = 0`, `mutex = 1`. Producers wait on space before locking; consumers wait on items before locking.

## Problem-solving checklist

- **Scheduling:** record arrival, remaining burst, ready queue, tie rules, quantum and overhead; stop the timeline at each arrival, completion, or relevant quantum boundary. Check total CPU work and compute all three metrics separately.
- **Process code:** draw a process tree for each `fork`; mark return values and separate address spaces; remember output ordering is not guaranteed without synchronization.
- **xv6 trace:** identify user/kernel mode, trapframe versus scheduler context, state transition, scheduler choice, and return path.
- **Concurrency:** list shared data, possible interleavings, lock ownership and ordering; identify which waits may block and who can wake them.

**Course sources:** Professor slides and whiteboards named in the linked topical notes; submitted Assignments 1–4; [[Homework 2]], [[Homework 4]].
