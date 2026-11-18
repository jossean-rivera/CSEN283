# Address Spaces, Paging, and Kernel Structures

This note covers the address-space and kernel topics recorded in class. The xv6 layout and function names refer to the teaching version discussed in the course; other systems and xv6 versions may differ.

Sources:
- Class Week 2 - Day 2, paging and process structures.
- Class Week 3 - Day 1, kernel image and user space.
- Class Week 8 - Day 1, introduction to the next memory-management topic.
- Professor Notes Lecture2-2.jpg and Professor Notes Lecture3-1.jpg, in the CSEN 283 Documents folder.

## Logical and physical memory

- A program uses logical addresses, also called virtual addresses, to access memory.
- Physical memory is the actual RAM addressed by the hardware.
- A process can see a continuous range of virtual addresses even when its data is stored in separate locations in physical memory.
- The operating system creates and manages page tables. The memory management unit, or MMU, uses those tables to translate virtual addresses into physical addresses and check access permissions.
- A process's memory can include code, global variables, a heap, and stacks.

## Paging

- Paging divides virtual memory into fixed-size pages and physical memory into frames of the same size.
- A page table maps a virtual page to a physical frame.
- Paging allows a process's pages to occupy nonadjacent physical frames. The system does not need one continuous physical block for the entire process.
- A physical frame that is not in use can be allocated for another purpose.

The professor illustrated pages 0 through 4 mapped to different physical frames. The written diagram in the original class note gives the following illustrative mapping:

- Virtual page 0 maps to physical frame 6.
- Virtual page 1 maps to physical frame 2.
- Virtual page 2 maps to physical frame 0.
- Virtual page 3 maps to physical frame 5.
- Virtual page 4 maps to physical frame 8.

The accompanying prose and diagram in the original note disagree about some frame numbers. The numbers above preserve the written diagram's example; the important concept is that the mappings need not be contiguous.

## Page number and offset

A virtual address can be divided into a virtual page number and an offset within that page.

- A 4 KiB page holds 4,096 bytes, or 2 to the power of 12 bytes.
- It takes 12 bits to identify a byte within that page. Those bits are the offset.
- With a 32-bit virtual address and a 4 KiB page, the remaining 20 bits identify the virtual page number.
- The page table identifies the corresponding physical frame. The offset identifies the same position within that frame.
- Physical address = physical frame number multiplied by page size, plus the offset.

The original class note asks whether the OS performs the conversion. The MMU performs address translation using page tables set up and maintained by the OS. The original note's formula using the page-table base plus page number times page size is not a general physical-address translation formula.

## Root page tables

- A page table can become large. A multilevel page table uses a root table and lower-level tables to organize mappings.
- A process typically has a page-table root identifying its address space. Multiple processes can have different root tables in physical memory.
- The root table contains entries that lead to lower-level tables. Those tables eventually provide entries that identify physical frames and permissions.
- The kernel maintains information about a process's page-table root in its process-management data. The processor uses a page-table root register when translating addresses.
- The exact table layout, entry size, indexing, and alignment depend on the architecture. The root table must be stored in the form and alignment the hardware requires.

Example relationship:

- Process A has process-management information pointing to root table A.
- Process B has process-management information pointing to root table B.
- Each root can point to lower-level page tables, which map the process's virtual pages to physical frames.

## Stack, heap, and pointers

- The heap is part of the process's address space and is shared by threads in that process. Memory can be allocated dynamically from it, for example with malloc.
- Each thread has its own user stack. The stack stores function-call state such as local variables and return information.
- A local variable named x in one thread is separate from a local variable named x in another thread.
- A local pointer variable belongs to the stack frame in which it is declared. The object it points to can be in shared heap memory, so multiple threads can hold pointers to the same object.
- A kernel stack is separate from a thread's user stack. It is used when execution is in the kernel.

A process's user address space can include:

- Program code and global data.
- Heap memory.
- Shared-library code and data.
- File-backed mappings.
- A separate user stack for each thread.

The kernel manages page tables and process structures. Memory-region information can describe a region's start, end, source such as a file, and access permissions. Exact structures and address-space layouts depend on the operating system.

## Process and thread control information

- A process control block, or PCB, records information about a process and its resources.
- A thread control block, or TCB, records information needed to manage a thread's execution and scheduling.
- The basic xv6 example combines process and execution information in its process structure. Linux organizes task and resource information differently.

The xv6 process structure discussed in class includes:

- State, such as RUNNING, SLEEPING, RUNNABLE, or ZOMBIE.
- A channel pointer identifying the event or object on which a process sleeps. A wakeup on the same channel can make it runnable.
- Process ID and parent process.
- Kernel stack information.
- User page table pointer.
- Trap frame, which stores user register values for trap handling and return to user mode.
- Saved context, used when switching kernel execution.
- Open files and process name.

## xv6 kernel image

The professor described the course's xv6 kernel image using the kernel linker script, kernel.ld. The kernel image is linked from many object files and contains code and data sections. In the illustrated layout, the kernel starts at physical address 0x80000000.

- entry.o comes from entry.S. Early entry code sets up a stack and calls start().
- Other linked object files include start.o, proc.o, and main.o.
- usertrap() handles traps from user mode, including system calls, timer and device interrupts, and faults.
- kerneltrap() handles traps that occur while the processor is already in kernel mode.
- kernelvec is the kernel trap entry point. It saves registers and calls kerneltrap().
- trampoline.o contains the trampoline code, including uservec and userret.
- Read-only constants are stored in the rodata section.
- Initialized global and static variables are stored in the data section.
- Zero-initialized global and static storage is represented by the bss section.

Object files contribute to the linked sections; the simplified layout is not a claim that each whole object occupies one uninterrupted block in the final image.

## Trampoline and trap frame

A trap transfers execution to a handler. The class discussed system calls, interrupts, and exceptions or faults.

- RISC-V does not automatically switch from the user page table to the kernel page table when a trap occurs.
- In xv6, the trampoline page is mapped at the same high virtual address in both page tables. This allows its code to continue executing across the page-table switch.
- uservec saves user registers in the process's trap frame, switches to the kernel page table, and enters usertrap().
- On return, userret switches back to the user page table, restores user registers, and returns to user mode.
- In the course's xv6 layout, the trap frame is mapped one page below the trampoline in the user page table.

## System calls and process states

A user program can call system-call wrappers. A wrapper enters the kernel, where the requested operation is handled.

- getpid() returns the calling process's ID. It normally returns without blocking.
- read() may block if requested data is not ready. The process sleeps; when the data becomes available, it can become runnable and eventually resume.
- exit() terminates the process. In xv6, the process becomes a zombie until its parent collects its status with wait().
- The sleep function waits and gives up the CPU. After wakeup, the process becomes runnable and waits to be scheduled.

Process states discussed include runnable, running, sleeping, and zombie. A system call or trap does not necessarily cause a context switch.

## xv6 kernel and user virtual spaces

In the course's xv6 kernel address space:

- The kernel and physical memory are directly mapped in the illustrated region beginning at 0x80000000.
- Kernel stacks occupy a separate per-process area and have guard pages.
- The address space also includes unused regions and the high-address trampoline mapping.
- Kernel code executes in kernel mode. The trampoline is mapped near the top of the supported virtual-address range, MAXVA.

In the illustrated xv6 user address space:

- Program text begins near address 0.
- Initialized data and bss follow the program text.
- The process has a user stack and an expandable heap.
- The trap frame and trampoline are mapped near the top of the supported virtual-address range.
- User code executes in user mode; the trap-related mappings have permissions appropriate for kernel use.

## Next memory-management topic

In Week 8, Day 1, the professor introduced memory management and described a plan to move from older management systems to modern ones. The available note does not yet record further details from those slides.
