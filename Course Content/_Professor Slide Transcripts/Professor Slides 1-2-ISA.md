# Professor Slides 1-2-ISA

> [!warning] Reference transcript, not confirmation of lecture coverage
> Extracted text from the professor’s slide PDF. Page numbers are PDF page numbers. Extraction can omit diagrams or distort reading order and formulas. Refer to the original PDF for full content. A slide’s presence does not establish that it was discussed. Prioritize the topic files’ lecture records.

**Original PDF:** `/Users/jossean/Documents/CSEN 283/Professor Slides 1-2-ISA.pdf`

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



          Bottom-UP

Computer Architecture - ISA
```

## Slide 3

```text
Operating Systems
       Computer System




  Software

                                                                          we are here



Software/Hardware
     Interface
       (ISA)





  Hardware
```

## Slide 4

```text
Computer System Hardware                                                   Operating Systems


                                                             programs and data
                                                         are stored in a storage unit
 Historically there have been 2 types                       called memories and
             of Computers:                                   are treated the same
      Fixed Program Computers
         –Function is speciﬁc
        Can't be programmed,
            e.g. Calculators.
    Stored Program Computers –
         Can be programmed,
   Applications are stored on them




      Modern computers is                     Arithmetic and
 “stored -program” computer                     Logic Unit
            proposed by
  John von Neumann in 1945
```

## Slide 5

```text
Computer System Hardware                                                           Operating Systems

           John von Neumann (1903 - 1957)
                    Hungarian-American
     mathematician, physicist, computer scientist,
                         and polymath.


















                 He is a super smart person (most intelligent person ever lived)
      By the time of his death in 1957 at the of 53, the Hungarian polymath had not only
  revolutionized several subﬁelds of mathematics and physics but also made foundational
  contributions to pure economics and statistics and taken key parts in the invention of the
                      atomic bomb, nuclear energy and digital computing.
```

## Slide 6

```text
Operating Systems











x86 architecture
```

## Slide 7

```text
Computer System Hardware                                              Operating Systems




      1978, Intel ﬁrst 16-bit processor, 8086

     This is the ancestor of IA-32 processor
              (Intel Architecture 32-bit)



   All later processors are backward compatible
                            with it.




         which means, x86 CPU can run
             old 8086 machine code
```

## Slide 8

```text
8086 architecture                                                             Operating Systems



                                                       1）address（Segment ×16 + Offset       ）

       8086 is 16-bit Real Mode                           Physical address calculation：

                                                  PhysicalAddr = SegmentRegister << 4 + Offset
    a program can access/modify
        all addressable memory,                         done by CPU adder,  pure hardware
               I/O addresses                                  2）no paging hardware
                      and                                 CPU does not check page table
              other hardware                                   3）no privilege check
                                                               No privileged register
              This is not safe.                         👉  real mode cannot run modern OS

    and it provides no support for
           memory protection,
                multitasking,
        or code privilege levels.
```

## Slide 9

```text
Operating Systems
8086 architecture
                                                                                                    CS(code)，
                                                                                                     DS(data)，
                                                                                                    SS(stack)…
                                                                                                  hold segments


                                                                                                     SP(stack),
                                                                                                  IP(instruction)…
                                                                                                     hold offset
                                                                                                        value

                                                                                                    for example:
                                                                                                   stack address
                                                                                                          is
                                                                                                       SS:SP
```

## Slide 10

```text
Operating Systems
8086 architecture




An instruction is just an ordinary binary string



   CPU is able to retrieve and execute one
                instruction at a time



  if CPU see a non-instruction binary string
         there is going to be exception


       to make sure there is no problem,
         instruction and data are stored
               separately in memory


   all valid instructions recognized by CPU
                       is called
                   Instruction Set
                  of this processor
```

## Slide 11

```text
Operating Systems
8086 architecture
```

## Slide 12

```text
Operating Systems
8086 architecture
























                          Solution:
                   segment the memory
```

## Slide 13

```text
Operating Systems
8086 architecture




















                       Use
      segment : offset (logical address)
      to access the whole 1M memory
```

## Slide 14

```text
Operating Systems
 8086 architecture


                                                                                              this is just one way
                                                                                               of segmentation.

                                                                                               you can have less
   16bit register can access                                                                       segments
     64k different locations                                                                           and
                                                                                               bigger oﬀset area
                                                                                              (as long as it is less
                                                                                                    then 64k)


make each location a
        segment
 (so 64k segments)




within each segment                                                                                    so 64k
        there are                                                                               logical addresses
  16 memory bytes                                                                                 can reference
                                                                                                   1M physical
                                                                                                    addresses

          64K x 16 = 1M
```

## Slide 15

```text
Operating Systems
8086 architecture




       Another reason for not using
        Absolute Physical Address




               Most of the time,
            we don’t get to decide
            what memory location
     program/data is physically loaded,
      the program is randomly loaded
      depend on where there is empty
          space in physical memory





     For the program to be able to run
       from any location in memory，
    it is better to use logical addresses
```

## Slide 16

```text
x86 architecture                                                     Operating Systems












                                Protected mode
```

## Slide 17

```text
x86 architecture                                                   Operating Systems




                                                     Protected mode




             In multi-task, multi-user environment,
          there will be multiple tasks in the memory,
                   to protect it from each other,
                protected mode is very necessary
```

## Slide 18

```text
x86 architecture                                                              Operating Systems
                                                    Protected mode


1982： 80286 introduced protected mode
1985： 80386, ﬁrst 32bit processor

32bit register and address bus
able to access 232 = 4G memory

















        CR0: control register，bit 0 is PE (Protection Enable)

                      CR0.PE = 0 → real mode
                   CR0.PE = 1 → protected mode
```

## Slide 19

```text
x86 architecture                                                                      Operating Systems


                           IA-32 Protected mode
         Memory access still use: segment_address : oﬀset
           (There is no limitation for single segment length,
        you can make the whole 4G memory as one segment)
              but each segment info need to be recorded                           example of descriptor

       so that if your oﬀset exceed the limit of this segment,                      base  = 0x00400000
                    there will be an error (an interrupt)                                limit = 1 MB
                                                                                         read/write
                                                                                      privilege = ring 3

          an 8-byte is used to record
              each segment info.
                  This is called
              Segment Descriptor                                    A 48bit register (GDTR) is used
                                                                          to keep track of GDT
Each segment has one segment descriptor.



   All segment descriptors form a table:
        GDT (Global Descriptor Table)
```

## Slide 20

```text
x86 architecture                                                                          Operating Systems



                                    4x8 = 32 bit address



   GDT can be
stored anywhere
   in memory.
but mostly within                                                                  GDT is not created
        1M                                                                           by user program
                                                                                      but created by
                                                                                    Operating system


 System need to
   deﬁne GDT                                                                           when power on, 80386
 before entering                                                                      processor automatically
protected mode                                                                             enter Real Mode




                                                                                          After some set up,
                                                                                        it will be able to run in
                                                                                           Protected mode
```

## Slide 21

```text
x86 architecture                                                                                        Operating Systems

                                                  Interrupts
                                                                                      After issuing INT n instruction，
                                                                                             CPU hardware will：
                       Three Types of interrupt:
                                                                                                 execute INT n
                          Hardware interrupt                                                              ↓
                           I/O device, clock                                                   CPU check IDT[n]
                                                                                       （Interrupt Descriptor Table）
                  Internal interrupt （Exception）                                                          ↓
                               caused by                                                        check privilege
    instruction error, divide by 0, overﬂow, invalid instruction                                          ↓
                      (not deﬁned, or too long)                                            switch privilege / stack
                                                                                                          ↓
                             Soft Interrupt                                                        store state
            program issue interrupt instruction （INT）                                                     ↓
for example, program set break point to check memory content                              set PC = handler address





                 another famous example is BIOS interrupt (or BIOS function call)
                                they are basic functions for using devices.
                  these program are established when power on and set by BIOS
                  so these interrupts are available before MBR is loaded by BIOS
```

## Slide 22

```text
x86 architecture                                                   Protected mode                         Operating Systems



Protected Mode is the foundation of modern operating systems such as Windows and Linux.

- What does Protected Mode provide?
 •   Virtual memory (paging)
 •   Privilege levels (rings 0–3)
 •   Segment-based protection (access permission checks)
 •   Page-level permissions (R/W/X/U/S)
 •   Controlled interrupts and traps
 •   Restriction of user-mode instructions

- What hardware mechanisms does Protected Mode rely on?

1: Paging
Hardware mechanisms:
 •   MMU/TLB automatically perform page table walks
 •   Page Table Entries carry permission bits (R/W/U), checked by hardware
 •   Any violation → hardware raises a Page Fault

Once paging is enabled on x86, the CPU cannot bypass it.
Address translation is enforced entirely by hardware.

2: Privilege Levels (Rings 0–3)
Hardware maintains a CPL (Current Privilege Level), derived from the lower 2 bits of the code
segment selector:

For every instruction, the CPU checks:
 •   Whether is allowed to execute this instruction
 •   Whether is allowed to access the target segment (CPL vs DPL)
 •   Whether I/O port access is permitted
If not permitted:
➡   CPU immediately traps into #GP (General Protection Fault).
```

## Slide 23

```text
Operating Systems
x86 architecture



              Boot Sequence
```

## Slide 24

```text
x86 architecture                                                                 Operating Systems


                    Hard drive                                          BIOS will read it
  track 0 sector 1 is master boot sector                           (if boot from HardDrive)
  it contain MBR (Master Boot Record)                          You can write program yourself
          DPT (Disk Partition Table)                              and saved into this sector
```

## Slide 25

```text
x86 architecture                                                                       Operating Systems


              BIOS - Basic Input/Output System

             BIOS is a little program burned in a
                     Read Only Memory ROM
           and come with the computer hardware



                 When
     the computer is power on,
        the program in BIOS
           will be executed

               it mainly:

        execute self-check of
    various parts of the system
   (like if memory chip missing)

      set Interrupt Vector Table
          (at memory address 0)

       initialize the processor

      provide hardware device
                  interface
```

## Slide 26

```text
x86 architecture                                                    Operating Systems


BIOS - Basic Input/Output System
```

## Slide 27

```text
x86 architecture                                                            Operating Systems


           BIOS will read 0 track 0 sector on hard drive (512bytes)，
                      which is Master Boot Record (MBR) ，
                                 load it into memory，
                              then jump to it to execute













             It will then
    load the rest of the kernel
```

## Slide 28

```text
Operating Systems











RISC-V architecture
```

## Slide 29

```text
Operating Systems
RISC-V architecture
                                                                      In Computer Architecture/Organization
                                                                                         course,
                                                                                   we designed this
                                                                                      (single cycle)































                                                                           We know    how  an  machine   instruction
                                                                          0000000   00111  00110  000 00101  0110011
                                                                                        (add x5, x6, x7)
                                                                                         is executed
```

## Slide 30

```text
Operating Systems
RISC-V architecture
                                                           The design above is
                                                         only executing user level
                                                     instruction, because it is based
                                                            on Unprivileged ISA


                                                                  But an
                                                           Operating System
                                                             cannot be built
                                                     on top of unprivileged ISA.
                                                               It is build on
                                                         complete RISC-V ISA





                                                         RISC-V Architecture =
                                                    Unprivileged (user level) ISA +
                                                        Privileged Architecture
```

## Slide 31

```text
Operating Systems
RISC-V architecture

                  RISC-V unprivileged ISA （user level）

                                                           User level instructions

                                                                 For example:

                                                                add x1, x2, x3
                                                                 lw x3, 30(x4)
                                                                 sw x5, 40(x6)
                                                               beq x7, x8, EXIT



                                                             Unprivileged ISA

                                                            user level registers
    general purpose Registers                                instruction format
            （user level)                                    addressing mode…
```

## Slide 32

```text
Operating Systems
RISC-V architecture

                   RISC-V privileged architecture




 Privileged
architecture       =
                            Privileged
                           instructions                Additional functionality
                                             +               required for
                                                     running operating systems
                                                                  and
                                                     attaching external devices



              Assuming we built an Operating System,
                       how exactly system start?
```

## Slide 33

```text
RISC-V architecture                                                Operating Systems



                                            System start up sequence of
 Machine                                             RISC-V system
Power on

         BootROM
   (boot code) execution

                     Firmware
                     execution


                               Bootloader


                                                Kernel
                                              execution         User Program

                                                                  execution
```

## Slide 34

```text
System start up sequence of RISC-V system                                                Operating Systems


                                                                                               Machine
                                                                                               Power on
                                                      CPU starts in a very primitive state.

                                                               No page tables exist;
                                                                no virtual memory;
                                                                      no kernel;
                                                                   no user space.
The RISC-V spec deﬁnes                                     Memory controllers, clocks,
             the                              UART (send byte data bit-by-bit over single wire)
      privilege level                                                    and
      (M/S/U mode),                     interrupt controllers (PLIC, CLINT, not part of ISA) may not
            and                                                   yet be initialized.

            CSR                               Execution is in highest privileged mode (m-mode)
   (Control and Status
         Registers)                                              PC = reset vector
                                        (the ﬁxed starting physical address for executing following
       For example:                                          BootROM booting  code)
   “mstatus” register.
                                                           For example: 0x0000_1000
   initially, mstatus set
      to be m-mode
```

## Slide 35

```text
System start up sequence of RISC-V system                                        Operating Systems


                                                                               BootROM
                                                                         (boot code) execution


                                     BootROM is tiny minimal immutable code burned
                                                     into the read-only chip.

                                            It is the first code that runs after reset

                                     It is running in highest privileged mode (m-mode)

                                                       It is responsible for:

RISC-V privileged spec                     Performing minimal hardware bring-up
         used:                                   (clock, basic memory access),

M-mode (full privilege)                  Locating and load the next-stage firmware
    Machine CSRs                         (openSBI/bootLoader, usually from flash).

                                              Entering fallback or recovery mode
                                              if firmware is missing or corrupted.
```

## Slide 36

```text
System        start   up    sequence         of  RISC-V       system                            Operating Systems

                                                                                                    Firmware
                                                                                                    execution


                                                      Firmware = Initialization + SBI + trap handler

                                                                      Initialization code:
                                                 initialize UART/initialize timer/CLINT/initialize PLIC,
                                                       initialize memory controller/enable caches
                                                       (these are not part of Privileged ISA spec)
 RISC-V privileged spec
             used:
                                                Binary library API (SBI - Supervisor Binary Interface):
  M-mode (full privilege)                      These library function calls are implemented by calling
       Machine CSRs                                            RISC-V privileged instructions
                                                        (which is executed directly by hardware,
(mstatus, mtvec, mcause)                     for example: mret, sret, wfi, sfence.vma, read/write status)
      mret   instruction
      trap delegation                                           Examples of these SBI calls:
             ecall                        sbi_set_timer( ) / sbi_console_putchar( ) / sbi_system_reset( )
        (System call)                       RISC-V provided an implementation of SBI called: OpenSBI
             PMP
   (memory protection)                                             Trap Handler programs
                                                                          (I/O devices)
Firmware is the  main consumer                      Finally load next-stage bootloader (e.g. U-Boot)
   of privileged ISA M-mode
            features.
```

## Slide 37

```text
System start up sequence of RISC-V system                              Operating Systems

                                                                        Firmware
                                                                        execution
```

## Slide 38

```text
System start up sequence of RISC-V system                                                      Operating Systems

                                                                                                    Bootloader


                                                         A small program independent of Kernel.

                                                It prepares the environment for the OS kernel and then
                                                            loads and starts the Kernel binary.

                                                     Not provided by chip hardware manufacturer.
                                                  But created by OS community open source project.
                                                                 Belong to OS ecosystem

RISC-V privileged spec                                             For example:  U-Boot
           used:
                                               Bootloader is running in second highest privileged mode
          S-mode                                                (s-mode, supervisor mode)

 (supervisor privilege)                                       It jump to entry point of Kernel
    Supervisor CSRs                                             which is “entry.S” program
(sstatus, scause, stvec,
       sepc, stval)                            Kernel entry point address is defined in ELF header file:

     sret instruction                                      for example:  e_entry = 0x80200000
     trap delegation                                                  Bootloader then:
   ecall (System call)
   page-table format                                                 load kernel to RAM
    s-mode interrupt                                                  parse ELF header
                                                                  find entry point address
                                                               jump to entry point (entry.S)
```

## Slide 39

```text
System start up sequence of RISC-V system                                               Operating Systems
                                                                                         Kernel
                                                                                       execution
                                                              entry.S is executed
                                                  it then call “start_kernel( )” function
                                                  which is the main function of kernel

                                            kernel is using in S-mode (supervisor mode)

                                                           Set up a stack pointer.
RISC-V privileged spec                                 Initialize memory segments.
          used:                               Build early page tables and program satp.
                                             Set stvec to install the kernel’s trap handler.
         S-mode                              Configure S-mode interrupt and status bits.
 (supervisor privilege)                        Transition into the kernel’s C entry point
   Supervisor CSRs                                        (start_kernel, main, etc.).
(sstatus, scause, stvec,
       sepc, stval)                Firmware handles only the traps that must be processed in M-mode
     sret instruction               (such as S-mode ecalls for SBI services and machine-level faults).
    trap delegation
   ecall (System call)                                    All normal OS-level events
    page-table rules               (syscalls, page faults, external interrupts, timer interrupts, and IPIs)
      satp register                                      must be delegated to S-mode
sfence.vma instruction                           and handled by the operating system itself.
```

## Slide 40

```text
System start up sequence of RISC-V system                                   Operating Systems


                                                                       User Program
                                                                         execution



                                          OS creates the first user process:

                                  Load an ELF binary into user virtual memory.
                                     Create a Trapframe for the user process.
RISC-V privileged spec                     Switch to the user’s page table.
         used:                   Execute sret to drop from S-mode into U-mode.

       U-mode                            At this point, the kernel becomes a
    (user privilege)                                 “service provider”
      Trap entry                               and user programs run in
  (trampoline page)                    isolated, unprivileged address spaces.
    sret instruction
  ecall (System call)
 U->S/S->U transition
   page protection
```

## Slide 41

```text
System build on RISC-V architecture                                    Operating Systems


                         Possible systems
























   Application running                                 Application running
       on bare metal                                           on OS
```

## Slide 42

```text
System build on RISC-V architecture                                                           Operating Systems



                                                    To   make      application       be  able    to  run   on
                                                                  RISC-V      architecture,
                                                     AEE    need     to  provide     an   interface:     ABI

                                                       ABI    =  Application       Binary     Interface

                                                  =  RISC-V      user    level   ISA   +  set   of  ABI   calls


                                                       ABI    standard      is  de  ﬁ ned   by   RISC-V
                                                             it is not   an   implementation.
                                             ABI    is a  user-level      contract      implemented          by  the
                                                        application       runtime      and    libraries.

                                                       The    implementation           is provided       by
                                                         OS/compiler/runtime               developer

                                                 ABI   implementation           is  not   part   of  ﬁ rmware

Application Execution Environment


    AEE    =  CPU     +  ﬁ rmware       (trap   handler)     +  C   runtime     (crt0,   init)  +  libraries
```

## Slide 43

```text
System build on RISC-V architecture                                                              Operating Systems


                                                To   make     OS    (run   in  S-mode)        be   able    to  run   on
                                                                    RISC-V       architecture,
                                                      SEE    need     to  provide      an   interface     ：   SBI

                                                          SBI   =  Supervisor         Binary     Interface

                                                             SBI    =  RISC-V      user    level   ISA    +
                                                             RISC-V      supervisor-level           ISA   +
                                                                        set   of   SBI   calls

                                                   SBI     is a  RISC-V       spec    (standard        interface)
                                                   There     is a  standard        implementation            of  SBI
                                                               called     OpenSBI        by   RISC-V
                                                  SBI   could     also    be   provided       by   other    vender


                                                          OS/SBI/SEE        together     provide    AEE    for
                                                                            Applications
                                         However,      AEE   without     an  OS   is very   diﬀ erent   from   AEE    with   an
                                           OS.   The   structure,    components,        and   responsibilities      change
                                                                            completely.


                                                         Supervisor        Execution        Environment
                                                                 SEE    =  M-mode         ﬁ rmware
```

## Slide 44

```text
Operating Systems
                             RISC-V architecture


                                                                                         U-mode for application



                                                                                     S-mode for operating system


                                                                                                    M-mode
                                                                                      low level access to machine
                                                                                                inplimentation



    All hardware implementation must provide M-mode.                                      System runs application code in
Many RISC-V implementations also support at least U-mode.                                      U-mode until some trap
    S-mode can be added to provide isolation between a                                   (supervisor call or timer interrupt)
                supervisor-level S and the SEE                                            forces a switch to a trap handler
                                                                                            which usually runs in a more
                                                                                                   privilieged mode.
```

## Slide 45

```text
Operating Systems
                          RISC-V architecture




                  To  build   system,     RISC-V     ISA  provides     two   core   features:

                         Instructions     (related    to di ﬀ erent   privilege    level)

                                      CSR    (control    state   register)



                                      CSRs      are    used      to  de  ﬁ  ne
       privilege,       traps,     exceptions,           virtual     memory,         and    protection




Address        for   CSR      is  12bit    (which       means,        in  a  instruction,         12   bit   is  set
                                              aside      for   CSR)
               This     means,        there     could      be   up   to   2 12  =  4096     CSRs
```

## Slide 46

```text
Operating Systems
           CSRs





        Instructions
         csrr/csrw

     csrr t0, mstatus

    la   t0, trap_vector
    # t0 = trap handler
          address
       csrw stvec, t0




   CSRs are designed
   mainly for privileged
        control only

  Unprivileged software
interacts with the system
    via syscalls (ecall),
         NOT CSRs
```

## Slide 47

```text
Machine-Level ISA           Operating Systems




                            Instructions

                                ecall
                      (system call to mtvec)
                               ebreak
                   (break point for debugging)
                                mret
                   (return from trap to mepc)
                                 wﬁ
                        (wait for interrupt)
```

## Slide 48

```text
Operating Systems

Supervisor-Level ISA



                    Instructions

                        ecall
                       ebreak
                         sret
                    sfence.vma
```

## Slide 49

```text
Example                                           Everybody is                    Operating Systems
                                                   running in
                                                  M-mode only
```

## Slide 50

```text
Operating Systems





entry.S is in M-mode.
kernel is running in
       S-mode




















                                                            Example
```

## Slide 51

```text
Operating Systems

























entry.S running in M-mode.
kernel is running in S-mode
     and create a trap








              Example
```

## Slide 52

```text
Operating Systems
RISC-V Trap Handling Classiﬁcation Table
 (Firmware vs. OS, M-mode vs. S-mode)









































                        By default, all traps are in M-mode, some of them might be delegated to S-mode
```
