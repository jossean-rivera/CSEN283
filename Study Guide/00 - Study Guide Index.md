# Operating Systems Study Guide

A topic-based companion to the CSEN 283 lectures and submitted assignments through Week 7. Follow the links in order for a first pass, then return to the practice sections before exams and projects.

1. [[01 - Operating System Foundations]] — what an OS does, protection, interrupts, and history.
2. [[02 - Architecture and Boot]] — ISA, privilege, x86, RISC-V, and startup.
3. [[03 - Processes Threads and Memory]] — harts, address spaces, paging, stacks, and control blocks.
4. [[04 - Process Creation and xv6]] — `fork`, `exec`, `wait`, kernel images, and traps.
5. [[05 - Scheduling Mechanics]] — process states, context switches, xv6, and metrics.
6. [[06 - Scheduling Policies and Examples]] — FCFS through EEVDF, with worked timelines.
7. [[07 - Concurrency Locks and IPC]] — races, critical sections, locks, and communication.
8. [[08 - Semaphores and Coordination]] — ordering, producer-consumer, and deadlock prevention.
9. [[09 - Exam and Assignment Practice]] — integrated questions, answers, and problem-solving checks.

> [!tip] How to study
> Explain each diagram aloud, solve the scheduling workload without looking, trace the process examples, and identify which state or shared variable changes at each step. Then compare with [[Homework 4]] and the submitted assignments.

## Sources and scope

Professor slides and whiteboard images in `/Users/jossean/Documents/CSEN 283` are the primary course materials. Individual pages name relevant slide decks, whiteboards, class notes, and homework. Whiteboard notes are handwritten diagrams and supplement the slides; original PDFs and images remain in the Documents folder. Slide and assignment PDFs were reviewed through extracted text, so consult the originals for diagrams and visual details that text extraction may omit. The root notes and [[Homework 2]] / [[Homework 4]] retain the original lecture and solution detail. The guide describes material covered so far, not a complete operating systems textbook. Assignment PDFs 1–4 were reviewed as exercises, not assumed to be an answer key.

A labeled external link appears where a source beyond the professor's materials or your notes is used. Course examples involving xv6 depend on the course's version and should be checked against the assigned source tree.
