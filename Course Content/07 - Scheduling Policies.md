# Scheduling Policies

> [!info] Source priority
> The following class-note excerpts record topics the professor discussed. They are retained in detail as lecture records. Warnings distinguish corrections from the original record; pseudocode is illustrative unless explicitly stated otherwise.

**Professor materials:** Professor Slides 3-2-scheduling-theory1.pdf; Professor Slides Lecture4-1-scheduling-theory1.pdf; Professor Notes Lecture4-1fall2026.jpg; Professor Notes Lecture4-2fall2026.jpg. All listed materials are in `/Users/jossean/Documents/CSEN 283/`.

> [!warning] Policy assumptions
> The MLFQ rules and queue sizes shown are the lecture’s illustrative variant. The assignment gives its own explicit wakeup, allotment, and tie-breaking rules. Non-preemptive SJF minimizes average waiting under the appropriate known-burst assumptions; arrivals and scheduling decisions matter. EEVDF deadlines belong to a request, so use its starting virtual runtime rather than repeatedly adding a complete slice to advancing runtime.


---

## Lecture record: [[Class Week 4 - Day 1]] (lines 90–105)

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

---

## Lecture record: [[Class Week 4 - Day 2]] (lines 8–173)

# Process Scheduling

## Continue

- **Non-preemptive:** once a process is running, it continues until it finishes, blocks, or voluntarily yields the CPU.
- **Preemptive:** a running process can be forced to pause so another process can run.

So far, we checked [[Class Week 4 - Day 1#Basic Scheduling Algorithms]]:

- **First-Come, First-Served (FCFS)**
  - Select by earliest arrival time.
  - Non-preemptive in the basic form.
  - Easy and “fair” in arrival order (no starvation).
  - Turnaround time can be high.
- **Shortest Job First (SJF)**
  - Select the process with the shortest CPU burst.
  - Non-preemptive.
  - Minimizes average waiting time when CPU-burst lengths are known.
  - Long jobs can remain at the bottom of the queue (starvation).
  - CPU-burst length is usually estimated from a process's previous CPU bursts; it cannot generally be known exactly in advance.
- **Shortest Remaining Time First (SRTF)**
  - Select by shortest remaining CPU time.
  - The preemptive version of SJF.
  - Long jobs can also starve.

*Priority is not necessarily a field stored in the process data structure. It can be an **implicit priority** derived from a scheduling policy, such as arrival time or remaining time.*

### Example for Shortest Remaining Time First

| Process | Arrival Time | Total CPU Time* |
| ------- | ------------ | --------------- |
| T1      | 0            | 7               |
| T2      | 2            | 4               |
| T3      | 4            | 1               |
| T4      | 5            | 4               |

*CPU time does not include I/O time.*

If non-preemptive (**SJF**), T1 runs to completion because it is the only task at time 0. At time 7, the scheduler selects the shortest waiting task: T3, then T2 (which arrived before T4 when their CPU times tie), then T4.

```mermaid
flowchart LR
    t0((0)) --> t1["T1<br/>0–7"] --> t3["T3<br/>7–8"] --> t2["T2<br/>8–12"] --> t4["T4<br/>12–16"] --> done((16))
```

If preemptive (**SRTF**), T1 runs from 0 to 2, when T2 arrives and preempts it with 5 units remaining. T2 runs from 2 to 4; T3 then arrives with the shortest remaining time and runs to completion. T2 resumes from 5 to 7, followed by T4, then T1 finishes.

```mermaid
flowchart LR
    t0((0)) --> t1a["T1<br/>0–2"] --> t2a["T2<br/>2–4"] --> t3["T3<br/>4–5"] --> t2b["T2<br/>5–7"] --> t4["T4<br/>7–11"] --> t1b["T1<br/>11–16"] --> done((16))
```

### HRRN (Highest Response Ratio Next)

`response_ratio = (waiting_time / service_time) + 1`

Equivalently: `response_ratio = (waiting_time + service_time) / service_time`.

- If waiting time is long, the response ratio increases.
- A short job has a high response ratio because its `service_time` is smaller.

| Process | Arrival Time | Service Time |
| ------- | ------------ | ------------ |
| P0      | 0            | 3            |
| P1      | 2            | 6            |
| P2      | 4            | 4            |
| P3      | 6            | 5            |
| P4      | 8            | 2            |

If non-preemptive, P0 runs from time 0 to 3. Then P1 runs from time 3 to 9. Before time 9, P2, P3, and P4 arrive in the queue. If we only check service time, P4 takes precedence, but P2 has waited the longest.

- `P2_response_ratio = (9 - 4) / 4 + 1 = 2.25`
- `P3_response_ratio = (9 - 6) / 5 + 1 = 1.60`
- `P4_response_ratio = (9 - 8) / 2 + 1 = 1.50`

P2 has the highest response ratio, so P2 runs next after P1. P2 runs from time 9 to 13. At this point, recalculate the ratios:

- `P3_response_ratio = (13 - 6) / 5 + 1 = 2.40`
- `P4_response_ratio = (13 - 8) / 2 + 1 = 3.50`

P4 has the highest response ratio, so it runs from time 13 to 15. Finally, P3 runs from time 15 to 20.

```mermaid
flowchart LR
    t0((0)) --> p0["P0<br/>0–3"] --> p1["P1<br/>3–9"] --> p2["P2<br/>9–13"] --> p4["P4<br/>13–15"] --> p3["P3<br/>15–20"] --> done((20))
```

### Round Robin (RR)

All processes have the same priority and take turns using a fixed time quantum (time slice). For example, let the time slice be 2 seconds.

This policy must be preemptive.

- If the quantum is too small (`q → 0`), the system spends most of its time context-switching.
- If the quantum is extremely large (`q → ∞`), no runnable process is preempted before it finishes. Therefore, RR behaves like FCFS: processes run in arrival order until completion.

### Priority Scheduling

Assign each process a priority. The user or administrator can set an **external (static) priority**, or the system can dynamically adjust priority according to the running situation.

### Multilevel Priority

Multilevel priority is also based on an external priority set by the user or administrator. We use priority levels, for example from 0 to *n*, where 0 is the highest priority. Each level can contain multiple processes in its own ready queue.

### Multilevel Feedback Queue (MLFQ)

MLFQ is similar to multilevel priority scheduling. The smaller the number, the higher the priority. Each level has a ready queue of processes at that priority. The rules are:

1. A newly arrived or awakened process enters the highest-priority queue.
2. Schedule a process from the highest non-empty queue.
3. Use Round Robin within the same queue.
4. If a process uses its entire time allotment at a level, lower its priority.
5. To avoid starvation, periodically perform a **priority boost**.

Basically, the scheduler dynamically checks how much CPU time each process uses and adjusts its priority level.

| Priority | Maximum time at each level | Process ready queue |
| -------- | -------------------------- | ------------------- |
| 1        | `T`                        | `p1 → p2 → p3`      |
| 2        | `2T`                       |                     |
| 3        |                            |                     |
| ...      |                            |                     |
| n        | `2^(n - 1)T`               |                     |

Example: three levels with three corresponding queues:

1. First-queue maximum time: 8 ms
2. Second-queue maximum time: 16 ms
3. Third-queue maximum time: 32 ms

Say we have three jobs (A, B, and C):

- A total CPU time: 5 ms
- B total CPU time: 15 ms
- C total CPU time: 40 ms

1. A runs in the first queue and completes in 5 ms.
2. B runs for 8 ms, has 7 ms remaining, and is demoted to priority 2.
3. C runs for 8 ms, has 32 ms remaining, and is demoted to priority 2.
4. In priority 2, B runs its remaining 7 ms and completes.
5. C runs for 16 ms, has 16 ms remaining, and is demoted to priority 3.
6. C runs for its remaining 16 ms and completes.

### Types of jobs

There are two types of jobs:

- **CPU-bound:** spends most of its time running on the CPU.
- **I/O-bound:** spends much of its time waiting for I/O; interactive programs, such as text editors, are often I/O-bound.

**“Gaming the scheduler”:** a CPU-bound process can pretend to be interactive by voluntarily sleeping at 7 ms, just before an 8 ms quantum ends, to avoid demotion. A defense is to use **cumulative CPU time** at each priority level rather than resetting the allotment whenever a process yields.

### Completely Fair Scheduler (CFS)

CFS is Linux's older fair scheduler; modern Linux has been transitioning toward EEVDF. CFS tries to make CPU allocation more fair, including for important jobs that run for a long time.

CFS tracks **virtual runtime**, which weights actual CPU running time:

`virtual_runtime_increment = actual_cpu_time_increment × (1024 / weight)`

The larger the weight, the more slowly virtual runtime increases. Thus, a higher-weight process can receive more CPU time while still having a relatively low virtual runtime. The scheduler selects the runnable process with the smallest virtual runtime.

### Next Time

- Newer Linux scheduling
- Process competition and synchronization

---

## Lecture record: [[Class Week 5 - Day 1]] (lines 1–86)

*Continuing scheduling policies. So far, we have seen:*
- FCFS
- SJF
- SRTF
- HRRN
- RR
- Priority Scheduling
- MLFQ (Multi-Level Feedback Queue)

In MLFQ, processes can "fool" the scheduler by voluntarily giving up the CPU (for example, by calling the sleep function) just before their time slice ends. The scheduler may treat them as interactive and keep them at a higher priority instead of demoting them. A common defense is to track total CPU time used at a priority level and demote a process once it uses its full allotment, even if it yields frequently.

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
