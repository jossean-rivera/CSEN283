# CSEN 283 — Course Content

This folder is a detailed, topic-organized record of the operating systems course through the available Week 8, Day 1 notes. It is source material for another agent or tool to use when building quizzes, guides, flashcards, and related learning resources. It is not a record of what will definitely appear on an exam.

## How to interpret this material

1. **Recorded lecture discussion:** The topic files reproduce substantial excerpts from the corresponding `Class Week …` notes, including examples, diagrams, and caveats. A topic recorded there has stronger evidence of class coverage than a topic that appears only in a slide deck.
2. **Corrections and clarifications:** Callouts at the start of topic files flag known errors or ambiguities in the original lecture records. These take precedence over the erroneous passage when creating derivative material. Where an outside source was needed, its link is next to the clarification.
3. **Professor materials:** Each topic file names relevant professor slide decks and whiteboard images in `/Users/jossean/Documents/CSEN 283/`. The whiteboard images are original visual sources; the slide PDFs remain the authority for their layout and diagrams.
4. **Slide transcripts:** [[_Professor Slide Transcripts/README|Slide transcript index]] provides searchable text for all eight available slide decks, by PDF page. Text extraction can scramble content or omit diagrams. A slide alone does not prove that the topic was discussed.
5. **Homework:** [[Homework 2]] includes xv6 build and address-layout diagrams; [[Homework 4]] includes scheduling examples, timelines, assumptions, and calculations. Submitted Assignment PDFs 1–4 are in the Documents course folder. Homework reflects exercises and student responses; it is supporting evidence, not an automatic answer key.

> [!important] For a downstream content-generation agent
> Use the lecture records as the primary coverage boundary. Resolve conflicts using the correction callouts and professor sources; clearly label outside references. Do not treat open questions, typos, or illustrative snippets as verified facts or executable code. Distinguish a professor example's assumptions from general rules. If a slide contains material not corroborated by a class note, mark its coverage as unconfirmed rather than discarding it or asserting it was taught.

## Topics

1. [[01 - Operating Systems and History]] — functions, generations, architecture of OSs, interrupts, DMA, and Unix history.
2. [[02 - Architecture Privilege and Boot]] — x86, RISC-V, privilege modes, boot sequence, harts, and pipeline.
3. [[03 - Processes Threads and Harts]] — execution contexts, resource sharing, and thread lifetimes.
4. [[04 - Address Spaces Paging and Kernel Structures]] — memory translation, stacks, page tables, PCB/TCB, kernel image, and user/kernel address spaces.
5. [[05 - Process Creation and Lifecycle]] — `fork`, `exec`, `wait`, orphaning, and zombies.
6. [[06 - Scheduling Mechanisms and States]] — runnable/running/sleeping, context switches, xv6 scheduling, and scheduling criteria.
7. [[07 - Scheduling Policies]] — FCFS, SJF, SRTF, HRRN, RR, priority, MLFQ, CFS, EEVDF, and EDF.
8. [[08 - Process Relationships and IPC]] — competition, cooperation, races, pipes, FIFO, shared memory, and message queues.
9. [[09 - Critical Sections and Locks]] — mutual exclusion, progress, starvation, Dekker, Peterson, atomic operations, spinlocks, and mutexes.
10. [[10 - Semaphores and Producer Consumer]] — signaling, ordering, blocking, and bounded-buffer synchronization.
11. [[11 - Condition Variables and Lost Wakeups]] — condition predicates, atomic wait, producer–consumer, and bank-account example.
12. [[12 - Readers and Writers]] — reader preference, writer preference, counters, and synchronization pitfalls.
13. [[13 - Dining Philosophers and Priority Inversion]] — deadlock, livelock, ordering resources, priority inheritance, and ceiling.

## Source and scope notes

- The original class notes, homework, and professor materials are unchanged. Lecture excerpts retain their original wording, spelling, and examples, so consult their correction callouts.
- Notes with dates in their front matter are retained as recorded; ordering here uses file names and topic relationships rather than assuming each metadata date is accurate.
- Week 8, Day 1 announces memory management, but the note does not include that lecture's subsequent details. Earlier paging and memory topics are included; no unrecorded later material is represented as covered.
- The professor's whiteboards and slides available locally were used to identify relevant primary sources. The slides were reviewed using text extraction, not a complete visual inspection of every page; diagrams in the PDFs may not appear in the transcripts. The existing class-note diagrams remain embedded where recorded.
