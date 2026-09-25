---
date: 2026-09-22
course: COEN 283
topic: Operating Systems
---
# Operating Systems — Lecture 1

**Professor:** Yuan Wang  
**Reference:** Silberschatz, Galvin, and Gagne, *Operating System Concepts* (9th ed.)

Starting the class by discussing the slides in the file: `283-01-Introduction2026fall.pdf`

COEN 283 - Professor Yuan Wang - Lecture 1

General introductions and basic rules

Technically, there's no textbook but mentioned a reference book by Silberschatz, Galvin, and Gagne "Operating System Concepts (9th Edition)"

## What is an operating system?

An **operating system (OS)** is software that sits between computer hardware and users' applications.

`Hardware → OS → Applications / Users`

It provides a convenient, protected way for programs to use hardware.

## Why do we need an OS?

1. **Ease of use:** Hardware is difficult to program directly. The OS exposes a small set of core services through **system calls** (the system-call API).
2. **Resource sharing:** Multiple programs must safely share the CPU, memory, storage, and devices.

## Memory and storage

- **RAM (main memory):** Fast, temporary working memory for running programs.
- **Disk/storage:** Persistent data storage.
- A **process** is a running program. Each process has its own protected address space containing its code, data, and stack.
- The OS manages memory and provides the **file system**, which organizes data on storage.

## Examples of operating systems

- macOS (Unix-based)
- Linux (Unix-like)
- Windows
- **Unix:** A family of multiuser, multitasking operating systems originally developed at Bell Labs. It influenced macOS and Linux. 

## Early computing history

What exactly operating systems do? The professor discusses the history of computer systems to get a better picture. 
### First generation (about 1945–1955)

- Built with **vacuum tubes**: glass electronic switches that control the flow of electricity. They were large, hot, and power-hungry.
- Programs were written in machine language and entered using switches, plugboards, or punched cards.
- There was generally **no operating system**; one program used the machine at a time.
- Setup was slow, so machine utilization was low.

**ENIAC**: first electronic general purpose computer (1945)
- no Operating Systems
- programmed by plugboard/switches
- perform decimal arithmetic

#### IBM 704 (introduced in 1954)
(first commercial computer)

An early vacuum-tube scientific computer made by IBM. It was notable for hardware floating-point arithmetic and magnetic-core memory. It initially ran jobs directly rather than a modern OS; later monitor programs helped automate batch-job execution.

### Second generation (about 1956–1965)

- **Transistors** replaced vacuum tubes. A transistor is a small semiconductor device that acts as an electronic switch or amplifier; it is faster, smaller, and more reliable than a vacuum tube.
- Computers became smaller, faster, and more reliable.
- Language: **assembly** language program stored in memory (move from magnetic drum to magnetic core)
- **Batch systems** began to automate running collections of jobs.

### Third generation (about 1965–1971)

- Built with **integrated circuits (ICs)**.
- Transistors were minimized and placed into silicon chips (semi conductor)
- Operating systems supported **multiprogramming** and **time-sharing**: several programs could share the CPU.
- Example: IBM System/360 and OS/360.

### Fourth generation (from about 1971)

- Built with **very-large-scale integration (VLSI)** and microprocessors.
- This enabled personal computers and modern operating systems.

## OS concepts

### Simple system

A system that runs one application directly on hardware.

`Application → Hardware`

### Conventional system

A system that runs multiple applications through one OS kernel.

`Applications → OS (kernel) → Hardware`

A computer has one **kernel**: the privileged core of the OS that manages hardware and system resources.

### User mode and kernel mode

The instruction-set architecture (ISA) defines privilege levels.

- **User mode:** Applications run with limited privileges. They cannot directly access protected memory or hardware.
- **Kernel mode:** The OS runs with full privileges and can manage hardware and protected resources.

### Kernel design

- **Monolithic kernel:** Most OS services run together in kernel space. It can be fast but has a large trusted code base.
- **Microkernel:** Only essential services run in kernel space; other services run in user space. It is more modular, but communication can add overhead.

### Traps and interrupts

A **trap** transfers control from user space to the kernel so the OS can handle an event. A trap handler performs the required kernel work.

- **System call:** An application deliberately requests an OS service (for example, `ecall → trap handler`).
- **Exception:** A CPU-detected error or special condition, such as division by zero.
- **Timer interrupt:** A periodic hardware event that lets the OS regain CPU control and schedule processes.
- **Device interrupt:** A hardware event from a device, such as a keyboard or disk.

Operating systems are largely **event-driven**: they respond to system calls, exceptions, interrupts, and user actions such as clicks.

> OS (Kernel) is a big event-driven loop.
> - Professor slides

### System Call
A user-level program is supported by lower-level OS functions. A **system call** lets a program request a service from the OS kernel, such as file I/O, process creation, memory allocation, or device access.

> [!info] System-call path
> `Program → system-call instruction (for example, ecall) → trap handler → kernel service → return to program`

In the 1960s, two techniques became more mature:
1. Interrupt technique
2. Data-channel technique

### Interrupt Technique
An **interrupt** is a signal from hardware or software that tells the CPU that an event needs attention. The CPU then does the following:

1. pauses the current program between instructions;
2. runs the appropriate interrupt handler; and
3. resumes the interrupted program when appropriate.

An individual instruction (such as `ADD x1, x2, x3`) normally runs to completion; interrupts are recognized between instructions.

Each interrupt has an interrupt number (or vector). The **Interrupt Vector Table (IVT)** maps that number to the memory address of its interrupt handler.

### DMA (Direct Memory Access)
Another technique for I/O. **DMA** lets a device controller transfer a block of data directly between a device and main memory without the CPU handling each byte. The CPU starts the transfer and is usually interrupted when it finishes.

### Time-Sharing System
**Time slicing** gives each program a turn on the CPU for a fixed, short period of time. Time-sharing lets many users at terminals share one central computer interactively.

Time-sharing was the dominant model for shared mainframe systems until personal computers became widespread in the 1980s.

## Operating Systems Development

### **Multics**: (Multiplexed Information and Computing Services) MIT, 1965
An early time-sharing system.

See <https://www.multicians.org/>.

### Unix: Bell Labs, 1969–1970
- Developed by Bell Labs researchers after Bell Labs withdrew from the Multics project; Unix was inspired by Multics but was much smaller and simpler.
- Supports multitasking and multiple users.
- Originally written in assembly language, then largely rewritten in C in 1973.
- Commercial Unix systems included:
    - HP-UX (Hewlett-Packard)
    - AIX (IBM)
    - Solaris (Sun Microsystems)
    - SCO UNIX (SCO)
- MINIX ("mini-Unix") was released in 1987.
- Unix-like systems include:
    - BSD (Berkeley Software Distribution), including FreeBSD and OpenBSD
    - Linux
- In 2000, Apple released Darwin, a Unix-like system that became the core of Mac OS X (later renamed macOS).

#### Unix and C programming
C was developed at Bell Labs by Dennis Ritchie (1941–2011) in the early 1970s to implement Unix and its utilities. Rewriting most of the Unix kernel in C made it easier to port Unix to different hardware platforms. Unix and C developed closely together because Ritchie, Ken Thompson, and their colleagues designed both the language and the operating system around each other's needs.

#### POSIX Standard

- **POSIX** stands for **Portable Operating System Interface**.
- It is a family of standards, originally developed by IEEE and later standardized jointly with ISO/IEC.
- POSIX specifies common OS interfaces, such as system calls, shell commands, and utilities, to improve software portability among Unix-like systems.
- Linux used POSIX as an important compatibility target while it was being developed, helping it work similarly to many Unix systems.

### GNU project
Announced by Richard M. Stallman (RMS) in 1983; development began in 1984.

- Its goal was to develop a complete, Unix-like operating system made entirely of free software.
- **GNU** is a recursive acronym for “GNU's Not Unix.”
- By the early 1990s, GNU had developed many important free programs, including Emacs, the Bash shell, GCC (GNU Compiler Collection), and GDB (the GNU Debugger). These programs provided a strong environment for Linux.
- This work was one foundation for the rise of Linux.

> [!info] Is GNU a complete operating system or a library of useful tools?
> GNU is an operating-system project, not a library. Its tools are widely used with the Linux kernel to form a complete GNU/Linux system. GNU's own kernel, GNU Hurd, remains less widely used.

### MINIX
By Andrew S. Tanenbaum (AST) in 1987.

- A Unix-like system for IBM PC-compatible computers, created for teaching operating-system design.
- Uses a microkernel architecture.
- **MINIX 3** is the current major version.
- Its source code made it possible for students and hobbyists to study a real operating system. Linus Torvalds used MINIX before developing Linux.

### Linux
Created by Linus Torvalds in 1991; version 0.02 was released on October 5, 1991.

- A free, Unix-like **kernel**, initially inspired by MINIX.
- Background context for Linux:
    - Commercial Unix systems were expensive and mainly used on larger computers, limiting access for PC users.
    - Unix source code was generally proprietary and unavailable to the public.

#### Tanenbaum–Torvalds Debate

On the `comp.os.minix` newsgroup in 1992, Tanenbaum and Torvalds debated Linux's design. The debate contrasted **microkernels** with **monolithic kernels**.

**Next example:** xv6, which will be covered in the following class.
