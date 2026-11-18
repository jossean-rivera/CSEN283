# Professor Slides Lecture3-1-process-fork

> [!warning] Reference transcript, not confirmation of lecture coverage
> Extracted text from the professor’s slide PDF. Page numbers are PDF page numbers. Extraction can omit diagrams or distort reading order and formulas. Refer to the original PDF for full content. A slide’s presence does not establish that it was discussed. Prioritize the topic files’ lecture records.

Note: Sleep-function calls were rewritten as descriptions for Thea compatibility. Code examples containing those descriptions are not executable; consult the original PDF for exact syntax.

**Original PDF:** `/Users/jossean/Documents/CSEN 283/Professor Slides Lecture3-1-process-fork.pdf`

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






Process Creation
```

## Slide 3

```text
Creation                                                                                       Operating Systems

                                                      PID >= 0

Each process has process ID                               PID could be reused after other
                                                                  process terminated
    PID 0:  idle process


    PID1:  “init” process






 “init” process is invoked
      by kernel at the
         end of the
      boot procedure.
        it never dies
it is a normal user process
   not a system process
        within kernel
(in MacOS, this process is
           called
         “launchd”)
```

## Slide 4

```text
Creation                                                                      Operating Systems
                                           >ps -a
                                           ps -eo pid,ppid,user,comm
                                                                                          “htop”
                                                                                         utilities
                                                                                       on MacOS




















                                                                                     or
                                                                                    > top
```

## Slide 5

```text
Creation                                                                   Operating Systems




The system call “fork( )” is responsible to create new processes




    Parent process                   fork( )               Child process




            when parent process call fork( )                        this is a copy of
               it will create a copy of itself                        the parent
                                                                        process




           now there are 2 processes executing in the system
```

## Slide 6

```text
Operating Systems



  Process tree






 “init” start
these servers
 (“daemon”)
```

## Slide 7

```text
Operating Systems








Creation - fork( )
```

## Slide 8

```text
Creation - fork( )                                                                              Operating Systems


                                                                                            a copy of parent process
                                                                                                       is created
                                                                                                  but with diﬀerent
   Both processes will                                                                             address space
   start their execution
  at the next statement
 following the fork() call







   variables
   initialized                                                                                            if the parent
before the fork()                                                                                      changes the value
  call have the
same values in                                                                                          of this variable x
 both address                                                                                               later, the
    spaces                                                                                              modification will
                                                                                                         only affect the
                                                                                                         variable in the
                                                                                                        parent process's
                                                                                                         address space.
```

## Slide 9

```text
Creation - fork( )                                                                Operating Systems
                                                     Example














                                                                                 this code
                                        the code after fork( )               will be executed
                                                                                  by both
                                                                             parent and child
```

## Slide 10

```text
#include  <stdio.h>                                                                                                           Operating Systems
#include  <string.h>
#include  <sys/types.h>
#define   MAX_COUNT  500             same code
#define   BUF_SIZE   100                 from

int  main(void)                    previous slide
{
     pid_t  pid;
     int    i;
     char   buf[BUF_SIZE];

     fork();
     pid = getpid();
     for (i = 1; i <= MAX_COUNT; i++) {
          sprintf(buf, "This line is from pid %d,
                  value = %d\n", pid, i);
          write(1, buf, strlen(buf));
     }
     return 0;
}
 These two sections of                                                Output lines are intermixed in a rather unpredictable way.
  These two sections of code will run                                     Moreover, the order of these lines are determined by
code will run ocncurrentlyconcurrently.             .                                            the hart scheduler.




    #include  <stdio.h>                                                       #include  <stdio.h>
    #include  <string.h>                                                      #include  <string.h>                       In the process code after
    #include  <sys/types.h>                                                   #include  <sys/types.h>
    #define   MAX_COUNT  500                                                  #define   MAX_COUNT  500                                 fork( )
    #define   BUF_SIZE   100                                                  #define   BUF_SIZE   100                     the code itself doesn’t
    int  main(void)                                                           int  main(void)                                 know if the code is
    {                                                                         {                                                    parent code
         pid_t  pid;                                                               pid_t  pid;                                           or
         int    i;                                                                 int    i;
         char   buf[BUF_SIZE];                                                     char   buf[BUF_SIZE];                            child code

         fork();                                                                   fork();
         pid = getpid();                                                           pid = getpid();
         for (i = 1; i <= MAX_COUNT; i++) {                                        for (i = 1; i <= MAX_COUNT; i++) {
              sprintf(buf, "This line is from pid %d,                                   sprintf(buf, "This line is from pid %d,
                      value = %d\n", pid, i);                                                   value = %d\n", pid, i);
              write(1, buf, strlen(buf));                                               write(1, buf, strlen(buf));
         }                                                                         }
         return 0;                                                                 return 0;
    }                                                                         }
```

## Slide 11

```text
Operating Systems
              Sometimes, we need to distinguish
         parent code and child code in the program
                                                                                note:  fork( )’s return value and
                                                                                    getpid( )’s return value
We can     accomplish     this  by                                                       are different
checking    fork(  ) return  value
             like this
                                                              pid = fork( )




           bothboth parent parent and and child child has            fork( ) is called once but returns  twice
               hasvariable variable “pid” “pid”                      so  the two  “pid” has  diﬀerent  values


 /* code before fork( ) */                              /* code before fork( ) */

 pid = fork( );                                         pid = fork( );

 /* code after fork( ) */                               /* code after fork( ) */

             in the  parent   process                                   in the  child  process
           fork(  ) will return  the  PID                                fork(  ) will return  0
              of  the child  process
```

## Slide 12

```text
Operating Systems

      pid returned by fork()                      /* code before fork( ) */
   can be used to distinguish
    parent and child process.                     pid = fork( );

“pid” might be 3 diﬀerent values:                 /* code after fork( ) */



                                                                                child does not need fork( ) to provide
                                                                                                parent ID,
                                                    == 0                                there is only one parent.
         < 0                                  you are inside                       child process can call getppid( )
 creation of child                           a child process                                 to get parent ID
  process failed


                        > 0
             you are inside parent
                     process




               then this pid will be child’s id.
             parent can create many children,
               there is no function for parent
                to get process ID of a child.
    fork( ) will directly provide this child’s process ID
```

## Slide 13

```text
Creation - fork( )                                                                             Operating Systems

                               #include  <stdio.h>
                               #include  <sys/types.h>

                               #define   MAX_COUNT  500

                               void  ChildProcess(void);                /* child process prototype  */
                               void  ParentProcess(void);               /* parent process prototype */
     Example                   void  main(void)
                               {
                                    pid_t  pid;                                if fork( ) return 0, that means
 use fork() return                                                                 we are in child process
       value                        pid = fork();
                                    if (pid == 0)
          to                             ChildProcess();
   distinguish                      else
  child process                          ParentProcess();                         if fork( ) > 0, that means
                               }                                                  we are in parent process
         and
 parent process                void  ChildProcess(void)
                               {
                                    int   i;

                                    for (i = 1; i <= MAX_COUNT; i++)
                                         printf("   This line is from child, value = %d\n", i);
                                    printf("   *** Child process is done ***\n");
                               }

                               void  ParentProcess(void)
                               {
                                    int   i;

                                    for (i = 1; i <= MAX_COUNT; i++)
                                         printf("This line is from parent, value = %d\n", i);
                                    printf("*** Parent is done ***\n");
                               }
```

## Slide 14

```text
Creation - fork( )                                                                    Operating Systems

                                                  “pid”“pid”  inin  didiﬀﬀerenterent
                                                      processes process
                                                     areare  didiﬀﬀerenterent
```

## Slide 15

```text
Creation - fork( )                                                                                  Operating Systems
                                                 Result:
                                          parent output line
                                        and child output line
                                         will mixed together





                                                                                  CPU (Logic CPU = hart) scheduler
                                                                                       will assign a time quantum
                                                                                        to each process (thread),
                                                                                  the parent or the child process will
                                                                                 run for some time before the control
                                                                                        is switched to the other.
                                                                                 the running process will print some
                                                                                   lines before you can see any line
                                                                                     printed by the other process.







                                                                                               MAX_COUNT
                                                                                          (number of iterations)
                                                                                                need to be
                                                                                               relatively big
                                                                                                to see this
                                                                                                   eﬀect
```

## Slide 16

```text
Creation - fork( )                                         Operating Systems



     Practice             #include  <stdio.h>
     Example              #include  <sys/types.h>

what is the output        int main()
     of this?             {
                            fork();
                            printf("Hello world!\n");

                            return 0;
                          }                              after the fork()
                                                        parent and child
  result                                                  execute this


                hello world!
                hello world!
```

## Slide 17

```text
Creation - fork( )                                                           Operating Systems


                        #include <stdio.h>
                        #include <sys/types.h>
     Practice                                                      after the ﬁrst fork()
     Example            int main() {                                parent and child
                          fork();                                     execute this
what is the output        fork();
     of this?             printf("hello\n");
                          return 0;
                        }                                          p0 child 1


    result



           hello                                                                      p1 child
           hello
           hello
           hello                                                                 p0 child2
```

## Slide 18

```text
Creation - fork( )                                                            Operating Systems


                #include <stdio.h>                               after the ﬁrst fork()
 Practice       #include <sys/types.h>                             parent and child
 Example                                                             execute this
what is the     int main() {
output of         fork();                                                                   p1 child 1
   this?          fork();
                  fork();                           p0 child 1
                  printf("hello\n");
                  return 0;
                }



    result           hello
                     hello
                     hello                                                        p0 child 2
                     hello
                     hello
                     hello
                     hello                                                        p0 child 3
                     hello
```

## Slide 19

```text
Creation - fork( )                                                               Operating Systems

                        #include <stdio.h>
                                                    getppid(): gets the parent process ID
    Example             int main()                  getpid(): gets child process ID
       fork( )          {
    in a loop              int i;
                           for (i=0;i<3;i++)
     result                {
                              fork();
                              printf("[%d] [%d] i=%d\n", getppid(), getpid(), i);
[21945] [39647] i=0        }
[39647] [39648] i=0        printf("[%d] [%d] hi\n", getppid(), getpid());
[21945] [39647] i=1        exit(0);
[39647] [39648] i=1     }
[39647] [39649] i=1
[39648] [39650] i=1
[21945] [39647] i=2
[21945] [39647] hi
[39647] [39648] i=2
[39647] [39648] hi
[1] [39649] i=2
[1] [39649] hi
[1] [39650] i=2
[1] [39651] i=2
[1] [39650] hi                                                                            total of
[1] [39651] hi                                                                             8 “hi”
[1] [39652] i=2
[1] [39652] hi                                                                         14 printf(…, i)
[1] [39653] i=2
[1] [39653] hi
[1] [39654] i=2         5 parents plus “init”
[1] [39654] hi
```

## Slide 20

```text
Creation - fork( )                                                                 Operating Systems







       total
    number of
    processes
      forked
        by
      n fork()
       calls
```

## Slide 21

```text
Creation - fork( )                                                      Operating Systems




                      #include <stdio.h>
     Practice         void forkexample()
    Example           {
what is the output      // child process because return value zero
     of this?           if (fork() == 0)
                          printf("Hello from Child!\n");
                        // parent process because return value non-zero.
                        else
                         printf("Hello from Parent!\n");
                      }

    result            int main() {
                        forkexample();
                        return 0;
  Hello from Child!   }
 Hello from Parent!
        OR
 Hello from Parent!
  Hello from Child!
```

## Slide 22

```text
Operating Systems
  Creation - fork( )                                                     ﬂow chart equivalent
                                    #include <stdio.h>                      to the assembly
                                    void forkexample()                          sequence

                                    {

                                      if (fork() == 0)    // child process
            fork()                      printf("Hello from Child!\n");
                                      else                // parent process
           inside                      printf("Hello from Parent!\n");
              an                    }
       if statement.                int main() {
                                      forkexample();
                                      return 0;
                                    }

Note, internally, “if statement" is not like what we see here which is
natural-language-like high level language.

At compile time, the “if statement” will be translated into assembly code.

the assembly code sequence will  be like this:

1.                       jump to fork( ) function
2.                       cmp return_value, 0           ; compare fork return with 0
3.                       jump to “isfalse” label if false
4.                       code for child
5.                       jump to “next” label
6.      isfalse:     code for parent
7.         next:      rest of statements                      which means, fork is called
                                                               before the “if statement”
```

## Slide 23

```text
Creation - fork( )                                              Operating Systems



                    #include <stdio.h>

    Practice        void forkexample()
    Example         {
   what is the          int x = 1;
    output of           if (fork() == 0)
      this?                 printf("Child has x = %d\n", ++x);

                        else
                            printf("Parent has x = %d\n", --x);
                    }
                    int main()
                    {
                        forkexample();
  result                return 0;                 or
                    }

Parent has x = 0              Child has x = 2
Child has x = 2               Parent has x = 0
```

## Slide 24

```text
Creation - fork( ) — parent-child order                             Operating Systems


                       #include <stdio.h>

    Example            void forkexample()
To alter the order     {
       of                // child process because return value zero
 two processes,          if (fork() == 0) {
   we can call             sleep function with arguments 1;
     sleep function               printf("Hello from Child!\n");
what is the result        }
     of this?            // parent process because return value non-zero.
                         else {
                           sleep function with arguments 0;
                           printf("Hello from Parent!\n");
 result                  }
                       }

                       int main() {
                         forkexample();
                         return 0;
Hello from Parent!     }                                    child sleep for 1 second
Hello from Child!                                             so parent has better
                                                             chance to execute ﬁrst
```

## Slide 25

```text
Creation - fork( ) — parent-child order                                   Operating Systems



                        #include <stdio.h>

                        int forkexample()
    Example             {
                          //child process because return value zero
  check return            if (fork() == 0) {
 of parent and                sleep function with arguments 1;
                              printf("Hello from Child!\n");
  child in main               // parent process because return value non-zero.
                              return(1);
                            }
                          else {
                            sleep function with arguments 0;
                            printf("Hello from Parent!\n");
  result                    return(2);
                          }
                        }

                        int main() {
                          int ret;
Hello from Parent!        ret=forkexample();
 return value is 2        printf("return value is: %d\n", ret);
Hello from Child!         return 0;
 return value is 1      }
                                                                both processes return to here
```

## Slide 26

```text
Operating Systems






Who termination ﬁrst？
```

## Slide 27

```text
Parent terminate ﬁrst                                                                                         Operating Systems



        note                                                                                                      A process can cause the
                                                                                                              termination of another process
                                                                                                              via an appropriate system call.
   if the parent                                                                                              Usually, such a system call can
    finish first,                                                                                                         be invoked
        then                                                                                                      only by the parent of the
 what does the                                                                                                             process
“parent pointer”                                                                                                  that is to be terminated.
    of the child
     point to?



                                                                                                              All the resources of the process
   If P0 ﬁnished                                                                                                         — including
  before all child                                                                                             physical and virtual memory,
 processes ﬁnish                                                                                                          open files,
then child process                                                                                                        I/O buffers
    parent will                                                                                                       — are deallocated
      become                                                                                                      by the operating system.
        “init”



                                                    Whenever a process terminates,
                                         the kernel goes through all active processes to see
                       whether the terminating process is the parent of any process that still exists.
      If so, the parent process ID of the surviving process (orphan) is changed to be 1 (the process ID of init).
                                   This way, we’re guaranteed that every process has a parent
```

## Slide 28

```text
Parent terminate ﬁrst                                                                     Operating Systems


             Orphan process                                       Linux and UNIX address orphan
                                                             scenario by assigning the “init process”
                                                             as the new parent to orphan processes.
 When a parent process terminate
       before its child process,                                          The “init process”
   the child process becomes an                                      periodically invokes wait(),
                                                              thereby allowing the exit status of any
            “orphan process”                                  orphaned process to be collected and
                                                             releasing the orphan’s process identifier
         in process-table entry.                                       and process-table entry.





















                                                  An orphan process is not a problem.
                                    No special action is required from the user or the original parent.
```

## Slide 29

```text
Child terminate ﬁrst                                                       Operating Systems

                        #include <stdio.h>

                        void forkexample()
    Example             {
                          // child process because return value zero
what is the result        if (fork() == 0)
     of this?               printf("Hello from Child!\n");
                          // parent process because return value non-zero.
                          else {
                            sleep function with arguments 1;
 result                     printf("Hello from Parent!\n");
                          }
                        }

Hello from Child!       int main() {                     parent sleep for 1 second
Hello from Parent!        forkexample();                     so child has better
                          return 0;                        chance to execute ﬁrst
                        }


        If child terminate before parent, and if the parent is not checking child’s return status,
     then the resource associated with child (kernel process table) is not released by the system,
                (only when parent check the return status, will resource be released)
                           the child process is said to become ZOMBIE
```

## Slide 30

```text
Child terminate ﬁrst                                                                   Operating Systems



                                                      Zombie
                     All processes transition to this state when they terminate,
                            but generally they exist as zombies only briefly.

                              Once the parent check out the “dead body”,
                              the process identifier of the zombie process
                             and its entry in the process table are released.




When a process terminates,
      its resources are
         deallocated
 by the operating system.

  However, its entry in the
process table must remain
    there until the parent
     check out the child,
because the process table
contains the process’s exit
            status.
```

## Slide 31

```text
Child terminate ﬁrst                                                      Operating Systems


   ZOMBIE need           #include <stdio.h>

  to be dealt with       void forkexample()
                         {
                           // child process because return value zero     child sleep for 10 seconds
  To avoid child           if (fork() == 0) {
process becoming             sleep function with arguments 10;
    ZOMBIE,                  printf("Hello from Child!\n");
  parent need to           }
   “check out”             // parent process because return value non-zero.
    the child’s            else {
  “dead body”                wait(NULL);
 This is done by             printf("Hello from Parent!\n");
  calling wait()           }
function to check        }                                           wait( ) will block the parent
child return status                                                 until  kernel will send a signal
                         int main() {                                        to parent.
                           forkexample();                          (kernel send signal when child
                           return 0;                                         terminate)
    result               }



    Hello from Child!                             Parent can ignore this signal or provide a handler
   Hello from Parent!
```

## Slide 32

```text
Child terminate ﬁrst                                                                              Operating Systems

                                                                                   2.
                                                      At that point, the process may return a status value
                                                           (typically an integer) to its parent process
                                                      (termination status is in “stat” in wait() system call)













                                                   1.
    A process terminates when it finishes executing its final statement
and asks the operating system to delete it by using the exit() system call.


                              Note
               The name wait() is misleading:                                                  Analogy
             it does not mean the child is slow.
 It means the parent is waiting for the termination event,                    Child finishes the exam early and
     which may have already happened (in this case,                                      leaves the room.
       parent’s clean up job will happen right away)                          Parent comes later to pick up the
                                                                                                pencil.
     Parent can do it anytime, it just need to come to
                    pick up the dead body
```

## Slide 33

```text
Operating Systems
         wait( )


A call to wait() blocks the calling process until ONE of its child processes exits or a signal is
received.

After child process terminates, parent            continues its execution after wait system call instruction.

Child process may terminate due to any of these:
•  It calls exit();
•  It returns (an integer) from main
•  It receives a signal  (from the OS or another process)  whose default action is to terminate.



            if call with

            wait(&stat)

 “stat” is a pointer to integer.

   termination status of the
 terminated process is stored
 in the location pointed to by
          the argument.


    if don’t care about the status, call
                   wait(NULL)
```

## Slide 34

```text
Operating Systems
wait( )


                                   Linux Manual Page
              A child that terminates, but has not been waited for
                                  becomes a "zombie".

           The kernel maintains a minimal set of information about
                                       the zombie process
             (PID, termination status, resource usage information)
              in order to allow the parent to later perform a wait to
                              obtain information about the child.

                               As long as a zombie is not
          removed from the system via a wait, it will consume a slot in the
                                      kernel process table,
                                   and if this table fills,
                                        it will not be
                            possible to create further processes.

                                    If a parent process
            terminates, then its "zombie" children (if any) are adopted by
                                            init(1)
         which automatically performs a wait to remove the zombies.
```

## Slide 35

```text
Operating Systems
     wait( )
                             #include <stdio.h>
                             #include <sys/wait.h>
if we have more than         int main(void)
                             {
      one child,               pid_t pid;
         wait( )               if ((pid = fork()) < 0) {
      returns on                 printf("fork error");
    termination of             }
ANY of the children            else if (pid == 0) {               // first child

  (PID of that child)            if ((pid = fork()) < 0)          // fork again
                                   printf("fork error");
                                 else if (pid > 0)
 What if we want to                exit(0);    // parent from second fork == first child
 wait for a specific             // at this point, second value of pid == 0
process to terminate             // that means this is the second child;
(assuming we know                // the parent becomes init as soon
which process ID we              // as the real parent calls exit() in the statement above.
  want to wait for)              // continue executing,
                                 // when it is done, init will get our status.
                                 sleep function with arguments 2;
    use waitpid()                printf("second child, parent pid = %ld\n", (long)getppid());
                                 exit(0);
                               }
                               // pid > 0 case, this is the parent (the original process)
                               // pid is process id of the first child
                               if (waitpid(pid, NULL, 0) != pid)   // wait (blocked) for first child
                                 printf("waitpid error");

                               // continue executing,
                               // knowing that we’re not the parent of the second child.

                               exit(0);
                             }
```

## Slide 36

```text
Creation - wait( )                                                                                    Operating Systems






























                                                                                             even if second child sleep for
                                                                                                       2 second,
                                                                                                   doesn't guarantee
                                                                                              it will ﬁnish after ﬁrst child
```

## Slide 37

```text
Operating Systems
     wait( )             #include <stdio.h>

                         #include <string.h>
                         #include <sys/types.h>
You should wait for      int main(void) {
all children process
    to terminate           pid_t child_pid, wpid;
  so that no child         int status = 0;
 turn into zombie.         int n=10;

    you can use            //parent code (before child processes start)
      waitpid()            for(int id=0; id<n; id++) {
         to
wait for all children        if((child_pid = fork()) == 0) {
                               //child code
                               printf("in child: %d\n", getpid());
                               exit(0);
                             }
                           }

if, on the other hand,     // the parent waits for all the child processes
parent terminate ﬁrst,     while ((wpid = wait(&status)) > 0);
 all children’s parent
    point to “init”        // parent code (after all child processes end)
  “init” process will
    automatically          return 0;
 wait for all children
                         }
```

## Slide 38

```text
Operating Systems




       If a child wants to wait for its parent to terminate,
           a loop of the following form could be used:

                          while (getppid() != 1)
                                     sleep function with arguments 1;

                The problem with this type of loop,
                             called polling,
                     is that it wastes CPU time,
as the caller is awakened every second to test the condition.





                                                            To avoid     race conditions       and
                                                                     to avoid polling,
                                                        some form of signaling is required
                                                            between multiple processes.
```

## Slide 39

```text
Operating Systems
                                       How about
           creating a diﬀerent process instead of copy


                                                              Parent process
             After fork( ) call,                                         |
                                                                    fork( )
    kernel can load an executable                                        |
        image into the process                                 Child process
overwriting the old process memory                                       |
                                                                   exec( )
            by calling exec( )                                           |
                                                       Same process, new program
        fork() creates a process
 exec() changes what program the                        exec() destroys the current
               process runs                                       program
 (it does not create a new process)                 If the parent calls exec( ) directly:
                                                       The parent would disappear
             Most of the time
        exec( ) will follow fork()
           but not necessarily





     exec() replaces the current process image with a new program;
                     （The child is now running /bin/ls）
         if it succeeds, the original program no longer exists and
                     execution never returns to the caller.
    （control flow never goes back to the instruction after the exec()
                         call in the original program）
```

## Slide 40

```text
xv6 example                                                              Operating Systems




kexit(int status)
{
  struct proc *p = myproc();

  if(p == initproc)
    panic("init exiting");

  // Close all open ﬁles.
  …

  // Give any children to init.
  reparent(p);                                                          reparent(struct proc *p)
                                                                        {
  // Parent might be sleeping in wait().                                  struct proc *pp;
  wakeup(p->parent);
  …                                                                       for(pp = proc; pp < &proc[NPROC]; pp++){
  p->state = ZOMBIE;                                                        if(pp->parent == p){
                                                                              pp->parent = initproc;
  // Jump into the scheduler, never to return.                                wakeup(initproc);
  sched();                                                                  }
  panic("zombie exit");                                                   }
}                                                                       }
```

## Slide 41

```text
Operating Systems
                                                xv6 example


// Wait for a child process to exit and return its pid.
// Return -1 if this process has no children.
kwait(uint64 addr) {
  struct proc *pp;
  int havekids, pid;
  struct proc *p = myproc();

  for(;;) {
    // Scan through table looking for exited children.
    havekids = 0;
    for(pp = proc; pp < &proc[NPROC]; pp++) {
       if(pp->parent == p) {
           // make sure the child isn't still in exit() or swtch().
          havekids = 1;

          if(pp->state == ZOMBIE) {
             // Found one.
             pid = pp->pid;
             if(addr != 0 && copyout(p->pagetable, addr, (char *)&pp->xstate, sizeof(pp->xstate)) < 0) {
                   return -1;
             }
             freeproc(pp);
             return pid;
         }
       }
    }
    // No point waiting if we don't have any children.                                 If there are children but none have
    if(!havekids || killed(p)){
      return -1;                                                                                        exited yet, wait
    }
    // Wait for a child to exit.                                                               exit() will wake up parent
    sleep function with arguments p, &wait_lock;  //DOC: wait-sleep
  }
}
```

## Slide 42

```text
Operating Systems









End
```
