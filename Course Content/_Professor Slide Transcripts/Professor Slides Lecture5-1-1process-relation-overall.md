# Professor Slides Lecture5-1-1process-relation-overall

> [!warning] Reference transcript, not confirmation of lecture coverage
> Extracted text from the professor’s slide PDF. Page numbers are PDF page numbers. Extraction can omit diagrams or distort reading order and formulas. Refer to the original PDF for full content. A slide’s presence does not establish that it was discussed. Prioritize the topic files’ lecture records.

**Original PDF:** `/Users/jossean/Documents/CSEN 283/Professor Slides Lecture5-1-1process-relation-overall.pdf`

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







Process Relationships
```

## Slide 3

```text
Operating Systems
                                In a multi-tasking system,
                       there are many processes at any moment

                  There are two types of relationship between them


                 competition                                 collaboration


          process                          loose                              tight
       compete for                    collaboration                      collaboration
     shared resource


      process                Each process performs its
        using                 own task independently.                   Processes work
  shared variable                                                       closely together,
                                   but cooperate by                  sharing data or jointly
no communication              exchanging information.                  performing a task.

   special case                    communication/                       communication/
          of                       synchronization                      synchronization
 synchronization
```

## Slide 4

```text
Process Relationship                                                   Operating Systems


                                 collaboration


                   loose                                    tight
              collaboration                            collaboration




          synchronization                           for exchange large data
     to make sure operation
is following condition / ordering                       synchronization
                                                        is also necessary
      small message/signal
        for synchronization                                  example
                                                data collector / data analyser
              example                                  order is important
     janitor / washroom user                                 but also
    two independent process                            dependent on data
```

## Slide 5

```text
Operating Systems






Competition
```

## Slide 6

```text
Competition                                                       Operating Systems




       when multiple processes (or threads)                              intersection
are trying to do something with shared resource,                              is the
            there will be several issues,                              shared resource
                      for example,
      the final outcome might be unexpected
```

## Slide 7

```text
Competition                                                 Operating Systems



    Multiple threads execute independently and concurrently,
            potentially accessing the same shared data
                without coordinating with each other.

    They don’t need to be unaware of the existence of others

                    Since these processes/threads
             share a set of computer system resources,
 it is inevitable that multiple processes compete for resources.

  A race condition occurs when the outcome of a computation
depends on the timing or interleaving of concurrent operations.

When multiple processes compete for share resources such as
             hard devices / storage / processors / ﬁles,
the operating system must coordinate the process's demand for
                                 resources.
```

## Slide 8

```text
Competition - Race Condition                                        Operating Systems



           For example                                              P1, P2 are sharing
  2 threads P1, P2 executing                                      one variable “count”
           concurrently.












P1 increment                                       P2 decrement
    count                                               count
```

## Slide 9

```text
Competition - Race Condition                                              Operating Systems



In assembly language,  the instructions of the threads are like this
              (multiple micro steps of using register R0)



                                                                                               same
                                                                                                here




                                                     - read count
                                                   - increase count
                                                     - write back
```

## Slide 10

```text
Operating Systems
Competition - Race Condition







                                                                             one possible
                                                                               execution
                                                                               sequence










                                                                                  count = 5
                                                                                at  the end
```

## Slide 11

```text
Operating Systems
Competition - Race Condition






                                                                                     another possible
                                                                                         execution
                                                                                         sequence







                                                                                            count = 4
                                                                                          at  the end




                                                                                          the result is
                                                                                         unpredictable.
                                                                                          very difﬁcult
                                                                                            to debug
```

## Slide 12

```text
Competition                                                           Operating Systems

  The operating system needs to ensure that
 the threads can access the critical resources
                       mutually

The mutex of the process (mutual exclusion) is
                      the means
     to resolve the competition relationship







                                                       Thread Mutex
                                                   when several threads
                                        want to use the same shared resource,
                                                a maximum of one thread
                                           is allowed to be used at any time,
                                               the other threads must wait
                                      until the resource-owning thread releases
                                                        the resource.
```

## Slide 13

```text
Competition - Race Condition                                               Operating Systems


                 two control problems in resource competition:



                     deadlock
                                                                                starvation
     if a group of threads get some resources
              and want to get the                                a thread is indeﬁnitely delayed because
    resources that other threads occupy,                              other threads always take
    eventually all the threads will fall into                                precedence over it.
                    deadlock.
```

## Slide 14

```text
Operating Systems






                                  Synchronization








Synchronization applies to both processes and threads.
We  will primarily   use   threads   to  illustrate  synchronization
                              problems.
```

## Slide 15

```text
Operating Systems
Process Communication/Synchronization






                                         Synchronization
      Some processes require a collaborative effort to accomplish the same task
                       The execution of one process relies on another

                      Since each process is carried out independently
                                  and at an unpredictable pace
                   collaborative processes need to coordinate their work

       When one of the cooperation processes arrives at the coordination point,
                                       it should block itself
            until the partner process sends a coordinated signal or message
                              to wake up and continue execution.

   The coordination between such collaborative processes waiting for each other's
                  messages or signals is called process synchronization.
                         The typical technique is Semaphore (signal)



    process mutex is a special  case of process synchronization relationship that uses
                                  mutually exclusive shared resources,
                and is a coordination of the use of resource order for processes.
```

## Slide 16

```text
Operating Systems
Process Communication/Synchronization



              Common process synchronization method
   that can actually be used for mutual exclusion of processes.






    Software Solution                          Hardware Solution

   - Dekker algorithm                          - Interrupt method
  - Peterson algorithm                  - Atomic machine instruction
```

## Slide 17

```text
Operating Systems







                 IPC
Inter Process Communication
```

## Slide 18

```text
Operating Systems
Process Communication/Synchronization

                                  Communication
                       IPC (interprocess communication)
                                   in many cases
 there is a need to exchange large amounts of data between processes

        For example, to send a batch of messages or an entire file,
     this can be accomplished through a communication mechanism,

  There are many ways to communicate between processes, including:
                                  Signal (signal);

                                  Shared memory
                        controlled by the semaphore and
        its primitive operation (PV, read-write lock, enhancement);

                         Messaging (message passing)
                   for mailbox and send/Receive primitives.

                               Shared file (pipeline);
```

## Slide 19

```text
Operating Systems
 IPC - Inter Process Communication


There are many ways to communicate between processes:




                            - pipe
                            - FIFO
                    - message queue
                    - shared memory
                  - semaphore (signal)
      (for synchronizing the communication)
                 - socket (client-server)
```

## Slide 20

```text
Operating Systems
         IPC - Inter Process Communication

       - pipelines


•   Oldest mechanism for IPC in UNIX
•   Conceptually, a pipe is a connection between
    two processes, such that the standard output
    from one process becomes the standard
    input of the other process.
•   Pipe is one-way communication only
    i.e we can use a pipe such that one process
    write to the pipe, and the other process reads
    from the pipe.
•   pipe is an area of main memory that is treated
    as a “virtual file”.
```

## Slide 21

```text
Operating Systems
          IPC - Inter Process Communication

        - pipelines
                                                         output
Unix shell pipe example:
                                          string edit.
     produce the                     replace all hate with      love-love-love-love-love
   input for the next    pipe
      command                                 love              love-love-love-love-love
                                                                love-love-love-love-love
                                                                love-love-love-love-love
> cat mytextfile | sed s/hate/love/g                            love-love-love-love-love
                                                                love-love-love-love-love
                                                                love-love-love-love-love
 love-hate-love-hate-love                                       love-love-love-love-love
 love-hate-love-hate-love                                       love-love-love-love-love
 love-hate-love-hate-love
 love-hate-love-hate-love
 love-hate-love-hate-love                     Every time you type a sequence of commands in a
 love-hate-love-hate-love                              pipeline for the shell to execute,
 love-hate-love-hate-love                            the shell creates a separate process
 love-hate-love-hate-love                                     for each command
 love-hate-love-hate-love                     and links the standard output of one process to the
                                                           standard input of the next
         mytextﬁle                                               using a pipe.
```

## Slide 22

```text
Operating Systems
        IPC - Inter Process Communication

       - pipelines

•  Only between processes that have a common ancestor
   (normally between parent/child process)
   The pipe can be used by the creating process, as well as all its
   child processes, for reading and writing.
   One process can write to this “virtual file” or pipe
   and another related process can read from it.
•  If a process tries to read before something is written to the pipe,
   the process is suspended until something is written.
•  The pipe( ) system call finds the first two available positions in
   the process’s open file table and allocates them for the read and
   write ends of the pipe.
```

## Slide 23

```text
Operating Systems
     IPC - Inter Process Communication

  - pipelines
                                                                   fd: ﬁle descriptor
                                        integer, index into a per-process ﬁle descriptor table maintained by the kernel,
                                         that in turn indexes into a system-wide table of ﬁles opened by all processes,
                                                                     called the ﬁle table.











  two ends of the pipe
  connected in a single
           process.





A pipe in a single process is                                            the data in the pipe ﬂows
          next to useless                                                    through the kernel.
```

## Slide 24

```text
Operating Systems
          IPC - Inter Process Communication

        - pipelines

Example programs





                                                                            a system call pipe( )
                                                                                 will create
                                                                            two ﬁle descriptors
                                                                                     fd[0]
                                                                                     fd[1]

                                                                           fd[0] is used for read
                                                                           fd[1] is used for write
```

## Slide 25

```text
Operating Systems
           IPC - Inter Process Communication

         - pipelines
                                                                 for a pipe from child to parent
Example programs                                                (that is, parent read from child),
                                                            parent close the write descriptor fd[1],
                                                              child close the read descriptor fd[0]















  close(fd[0])

                            close(fd[1])
```

## Slide 26

```text
Operating Systems
             IPC - Inter Process Communication

           - pipelines

Example programs
                                    fd: ﬁle descriptor                                        pipe()
                                                                                            system call      fork( ) create child

                                                                                                                  process




                                                                                                           child close read




                                                                                              child write command line text



                                                                 parent
                                                                  close
                                                                  write


                                                                   write to                                      parent read
                                                                   screen




                                                              wait                               > a.out  “message for you”
                                                           child to end

                                                                                                               command line
```

## Slide 27

```text
Operating Systems
IPC - Inter Process Communication
                                                              > man 2 pipe



                                                                      type this in terminal
```

## Slide 28

```text
IPC - Inter Process Communication                                         Operating Systems

                                                               > man 2 write



                                                                       type this in terminal
```

## Slide 29

```text
Operating Systems
         IPC - Inter Process Communication

   - FIFO


-    Is a named pipe
-    Is an extension to the traditional pipe concept on Unix.
     A traditional pipe is “unnamed” and lasts only as long as the process.
-    A named pipe, however, can last as long as the system is up,
     beyond the life of the process.  It can be deleted if no longer used.
-    Usually a named pipe appears as a file and generally processes attach to it for
     inter-process communication.
-    A FIFO file is a special kind of file on the local storage which allows two or more
     processes to communicate with each other by reading/writing to/from this file.
-    A FIFO special file is entered into the filesystem by calling mkfifo() in C.
```

## Slide 30

```text
Operating Systems
        IPC - Inter Process Communication

  - FIFO

-    Once we have created a FIFO special file in this way,
     any process can open it for reading or writing,
     in the same way as an ordinary file.
-    However, it has to be open at both ends simultaneously before
     you can proceed to do any input or output operations on it.
-    Since named pipe (FIFO) is a kind of file, we can use all the
     system calls associated with it i.e. open, read, write, close.
-    Unnamed pipes can be used only between related processes
     when a common ancestor has created the pipe.

     With FIFOs, however, unrelated processes can exchange data.
```

## Slide 31

```text
Operating Systems
  IPC - Inter Process Communication
                                                                           - FIFO - example




                                                                            check /tmp directory,
                                                                                you should see
                                                                                 a ﬁle “myﬁfo”

















                                                                                   > man 2 mkﬁfo

write to named pipe                       read from named pipe                to check manual page
```

## Slide 32

```text
IPC - Inter Process Communication                                                          Operating Systems






                                                                                                         - FIFO - example














                                                          read from named
                                                               pipe






write to named pipe

                                                              write to named
                                                                   pipe


read from named pipe
```

## Slide 33

```text
Operating Systems
         IPC - Inter Process Communication
                                                                                                                     > man mkﬁfo

FIFO - example                                   There is also shell command “mkfifo”                         to check manual page

   shell script                                              to create named pipe:

                                                               > mkfifo myfifopipe



                                                                                                                         run the read.sh
                                                                                                                         in background:

                                                                                                                          > ./read.sh &

                                                                                                                       then run write.sh

                                                                                                                    > ./write.sh “love you”









                                                                                   writes to the pipe created by the read script.
                                                                                      it checks to make sure the pipe exists,
                                           read from a pipe.                                   then writes to the pipe.
                                        It ﬁrst creates the pipe                        If an argument is given to the script,
                                           if it doesn't exist,                                it writes it to the pipe;
                                         then it reads in a loop                       otherwise, it writes "Hello from PID".
                                           till it sees "quit":
```

## Slide 34

```text
Operating Systems
        IPC - Inter Process Communication

 - FIFO                  To summarize, there are two uses for FIFO







 FIFOs are used by shell commands to
                  pass data
    from one shell pipeline to another
without creating intermediate temporary               FIFOs are used as meeting points
                      ﬁles.                              in client–server applications
                                                             to pass data between
           FIFOs can be used to                            the clients and the servers.
        duplicate an output stream
      in a series of shell commands.
```

## Slide 35

```text
Operating Systems
          IPC - Inter Process Communication

         shared memory


-    Two or more processes using shared memory to
     communicate

-    Normally  process is not able to access other
     process’s memory

-    But shared memory can be setup by the process to
     communicate (by system call)

-    The data structure is defined by processes and not
     under the control of operating system

-    The processes are also responsible for making sure
     that they are not writing to the same location
     simultaneously
```

## Slide 36

```text
IPC - Inter Process Communication                                       Operating Systems

        shared memory

Typical example of process coordination:

                  Producer-Consumer problem









      producer
       produce
     information
                                                                                     consumer
                                                                                     consume
                                                                                    information


                           Producer/Consumer must be synchronized,
        consumer does not try to consume an item that has not been totally produced
```

## Slide 37

```text
IPC - Inter Process Communication                                            Operating Systems

        shared memory

                  Producer-Consumer problem




  Two types of buﬀer:

  1. unbounded (unlimited size)
      - consumer wait for new
         items, but producer never
         wait

  2. bounded  (limited size)
      - consumer wait for new
         items, produce wait for
         spaces
```

## Slide 38

```text
IPC - Inter Process Communication                                  Operating Systems

        shared memory

Bounded buffer









                                                                            Circular array
                                                                             write at “In”
                                                                           Read from “Out”
```

## Slide 39

```text
IPC - Inter Process Communication                                        Operating Systems

         shared memory

Bounded buffer
                                                                               Buffer is full
                                                                           next “in” catch out












                Buffer is empty
                “out” catch “in”



           Synchronization is needed.
    Semaphore will be used for this purpose
          （more on semaphore later）
```

## Slide 40

```text
IPC - Inter Process Communication                            Operating Systems

       shared memory                                        Shared Memory Control
Unix system call example - shmctl( )







     “cmd” is used to specify diﬀerent                      buﬀer pointer

        commands to be performed
                   including
       setting up the shared memory,
             fetch buﬀer pointer,
        remove the shared memory,
          lock the shared memory
```

## Slide 41

```text
IPC - Inter Process Communication                                                              Operating Systems

           shared memory
                                                                                                     The ftok function creates a
Linux example to show more detail of how to use                                                           identifier (token)
                                                                                                 to be used with the IPC functions
shared memory to communicate                                                                         (semget, shmget, msgget)

                                                                                                   ftok token is valid across the
                              Writer process                                                        system, so that two or more
                                                                                                 independent processes have the
                                                                         Reader process            same access to IPC recourses
                                                                                                     (shared memory, message
                                                                                                                queue)

                                                                                                   So if you have two programs,
                                                                                                       both of which execute

                                                                                                       key = ftok("/home/
                                                                                                        somefile", 'a');

                                                                                                 both will get the same token and
                                                                                                   can therefor access the same
                                                                                                    resources (shared memory,
                                                                                                         message queues).

    To  run:                                                                                      The specified path must specify
                                                                                                 an existing file that is accessible
    > ./writer                                       To run:                                            to the calling process
    Write Data : i love you
                                                     > ./reader
                                                     Data read from memory: i love you
```

## Slide 42

```text
Operating Systems
         IPC - Inter Process Communication

          message queue

-     a linked list of messages stored in kernel
-     identified by message queue identifier
-     managed by kernel through system calls
-     a new queue is created or an existing queue
      opened by system call  (e.g. msgget() in Linux)
-     all processes can exchange
      information through access to a
      common system message queue
-     slower than shared-memory
```

## Slide 43

```text
Operating Systems
            IPC - Inter Process Communication

             message queue

Linux example to show more detail of how to use
message queue to communicate




                                Writer process                                            Reader process



                                     The msgsnd() system call                                      The msgrcv() system call
                                      appends a copy of the                                        removes a message from
                                            message                                                       the queue
                                        pointed to by msgp                                            specified by msqid
                                      to the message queue                                         and places it in the buffer
                                        whose identiﬁer is                                            pointed to by msgp
                                        speciﬁed by msqid.
```

## Slide 44

```text
Operating Systems
        IPC - Inter Process Communication

          semaphore



To access the shared memory a process need to do the following:

1. Check the semaphore, if semaphore > 0, the process
    can use the resource.   then semaphore -=1
                                                                           This is mainly not
     indicating that it has used one unit of  resources                       for message
                                                                               exchange.
2. if semaphore==0, the process goes to sleep until                          we will discuss
     it becomes > 0, then wake up and return to 1                                 this
                                                                                in detail
3. if a process is done with resources, semaphore+=1                               in
                                                                                 thread
                                                                            synchronization
```

## Slide 45

```text
Operating Systems
Process Communication/Synchronization


                                        Linux
                      process synchronization methods:
                                SEM (semaphore),
                           File Locking/record locking
                 (through the FCNTL settings, record Lock)
                                        Futex
             (Fast User-state mutex based on shared memory).

                             Pthread_mutex. (POSIX)
                                   pthread_cond
                 (condition variables) for threads (pthread).

                      process communication methods:
                                    pipe (pipe),
                                FIFO (named pipe),
                                  socket (socket),
                             SHM (Shared memory),
                         MSG queue (Message queue),
                                 mmap (file map).
```

## Slide 46

```text
Operating Systems
Process Communication/Synchronization



                                    Windows


                           process synchronization      :
                                      Mutex
                                   Semaphore
                                      event

                           process communication:
                               memory mapping
                                    pipelines
                                    messages

                    (memory mapping is the most basic,
                other process communication methods are
                    internal memory mapping to complete)
```

## Slide 47

```text
Operating Systems
Process Communication/Synchronization


                                                                     To summarize
























                                                                    signal is also used here
```

## Slide 48

```text
Operating Systems






to be continued
```
