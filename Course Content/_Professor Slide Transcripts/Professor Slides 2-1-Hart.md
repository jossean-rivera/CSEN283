# Professor Slides 2-1-Hart

> [!warning] Reference transcript, not confirmation of lecture coverage
> Extracted text from the professor’s slide PDF. Page numbers are PDF page numbers. Extraction can omit diagrams or distort reading order and formulas. Refer to the original PDF for full content. A slide’s presence does not establish that it was discussed. Prioritize the topic files’ lecture records.

Note: Sleep-function calls were rewritten as descriptions for Thea compatibility. Code examples containing those descriptions are not executable; consult the original PDF for exact syntax.

**Original PDF:** `/Users/jossean/Documents/CSEN 283/Professor Slides 2-1-Hart.pdf`

**Pages with no extracted text:** None; diagrams may still be omitted.

## Slide 1

```text
Operating Systems
                     CSEN 283
                    Yuan Wang
```

## Slide 2

```text
Operating Systems










Hart - Hardware Thread
```

## Slide 3

```text
Operating Systems
RISC-V architecture


            This is an RISC-V ISA implementation, and execution pipeline
```

## Slide 4

```text
Operating Systems
RISC-V architecture



                                                                  This one execution pipeline is called
                                                                                      HART
                                                                              (Hardware Thread)
                                                                                        or
                                                                                  Logical CPU







                      L1 cache memory                                                          L1 cache memory
                      （private to hart）                                                        （private to hart）











      HART
```

## Slide 5

```text
in a real system,                                                                      Operating Systems

there are more than 1 hart                                             each hart has its private

                                                                  general registers，PC and CSRs
                                                                                 (state)
```

## Slide 6

```text
Operating Systems

                                                                                                         There could be different
                                                                                                         ways to implement hart


                                                                                                      physically separate pipeline

                                                                                                Hart 0: IF -> ID -> EX -> MEM -> WB
                                                                                                Hart 1: IF -> ID -> EX -> MEM -> WB

                                                                                                         each has its own state


                                                                                                           partially shared pipeline

                                                                                                                  shared Fetch
                                                                                                                          |
                                                                                                                 shared Decode
                                                            shared pipeline                                          /           \
             Note：                                       one physical pipeline                              Hart 0 EX       Hart 1 EX
hart is architectural concept                   Hart: IF -> ID -> EX -> MEM -> WB                                 |                   |
 not necessarily physically                       but separate architectural state                              MEM          MEM
      separate pipeline.                             Hart 0: PC, x0-x31, CSR...                                   |                   |
                                                     Hart 1: PC, x0-x31, CSR…                                   WB              WB
     RISC-V require that
each hart have its own state                           each cycle, switch hart                                still, separate state
    (registers, PC, CSRs),                            cycle 1: Hart 0 instruction
         so that it is an                             cycle 2: Hart 1 instruction                                 note：state
   independent execution                              cycle 3: Hart 0 instruction                        can be physically two set
                                                      cycle 4: Hart 1 instruction                          or architectural two set
```

## Slide 7

```text
Operating Systems
if we put memory together in SoC (System on Chip)








   L1 cache                                                           L1 cache













    L1 cache                                                            L1 cache
```

## Slide 8

```text
Roles of OS vs Hardware                                       The OS lives almost entirely in a logical world.                    Operating Systems
                                                                   Hardware lives in the physical world.
```

## Slide 9

```text
Operating Systems
    What OS is seeing


                                                                                  The OS sees only

                                                                                            harts
                                                                                       page tables
                                                                                   virtual addresses
                                                                                     physical pages
                                                                                         interrupts





The OS does not see                                                                         OS manages:
                                                   To OS,
             L1                            Address 0x80001234 is                Which virtual addresses map to which
             L2                               a place in process                            physical pages
                                                   space,
             L3                                                                  Which hart is running which process
 memory coherence                        even though it is actually in:                    Context Switch
       cache lines                               L1 on hart0
       writebacks                                L2 on core1                                  Permission
        snooping                                   DRAM                                       TLB table
           fabric
```

## Slide 10

```text
Operating Systems






                                                                                            The central job of OS
                                                                                                            is
                                                                                               manage SHARING
                                                                                                            of
                                                                                               Limited hardware
                 When scheduling process to HART，                                                    resources:

   scheduler  is running on each hart , it will pick a RUNNABLE
                              process to run                                                             HART
instead of a central scheduler to pick a Hart to run an RUNNABLE                                  (Logical CPU)
                                  process                                                              Memory

                            Operating System                                                               and
                                      |                                                                Devices
                           __________________
                           |               |               |                                  (among competing
                        CPU0      CPU1      CPU2
                        Hart0      Hart1       Hart2                                                 programs)
                          |                 |               |
                   scheduler   scheduler   scheduler
                          |                |                |
                      proc A       proc B       proc C
```

## Slide 11

```text
Operating Systems












Process and Thread
```

## Slide 12

```text
Operating Systems







                                                          Process
         Program
                                                   Program code/data
     A file on DISK               create              Address space
       Static code                                (page table/memory)
  (set of instructions,                                     Heap
with data and symbols)                               File descriptors

Program does not run                                  A process is a
                                                   resource container
```

## Slide 13

```text
Process                                                    Operating Systems



Program: on DISK

























               Process: in RAM
```

## Slide 14

```text
Process                                                                    Operating Systems
                                                       A process of a program
                                                                  is an
                                                       execution environment.

                                                 It is a data structure that contain
                                                 everything needed for execution.

                                               It is like a high level virtual machine
                                                        with code in memory,
                                             data/stack/heap/page table in memory,
                                                                ﬁles info,
                                                      process ID, signals, etc.

                                                 This environment is isolated from
                                                           other processes.


                     IPC （Inter Process Communication）
                           Producer-Consumer pattern
                 Data exchange between processA/processB
                    (implemented by thread in each process)
                             IPC don’t share memory
                    (Threads for on process share memory)
                               Kernel provide buﬀer
```

## Slide 15

```text
Thread                                                                    Operating Systems


                                                                             Process
             Program
                                                                      Program code/data
          A file on DISK                                                 Address space
            Static code                                              (page table/memory)
       (set of instructions,                                                   Heap
    with data and symbols)                     create                   File descriptors

     Program does not run                                           A process is a resource
                                                                            container



   A thread runs on a HART
          scheduled by                                    Threads of a process
       kernel’s scheduler
                                                   an execution of process’s code
   When a thread causes an                                         it has a
exception, syscall, or interrupt,          program counter/registers/stack/scheduling
 the hart traps into the kernel                                     state
 on behalf of that thread, and
the kernel handles it using that             Running is done by a process’s threads,
         thread’s state.                                not by the process itself
```

## Slide 16

```text
$ ./a.out 50
                                                                                                                           thread:200712192, counter=3 Operating Systems
         Thread                                                                                                            thread:200712192, counter=4
                                                                                                                           thread:200712192, counter=5
                                                                                                                           thread:200712192, counter=6
                                                                                                                           thread:200712192, counter=7
                                                                                                                           thread:200712192, counter=8
                                                                                                                           thread:200712192, counter=9
       Example                                                                                                             thread:200712192, counter=10
                                                                                                                           thread:201248768, counter=3
                                                                                                                           thread:201248768, counter=12
   synchronous                                                                                                             thread:201248768, counter=13
                                                                                                                           thread:201248768, counter=14
                                                                                                                           thread:201248768, counter=15
         thread                                                                                                            thread:201248768, counter=16
                                                                                                                           thread:200712192, counter=11
                                                                                                                           thread:200712192, counter=18
                                                                                                                           thread:200712192, counter=19
                                                                                                                           thread:200712192, counter=20
                                                                                                                           thread:201248768, counter=17
                                                                                                                           thread:201248768, counter=22
                                                                                                                           thread:200712192, counter=21
                                                                                                                           thread:201248768, counter=23
                                                                                                                           thread:201248768, counter=25
   This program                                                                                                            thread:201248768, counter=26
                                                                                                                           thread:201248768, counter=27
creates 3 threads,                                                                                                         thread:201248768, counter=28
                                                                                                                           thread:201785344, counter=3
 each thread will                                                                                                          thread:201785344, counter=30
                                                                                                                           thread:201248768, counter=29
     increment a                                                                                                           thread:201785344, counter=31
  shared variable                                                                                                          thread:201785344, counter=33
                                                                                                          wait for the 3 thread:201248768, counter=32
  until it reaches                                                                                                         thread:201785344, counter=34
                                                                                                            threads to thread:201785344, counter=36
  an upper limit.                                                                                                          thread:201248768, counter=35
                                                                                                             terminatethread:201785344, counter=37
                                                                                                                           thread:201785344, counter=39
                                                                                                                           thread:201785344, counter=40
  then the parent                                                                                                          thread:201785344, counter=41
                                                                                                                           thread:201785344, counter=42
        join the                                                                                                           thread:201785344, counter=43
                                                                                                                           thread:201785344, counter=44
threads and print                                                                                                          thread:201785344, counter=45
      the shared                                                                                                           thread:201785344, counter=46
                                                                                                                           thread:200712192, counter=24
        variable                                                                                                           thread:200712192, counter=48
                                                                                                                           thread:201785344, counter=47
                                                                                                                           thread:200712192, counter=49
                                                                                                                           thread:201785344, counter=50
                                                                                                                           thread:201248768, counter=38
                                                                                                                           counter = 50
```

## Slide 17

```text
Thread                                                                                                 Operating Systems


                                                                       If a process call “pthread_create”,
                                                                       then there is another thread created.

If a process does not                                                  Now there are two threads:
call “create_thread”,                                                  Process
then there is a thread                                                 ᵓᴷᴷ Thread 0 ← original main thread
called “main thread”                                                   └── Thread 1 ← pthread_create

Process
└── Thread 0 ← main thread
Thread 0 has it own：                                                   Process
                                                                       +--------------------------------------+
PC                                                                     | code                                              |
registers                                                              | global variables                            |
stack                                                                  | heap                                              |
                                                                       | ﬁles                                                |
This is a “single threaded process”                                    |                                                        |
                                                                       | Thread 0           Thread 1               |
                                                                        | PC                    PC                       |
                                                                       | registers           registers              |
                                                                        | stack               stack                    |
                                                                       +--------------------------------------+
```

## Slide 18

```text
Thread                                                                   Operating Systems



         Within one process,
            there could be
            one or multiple
           software threads

           They are sharing                       Process
   code, global variables, heap…                 ├── shared code
           but each thread                       ├── shared global variables
              has its own                        ├── shared heap
             private stack                        │
           (local variables)                     ├──      Thread 0
             and registers                        │     ├──     private registers
                                                  │     └──     private stack
                                                  │
Process A                                        └──      Thread 1
        |                                        ├──             private registers
├──      Thread 0 --> Hart 2                     └──             private stack
        |
└──      Thread 1 --> Hart 5
```

## Slide 19

```text
Operating Systems
      Application programs run by system




  If process does not
     create thread                                                                              A process owns
then process = thread                                                                          code and memory.
                                                                                    A thread is an execution of that code
                                                                                                    on a CPU.



      There is also
           IPC
    （Inter Process                                                                           Multiple threads run the same code
   Communication）

  Producer-Consumer
         pattern                                                                                            Threads are sharing
     Data exchange                                                                                    memory only if they belong to
        between                                                                                                 the process

  processA/processB
(implemented by thread
    in each process)
IPC don’t share memory                                                                                      Threads are
(Threads for on process                                                                                            for
     share memory)                                                                                           parallelism

 Kernel provide buﬀer
```

## Slide 20

```text
To   sumarize                                                                                                    Operating Systems










                                                                                               Hart sharing
          Sharing
                                                                                                 Problem:
                                                                    Who runs now? Who waits? Who is preempted?

                                                                                            Kernel solution:
                                                                                                 Scheduler
                                                                                            Timer interrupts
                                                                                           Context switching



                                                                                            Memory sharing
                                                                               Threads in a process share memory.
                                                                                          Processes must not.
                                                                                                 Problems:
                                                                              Threads must see each other’s writes
                                                                                     Processes must be isolated
                                                                              One process must not corrupt another
                                                                                            Kernel solution:
                                                                                              Virtual memory
                                                                                                Page tables
                                                                                             Address spaces
                                                                                               Copy-on-write
                                                                                       Shared memory (explicit)
```

## Slide 21

```text
To    sumarize                                                                                                   Operating Systems


                                                                                                   Kernel data sharing
                                                                              All harts execute the same kernel code and modify:
                                                                                                          run queues
                                                                                                         page tables
                                                                                                          file system
                                                                                                       device queues
                                                                                                           Problem:
                                                                             Two harts modifying the same data at the same time
                     Sharing                                                                          Kernel solution:
                                                                                                              Locks
                                                                                                     Atomic operations
                                                                                                      Memory ordering
                                                                                                       Critical sections



                                                                                                          Device sharing
                                                                                                     Many processes want:
                                                                                                                  disk
                                                                                                               network
                                                                                                               terminal
                                                                                                             Problems:
                                                                                                  Who gets the device now?
                                                                                                  How to avoid mixing data?
                                                                                                 How to sleep while waiting?
          Process isolation + cooperation                                                                Kernel solution:
  Processes must be isolated, but still communicate.                                                       Device drivers
                       Problem:                                                                               Interrupts
How do isolated address spaces exchange data safely?                                                            Buffers
                   Kernel solution:                                                                       Sleep / wakeup
                          IPC
                    Pipes, sockets                                                                         I/O scheduling
          Shared memory (explicitly granted)
```

## Slide 22

```text
Operating Systems


               The kernel exists to manage
 sharing of harts, memory, kernel state, and devices
      among competing threads and processes.







5 Topics
```

## Slide 23

```text
Operating Systems









Scheduler
```

## Slide 24

```text
Operating Systems
Scheduler







                     Example: xv6
```

## Slide 25

```text
Operating Systems
Example: xv6


    xv6 does not recognize “core”                        1 process =  1 thread
it sees the system as a set of “harts”












                                                                    shared
                                                                    memory
```

## Slide 26

```text
Operating Systems
xv6                                entry.S


















                                                                      start( )
                                                                     (start.c)
```

## Slide 27

```text
Operating Systems

        entry.S                          entry.S                     entry.S                        entry.S



























  entry.S
is executed
on all Hart
```

## Slide 28

```text
xv6                                                 start.c         Operating Systems





















                                                                    main( )
                                                                   (main.c)
```

## Slide 29

```text
Operating Systems

               start( )                         start( )                            start( )                         start( )



























   start( )
is executed
on all Hart
```

## Slide 30

```text
xv6                                                                                     Operating Systems
                                                                   main.c



                                                                                If it is Hart0, then
                                                                                       do the
                                                                         “global, once-per-machine”
                                                                                   initialization
                                                                        (set started = 1 to ﬂag ﬁnish)





                                                                               wait till Hart0 ﬁnish
                                                                                global initialization
                                                                                   (started == 1)
                                                                                       then do
                                                                                     “per hart”
                                                                                    initialization




                                                                                           scheduler( )
                                                                                              (proc.c)
```

## Slide 31

```text
Operating Systems


                                                              global initialization
                                                           (+ per-hart initialization
                                                                    for hart0)





                                                       per-hart                    per-hart                    per-hart
                                                     initialization             initialization               initialization










hart0 builds the shared
       kernel world;

         every hart
   conﬁgures itself to
 safely run inside that
            world.
```

## Slide 32

```text
xv6                                                                    proc.c                          Operating Systems


                                                                                             1 process =  1 thread

                                                   Inﬁnite loop

                                                                                                          note:
                                                                                              swtch( ) looks like a loop
                                                                                             that keeps calling swtch().
                                                                                           but as soon as it call swtch()
                                                                                             another thread will control
                                                                                                        the hart,
                                                                                        so scheduler() will not be running,
                                                                                          until scheduler is called again.





                                                                                                        swtch( )
                                                                                           (context switch to a process)










                                                   “wait for interrupt” instruction
```

## Slide 33

```text
Operating Systems

scheduler( )               scheduler( )              scheduler( )               scheduler( )
inﬁnite loop              inﬁnite loop               inﬁnite loop               inﬁnite loop




















                       all harts are running scheduler( ) loop

                  scheduler( ) ﬁnd a process to run on this hart
```

## Slide 34

```text
swtch.S                     swtch(struct context *old, struct context *new);          Operating Systems



                                                a0 → &old_proc->context               a1 → &new_proc->context



                                                    Save old context into
                                                     a0 pointed location



                                               thread = saved stack + saved return address


                                                    load new context from
                                                      a1 pointed location
                                                    into ra, sp, s# registers



                                                                  return to wherever
                                                                 “ra” (return address)
                                                                       pointed to.
                                                                       in this case:
                                                            starting code of new thread,
                                                                      into U-mode
```

## Slide 35

```text
swtch.S                                                                                Operating Systems



                                   User thread




                                             User thread stops when:




                                  call yield( )                                preempted
                                  to give up                                     by timer
                                     hart            sleep         exit
                                                                                interrupt





                 after
              user thread                 yield( )                  sched( )                swtch( )




                                                                                     scheduler( )
```

## Slide 36

```text
Operating Systems

                                                                     call yield( ) to give up hart,  reasons:

                                                    1. being cooperative, let other processes run even if there is
                                                                                 no timer interrupt.
  User thread                                    2. a process is waiting for something but does not want to block



                                                                                                               When user code does something
                                                                         sleep                                                 like:
                                                    cannot continue until some event happens.                            read(fd, buf, n);
                                                       then sleep will block this process until            The thread traps into the kernel (syscall)
                                                              someone calls wakeup( )                      “Data is not ready” is discovered inside
                                                                      Examples:                                          the trap handler
User thread stops when:                                           waiting for disk I/O                      Kernel code decides: “this thread must
                                                                   waiting for a pipe                                          wait”
                                                             waiting for a child process                    Kernel calls its internal sleep function
                                                                     for example：                              The thread is put into a wait state
                                                       user → ecall → kernel trap handler →                    The scheduler runs someone else
                                                                      sys_read()                                                So:
                exit                                  if data is not available now, call sleep function                sleep function is not a user function.
      thread (process)                                                                                          It is a kernel scheduling action.
             is done
                                                                                                               The kernel calls wakeup() when
 This process will never                          Preempted by timer interrupt:                                                 an
    be scheduled again                                                                                                  event happens.
       Its memory and                                           timer ﬁred.                                       wakeup( ) finds all threads
  resources will later be                                   trap into kernel                                 sleeping on that event and makes
     freed by its parent                                      (trap handler)                                         them runnable again
          (ﬁnal sleep)                                          call yield( )                                  and puts them in the runqueue
                                                          yield( ) call sched( )                            The scheduler will decide when they
                                              context switch back to scheduler( )                                         actually run.
```
