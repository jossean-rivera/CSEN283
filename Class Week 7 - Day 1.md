---
date: 2026-11-03
---

Last week, unfortunately, the professor was sick and classes had to be canceled.

# Thread/Process Recap

## Agenda

- Scheduling (done) ✅
- Competition (synchronization) — continue working on this

## Competition recap

**Problem context:** We have threads sharing a critical resource (such as a shared variable), and multiple threads may try to use it at the same time.

**Possible solutions**

- **Software solutions**
  - Not commonly used in practice by themselves.
  - [[Class Week 5 - Day 2#Dekker's Algorithm]]
  - [[Class Week 5 - Day 2#Peterson's algorithm]]
- **Hardware-assisted solutions**
  - Mostly used today.
  - Disabling interrupts: [[Class Week 5 - Day 2#Hardware Solutions]]
  - Atomic instructions
    - **Spinlock:** [[Class Week 5 - Day 2#Spin Lock]], using functions such as `test_and_set()` and `compare_and_swap()`.
    - **Mutex:** a blocking lock (we will study this today).

## Mutex

With a spinlock, we have a single lock field (`0` or `1`). A thread repeatedly waits until it can enter the critical section.

A mutex instead blocks the waiting thread: it “goes home and sleeps” until the lock becomes available. A mutex needs to track whether it is free, its owner, and a queue of blocked threads.

Data structure:

```c
typedef struct {
    bool free;
    int owner;
    pcb *p_block_q;  // pointer to the queue of blocked processes/threads
} mutex_t;
```

The `p_block_q` field represents a **waiting queue**. Each PCB in the queue represents a process or thread that tried to acquire the mutex while it was locked. The diagram is therefore:

`mutex → waiting PCB → waiting PCB → ...`

### Mutex lock and unlock implementations

One possible implementation is shown below. The operations that examine or modify the mutex and its waiting queue must themselves be atomic.

```c
lock(m)  // m is a mutex
    if m.free
        m.free = false
        m.owner = me
        return
    else  // locked
        if m.owner == me
            throw error  // non-recursive mutex: self-deadlock
        else
            enqueue(me, m.p_block_q)
            block(me)

unlock(m)
    if m.owner != me
        throw error
    else if m.p_block_q is not empty
        t = dequeue(m.p_block_q)
        m.owner = t       // transfer ownership to the woken thread
        wakeup(t)
    else
        m.free = true
        m.owner = NONE
```

> [!info] Mutexes and interrupt handlers
> A normal mutex may block or sleep while waiting. An interrupt handler cannot sleep because it must finish before the interrupted execution can resume, so it must not acquire a mutex. When interrupt and normal code share data, kernels typically use a spinlock instead. If the interrupted code holds that spinlock, local interrupts must be disabled while it holds the lock; otherwise, the interrupt handler could spin forever waiting for the code it interrupted to release it. A spinlock is still needed to exclude other CPU cores (harts).

### Mutex rules for avoiding deadlocks

1. Lock a mutex before entering the critical section that it protects.
2. Unlock it exactly once after leaving that critical section; normally, the same thread that acquired it must release it.
3. For multiple mutexes, acquire them in one consistent global order and release them in reverse order.

You can create deadlocks if you break these rules:

- **Self-deadlock:** A thread calls `lock(m)` and then calls `lock(m)` again before releasing it. With a non-recursive mutex, it waits for itself forever.
- **Nested-call self-deadlock:** Thread A calls `lock(m)` and then calls function B. If B calls `lock(m)`, Thread A again waits for itself.
- **Circular deadlock:** Two threads use mutexes `m1` and `m2`. If T1 acquires `m1` and waits for `m2`, while T2 acquires `m2` and waits for `m1`, neither can continue.

The solution is to use the same lock order in every thread:

- T1: `lock(m1)`, then `lock(m2)`
- T2: `lock(m1)`, then `lock(m2)`
- Release them in reverse order: `unlock(m2)`, then `unlock(m1)`

## Synchronization

Let there be two threads, T1 and T2. T1 is you and T2 is a janitor. When both enter a shared resource—the bathroom—we want T2 to run first. Therefore, the order needs to be controlled.

Another example: suppose we have several washrooms (shared resources), and many people—T1, T2, T3, ..., T7, ...—are trying to use them. We need to coordinate who uses a resource. A **semaphore** can do this by tracking the number of available resources.

### Semaphore types

1. **Binary semaphore:** has a logical value of `0` or `1`; similar to a mutex because it tracks whether one resource is available.
2. **Counting semaphore:** can track multiple instances of a resource.

```c
typedef struct {
    pcb *p_block_q;
    int count;
} sem_t;
```

Here, we increment or decrement the count for a shared resource. Semaphore operations must be **atomic**.

High-level implementation (busy waiting): the test and decrement in `wait` must occur atomically, as must the increment in `signal`. These are conceptual operations, not ordinary C statements.

```c
signal(s) {
    s.count++;
}

wait(s) {
    while (s.count <= 0) {
        ;  // spin
    }
    s.count--;
}
```

- `signal(s)`
  - Increment operation.
  - Also called the **V operation**; `V` comes from the Dutch *verhogen* (“increase”).
- `wait(s)`
  - Decrement operation, waiting if no resource is available.
  - Also called the **P operation**; it is often explained as from Dutch *proberen* (“to try/test”).

The busy-wait version wastes CPU time. A blocking semaphore uses a waiting queue instead. In this implementation, a negative count represents the number of waiting processes:

```c
void signal(sem_t *s) {
    s->count++;
    if (s->count <= 0) {       // a process is still waiting
        pcb *p = dequeue(s->p_block_q);
        wakeup(p);
    }
}

void wait(sem_t *s) {
    s->count--;
    if (s->count < 0) {        // no resource was available before decrementing
        enqueue(me, s->p_block_q);
        block(me);
    }
}
```

### Usage example 1

```c
void t1() {
    // ... statement 1
    signal(s);
}

void t2() {
    wait(s);
    // ... statement 2
}
```

If `s` starts at `0`, `t2` cannot execute statement 2 until `t1` has called `signal(s)`. This is **ordering synchronization**.

### Usage example 2

```c
void taskA() {
    wait(m);
    // critical section
    signal(m);
}

void taskB() {
    wait(m);
    // critical section
    signal(m);
}

void taskC() {
    wait(m);
    // critical section
    signal(m);
}
```

If `m` starts at `1`, it acts like a binary semaphore and allows only one task in the critical section at a time.

Suppose Task A runs and is interrupted in its critical section. The sequence is:

1. Task A calls `wait(m)`: `m` changes from `1` to `0`.
2. Task A is interrupted by Task B.
3. Task B calls `wait(m)`: `m` changes to `-1`, and B blocks.
4. Task C calls `wait(m)`: `m` changes to `-2`, and C blocks.
5. Task A resumes and calls `signal(m)`: `m` changes to `-1`, and B wakes up.
6. Task B executes the critical section, then calls `signal(m)`: `m` changes to `0`, and C wakes up.
7. Task C executes the critical section, then calls `signal(m)`: `m` changes to `1`, meaning the resource is free.

### Producer-consumer example problem

Suppose we have a shared, bounded memory buffer:

```c
char buffer[SIZE];
int in = 0;
int out = 0;

sem_t space = SIZE;
sem_t items = 0;
sem_t mutex = 1;
```

```c
void consumer() {
    while (true) {
        wait(items);          // wait until at least one item exists

        wait(mutex);          // protect the shared buffer and `out`

        take(buffer[out]);    // remove an item: critical section
        out = (out + 1) % SIZE;

        signal(mutex);        // leave the critical section
        signal(space);        // one more buffer slot is available
    }
}

void producer() {
    while (true) {
        wait(space);          // wait until at least one slot is empty

        wait(mutex);          // protect the shared buffer and `in`

        fill(buffer[in]);     // add an item: critical section
        in = (in + 1) % SIZE;

        signal(mutex);        // leave the critical section
        signal(items);        // one more item is available
    }
}
```

- `space` starts at `SIZE` and prevents a producer from adding to a full buffer.
- `items` starts at `0` and prevents a consumer from removing from an empty buffer.
- `mutex` starts at `1` and ensures only one producer or consumer updates the shared buffer and indices at a time.
- `(index + 1) % SIZE` wraps `in` and `out` around the circular buffer.
