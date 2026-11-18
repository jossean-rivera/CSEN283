# Semaphores and Producer Consumer

> [!info] Source priority
> The following class-note excerpts record topics the professor discussed. They are retained in detail as lecture records. Warnings distinguish corrections from the original record; pseudocode is illustrative unless explicitly stated otherwise.

**Professor materials:** Professor Notes Lecture7-1.jpg. All listed materials are in `/Users/jossean/Documents/CSEN 283/`.

> [!warning] Atomicity and initialization
> Semaphore wait and signal, including queueing and sleeping, require atomic coordination. The busy-wait listing is conceptual, not safe as ordinary C. The ordering example requires `s` initially 0. A counting semaphore can also hold 0 or 1; a binary semaphore’s logical permit value is at most 1.


---

## Lecture record: [[Class Week 7 - Day 1]] (lines 101–260)

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
    if (s->count <= 0) { // a process is still waiting
        pcb *p = dequeue(s->p_block_q);
        wakeup(p);
    }
}

void wait(sem_t *s) {
    s->count--;
    if (s->count < 0) { // no resource was available before decrementing
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

---

## Lecture record: [[Class Week 7 - Day 2]] (lines 4–6)

# Producer & Consumer Case
Going back to the consumer & producer case. The professor discusses that if in the consumer you switch the order of the calls `wait(items)` and `wait(mutex)`, then it would create another deadlock.
