# Professor Slides Lecture5-1-2process-relation-competition

> [!warning] Reference transcript, not confirmation of lecture coverage
> Extracted text from the professor’s slide PDF. Page numbers are PDF page numbers. Extraction can omit diagrams or distort reading order and formulas. Refer to the original PDF for full content. A slide’s presence does not establish that it was discussed. Prioritize the topic files’ lecture records.

**Original PDF:** `/Users/jossean/Documents/CSEN 283/Professor Slides Lecture5-1-2process-relation-competition.pdf`

**Pages with no extracted text:** None; diagrams may still be omitted.

## Slide 1

```text
Operating Systems
                     COEN 283
                     Yuan Wang
```

## Slide 2

```text
Operating Systems





Process competition
```

## Slide 3

```text
Process competition                                                  Operating Systems


Example: modify the data at the same time
(previous race condition example)
```

## Slide 4

```text
Process      competition                                                                 Operating Systems




    Two threads doing
             i++
            If the
   complete update of
          variable i                                                                  This   is called:
       (three steps)                                                                Load-and-Store
         is atomic,
  then there isn’t a race



  We need      data   to  be:
        sequentially
         consistent
 (so  that   i is 2 greater)




without synchronization,
      the result can
     be inconsistent
```

## Slide 5

```text
Process competition                                                         Operating Systems


Example: Producer-Consumer problem
 (from previous lecture, but modified, use counter to check full/empty)
Bounded buffer                              while loop check if counter reach maximum (full)


                                                                                    counter = 5











   add “counter”


       after the producer and consumer
              ﬁnish one operation,
     the value of counter is not necessary
          the correct value (which is 5)
                it could be 4 or 6
```

## Slide 6

```text
Process competition                                         Operating Systems



          If each thread uses variables that other threads
                           don’t read or modify,
                  no consistency problems will exist



                                  However,
              when one thread can modify a variable that
                    other threads can read or modify,
                   they have competition relationship




       we need to synchronize the threads to ensure that
they don’t use an invalid value when accessing the variable’s
                            memory contents.
```

## Slide 7

```text
Process competition                                                              Operating Systems

                              Race condition in kernel
                       Kernel code is also subject to Race Condition.
    For example, different kernel processes modify the same kernel data structure.

        When develop kernel, we want to make sure no Race Condition happen


                                    Nonpreemptive Kernel
        does not allow a process running in kernel mode to be preempted
                               (kicked out by other process);
a kernel-mode process will run until it exits kernel mode, blocks, or voluntarily
                                  yields control of the CPU
                                                                                  Meaning of “preemptive”:
                              this is free from race condition                    have power to get it ﬁrst

                                      Preemptive Kernel
      allows a process to be preempted while it is running in kernel mode

                this is more diﬃcult to design,  but more responsive,
   since there is less risk that a process will run for an arbitrarily long period
    before relinquishing the processor to a more urgent waiting processes.

                       this is better suitable for real time system
```

## Slide 8

```text
Process competition                                                               Operating Systems

                                       Critical Section                                      entering the
    The segment of code,                                                                    critical section
in which the process may be                                                                is equivalent to
changing shared variables,                                                                     entering
      updating a table,                                                                  the single restroom
         writing a ﬁle,
  is called critical Section
```

## Slide 9

```text
Process competition                                                                        Operating Systems

                                      Critical Section



        critical  section                          To protect  critical section,
will access critical resource
                                                       the critical section
critical resources are mostly                        can not be interrupted
        share variables                                (atomic execution)

        it could also be
           IO devices
    network connections




  We need     to  make    sure,
       no  two   threads
        are  executing
     the  critical  section
      at the  same    time
            that   is,
       critical  section
   need    to be  protected
```

## Slide 10

```text
Process competition                                               Operating Systems







To ensure critical section
    is not interrupted,
   the threads have to
         get hold of
               a
         key or lock
 so that other thread not
 having the key or lock,
     will have to wait,
          therefore,
   only one thread can
 access the variable at a
             time.
```

## Slide 11

```text
Process competition                                               Operating Systems

                          Critical Section - solution

Analogy - gas station restroom


                           you need
                          to acquire
                            the key
     critical             to be able
    resource               to use it












                                                      tasks
```

## Slide 12

```text
Process competition                                                                       Operating Systems

 Critical section                                                                                          restroom
         - protect




try to get the lock/key
    before enter
   critical section



but we access the
restroom through
  critical section
so we can think of
  critical section
 as restroom too.




  release lock/key
      after exit
   critical section



     key and lock are the same thing,
  once you get it, the restroom is yours
```

## Slide 13

```text
Process competition                                                Operating Systems


                                                                                       critical
   Critical Section - solution                                                        resource

Better Analogy - air plane washroom





  check and
      see
   if vacant

                                                                 tasks




                                                                                 idea

                                                  lock if you               use variable
                                                    have it                  for the lock
                                                                              0:  vacant
                                                                            1: occupied
```

## Slide 14

```text
Process competition                                                                Operating Systems

                                 Critical Section - solution
An intuitive/naive way is to use a variable as “lock”.








 wait till lock
     is 0                                            compare
  then set to                                          and
      1                                                 set
  then other
  thread will
 have to wait





     set lock                                       critical
     back to 0                                      section



    > ./a.out 100
    Shared: 9972                          lock failed                                                lock=0: available
    Expect: 10000                                                                                     lock=1: locked
```

## Slide 15

```text
Process competition                                                                                 Operating Systems

      Critical Section - solution
                                                                                      lock is a shared resource too ! ! ! !
The reason it is not working


                                         lock=0: available
                                          lock=1: locked







                                               we need this
                                               check on lock
                                                to be atomic

                                           T1.                        T2
   you saw the door                                                                                        but before you actually
      not locked                   not lock                                                                 lock it, someone else
                                                                                                          thought it is available too
                                                                      not lock
   you walk over and               lock = true
         lock it                                                      lock = true                            both lock the door
                                                                                                                and enter the
                                   critical section.                                                           critical section
                                                                       critical section                       at the same time
```

## Slide 16

```text
Process competition                                                     Operating Systems

                             Critical Section - solution

                                                                                      Disabling interrupts is
Requirement for solution:                                                              one way to implement
                                                                                         mutual exclusion
                                     mutual exclusive



                                                             In a uniprocessor system,
                                                      a task executing in a critical section
                  At most                                        may be preempted,
        one thread (or process)                  but no other task can enter a critical section
               can execute                            accessing the same shared resource
             a critical section                   until the ﬁrst task leaves its critical section.
              that accesses
     a particular shared resource
            at any given time.
                                                              in multiprocessor case:
                                                                     this means,
                                                         physically not at the same time
```

## Slide 17

```text
Process competition                                                  Operating Systems

                           Critical Section - solution
Requirement for solution:


                                                             tasks who want to get in,
                                                         should be able to at some point
               Progress                                             no deadlock



                                                            if a task waits indeﬁnitely for
           no starvation                                a resource because other tasks are
                                                           repeatedly given access to it，
                                                            then this task is in starvation



                                                         in restroom analogy,
                                                              this means,
                                                 one person keep using the restroom
                                                     other person cannot use it.
```

## Slide 18

```text
Process competition                                       Operating Systems

                     Critical Section - solution


There are two categories of solution:




          Software solution


                                                   Hardware solution
                                              (or with hardware support)
```

## Slide 19

```text
Process competition                                        Operating Systems








         Software solution




                                        Hardware solution
                                    (or with hardware support)
```

## Slide 20

```text
Process competition                                        Operating Systems

           Dekker’s Algorithm (1962)


Dutch mathematician
First proved correct algorithm to
mutual exclusion problem
between 2 processes/threads.

Two processes/threads attempts to enter “critical section” at the
same time, the algorithm will decide whose turn it is.

Two ﬂags are used for each process/thread:                         atomically
                want_to_enter[0],    want_to_enter[1]

if process/thread i want to enter, set want_to_enter[i] = true

Another variable is used to decide who has the turn:  turn
```

## Slide 21

```text
Process competition                                                                      Operating Systems

                   Dekker’s Algorithm (1962)













  if the other one want to enter                                                              want to enter

         and if not my turn
then i don’t want to enter anymore



                   wait for my turn


                if it becomes my turn
                (set by other process)
                  set intent to enter


if other does not want to enter
     enter critical section
 then set: you can enter next
i don’t want to enter anymore
```

## Slide 22

```text
Process competition                                         Operating Systems

                   Dekker’s Algorithm (1962)



view two
processes
together
```

## Slide 23

```text
Process competition                                                      Operating Systems

                            Dekker’s Algorithm (1962)


                                                                                if one is in the
-   guarantees mutual exclusion                                                 critical section
                                                                          the other one has to wait
-   free from deadlock
-   free from starvation


                                                                          dead lock happens when
                starvation happens when                                  everything body is waiting.
               one process cannot enter                                        in this algorithm,
           (the other process keeps entering)                              deadlock will not occur
                     in this algorithm,                                             because
     starvation will not occur because if after one                        if one process withdraw,
              process exit critical section,                                   the other process
                       this process                                               will not wait
       cannot keep entering the critical section
```

## Slide 24

```text
Process competition                               Operating Systems

        E. Dijkstra’s Algorithm (1965)




Dutch computer scientist
Proposed a solution for more than two
processes (one page paper)

guaranteed:
- mutual exclusion
- freedom from dead lock
- freedom from live lock
```

## Slide 25

```text
Process competition                                Operating Systems

           D. Knuth Algorithm (1968)





American computer scientist
guaranteed - freedom from starvation


both algorithm have a problem:

if task halt/crash at any point in critical entry section,
the whole system might block indeﬁnitely
```

## Slide 26

```text
Process competition                                               Operating Systems

               Bakery Algorithm (1973)


Leslie Lamport, American computer scientist

a simpler algorithm

allow halt in critical entry section

Idea:

like a bakery store:

each customer get an unique number from number machine
if one customer is served, next number is displayed.

customer: thread

It is possible that more than one thread would get the same number
```

## Slide 27

```text
Process competition                                       Operating Systems

             Peterson’s Algorithm (1981) - simpler

Gary Peterson.

Simpler
simple to prove its correctness

similar to Dekker’s algorithm

Two tasks: A, B

Two flags are used for each task:
      want_to_enter[A],    want_to_enter[B]

if task x want to enter,
set want_to_enter[x] = true

a variable to decide who has the turn:  turn
```

## Slide 28

```text
Process competition                                                   Operating Systems

             Peterson’s Algorithm (1981) - simpler


    I want to enter           want_to_enter[A] = true;
                              turn = B;
    you can enter next        while (want_to_enter[B] == true &&
                                     turn == B)

  if you want to enter and    {                                       Variable “turn” is like both
      it is your turn            // wait                           people see washroom is vacant,
        i will wait                                                      but only get to use it
                              }                                         if the other people say
                                                                             “after you”
                                                                        this avoid the situation
                              // critical section                     where both of them enter
execute critical section                                                  at the same time
                                       ...


 I don’t want to enter        // end of critical section
      anymore
                              want_to_enter[A] = false;
```

## Slide 29

```text
Process competition                                                 Operating Systems

                         Peterson’s Algorithm (1981)
                                                          if the other thread doesn’t want to
view two processes side by side                                 enter or it is my turn
                                                                   then proceed
want_to_enter[A] = true;
turn = B;
while (want_to_enter[B] == true && turn ==B)
{
   // busy wait
}                                      want_to_enter[B] = true;
                                       turn = A;
// begin critical section              while (want_to_enter[A] == true && turn == A)
         ...                           {
// end of critical section                // busy wait
                                       }
want_to_enter[A] = false;
                                       // begin critical section
                                                ...
  be able to enter critical section    // end of critical section
             only when                 want_to_enter[B] = false;
  the other person don’t want to
                 or
            it is my turn
```

## Slide 30

```text
Process competition                                          Operating Systems

                     Peterson’s Algorithm (1981)

Proof of correctness

                                 mutual exclusion

                              if mutual exclusion failed
                then both tasks could be in the critical section
                                 at the same time

                                      this means,

                both turn == A and turn == B at the same time

                                   this is impossible
```

## Slide 31

```text
Process competition                                                             Operating Systems

                             Peterson’s Algorithm (1981)

Proof of correctness
                                       neither task can be locked out

                                   assume taskA is doing while test forever

                                 then taskB will be doing one of the 3 things.

                             1.  don’t want to enter (want_to_enter[B] == false)
                                      then taskA can proceed to enter
                                           (the while test will break)

                                             2.  also doing while test.
                                 this is impossible, because turn is A or B
                                       one of the task will be able to proceed

                                             3.  repeatedly executing,
                                    then it will quickly set turn to A and
                                     never change back to B until A set it to B
                                     then taskA will be able to proceed
```

## Slide 32

```text
Process competition                                                  Operating Systems

                         Peterson’s Algorithm (1981)Assumption:
                                                              number of tasks N must be
for any number of processes                                        known in advanced.


// code for producer (j)                            // code for consumer i

// producer j is ready to produce an item           // consumer i is ready to consume an item
flag[j] = true;                                     flag[i] = true;

// but consumer (i) can consume an item             // but producer (j) can produce an item
turn = i;                                           turn = j;

// if consumer is ready to consume an item  // if producer is ready to produce an item
// and if its consumer's turn                       // and if its producer's turn
while (flag[i] == true && turn == i) {              while (flag[j] == true && turn == j) {
  // then producer will wait                          // then consumer will wait
}                                                   }

// start of critical section                        // start of critical section
// produce an item and put it into buffer           // consumer consume an item from buffer
// end of critical section                          // end of critical section

flag[j] = false;                                    flag[i] = false;
```

## Slide 33

```text
Process competition                                             Operating Systems

                       Software solution summary


Good:
                                                                Sequential consistency
 - run in user space                                           is a conservative memory
 - doesn’t require much from hardware                        model that does not allow any
                                                            instruction reordering on each
   (only require atomic read and                                          core.
    atomic write)
                                                                  This prevents many
Bad:                                                          optimizations and degrades
                                                                      performance

 - not guaranteed to work for modern                              Since some memory
   computer architectures.                                  instructions can be reordered,
   (because this algorithm is assuming                       achieving better performance
                                                              without losing correctness,
   memory is sequentially consistent,                          hence a relaxed or weaker
   this causes problem for Peterson’s                           memory models is used
   algorithm)
```

## Slide 34

```text
Process competition                                   Operating Systems

    Software solution summary - continue



also, it only guarantees to work on single processor,
   but not guarantees to work on multiprocessor
  (two tasks running on separate  processors might
                both enter critical section

           Modern computer system provide
               atomic low level instruction,
            its simpler to implement API like
                       POSIX pthread
                              and
                         Linux Futex
```

## Slide 35

```text
Operating Systems







Software solution



                       Hardware solution
                   (or with hardware support)
```

## Slide 36

```text
Process competition                                                  Operating Systems

                                 Hardware solution

The problem is critical section is entered at the same time.
By “at the same time”, in a single processor system, that means,
we switched to another tasks in the middle of the critical section.

And the way to switch to other task, is through interrupt.

So if we can disable interrupt, then task switching will not be happening.

  lock {
      disable_interrupt;                             loop {
  }                                                      non_critical section;
                                                         lock( );
  unlock {                                               critical section;
     enable_interrupt;                                   unlock( );
  }                                                  }
                 Implement lock                                          critical section
                      like this                                             become
                                                                             atomic
```

## Slide 37

```text
Process competition                                                Operating Systems

                   Hardware solution - problem

   if user program is allowed to                       it is also a costly operation
           disable interrupt
        it is really dangerous
    because, once the interrupt
              is disabled,                      It is ok for kernel to
kernel cannot get the control back.                   do this
      so user might potentially
              run forever


                      Disable interrupt does not work in
                multi-processor or multi-core environment,

                          because real concurrency is
            not using interrupt to context switch to other task

                (you can still disable interrupt for one core)
```

## Slide 38

```text
Process competition                                                                            Operating Systems

                                                               revisit

The reason lock is not working














                  T1.                        T2

           not lock
                                              not lock                    this check and
           lock = true                                                      lock need to                           lock=0: available
                                              lock = true                     be atomic                             lock=1: locked
           critical section.
                                              critical section
```

## Slide 39

```text
Process competition                                                         Operating Systems

                           Hardware assisted solution

Test_and_Set( )
Modern computer architecture provide atomic (read/write), that is,
read and write together is a complete unit, nothing will come
between these read and write.

This feature is in the form of atomic instruction, namely
“Test and Set” instruction (TSL)                                      lock == true:  locked
  // pseudo code                                                     lock == false: available
  function Test_and_Set(lock) {                                             get the lock value (read)
                                                                         set the lock to be locked. (write)
      current_value = lock;                                                  in one atomic action,
     lock = true;                                                   compare to previous “not-working” version,
     return current_value;                                         this is basically putting “lock=true” within the
                                                                                atomic action
  }

                                      if currently available, lock and return “false”
                                    if currently locked, keep locking and return “true”
              (return value will be used in the while or if statement of the lock implementation or the calling code)
```

## Slide 40

```text
Process competition                                                 Operating Systems

     Hardware assisted solution

Use Test_and_Set( ) to implement lock                               if the lock is “available”,
                                                                            will return
  function Lock(*lock) {                                                     “false”
                                                               and therefore break the while loop
      while(Test_and_Set(lock));                                    otherwise, return “true”
   }                                                             and keep checking while loop

  function Unlock(*lock) {
      lock = false
  }




                        Or use it directly
                             like this
```

## Slide 41

```text
Process competition                                                                Operating Systems

                             Hardware assisted solution

If Test_and_Set( ) is not provided by system, compare_and_swap( )
can be used to implement
                                                                          check a memory value
                                                              if same as expected-> swap to the other value
                                                                                (new value)
 // pseudocode
 function compare_and_swap(ptr, old, new) {
       if *ptr <> old
        return false                                      This deﬁnition is a general deﬁnition.
     *ptr = new                                             It is up to the lock implementation
     return true                                to interpret the parameters and to decide how to use it.

 }                                                     For example, the ptr can be the lock value,
                                              the old can be expected value (e.g. lock available, or false),
                                                            new  can be the lock being “true”,
                                                   if lock is not expected (false), then set to new (true)
                                                                     then return “true”
                                                 otherwise, if it is expected (false), simply return “false”
                                      (again, don’t confuse the return value of “false” and “true” with lock value,
                                         they will be used by the implementation code’s while loop condition)
```

## Slide 42

```text
Process competition                                           Operating Systems

                      Hardware assisted solution

Use compare_and_swap( ) to implement lock

 function Lock(*lock) {
     while(!compare_and_swap(&lock, false, true));
       return
 }

 function Unlock(*lock) {                   if lock is false (unlocked)
     lock = false                               then swap to lock
 }                                                and return true
                                              break the while loop

                                             if lock is true (locked),
                                                  return “false”
```

## Slide 43

```text
Process competition                                 Operating Systems









   Continue on handwritten presentation
```
