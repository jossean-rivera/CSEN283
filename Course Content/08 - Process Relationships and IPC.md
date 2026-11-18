# Process Relationships and IPC

> [!info] Source priority
> The following class-note excerpts record topics the professor discussed. They are retained in detail as lecture records. Warnings distinguish corrections from the original record; pseudocode is illustrative unless explicitly stated otherwise.

**Professor materials:** Professor Slides Lecture5-1-1process-relation-overall.pdf. All listed materials are in `/Users/jossean/Documents/CSEN 283/`.

> [!note] Shared access
> The count example is illustrative pseudocode. In C, unsynchronized conflicting accesses cause a data race and undefined behavior.


---

## Lecture record: [[Class Week 5 - Day 1]] (lines 87–199)

# Process Relationships

*Or thread relationships—we use “process” and “thread” loosely here.*

There are many processes at any moment. There are two types:

- **Competition**
  - Processes compete for shared resources.
  - We need synchronization to avoid conflicts.
- **Collaboration**
  - **Loose collaboration:** independent jobs must be synchronized to run in the correct order.
  - **Tight collaboration:** for example, the producer-consumer pattern processes large amounts of data.

## Competition

Without synchronization, there can be many issues. For example, two processes might write at the same time—like two cars entering an intersection at the same time and crashing.

Processes do not need to be aware of the existence of the others. It is inevitable that multiple processes will compete for limited resources in the computer.

For example:

```c
int count = 5;

void process1() // runs in a given thread
{
    count++;
}

void process2() // runs in another thread
{
    count--;
}
```

Even though each operation is one statement in C, it can require three assembly-level operations:

```c
process1:
    MOV count, R0
    ADDI R0, R0, 1
    MOV R0, count

process2:
    MOV count, R0
    ADDI R0, R0, -1
    MOV R0, count
```

An interleaving of these read-modify-write operations can cause a **race condition**, leaving `count` with an unexpected value. In C, unsynchronized concurrent access also creates a data race and undefined behavior.

#### Problems with bad synchronization

- **Deadlock:** a process waits for a resource held by another process, while that other process is waiting for a resource held by the first.
- **Starvation:** a process is indefinitely delayed because other processes continually acquire the resource.

### Synchronization

- Some processes require a collaborative effort to accomplish the same task.
- The execution of one process may rely on another.
- Since each process is carried out independently and at an unpredictable pace, collaborating processes need to coordinate their work.
- One process may need to **block** until another is done with a shared resource.

Solutions:

- **Software:**
  - Dekker's algorithm
  - Peterson's algorithm
- **Hardware:**
  - Disabling interrupts (in limited kernel contexts)
  - Atomic machine instructions

### Interprocess Communication (IPC)

In many cases, there is a need to exchange large amounts of data between processes. Threads in the same process already share an address space.

For example, sending a batch of messages or an entire file can be accomplished through a communication mechanism. Common IPC methods include:

- Signals
- Shared memory
- Message passing
- Shared files

There are many ways to communicate between processes:

- **Pipeline (pipe)**
  - The oldest IPC mechanism in Unix.
  - A pipe is a connection between two processes: the standard output of one becomes the standard input of the other.
  - Unix shell pipe example: `cat mytextfile | sed 's/hate/love/g'`
    - `cat` displays the file.
    - `sed` (*stream editor*) replaces every occurrence of `hate` with `love`.
  - Traditional unnamed pipes are typically used between related processes, such as processes created by the same shell.
  - If a process tries to read from an empty pipe while a writer is still open, it blocks until data is written.
  - Call `pipe()` to get two file descriptors in an array: `fd[0]` for reading and `fd[1]` for writing.
- **FIFO**
  - A FIFO is a named pipe.
  - A traditional pipe is unnamed and lasts only as long as the processes using it.
  - A named pipe has a filesystem name and persists until it is removed.
  - Create one with `mkfifo()`.
  - Since a named pipe is a kind of file, we can use `open()`, `read()`, `write()`, and `close()`.
  - With a FIFO, unrelated processes can exchange data.
- **Shared memory**
  - Two or more processes use a shared memory region to communicate.
  - The data structure is defined by the processes, not controlled by the system.
  - A typical example is the producer-consumer problem.
  - In System V IPC, `shmget()` creates or obtains a segment, `shmat()` attaches it, and `shmctl()` controls it.
- **Message queue**
  - A queue of messages stored in the kernel.
  - Managed by the kernel through system calls.
  - Use `msgsnd()` and `msgrcv()` to send and receive messages.
- **Semaphore**
  - A synchronization primitive that uses an integer counter.
  - We will look at it in more depth later.
