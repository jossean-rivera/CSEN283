# Professor Slides 01-Introduction

> [!warning] Reference transcript, not confirmation of lecture coverage
> Extracted text from the professor’s slide PDF. Page numbers are PDF page numbers. Extraction can omit diagrams or distort reading order and formulas. Refer to the original PDF for full content. A slide’s presence does not establish that it was discussed. Prioritize the topic files’ lecture records.

**Original PDF:** `/Users/jossean/Documents/CSEN 283/Professor Slides 01-Introduction.pdf`

**Pages with no extracted text:** None; diagrams may still be omitted.

## Slide 1

```text
Operating Systems
                       COEN 283
                      Yuan Wang
                        lecture 1
```

## Slide 2

```text
Who am I

• Name: Wang, Yuan
• Email:   ywang6@scu.edu
• Office: Heafey 205

  Friday 11:30am-1:30pm
```

## Slide 3

```text
Class Rules
```

## Slide 4

```text
Health Rules
```

## Slide 5

```text
Health Rules
```

## Slide 6

```text
Health Rules
```

## Slide 7

```text
About this course

• Operating Systems

• Lecture material and assignments:   Camino

• Grade:   assignments (50%) + exams (50%)

                (approximately)

• No text book.
                                   Operating System Concepts 9th Edition
  Example references:                by Silberschatz, Galvin, and Gagne

                                              internet/chatGPT
```

## Slide 8

```text
Operating Systems

Start
```

## Slide 9

```text
Operating Systems





 What is Operating System?
```

## Slide 10

```text
Operating Systems
      Operation System is Middle person
between bare computer and user/applications
```

## Slide 11

```text
Operating Systems
To  put    in  a  more      formal      but   still  a  simpli    ﬁ ed    way:
```

## Slide 12

```text
Operating Systems







What are the formal reasons we need
          Operating Systems ?
```

## Slide 13

```text
Operating Systems
Reason 1

                   Bare metal (naked computer) is hard to use


       A bare metal allow user to feed binary instructions to CPU and
                            execute binary instructions.

          Obviously,  programming a bare metal is not an easy job.


              Solution

              Provide a
    set of services (interface)
           in the form of
system calls API (through traps)

    This interface should be
  minimal but can combine to
 oﬀer big degree of generality
```

## Slide 14

```text
Operating Systems
Reason 2

                    Multiple programs are sharing a computer


        A computer is executing multiple programs at the same time.
                                    (multiprocessing)
    These programs are sharing system resources (CPU, memory, etc)


         CPU sharing issue

   All processes get a chance to
                execute
      (time-share/scheduling)

One process’s failure will not aﬀect
           other processes
        (isolation/protection)

   Process need to intentionally
      interact  with each other
          (synchronization)
```

## Slide 15

```text
Operating Systems
Memory Sharing Issue               Memory are referring to RAM and DISK


                                      Two tasks

 Each process has its own private
            address space

Process need to be smartly loaded
   into memory without physical                  Data need to be organized
               limitation.                                       and
  Multiple processes are sharing                       stored persistently
     memory. So process space                   (Data available after reboot)
              protection
        is also an issue here.                 This task is accomplished by
                                                            File System

    This task is accomplished by
            Virtual Memory
          (paging/swapping)
```

## Slide 16

```text
Operating Systems
Example - shell

                                                                   Application
                                                                    is running
                                                                       here
```

## Slide 17

```text
Operating Systems



For user, you can either use
              GUI

        for example,
        to open a ﬁle:
         double click
           on icon













                                                                                     or terminal

                                                                                    for example,
                                                                                   to open a ﬁle
                         image opened                                         use “open” command
```

## Slide 18

```text
Operating Systems









Examples of Operating Systems
```

## Slide 19

```text
Operating Systems
```

## Slide 20

```text
Operating Systems
```

## Slide 21

```text
Operating Systems
```

## Slide 22

```text
Operating Systems
```

## Slide 23

```text
Operating Systems








What exactly Operating System can do?

       history of computer systems
```

## Slide 24

```text
Operating Systems
    Weaving Machine


   parallel threads (the “warp”) are
stretched across a rectangular frame

  every other warp thread is raised
                    slightly.















                                                                        A green (the “weft thread”) is then passed
                                                                      (at a 90 degree angle to the warp) through the
                                                                      space (the “shed”) between the lower and the
                                                                                    upper warp threads.

                                                                      The raised warp threads are then lowered and
                                                                      the previously lower warp threads are raised,
                                                                     and the weft thread is then passed through the
                                                                              shed in the opposite direction.
```

## Slide 25

```text
Operating Systems
Loom
```

## Slide 26

```text
Operating Systems
Joseph Marie Jacquard (7 July 1752 – 7 August 1834)
                          French weaver
               invented Jacquard loom (1801)

            used “punch card” to control loom
   each card corresponds to one row of the design



                                               hole: hook can go through
                                                no hole: hook is blocked

                                              similar to the idea of 1 and 0
                                                     in computer














   https://www.youtube.com/watch?v=MQzpLLhN0fY
```

## Slide 27

```text
Operating Systems

          Charles Babbage
(26 December 1791–           18 October 1871)
        “Father of the Computer”                         Analytical Engine
                                                         general purpose
mathematician, philosopher, inventor and                   successor of
             mechanical engineer                            Difference
    originated the concept of a digital                       Engine
         programmable computer



















                                  Diﬀerence Engine
                                computer polynomial                            able to use
                                      function                                Punch Card
```

## Slide 28

```text
Operating Systems
             First Generation Computer
        vacuum tube computer (1940-1955)

ENIAC: ﬁrst electronic general purpose computer
(1945)
https://www.youtube.com/watch?v=B0jZMPZuOQU
no Operating Systems                                            First programmers
programmed by plugboard/switches                                    (all women)
perform decimal arithmetic
```

## Slide 29

```text
Operating Systems
                                First Generation Computer
                            vacuum tube computer (1940-1955)

- use machine language
- takes long time to set up program
  and end the program
- input:  punch card, tape
- low eﬃciency
- no interaction
- solve one problem at a time
   each user schedule a time to use the
   computer and the computer is all used by himself
```

## Slide 30

```text
Operating Systems
                                 First Generation Computer
                             vacuum tube computer (1940-1955)

In 1954, ﬁrst commercial computer IBM 704 were built. 136 were
sold.

In 1956, ﬁrst Operating System developed: GM-NAA I/O  (for
IBM704)
creators came from
General Motor Research and
North Amarican Aviation


  IBM 704 is a ﬁrst commercially
        successful  vacuum
     tube scientiﬁc  mainframe

           Main Function:
 Automatically execute a program
    after a previous program is
               ﬁnished
      Batch Processing               See next slide
```

## Slide 31

```text
Operating Systems
       Batch Processing


                      Programs called monitors
               (the forerunners of operating systems)
    were developed which could process a series, or “batch” of
        programs, often from magnetic tape prepared ofﬂine.



   The monitor
would be loaded
into the computer
 and run the ﬁrst
 job of the batch.

At the end of the
    job it would
  regain control
and load and run
the next until the
     batch was
     complete
```

## Slide 32

```text
Operating Systems
Batch Processing - simple batch                        only “one channel”.

                                                       CPU only attend to
                                                     one program at a time
```

## Slide 33

```text
Operating Systems
Batch Processing (improvement)
       multiprogrammed batch                                                    To reduce   CPU   idle time,
                                                                              “multi  channel ”  is managed
                                                                   The   system   is able to switch  to other  program
                                                                         before  the program    is totally ﬁnished
```

## Slide 34

```text
Second         Generation           Computer                                                              Operating Systems
      transister        computer          (1956     -  1965)


                                                                                                                                     .data
                                                                                                                                     a dw 02h
                                                                                                                                     b dw 08h
Transistor replace vacuum tube
                                                                                                                                     .code
                                                                                                                                     MAIN PROC
                                                                                                                                     mov ax,@data
                                                                                                                                     mov ds,ax
Tr a n s i s t o r was     invented         at   Bell    Labs      in  1947      but    did    not                                   mov ax,a
                                                                                                                                     mov bx,b
see    widespread             use    in   computers            until    the    late    1950s.                                        add ax,bx
                                                                                                                                     add ax,30h
computer          become          smaller,        faster,     cheaper,                                                               mov ah,02
                                                                                                                                     mov dx,ax
                                                                                                                                     int 21h
more      energy        e ﬃ  cient
                                                                                                                                     main endp
                                                                                                                                     end main
input:     Punch        Card
output:       print    out
Language:            assembly           language
program         stored       in   memory
(move       from      magnetic          drum
to   magnetic           core)
```

## Slide 35

```text
Third Generation Computer                             Operating Systems
          Integrated Circuit (1965-1971)

Transistors were minimized and placed into silicon chips (semi
conductor)
Invented by Jack Kilby
Nobel Prize in Physicson          December 10, 2000

Examples: mini-computer
                   DEC PDP series
                   IBM-360 series computers.

Much smaller
Has operating system
input: keyboard, monitor
consume less power
high level language
```

## Slide 36

```text
Forth Generation Computer                                  Operating Systems
     Very Large Scale Integrated Circuit (1971 – 1980)

Microprocessor                            IBM immediately released detailed information.
First IBM PC in 1981                          The US$36 IBM PC Technical Reference
                                           Manual included complete circuit schematics,
1983 IBM PC/XT                                  commented ROM BIOS source code,
                                       and other engineering and programming information
10 megabyte hard drive.                         for all of IBM's PC-related hardware,
256kB     of memory                          plus instructions on designing third-party
                                                             peripherals.
1984 IBM PC/AT
20 megabyte hard drive.
support multi-tasking (protected mode)

Operating System: DOS
(Disk Operating System)
```

## Slide 37

```text
Operating Systems





                                                                        Computer
                                                                       architecture/
                                                                       organization













We  want   to run
  program    on
      this
```

## Slide 38

```text
Operating Systems












OS concepts
```

## Slide 39

```text
Diﬀerent Systems                                                             Operating Systems



   Simple system                        Conventional operating system
that supports only                    that can support multiprogrammed
a single application                  execution of multiple applications
```

## Slide 40

```text
Operating Systems
Operating System


     Each running program, called a process,
           occupies memory containing
           instructions, data and stack：

 Instructions:  implement programs computation;
 Data: variables on which the computation acts;
 Stack: organizes the program’s procedure calls.





                                                                   A special program
                                                                that provide services to
                                                                   running programs
                                                                            and
                                                                 manage CPU/memory
                                                                        Resources

                                                                        It is called
A given computer typically as many
processes but only a single kernel                                         Kernel
```

## Slide 41

```text
Operating Systems
   Operating System

The whole system                                                                       User Space

   will become
     like this                                                                        Kernel Space




                                                                             there are simply 2 spaces
Software/
Hardware
 Interface
```

## Slide 42

```text
Operating Systems
           User mode vs Kernel mode


                                       Kernel mode is generally reserved for the lowest-level, most
    In Kernel mode, the                trusted functions of the operating system. Crashes in kernel
    executing code has                       mode are catastrophic; they will halt the entire PC.
complete and unrestricted
 access to the underlying
 hardware. It can execute
 any CPU instruction and
  reference any memory
           address.



 In User mode, the executing code
  has no ability to directlyaccess
   hardware or reference memory.

  Code running in user mode must
 delegate to system APIs to access
         hardware or memory.
  Due to the protection afforded by
this sort of isolation, crashes in user
    mode are always recoverable.                                                           These two modes
 Most of the code running on your                                                          aren't mere labels;
computer will execute in user mode.                                                     they're enforced by the
                                                                                             CPU hardware.
```

## Slide 43

```text
Operating Systems
       User mode vs Kernel mode





 In MacBook, open
  Activity Monitor
(Application/Utilities)
    you can see
    kernel_task


















          System and User
```

## Slide 44

```text
Operating Systems
       User mode vs Kernel mode



         In Windows, open
             Task Manager
       you can see kernel time

The  green line is total CPU time

     The red line is Kernel time.

     The gap between the two is
               User time.
```

## Slide 45

```text
Operating Systems
Operating System


        Diﬀerent design philosophy of an operating system
```

## Slide 46

```text
Operating System                                                                    Operating Systems

                                                                               OS (Kernel)
                                                                      is a big event-driven loop.

                                                                           It consists of set of
                                                                              “trap handler”

                                                                    Trap is the only gate to kernel

                                                                            It’s a dispatcher.



                                                                               will check out
                                                                           an example OS XV6
                                                                                     later
                                                                             for the detail ﬂow






Trap is the driving engine of an
       Operating Systems
```

## Slide 47

```text
Operating Systems
Operating System








               system call
```

## Slide 48

```text
Operating Systems
  Operating System


          User at higher level is supported by
                     lower level functions
                                                                  a way for acomputer   programrequests   a
                        - system call                              service from thekernelof  theoperating
                                                                                   system







The request
may include
 hardware-
   related
  services
(for example,
 accessing
 a hard disk
    drive)
```

## Slide 49

```text
Operating Systems
Operating System












               interrupt
```

## Slide 50

```text
Operating Systems
Operating System


       In 60s, two techniques become more mature:
                      Interrupt Technique

                  Data Channel Technique

        In 1961, ﬁrst multi-tasking operating system
           run on Leo III machine (British company)
















                                       https://www.youtube.com/watch?v=T9yFubSufck
```

## Slide 51

```text
Operating Systems
                           Interrupt Technique

Interrupt is a requesting signal  (from Hardware or Software) to CPU.

             CPU received the signal then do the following:

    interrupt the current                      “process the request”
        execution code                      is    done by calling a program
                                                          called
  process the request in a                        Interrupt Handler
        timely manner                            (There are diﬀerent
                                               Interrupt Handlers for
                                                 diﬀerent interrupts)
   return back to previous                      CPU will jump to the
           execution                             Handler to execute
                                                 when it is signaled
```

## Slide 52

```text
Interrupt Technique                                        Operating Systems




                  CPU
               execution
```

## Slide 53

```text
Interrupt Technique - example                              Operating Systems
```

## Slide 54

```text
Interrupt Technique                               Operating Systems



                 How to ﬁnd interrupt handler?

           Each interrupt has an Interrupt Number.

         Each Interrupt Handler has a start address

        x86 architecture has 256 interrupts (0 ~ 255)

An IVT (Interrupt Vector Table) is used to store the pair of:

         { Interrupt Number : Handler start address }
```

## Slide 55

```text
Interrupt Handler                                               Operating Systems



1023

                                                                                      0F = 15
                                                                                      10 = 16
                                                                                      1F = 31
                                                                                      20 = 32
                                                                                      3F = 63
                                                                                      40 = 64
                                                                                     FF = 255
```

## Slide 56

```text
Operating Systems
     Interrupt technique - issue

One issue with interrupt technique:








                                                               data transfer
                                                                    need
                                                                     the
                                                                involvement
                                                                      of
                                                                    CPU
```

## Slide 57

```text
Operating Systems
        DMA (Direct Memory Access)                                           ACC: accumulator register
                Channel technique                                   (for storing intermediate arithmetic and logic
                                                                                      results




















 specialized
processor for
     data
transmission
  between
memory and
     I/O
```

## Slide 58

```text
Operating Systems











Time-Sharing system
```

## Slide 59

```text
Operating Systems
Time-Sharing system

 In many situations like academic environment,
  students and faculties need direct access to
           central computers  (multi-user)
```

## Slide 60

```text
Operating Systems
    Time-Sharing system                                 Timesharing is particularly effective when

                                                           each user wants to use the computer
     Timesharing allows a central                           intermittently, such as for program
                                                         development, which has long periods of
  computer to be shared by a large                          editing followed by short test runs.
number of users sitting at terminals.
                                                         It is not suitable for the batch processing
                                                             that is typical of commercial data
Each program in turn is given use of                           processing or for large-scale
   the central processor for a fixed                                   computation.
                period of time

When the time is up, the program is
  interrupted and the next program
           resumes execution.

       This is called time slicing.

 A program can also be interrupted
 before the end of the time slice, for
  example it might have to wait for
                     input.
```

## Slide 61

```text
Operating Systems
   Time-Sharing system


Timesharing dominated academic computing until the late 1980s,
            when it was replaced by personal computers.

  Even then, the differences between academic and commercial
 computing remained and led universities to build the first large
                   networks of personal computers.

 As a result academic computing and the computing mainstream
followed separate paths for about thirty years, from the mid-1960s
                                to the 1990s.
```

## Slide 62

```text
Operating Systems
Time-Sharing system



     As far as the Operating System feature is concerned,
obviously we need the ability that manage switching different
               programs that are sharing the system
                             (context switch)

         The module that will do this, is call: scheduler





                                         Recall “Reasons for OS” earlier:

                                                       Services

                                                   Resource share
```

## Slide 63

```text
Operating Systems










Operating Systems Development
```

## Slide 64

```text
Operating Systems
        Operating Systems


Example of early time sharing systems

Multics: (Multiplexed Information and Computing Services)
MIT, 1965                                                   memory management technique of
https://www.multicians.org/                                  division of a computer's primary
                                                                  memory into segments

                                                             it is used to implement memory
                                                            protection and paging (and virtual
                                                                         memory)


                                                                is a memory management
                                                                technique that provides an
                                                          abstraction of the storage resources
                                                          that are actually available on a given
                                                                          machine

                                                           which creates the illusion to users
                                                              of a very large (main) memory.
```

## Slide 65

```text
Operating Systems
        Operating Systems
Unix
Bell Labs, 1970
multitasking, multiuser computer operating systems
originated from Multics
                                                          In 80s, commercial Unix like
                                                                   HP-UX (HP)
   People withdrawn from Multics                                    AIX (IBM)
                 project                                          Solaris (SUN)
      wanting to re-implement a                                 SCO Unix (SCO)
  less complicated system smaller                               MINIX (mini-unix)
                  scale                                                1987
          with the same goal
                                                            In 90s, Unix-like system:
                                                  BSD (Berkeley Unix, FreeBSD, OpenBSD)
                                                                      Linux

    Originally written in Assembly
       then rewritten in C (1973)
                                                 In 2000, Apple releasedDarwin, also a Unix
                                                  system, which became the core of the Mac
                                                   OS X operating system, which was later
                                                                renamed macOS.
```

## Slide 66

```text
Operating Systems
        Unix

                Unix distinguishes itself from its predecessors as:

                              the ﬁrst portable operating system

almost the entire operating system is written in the C programming language,
                  thus allowing Unix to reach numerous platforms


          C was originally developed at Bell Labs by Dennis Ritchie
       between 1972 and 1973 to make utilities running on Unix. Later,
it was applied to re-implementing the kernel of the Unix operating system.
```

## Slide 67

```text
Operating Systems


History of Unix-like system
```

## Slide 68

```text
Operating Systems
POSIX Standard


          Portable Operating System Interface for Computing Systems
              a cluster of standards developed by IEEE and ISO/IEC

    The standard is based on existing UNIX practices and experiences and
             describes the operating system's call service interface.

           By 1990, POSIX.1, in conjunction with the already adopted
 C language standard, was formally approved as the IEEE 1003.1-1990 (also
                     ANSI) and ISO/IEC 9945-1:1990 standards


   In the early 1990s, the formulation of the POSIX standard was in the
                                 final stage of voting,
                       which was between 1991 and 1993.
                                     At this point,
when Linux was just starting out, this UNIX standard provided extremely
                         important information for Linux,
                          enabling Linux to be developed
        under the guidance of standards and compatible with most
                              UNIX operating systems.
```

## Slide 69

```text
GNU project                                                            Operating Systems



      The GNU Project and the Free Software Foundation were founded by
  Richard M. Stallman (RMS) in 1984 to develop a complete operating system
                           similar to UNIX and free software:
                       GNU system (GNU is "GNU's Not Unix”).
Various GNU operating systems using Linux as the core are being widely used.
             Although these systems are often referred to as "Linux,"
                       Stallman believes that, strictly speaking,
                 they should be referred to as GNU/Linux systems.


    By the early 1990s, the GNU project had
  developed many high-quality free software,
   including the Emacs, Bash shell, gcc, gdb
                       debugger.
These softwares create a suitable environment
   for the development of the Linux operating
                        system.
 This is one of the foundations for the birth of
                          Linux
```

## Slide 70

```text
Operating Systems
          MINIX



   Developed by Andrew S. Tanenbaum (AST)
Dutch / American computer scientist (born in NY)

   He received his Bachelor of Science degree
in Physics from MIT in 1965 and his Ph.D. degree
in astrophysics from the University of California,
                   Berkeleyin 1971.

Very dedicated to teaching and published several
                   important books.
```

## Slide 71

```text
Operating Systems
         MINIX


                  1987. AST write a MINIX (clone of Unix) for IBM PC.

                        The purpose is for students to learn OS.

  As an operating system, MINIX is not an excellent one, but it provides system
    source code written in C and assembly language. This is the first time that
aspiring programmers or hackers have been able to read the operating system's
        source code. One of the aspiring programmers was Linus Torvalds



       The current version is MINIX 3
   It is a minimal but functional UNIX
 system with X, bash, pdksh, zsh, cc,
gcc, perl, python, awk, emacs, vi, pine,
 ssh, ftp, the GNU tools and over 400
  other programs. It is all based on a
 tiny microkernel and is available right
                      now.
```

## Slide 72

```text
Operating Systems
       MINIX

MINIX 1 and MINIX 3 are related in the same way as Windows 3.1
                and Windows XP are: same first name.

MINIX 1 was written as an educational tool; it is still widely used
                          in universities as such.

    MINIX 3 a highly reliable, self-healing, bloat-free operating
                                   system,



      MINIX 3 still has an active
development community with over
50 people attending MINIXCon 2016,
a conference to discuss the history
          and future of MINIX.
```

## Slide 73

```text
Operating Systems
MINIX


           MINIX is a microkernel architecture.
```

## Slide 74

```text
Operating Systems
MINIX


           MINIX is a microkernel architecture.
```

## Slide 75

```text
Operating Systems
          Linux



 Linux is a clone system of the UNIX operating system. It was born on
                                  October 5, 1991

    With the aid of the Internet, through the joint efforts of computer
enthusiasts all over the world, it has now become the most widely used
type of UNIX operating system in the world, and the number of users is
                                still growing rapidly
```

## Slide 76

```text
Operating Systems
         Linux - more detail history

  In 1981, IBM introduced the world-renowned microcomputer IBM PC


Between 1981 and 1991, the MS-DOS operating system was always the
               master of the microcomputer operating system


UNIX price is high, and it is used in big computers, PC users can not get
                                     close to it

                           UNIX source code is guarded against disclosure
```

## Slide 77

```text
Operating Systems
           Linux - more detail history

                                   MINIX appeared.

 with a book by Tanenbaum describing the principles of its design and
                               implementation details.

  Almost all computer enthusiasts around the world began to read this
  book in order to understand how the operating system works. It also
                        includes Linus Benedict Torvalds


At that time (in 1991), he was a sophomore in
 the Department of Computer Science at the
   University of Helsinki and a self-taught
  computer hacker. The 21-year-old Finnish
 young man likes to drum up his computer
 and test the performance and limitations of
the computer. But what he lacked at the time
 was a professional-level operating system.
```

## Slide 78

```text
Operating Systems
            Linux - more detail history

     During the same time, GNU developed number of software tools,
                                     including GNU C


To better learn computer knowledge (perhaps just for interest), Linus purchased
  a 386-compatible computer using Christmas lucky money and loans, and mail
              ordered a MINIX system software from the United States.


      While waiting for the MINIX software,
 Linus learned the hardware knowledge of Intel
                   80386 seriously.
  In order to be able to connect to the school's
      mainframe through a Modem dial-up,
    he uses assembly language and uses the
multitasking features of the 80386 CPU to create
          a terminal emulation program.
 Later, in order to copy his own software on an
    old computer to a new computer, he also
     compiled drivers for floppy disk drives,
    keyboards, and other hardware devices.
```

## Slide 79

```text
Operating Systems
         Linux - more detail history



 From April 1991, he began to
   develop his own operating
    system by modifying the
terminal emulation program and            According to Linus's news
        hardware drivers.                         release on the
                                         comp.os.minix newsgroup,
                                            we can see that he has
                                            gradually evolved from
                                          learning the MINIX system
                                                     stage to
                                          developing his own Linux
                                                      system.
```

## Slide 80

```text
Operating Systems
         Linux - more detail history


 On October 5th,
    1991, Linus
    published a
 message on the
  comp.os.minix
    newsgroup,
      officially
  announcing the
 birth of the Linux
   kernel system
  (Free minix-like
kernel sources for
386-AT). This news
 can be called the
birth declaration of
        Linux
```

## Slide 81

```text
Operating Systems
          Linux - more detail history


Linux is Monolithic Kernel
```

## Slide 82

```text
Operating Systems
          Linux - more detail history

Linux is Monolithic Kernel
```

## Slide 83

```text
Operating Systems
                     Tanenbaum-Torvalds Debate


 In early 1992, noticing
 the way that the Linux
  discussion had taken
 over the discussion in
     comp.os.minix,
 Tanenbaum decided it
was time to comment on
           Linux.





 "a giant step back into
        the 1970s"
```

## Slide 84

```text
Operating Systems
             Tanenbaum-Torvalds Debate


 Most older operating systems are monolithic, that is,
the whole operating system is a single a.out file that
   runs in 'kernel mode.'  This binary contains the
  process management, memory management, file
 system and the rest. Examples of such systems are
  UNIX, MS-DOS, VMS, MVS, OS/360, MULTICS, and
                         many more.


             True, linux is monolithic, and I agree that
        microkernels are nicer. With a less argumentative
       subject, I'd probably have agreed with most of what
           you said. From a theoretical (and aesthetical)
                        standpoint linux looses.
      If the GNU kernel had been ready last spring, I'd not
        have bothered to even start my project: the fact is
        that it wasn't and still isn't. Linux wins heavily on
                    points of being available now.
```

## Slide 85

```text
Operating Systems
             Tanenbaum-Torvalds Debate


 The alternative is a microkernel-based system, in which
   most of the OS runs as separate processes, mostly
  outside the kernel.  They communicate by message
   passing.  The kernel's job is to handle the message
      passing, interrupt handling, low-level process
  management, and possibly the I/O. Examples of this
design are the RC4000, Amoeba, Chorus, Mach, and the
                not-yet-released Windows/NT.



 If this was the only criterion for the "goodness" of a kernel,
     you'd be right.  What you don't mention is that minix
      doesn't do the micro-kernel thing very well, and has
    problems with real multitasking (in the kernel).  If I had
     made an OS that had problems with a multithreading
 filesystem, I wouldn't be so fast to condemn others: in fact,
 I'd do my damn best to make others forget about the fiasco.
```

## Slide 86

```text
Tanenbaum-Torvalds Debate                                           Operating Systems


 MINIX was designed to be reasonably portable, and
   has been ported from the Intel line to the 680x0
  (Atari, Amiga, Macintosh), SPARC, and NS32016.

LINUX is tied fairly closely to the 80x86.  Not the way
                             to go.


    The fact is that linux is more portable than minix.  What? I
     hear you say.  Porting things to linux is generally /much/
                   easier than porting them to minix.



   As most of you know, for me MINIX is a hobby,
something that I do in the evening when I get bored
      writing books and there are no major wars,
 revolutions, or senate hearings being televised live
on CNN.  My real job is a professor and researcher in
             the area of operating systems.
```

## Slide 87

```text
Operating Systems
Tanenbaum-Torvalds Debate


  You use this as an excuse for the limitations of minix? Sorry,
 but you loose: I've got more excuses than you have, and linux
     still beats the pants of minix in almost all areas.  Not to
    mention the fact that most of the good code for PC minix
            seems to have been written by Bruce Evans.


  your job is being a professor and researcher: That's one hell
   of a good excuse for some of the brain-damages of minix. I
  can only hope (and  assume) that Amoeba doesn't suck like
                               minix does.
```

## Slide 88

```text
Operating Systems
Tanenbaum-Torvalds Debate
                                                                Hello?

      My computer just crashed




         A multithreaded file system is only a
   performance hack.  When there is only one job
    active, the normal case on a small PC, it buys
    you nothing and adds complexity to the code.
    On machines fast enough to support multiple
 users, you probably have enough buffer cache to
       insure a hit cache hit rate, in which case
  multithreading also buys you nothing.  It is only
  a win when there are multiple processes actually
   doing real disk I/O.  Whether it is worth making
   the system more complicated for this case is at
                      least debatable.
```

## Slide 89

```text
Tanenbaum-Torvalds Debate                                                  Operating Systems




      Not true.  It's a performance hack /on a microkernel/, but it's an
      automatic feature when you write a monolithic kernel - one area
       where microkernels don't work too well (as I pointed out in my
       personal mail to ast).  When writing a unix the "obsolete" way,
        you automatically get a multithreaded kernel: every process
       does its own job, and you don’t have to make ugly things like
                  message queues to make it work efficiently.




      I still maintain the point that designing a monolithic
     kernel in 1991 is a fundamental error.  Be thankful you
    are not my student.  You would not get a high grade for
                            such a design :-)



       Well, I probably won't get too good grades even without you
```

## Slide 90

```text
Operating Systems
   Tanenbaum-Torvalds Debate



  My point is that writing a new operating system that is
closely tied to any particular piece of hardware, especially
      a weird one like the Intel line, is basically wrong.



    But /my/ point is that the operating system /isn't/ tied to any
        processor line: UNIX runs on most real processors in
   existence.  Yes, the /implementation/ is hardware-specific, but
   there's a HUGE difference.  You mention OS/360 and  MS-DOG
  as examples of bad designs as they were hardware-dependent,
    and I agree.  But there's a big difference between these and
    linux: linux API is portable (not due to my clever design, but
  due to the fact that I decided to go for a fairly-well-thought-out
                            and tested OS: unix.)



                sorry i overacted to your initial statement
                          i just love Linux too much
```

## Slide 91

```text
Operating Systems
                           Tanenbaum-Torvalds Debate

                                               LinuxCon 2009
                        "We're getting bloated and huge. Yes, it's a problem,"

                                            April 2019 interview
             Q: Have you and the kernel team been updating the kernel code to your
         satisfaction through the years? Is there any need or pressure to re-write any of
         the 25-year-old ever-expanding Linux code base? Perhaps in a more "modern"
                                             language than C?

         Linus: We've gone through many many big rewrites of most of the subsystems
                                             over the years……

           one of the advantages of a big unified source base for the whole kernel has
               been that when we need to make some big change, we can do so……

           As to C …. honestly, it doesn't look likely to change. The kind of languages
        people see under active development aren't for low-level system programming.
         They are to make it easier to create user applications with fancy UIs, etc. They
         explicitly don't want to do things a kernel needs, like low-level manual memory
                                                management.
```

## Slide 92

```text
Tanenbaum-Torvalds Debate                                                               Operating Systems


                                                              May 2006
              Actually, MINIX 3 and my research generally is **NOT** about microkernels. It is about
              building highly reliable, self-healing, operating systems. I will consider the job finished
                           when no manufacturer anywhere makes a PC with a reset button.

             I think improving reliability is the greatest challenge facing operating systems designers
             at this moment. The average user does not care about even more features or squeezing
                 the last drop of performance out of the hardware, but cares a lot about having the
                            computer work flawlessly 100% of the time and never crashing.

              So what do microkernels have to do with this goal? They make it possible to build self-
               healing systems. Moving most of the OS to a bunch of user processes, one for each
            driver plus various servers, does not reduce the number of bugs in the code but it greatly
             reduces the ability of each bug to do serious damage and it also reduces the size of the
                                                    trusted computing base.

              In our design, when most drivers fail, the reincarnation server can restart a fresh copy,
             and optionally save the core image of the dead driver for debugging, log the event, send
             email to an administrator or developer, etc. The system continues to run, and at the very
             least can be shut down gracefully without loss of work or data. Some components, such
             as the reincarnation server itself, the file server, and the process server are critical, and
            losing them crashes the system, but there is absolutely no reason to allow a faulty audio,
            printer, or scanner driver to wipe out the system. This is our goal: systems that can detect
               and repair their own faults. You can do this easily in a microkernel system. It is much
                                                 harder in a monolithic kernel.
```

## Slide 93

```text
Operating Systems


                   Tanenbaum-Torvalds Debate
```

## Slide 94

```text
Operating Systems
       Diﬀerent types of OS


                          Unix, Linux           a time bound system which has well
                                                   deﬁned ﬁxed time constraints.
           PC                                   Processing must be done within the
                                               deﬁned constraints or the system will
                                                                   fail.
      Multiuser
                                                          Example: RTLinux
Real Time (RTOS)
                                      a specialized operating system designed to
     Embedded                      perform a specific task for a device that is not a
                                     computer. (as oppose to general purpose OS)
       Network
                                                         also real time

   a specialized operating          Example: embedded linux (for android phone)
    system for a network                                       iOS
       device such as
 a router, switch or ﬁrewall.
```

## Slide 95

```text
Operating Systems
Operating System is not the only choice


                 Bare Machine Computing (BMC)
              (as oppose to OS-based computing)

             Programming based on bare machine

      OS-based system: hard to build SECURE system
                (attacker can gain admin privilege)

  BMC: simple, eﬃcient, small, secure (immune to attack)

                         Single execution environment

                           Direct access to hardware

           Using assembly language and languages like C/C++
                  to write program (just like writing an OS)
```

## Slide 96

```text
Operating Systems












 End
```
