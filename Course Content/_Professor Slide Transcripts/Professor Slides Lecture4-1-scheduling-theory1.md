# Professor Slides Lecture4-1-scheduling-theory1

> [!warning] Reference transcript, not confirmation of lecture coverage
> Extracted text from the professor’s slide PDF. Page numbers are PDF page numbers. Extraction can omit diagrams or distort reading order and formulas. Refer to the original PDF for full content. A slide’s presence does not establish that it was discussed. Prioritize the topic files’ lecture records.

**Original PDF:** `/Users/jossean/Documents/CSEN 283/Professor Slides Lecture4-1-scheduling-theory1.pdf`

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
（theory）
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
CPU Scheduling                                                             Operating Systems



                 There are three issues for scheduler




           When                            How                             Who






                                                                        policy to select a
      when the                     how to bind to CPU                 process to execute
      scheduler
  should be invoked                   context switch                       Scheduling
                                                                            algorithm
```

## Slide 6

```text
CPU Scheduling                                                    Operating Systems


                                                                      When
```

## Slide 7

```text
CPU Scheduling                                                         Operating Systems


                                                           When


 Selection happens when any of  these events happens

                                  new task created

                             blocked task is awakened

                                 task is terminated

                             I/O interrupt, wait for I/O

                        timer interrupt (time slice expired)

                            task priority changed (later)
```

## Slide 8

```text
CPU Scheduling                                                           Operating Systems


 When any of those events happen, the following will happen:

                    Current running process interrupted

                            Control switch to kernel

                                   handle event

                            process status changed
                      (including new processes created)

                              ready queue changed                      scheduling happens
                                                                              before
                                                                             returning
                                                                        back to user mode

                                        scheduler
                                 select a process from
                    ready queue based on scheduling algorithm
```

## Slide 9

```text
CPU Scheduling                                                             Operating Systems

                                                  ready queue















          Scheduler                                         Ready queue
      is responsible for                            could be a separate queue
       taking the CPU                                              or
      away from a task                                       logic queue
              and                                   xv6 is logical ready queue
 give it to another task in                    (scan one process list  “runqueue”
         ready queue                                 to pick RUNNABLE one)
```

## Slide 10

```text
CPU Scheduling                                                                  Operating Systems

                                        Context Switch

        Context switch is
      always switch thread                                             Save
       (execution context)
                                                                 CPU registers
                                                            PC (program counter)
       if thread belongs to one process                        SP (stack pointer)
          only switch register/kstack                             kernel stack
                                                                scheduling state
         not address space/page table                                 status,
                                                                     registers
               (lightweight switch)


                                                    if thread belongs to diﬀerent process,
how to know when to switch a thread                        then heavyweight switch:
and when to switch the whole process                             register/kstack
        when selecting a thread,                           address space/page table
 check if it belong to current process                               TLB ﬂush
```

## Slide 11

```text
CPU Scheduling                                                                          Operating Systems

                                                                                  Context Switch

   Process switch:

         Save
  Program Counter
        status,
       registers

  update PCB of P1

insert P1 into queue

P2.status = ‘running’

 restore PCB of P2






 Cost of context switch

    depend on types
        of switch
```

## Slide 12

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

## Slide 13

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

## Slide 14

```text
Scheduling                                                      Operating Systems



          xv6-style thinking (mechanism-focused)

                Runnable? →      Yes   →  Run next



                      Modern OS thinking (policy-focused)

                                      Runnable?

                                   →  What priority?
                           → How much CPU has it used?
                                  →  Is it interactive?
                          →  Which CPU cache does it fit?
                          →  Is there a real-time deadline?
                               →  Is another CPU idle?
```

## Slide 15

```text
Scheduling Algorithms                                          Operating Systems











                                     Priority
                                        and
                                      Queue
```

## Slide 16

```text
Scheduling Algorithms                                                                                      Operating Systems
                                                                                                                 Priority

                     To   deciding        which      process        should       be   running       next,
                    most      of   the   scheduling          are   based       on   the    concept        of
                                                   process        priority



        process      priority    is a                  Priority   P for  process    p  is de ﬁned   by  priority  function   :
                  number                                                         P  =  priority (p)
          for  each     process                                          P  can  be  static  or  dynamic



                 static     priority                                                  dynamic         priority

             remains         constant                                                recompute            every
  for   the    life  spanof        the   process                          time     the    scheduler          is  invoked




            Typically,     the   priorities    are   positive     integers     (it could    be   negative      too)
                      with   lower    numbers        treated    as   higher    or  lower     priorities,
                                      depending        on   the   implementation.
```

## Slide 17

```text
CPU Scheduling                                                     Operating Systems

                                                                     Priority

                     The scheduler is invoked either
                          periodically (time slice)
                                        or
                          as the results of events
      that can aﬀect the priorities of the existing processes.

        The role of the scheduler is then to guarantee that,
               at the time it made scheduling decision,
              np highest-priority processes are running,
               (np is the number of harts in the system)

                               In other words,
          the priority of any running process must always
  be greater than or equal to that of any ready_a (ready active)
                                    process.
```

## Slide 18

```text
CPU Scheduling                                                       Operating Systems


        At certain points in time, the scheduler evaluates the
                                priority function
                  for all active processes in the system.


                               For each process,
                     the priority function yields a value,
                      referred to as the current priority.
     The processes with the highest priorities are assigned to the
                                 existing CPUs.


         The priority function may be evaluated whenever the
         RUNNABLE（ready）thread/process group changes.
But whether a higher-priority process can immediately preempt the
                     running one depends on whether
                             the scheduler (policy)
                      is preemptive or non-preemptive
```

## Slide 19

```text
Scheduling                                                                                          Operating Systems


                                      Two different triggers of scheduling







                Voluntary Blocking                                               Involuntary Blocking
                 (Non-preemptive)                                                     (Preemptive)

                  Who initiates it?                                                 Who initiates it?
      The currently running thread itself                                     The hardware timer/other
                (in kernel context).                                          (asynchronous interrupt).
                         Why?                                                              Why?
The thread cannot make progress and must                         Enforce fairness: no thread may run forever.
                wait for a condition
           (I/O, pipe, child exit, time).                          State transition: RUNNING -> RUNNABLE

 State transition:  RUNNING -> SLEEPING                                    Can it run again immediately?
                                                                             Yes — it remains runnable.
         Can it run immediately again?
       No — it must be explicitly woken                                                                                  timer interrupt handler calls:    yield();
                    via wakeup().
```

## Slide 20

```text
CPU Scheduling                                                                                  Operating Systems


                     preemptive                                                    non-preemptive
  permits a currently running process                                a process, once started, is always
to be stopped and the CPU assigned to                                    allowed to run when logically
  another process with higher priority.                                                  possible




   decision to attempt a preemption may be made when                        scheduling decisions are made
                   a new process arrives or                                        only when a process
  when an existing process is awakened as a result of a                                   terminates
                    message or interrupt.                                                       or
                                                                                         blocks itself
      Preemption also may be performed periodically                                    (sleep/ IO wait),
           each time a process has executed for                                                 or
                  a period of q milliseconds.                                          voluntarily yield
  In this case, the algorithm is called quantum-oriented,
                  with q as its quantum size.

    Preemption also may occur whenever the priority of
        a ready process rises above the priority of a
                  currently running process.
 This could occur in systems that dynamically change the
          relative priorities of existing processes.
```

## Slide 21

```text
CPU Scheduling                                                         Operating Systems




          At the algorithm level, we have diﬀerent policies


                                Examples:

                    SJF → non-preemptive version
                  SRTF → preemptive version of SJF
            Round Robin → requires preemption to exist




                        Some scheduling policies come in
                  both preemptive and non-preemptive forms,

                   while others inherently require preemption.
              for example, RR has to preempt because of timer).
```

## Slide 22

```text
CPU Scheduling                                                                   Operating Systems




     A system chooses preemptive or non-preemptive scheduling based on
    responsiveness needs, fairness goals, and kernel complexity trade-oﬀs.



    Preemptive scheduler — allow                              Non-preemptive scheduler
                preemption                                     chosen when you want:
                                                                Simpler kernel design
        Chosen when you want:                                       Lower overhead
   Good interactive responsiveness                          Easier correctness reasoning
            Fair CPU sharing                             Deterministic execution segments
           Bounded wait time                                   Why system chooses it:
  Real-time guarantees (with priority                  No timer hardware (early systems, tiny
               preemption)                                             embedded)
                                                      Critical sections must not be interrupted
                 Typical in:                           Cooperative task model is acceptable
General-purpose OS (Linux, Windows,                                    Typical in:
                  macOS)                                   Very small embedded systems
           Interactive systems                                     Early OS designs
         Time-sharing systems                               Teaching kernels (sometimes)
                                                                 Cooperative runtimes
```

## Slide 23

```text
CPU Scheduling                                                                     Operating Systems

                                                                  policy


   Preemptive scheduling policies are                      Although they are more economical,
                    usually                            nonpreemptive algorithms are usually not
              more costly than                                            adequate
            nonpreemptive ones                                                 in
   because of process switching times,                                   real-time or
the additional logic in the scheduler itself,                       time-shared systems.
 and the possible swapping of programs                 (which need to permits a currently running
                   and data                                process to be stopped and the CPU
     between primary and secondary                      assigned to another process with higher
                  memories.                                               priority.)d




                  Many systems use a combination of both strategies.
                                            For example,
          a critical part of an OS process, such as a kernel routine, may be
                                           nonpreemptive,
                      whereas most user processes are preemptive.
```

## Slide 24

```text
CPU Scheduling                                                                Operating Systems


                         preemptive / non-preemptive kernel

              Can the kernel itself be interrupted while it is running?




                                                             Non-preemptive kernel
           Preemptive kernel                          Once execution enters the kernel,
Kernel execution can be interrupted                       it runs until it blocks, yields,
 by a timer or higher-priority event                        or returns to user mode
     A context switch may occur
       while inside kernel code                          No forced preemption inside
                                                                    kernel code
    Requires ﬁne-grained locking
                                                                  Simpler design
        Used by modern OSes                                  Used in early Unix and
      (Linux, Windows, macOS)                                    teaching kernels
```

## Slide 25

```text
CPU Scheduling                                             Operating Systems




                               Priority queue implementation



 A priority queue combines several simple queues into
                  one common data structure.

              In addition to its application data,
     each queue element contains a priority number
         associated with the corresponding object
```

## Slide 26

```text
CPU Scheduling                                                                              Operating Systems

                                                Priority queue                                point to
                                               implementation                     processes at that priority
                Binary heap                                                                     level
              works well with
                  priorities
             that are not ﬁxed
              but range over
               large domain
                of numbers




      Binary heap
 is a complete binary
 tree where the value
(priority number in our
          case)
   at the root of any
  subtree is always
smaller than the value
  of any node in the
        subtree.
       (min heap)                                                                         This is an example of a
                                                                                             binary heap with 10
                                                                                          diﬀerent priority values
```

## Slide 27

```text
CPU Scheduling                                                                            Operating Systems

















     A binary heap is also suitable for kernel-level priority queues because it can be
                                 implemented eﬃciently as an array.

                           The tree root is placed at index 1 of the array.
         The left and right subtrees are stored recursively using the following rule:

                                              For any node i:
                                   Its left subtree starts at index 2i,
                                Its right subtree starts at index 2i + 1.
```

## Slide 28

```text
Operating Systems














Scheduling Objectives
```

## Slide 29

```text
Scheduling                                                              Operating Systems

                  Terminology
```

## Slide 30

```text
Scheduling policy consideration                               Operating Systems




                                     Time taken by process on CPU
Burst Time                                     (CPU time)

                                    (Total time = CPU time + I/O time)



Arrival Time                       The time when a process enter
                                              ready state




 Exit Time                       The time when a process complete
                                               execution



         Turn-around time = exit time - arrival time
 = burst time + waiting time (CPU time + non-CPU time)
```

## Slide 31

```text
CPU Scheduling                                                                       Operating Systems



                                                            A task is generally alternating
                                                                 CPU use (CPU burst)
                                                                 and IO use (I/O burst)

                                                           Different process have different
                                                            pattern of using CPU and I/O
```

## Slide 32

```text
Scheduling                                                        Operating Systems




























                                Scheduler tend to give I/O-bound preference.
                                 Because they don’t occupy CPU that much
```

## Slide 33

```text
Scheduling policy consideration                                      Operating Systems



CPU Utilization                                  Busy time / total time

                                                  the busier the better



 Waiting Time                                     Time in ready queue

                                                 the shorter the better




                                          From request to run to ﬁrst response
Response Time




request to run
 (submission                    the shorter the better                  ﬁrst response
 ready state)
```

## Slide 34

```text
CPU Scheduling                                               Operating Systems

       Scheduling policy consideration



    Throughput                                       Response Time




      Turnaround Time                         CPU Utilization




                          Waiting Time


                                                       more ….
```

## Slide 35

```text
Scheduling                                                                Operating Systems
                more ….


     Core objectives

          Fairness
        Throughput
Latency / responsiveness
      CPU utilization
  Starvation avoidance





                                     Job Categories
```

## Slide 36

```text
Scheduling policy consideration                                       Operating Systems


 Throughput                              Number of processes executed  / unit time

                                           eﬃciency of CPU, the bigger the better



Turnaround Time                            The time required for a process to complete








        request to run (submission)                                    ﬁnish execution
      but most of the time has to wait
          before actually executed


                        the shorter the better
```

## Slide 37

```text
Scheduling                                                                      Operating Systems


Values that aﬀect Priority function:  P = priority(p)


                                                  This function may use
                                          diﬀerent attributes of the process or
                                                  the system as a whole
                                                  to generate the value.

                                     The most common attributes applicable to a
                                            given process are the following:



 attained services                real time in system



                   total service time                        deadline




                   periodicity                  external priorities           memory requirement
```

## Slide 38

```text
Scheduling                                                                          Operating Systems


                                               Priority


                                                           attained service time

attained service time                                  the amount of time during
                                                     which the process was using
  real-time in system                         the CPU since its arrival and is often the
                                             most important parameter for scheduling.
   total service time                                   (accumulative CPU burst)

         deadline
        periodicity                                                    CPU Burst 1:  5 ms
                                            Example                             I/O
                                        a process has 3                CPU Burst 2:  8 ms
    external priority                     CPU Bursts                            I/O
                                                                       CPU Burst 3:  4 ms
memory requirements

                                                  after first Burst，Attained Service Time = 5 ms
                                               after second Burst，Attained Service Time = 13 ms
                                                 after third Burst，Attained Service Time = 17 ms
```

## Slide 39

```text
Scheduling                                                                Operating Systems


                                        Priority




   attained service time                              real time in system
                                                      （turnaround time）
    real-time in system
                                             actual time the process has spent
      total service time                       in the system since its arrival.

           deadline                                 T.completion - T.arrival
                                                                 or
          periodicity                               T.cpu + T.i/o + T.waiting

      external priority

  memory requirements
```

## Slide 40

```text
Scheduling

                                                 Priority




                                                               The total service time
                                                                            .
 attained service time
                                                         at the point the process departs,
  real-time in system                               T.total_service = T.attained_service_time

    total service time                        Some systems assume that the total service time
                                                                is known in advance.

          deadline                              This is usually true of repetitive service tasks,
                                        such as the processing of a stream of signals or messages.
        periodicity                        In other cases, the total service time frequently can be
                                        predicted based on the most recent behavior of the process
     external priority
                                                         Since processes tend to remain
memory requirements                                       processor-bound or I/O-bound
                                                          for significant periods of time,
                                        we can devise a function that predicts the next total service
                                                 time period based on the most recent periods
```

## Slide 41

```text
Scheduling

                                                Priority




                                                                     Deadline
attained service time
                                      is a point in real time by which a task must be completed.

 real-time in system                As an extreme example, the results of a weather forecast are
                                        useless after the date for which the prediction is made.
  total service time
                                      Deadlines are specially important in real-time applications.
        deadline                                                  For example,
                                     the transmission of a video or audio signal can be seriously
       periodicity                     distorted if deadlines for most data packets are not met.

                                                In safety and mission-critical applications,
   external priority                   computations that are not completed in time may lead to
                                             extremely costly or life-threatening situations.
        memory
     requirements                             In these systems, priorities often correspond
                                                            directly with deadlines:
                                             the shorter the deadline, the higher the priority
```

## Slide 42

```text
Priority


                                                                       Periodicity

                                                    refers to the repetition of a computation.

attained service time                                     In many real-time applications,
                                                            the same, usually very short,
                                                   computation is repeated at fixed intervals.
 real-time in system
                                          Many other applications that are not normally considered
  total service time                                as real-time also use periodic processes.

                                                                    One example is
         deadline                       an email system that checks periodically for mail or message
                                                                        arrivals.
       periodicity
                                               Even event-driven processes that are not periodic
   external priority                               are often handled as periodic processes by
                                                    polling for events at a suitable frequency.
         memory                           A fixed period automatically imposes an implicit deadline:
     requirements                                 The current computation must be completed
                                                        before the start of the next period.
                                                  The converse, however, is not generally true.

                                               Deadlines may be imposed for reasons other than
                                                               periodic computations
```

## Slide 43

```text
Priority



                                                            External priorities

                                           are values assigned explicitly to processes.
attained service time
                                       They may be used as base priorities to differentiate
 real-time in system                          between different classes of processes.

  total service time                              One common policy, for example,
                                   is to assign the highest priorities to real-time processes,
        deadline                                      the next lower priorities to
                                                         interactive processes,
      periodicity                                and the lowest to batch processes.

   external priority                                  In a business organization,
                                      the priority of a user process may correspond to the
                                             user’s position in the company hierarchy.
        memory
     requirements                                               Historically,
                                       when computers were more expensive than people,
                                      a cost based on the type of resources employed was
                                          frequently associated with each priority level.
```

## Slide 44

```text
Priority




                                             The memory requirements
attained service time
                               specify the amount of main memory the process
 real-time in system                           needs for its execution.

  total service time             This serves was a major scheduling criterion
                                                      in batch OSs.
       deadline
                                                In interactive systems,
      periodicity              it is also important since it is a good measure of
                                                 swapping overhead.
  external priority
                                               However, other criteria,
       memory                              typically those related to time,
    requirements                            are usually more important.
```

## Slide 45

```text
Priority


                                      In addition

                       the priority function can consider
                 various attributes of the system as a whole.

       Notably, the overall system load is an important parameter,
            since it has a significant effect on system response.

  Under heavy load, some schedulers would attempt to maintain good
          response to high-priority processes by discriminating
                   more strongly among external priorities.

    Thus, the distinction between the “rich” and the “poor” becomes
         even more pronounced when resources become scarce.

        Others take a more egalitarian point of view by attempting
                  to increase the overall system throughput,
                for example by reducing swapping overhead
                   through the use of larger quantum sizes.
This idea improves the general conditions for all participants uniformly.
```

## Slide 46

```text
Priority




            Conflicts among processes with equal priority.

        If the likelihood of two or more processes having the
           same priority is low because of the nature of the
    priority function, the choice could be performed at random.

              In quantum-oriented scheduling algorithms,
           processes with equal highest priority are usually
                            assigned quanta in a
                        cyclic (round-robin) manner.

                           In most other schemes,
                 the chronological ordering of process
                  arrivals serves as the arbitration rule;

 i.e., processes with equal priority are dispatched on a FIFO basis.
```

## Slide 47

```text
Operating Systems
Basic scheduling algorithms




            First In First Out (FIFO)
    (aka FirstComeFirstServed - FCFS)

           Shortest Job First (SJF)

         Earliest Deadline First (EDF)

               Round Robin (RR)

  Shortest Remaining Time First (SRTF)
```

## Slide 48

```text
Operating Systems





    Scheduling
- First In First Out
```

## Slide 49

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

## Slide 50

```text
Operating Systems




     Scheduling
- Shortest Job First
```

## Slide 51

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

## Slide 52

```text
Operating Systems







          Scheduling -
Shortest Remaining Time First
```

## Slide 53

```text
Scheduling - SRTF                                             Operating Systems



  SRTF always schedules the process with the smallest remaining
                                 execution time.

         Each process has an estimate of remaining CPU time

The scheduler always runs the process with the least remaining time

        If a new process arrives with a shorter remaining time,
                it preempts the currently running process




    It is provably optimal for minimizing average turnaround time

                     Serves as a theoretical benchmark

                  Rarely used directly in real OS kernels
```

## Slide 54

```text
Scheduling - SRTF                                   Operating Systems






     Why real OS don’t implement pure SRTF

         - Exact remaining time is unknown

       - CPU burst lengths are unpredictable

- Requires frequent preemption, lead to overhead

                - Can cause starvation
```

## Slide 55

```text
Scheduling - EDF                                                            Operating Systems

                                                                             Proof:
          Earliest Deadline First
                                                                   If there is a schedule
 Schedule in order of earliest deadline                  whose jobs list meet all deadlines,
                                                                 let S be such schedule.
EDF is the optimal scheduling policy for
  meeting deadlines on a single CPU.                   Let j1 and j2 be two neighboring jobs

             It can be used as                                                 in S
           an golden standard                              so that j1.deadline > j2.deadline
         for whether a schedule
            can meet deadline                                if both j1 and j2 finish before
                                                                       an earlier time,
                                                                of course they can finish
   Doesn’t mean use EDF can always                                  before a later time.
            meet all deadline.
                                                         Let S’ be S with j1 and j2 switched
         It is just optimal policy.
  If any policy can meet all deadlines,                        S’ also meets all deadlines
                 EDF can.
                                                            Repeat until all jobs are sorted
   If EDF can not meet all deadlines,
              no policy can.                                   Resulting schedule is EDF
```

## Slide 56

```text
Scheduling - EDF                                              Operating Systems


                             Advantage
                   Meets deadlines if possible
                         Free of starvation


                           Disadvantage
    Cannot decide when to run jobs without deadlines




                                  Note
FIFO, EDF, SJF are NOT using time slice in its original form.

   But doesn’t mean execution order cannot be changed,
    For preemptive like EDF,  an earlier deadline job can
                   preempt a late deadline job.
      Even for non-preemptive policy, for example, SJF,
                       whenever CPU is idle,
         it will pick a shortest job from Ready Queue
```

## Slide 57

```text
Operating Systems







Scheduling -
 Round Robin
```

## Slide 58

```text
Scheduling - RR                                               Operating Systems




















RR tournament:  Each team play everyone plays against everyone else



 RRScheduling: everyone gets a turn to execute for time slice length
```

## Slide 59

```text
Scheduling - RR                                                Operating Systems



                                 Use time-slice

                  Each job allowed to run for a quantum

                quantum=some configured period of time

    Context is switched (at the latest) at the end of the quantum
                                 （preemption)
Next job is the one on the run queue that hasn’t run  for the longest
                                 amount of time



                       What is a good quantum size?

                           Too long   : become FIFO

            Too short    : time is wasted on context switching

         Typical quantum:       about 100x cost of context switch
```

## Slide 60

```text
Scheduling - RR                                              Operating Systems
```

## Slide 61

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

## Slide 62

```text
Operating Systems






Multiple Scheduling Queues (MLFQ)
```

## Slide 63

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

## Slide 64

```text
Multiple Scheduling Queues (MLFQ)                            Operating Systems







                                Core Idea

  Partition the ready queue into several separate queues,
                                each with

                           - different priority
                       - different time quantum

Process are assigned to one queue based on different classes.

  Scheduler always picks from the highest non-empty queue
```

## Slide 65

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
