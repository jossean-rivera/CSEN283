# Professor Slides 3-2-scheduling-theory1

> [!warning] Reference transcript, not confirmation of lecture coverage
> Extracted text from the professor’s slide PDF. Page numbers are PDF page numbers. Extraction can omit diagrams or distort reading order and formulas. Refer to the original PDF for full content. A slide’s presence does not establish that it was discussed. Prioritize the topic files’ lecture records.

**Original PDF:** `/Users/jossean/Documents/CSEN 283/Professor Slides 3-2-scheduling-theory1.pdf`

**Pages with no extracted text:** None; diagrams may still be omitted.

## Slide 1

```text
Operating Systems
                      CSEN 283
                     Wang, Yuan
```

## Slide 2

```text
Operating Systems






   Scheduling
（more theory）
```

## Slide 3

```text
Scheduling                                                          Operating Systems

                        At the implementation level,
      modern OS schedulers schedule threads, not processes.


                            “Process scheduling”
       is a conceptual abstraction, not a separate mechanism.



                   A process is:
 an execution environment （resource pool）                          Threads:
                  address space                                 execute code
                         files
                       signals                                   enter kernel
        containing one or more threads                               block
                                                                   wake up
             not a schedulable entity.                         get scheduled

         Isolation is at the process level
```

## Slide 4

```text
Scheduling                                                                 Operating Systems


     The schedulable entity is a thread
(kernel thread / task / lightweight process).

                    Each thread:

                  Runs on a hart

                    Has its own
  registers, kernel stack, execution state

     Knows which process it belongs to
              (resource container)                                       xv6

                  This is true for:                          One thread per process
                                                         Scheduler    “looks like” process
                          xv6                                        scheduling
                        Linux
                      Windows                             But the unit being   switched   is
                                                          still a thread (kernel context)
                         BSD
```

## Slide 5

```text
Scheduling                                                                       Operating Systems


        Why xv6 is not enough                                         Through xv6,
                                                                     we understand
 xv6: round-robin, single run queue                            scheduling mechanism:

               No priorities                                 context switch code (swtch)
                                                                       run queue
           No fairness notion                                        timer interrupt
                                                                 yield / sleep / wakeup
       No scalability awareness                                       trap handling

                                                                  But we are missing:
   No cache / topology awareness
                                                                   Scheduling Policy
        No real-time guarantees
                                                                    which concerns:

                                                      Which runnable thread should run next
                                                                How long it should run
                                                               Whether fairness matters
                                                               Whether latency matters
                                                               Whether priority matters
                                                           Whether cache locality matters
```

## Slide 6

```text
Scheduling                                                               Operating Systems




    xv6-style thinking (mechanism-focused)

          Runnable? →        Yes   →   Run next





                                Modern OS thinking (policy-focused)

                                               Runnable?

                                             → What priority?
                                    →  How much CPU has it used?
                                            → Is it interactive?
                                    → Which CPU cache does it fit?
                                    →  Is there a real-time deadline?
                                         → Is another CPU idle?
```

## Slide 7

```text
Scheduling                                                              Operating Systems

 Terminology
```

## Slide 8

```text
Scheduling                                                     Operating Systems





                       Context switch overhead

                        Cost of saving registers
                                      +
    Cost of scheduler determining the next process to run
                                      +
                      Cost of restoring registers
                                      +
              Various caches may need to be flushed
                           (L1, L2, L3, TLB, ...)
```

## Slide 9

```text
Scheduling                                                          Operating Systems



        Core objectives

            Fairness
          Throughput
 Latency / responsiveness
        CPU utilization
    Starvation avoidance
```

## Slide 10

```text
Scheduling                                                                                          Operating Systems


                                   Two different triggers of scheduling







                Voluntary Blocking                                                 Involuntary Blocking
                 (Non-preemptive)                                                       (Preemptive)

                  Who initiates it?                                                   Who initiates it?
      The currently running thread itself                                       The hardware timer/other
                (in kernel context).                                            (asynchronous interrupt).
                         Why?                                                                Why?
The thread cannot make progress and must                                 Enforce fairness: no thread may run
                wait for a condition                                                        forever.
           (I/O, pipe, child exit, time).                            State transition: RUNNING -> RUNNABLE
 State transition:  RUNNING -> SLEEPING
                                                                             Can it run again immediately?
         Can it run immediately again?                                         Yes — it remains runnable.
       No — it must be explicitly woken                                                                                  timer interrupt handler calls:    yield();
                    via wakeup().
```

## Slide 11

```text
Scheduling                                                                  Operating Systems



                                           Priority
```

## Slide 12

```text
Scheduling                                                            Operating Systems


                               Priority function input



                             attained service time

                              real-time in system

                               total service time

                                     deadline

                                    periodicity

                                external priority

                            memory requirements
```

## Slide 13

```text
Scheduling                                                    Operating Systems


                                 Priority






                        attained service time

                   is the amount of time during
             which the process was using the CPU
       since its arrival and is often the most important
                     parameter for scheduling.
```

## Slide 14

```text
Scheduling                                                    Operating Systems


                                 Priority


                         real time in system

           is the actual time the process has spent
                  in the system since its arrival.

       It is composed of the attained service time and
       the waiting time during which the process was
                       ready but not running.

       Note that the attained service time increases at
           the rate of the actual (real) time when the
       process is running and remains constant when
               the process is waiting for the CPU.
```

## Slide 15

```text
Scheduling                                                                         a


                                             Priority


                                     The total service time

            is the CPU time the process will consume during its lifetime,
                                      ( before it departs ) .

         at the point the process departs, its total service time is equal to
                                  the attained service time.

           Some systems assume that the total service time is known in
       advance. This is usually true of repetitive service tasks, such as the
                    processing of a stream of signals or messages.

         In other cases, the total service time frequently can be predicted
                  based on the most recent behavior of the process:

          Since processes tend to remain processor-bound or I/O-bound
            for significant periods of time, we can devise a function that
       predicts the next total service time period based on the most recent
                                              periods
```

## Slide 16

```text
Scheduling                                                                      a


                                           Priority



                                           Deadline

            is a point in real time by which a task must be completed.

          As an extreme example, the results of a weather forecast are
             useless after the date for which the prediction is made.

           Deadlines are specially important in real-time applications.
        For example, the transmission of a video or audio signal can be
          seriously distorted if deadlines for most data packets are not
                                              met.

         In safety- and mission-critical applications, computations that
            are not completed in time may lead to extremely costly or
                                life-threatening situations.
            In these systems, priorities often correspond directly with
            deadlines; the shorter the deadline, the higher the priority
```

## Slide 17

```text
Priority


                                         Periodicity

                       refers to the repetition of a computation.

In many real-time applications, the same, usually very short, computation is
                               repeated at fixed intervals.

 Many other applications that are not normally considered as real-time also
                                 use periodic processes.

One example is an email system that checks periodically for mail or message
                                           arrivals.

   Even event-driven processes that are not periodic are often handled as
        periodic processes by polling for events at a suitable frequency.

    A fixed period automatically imposes an implicit deadline: The current
   computation must be completed before the start of the next period. The
                       converse, however, is not generally true.

  Deadlines may be imposed for reasons other than periodic computations
```

## Slide 18

```text
Priority



                                 External priorities

                 are values assigned explicitly to processes.

They may be used as base priorities to differentiate between different
                              classes of processes.

One common policy, for example, is to assign the highest priorities to
real-time processes, the next lower priorities to interactive processes,
                      and the lowest to batch processes.

     In a business organization, the priority of a user process may
      correspond to the user’s position in the company hierarchy.

Historically, when computers were more expensive than people, a cost
 based on the type of resources employed was frequently associated
                              with each priority level.
```

## Slide 19

```text
Priority






                    The memory requirements

  specify the amount of main memory the process needs
                           for its execution.

 This serves as a major scheduling criterion in batch OSs.

In interactive systems, it is also important since it is a good
 measure of swapping overhead.  However, other criteria,
typically those related to time, are usually more important.
```

## Slide 20

```text
Priority




                                         In addition

the priority function can consider various attributes of the system as a whole.
   Notably, the overall system load is an important parameter, since it has a
                        significant effect on system response.

     Under heavy load, some schedulers would attempt to maintain good
 response to high-priority processes by discriminating more strongly among
                                    external priorities.

Thus, the distinction between the “rich” and the “poor” becomes even more
                   pronounced  when resources become scarce.

            Others take a more egalitarian point of view by attempting
to increase the overall system throughput, for example by reducing swapping
  overhead through the use of larger quantum sizes. This idea improves the
                  general conditions for all participants uniformly.
```

## Slide 21

```text
Priority





            Conflicts among processes with equal priority.

        If the likelihood of two or more processes having the
           same priority is low because of the nature of the
    priority function, the choice could be performed at random.

In quantum-oriented scheduling algorithms, processes with equal
           highest priority are usually assigned quanta in a
                       cyclic (round-robin) manner.

         In most other schemes, the chronological ordering
           of process arrivals serves as the arbitration rule;
 i.e., processes with equal priority are dispatched on a FIFO basis.
```

## Slide 22

```text
Scheduling                                                  Operating Systems



                   Basic scheduling algorithms

                       First In First Out (FIFO)
               (aka FirstComeFirstServed - FCFS)

                      Shortest Job First (SJF)

                   Earliest Deadline First (EDF)

                          Round Robin (RR)

             Shortest Remaining Time First (SRTF)
```

## Slide 23

```text
Scheduling - FIFO                                                                              Operating Systems



                                                                                              Advantage
                                                                                                Simple
                                                                                            Low-overhead
                                                                                            No Starvation

                                                                                            Disadvantage
                                                                                   Average turnaround time very
                                                                                     sensitive to schedule order

                                                                                          Not responsive to
                                                                                           interactive jobs
```

## Slide 24

```text
Scheduling - SJF                                                                       Operating Systems













                                                                                 Advantage
                                                                    Optimal average turnaround time


                                                                               Disadvantage
                                                                         High variance (worst) in
                                                                              turnaround time
                                                                          (some finish very early,
                                                                               some very late
                                                                      Low variance → jobs finish at
                                                                          roughly similar times)

                                                                   Needs estimate of execution time

                                                                       Heavily favoring short jobs

                                                                           Can starve long jobs
```

## Slide 25

```text
Scheduling - EDF                                                             Operating Systems


                                                                                Proof:
             Earliest Deadline First
    Schedule in order of earliest deadline                            If there is a schedule
                                                            whose jobs list meet all deadlines,
  EDF is the optimal scheduling policy for                          let S be such schedule.
     meeting deadlines on a single CPU.
                                                          Let j1 and j2 be two neighboring jobs
                It can be used as                                                 in S
              an golden standard
            for whether a schedule                            so that j1.deadline > j2.deadline
               can meet deadline
                                                                if both j1 and j2 finish before
                                                                          an earlier time,
                                                         of course they can finish before a later
      Doesn’t mean use EDF can always                                            time.
               meet all deadline.
                                                            Let S’ be S with j1 and j2 switched
            It is just optimal policy.
If any policy can meet all deadlines, EDF can.                   S’ also meets all deadlines

      If EDF can not meet all deadlines,                       Repeat until all jobs are sorted
                 no policy can.
                                                                  Resulting schedule is EDF
```

## Slide 26

```text
Scheduling - EDF                                             Operating Systems










                            Advantage
                  Meets deadlines if possible
                        Free of starvation


                          Disadvantage
    Cannot decide when to run jobs without deadlines
```

## Slide 27

```text
Operating Systems










                                 Note
FIFO, SJF, EDF are not using time slice in its original form.
Which means, the whole job is executed before executing
            next job (the scheduled order is fixed)
```

## Slide 28

```text
Scheduling - RR                                       Operating Systems

















                   round-robin tournament
```

## Slide 29

```text
Scheduling - RR                                             Operating Systems





                                Use time-slice
                 Each job allowed to run for a quantum
               quantum=some configured period of time
    Context is switched (at the latest) at the end of the quantum
                                （preemption)
Next job is the one on the run queue that hasn’t run  for the longest
                               amount of time




                      What is a good quantum size?
                          Too long: become FIFO
           Too short: time is wasted on context switching
                              Typical quantum:
                   about 100x cost of context switch
```

## Slide 30

```text
Scheduling - RR                                              Operating Systems
```

## Slide 31

```text
Scheduling - RR                                              Operating Systems
```

## Slide 32

```text
Scheduling - RR                                                                        Operating Systems







      mixture of
   one I/O-bound
          and
   two CPU-bound


















                     Advantage
                    No starvation
             Can reduce response time

                    Disadvantage
              Context switch overhead
             Mix of I/O and CPU bound

Bad average turnaround time for equal length jobs
```

## Slide 33

```text
Scheduling - SRTF                                             Operating Systems



   SRTF always schedules the process with the smallest remaining
                                 execution time.

          Each process has an estimate of remaining CPU time

The scheduler always runs the process with the least remaining time

If a new process arrives with a shorter remaining time, it preempts the
                           currently running process




     It is provably optimal for minimizing average turnaround time

                     Serves as a theoretical benchmark

                   Rarely used directly in real OS kernels
```

## Slide 34

```text
Scheduling - SRTF                                            Operating Systems








         Why real OS don’t implement pure SRTF

             - Exact remaining time is unknown

           - CPU burst lengths are unpredictable

    - Requires frequent preemption, lead to overhead

                    - Can cause starvation
```

## Slide 35

```text
Multiple Scheduling Queues (MLFQ)                           Operating Systems



                            Motivation

Different processes have different CPU usage patterns

                  Interactive → short bursts
               Batch / compute → long bursts
       One single queue + one policy cannot fit all




       Process can be classified into different groups

                       - System processes
                     - Interactive processes
                 - Interactive editing processes
                        - Batch processes
                       - Student processes
```

## Slide 36

```text
Multiple Scheduling Queues (MLFQ)                             Operating Systems








                                     Core Idea

Partition the ready queue into several separate queues, each with

                                - different priority
                            - different time quantum

    Process are assigned to one queue based on different classes.

      Scheduler always picks from the highest non-empty queue
```

## Slide 37

```text
Multiple Scheduling Queues (MLFQ)                                   Operating Systems



                 Scheduling Rules (Classic MLFQ)

            Higher priority queues are scheduled first

                Round-robin within the same queue

           If a process uses up its time slice → demote

        If a process yields early (I/O) → keep or promote

           Periodic priority boost to prevent starvation




                    Multiple scheduling queues
     use feedback to dynamically adjust process priority,
                  favoring interactive workloads
   while still allowing CPU-bound jobs to make progress.
```
