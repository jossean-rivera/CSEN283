---
date: 2026-10-22
tags:
---
## Continue Process Relationship

Types:
1. Competition
2. Collaboration

Both can require processes to be synchronized.

### Lock Problem
Last time, we discussed a naive lock in C that checks a global integer before proceeding. This is wrong because checking and acquiring the lock must be one atomic operation. **The lock itself is a shared resource too.**

### Requirement 1 - Mutual Exclusion
At most one thread or process can execute a critical section that accesses a particular shared resource at any given time. 
On a uniprocessor, a task in a critical section may be preempted, but another task must not enter a critical section protected by the same lock. In limited kernel contexts, disabling local interrupts can prevent such preemption. On a multiprocessor, disabling interrupts on one CPU does not stop other CPUs from accessing the resource. 

### Requirement 2 - Progress
If the critical section is free and tasks want to enter, the choice of the next task cannot be postponed indefinitely. 

### Requirement 3 - No starvation
No task should wait indefinitely because other tasks are repeatedly given access to the resource. 

## Categories of Solution
1. Software solutions — useful for understanding synchronization, though these classic algorithms are rarely used alone today.
2. Hardware solutions 

### Software solutions
#### Dekker's Algorithm
Consider two chess players taking turns using a shared pointer to indicate the active player. Strict alternation works when both players want to play. For a shared bathroom, however, one person might not want to enter, leaving the other waiting indefinitely.

Dekker's algorithm combines a turn variable with flags indicating interest. If one process does not want to enter, the other can proceed. 

```cpp
p0: 
wants_to_enter[0] = true;
while (wants_to_enter[1]) {
	if (turn != 0) { // both p0 and p1 want to get in
		wants_to_enter[0] = false; // it's not p0 turn, then walk out 
		while (turn != 0) {
			// busy wait, wait for my turn
		}
		wants_to_enter[0] = true;
	}
}

// critical section ...

turn = 1; // change direction
wants_to_enter[0] = false; // indicate p0 is not interested anymore

p1:
wants_to_enter[1] = true;
while (wants_to_enter[0]) {
	if (turn != 1) { // both p0 and p1 want to get in
		wants_to_enter[1] = false;  // it's not p1 turn, then walk out
		while (turn != 1) {
			// busy wait, wait for p1's turn
		}
		wants_to_enter[1] = true;
	}
}

// critical section ...

turn = 0; // change direction
wants_to_enter[1] = false; // indicate p1 is not interested anymore
```

#### Peterson's algorithm
Peterson's algorithm simplifies the two-process solution:
```cpp
pA:
wants_to_enter[A] = true;
turn = B; // Turning at the same time, but eventually either of them will win
while (wants_to_enter[B] == true && turn == B) { // wait while B is interested and it is B's turn
	// Busy wait
}

// critical section ...

wants_to_enter[A] = false; // Restore

pB:
wants_to_enter[B] = true;
turn = A;
while (wants_to_enter[A] == true && turn == A) { // wait while A is interested and it is A's turn
	// Busy wait
}

// critical section ...

wants_to_enter[B] = false; // Restore
```

#### Other algorithms
- E. Dijkstra's Algorithm (1965)

These algorithms assume atomic reads and writes with suitable memory ordering. Ordinary shared variables in modern C or C++ do not provide those guarantees.

### Hardware Solutions
General idea:
```
lock() {
	disable interrupt;
}

unlock() {
	enable interrupt;
}
```

Disabling interrupts can prevent interrupt-driven preemption on the local CPU while kernel code enters a critical section.

**User processes** generally cannot disable interrupts. They use locks built from atomic operations; they can still be interrupted, but another thread must acquire the lock before entering the protected critical section. 

If **Kernel process**, we consider two cases for single and multi processors:
- Single hart: in suitable kernel contexts, disable interrupts before the critical section and restore the previous interrupt state afterward.
- Multiple harts: use a lock to exclude other harts; also disable local interrupts when an interrupt handler could acquire the same lock. 

Types of lock:
- Busy wait lock - occupies the CPU while the process waits for the lock to be available. 
	- Spin lock - state: RUNNING
	- Wait in CPU
- Blocking/sleep lock - when the current process doesn't get a lock, then it sleeps, meaning that it will yield the CPU.
	- Mutex - state: SLEEPING
	- Wait in a blocked queue

#### Spin Lock
`test_and_set()` atomically sets a lock and returns its previous value. Depending on the architecture, it can be provided by a dedicated instruction or implemented using another atomic primitive, such as atomic exchange.

The following is pseudocode: each test-and-set or compare-and-swap operation is atomic, and lock acquisition and release require appropriate memory ordering. Ordinary loads and stores do not make these functions atomic.
```cpp
test_and_set(lock) {
	current_value = *lock;
	*lock = true; // lock it
	return current_value; // we lock it and return the previous value
}

// implementation of lock with test_and_set
function lock(*lockObj){
	while (test_and_set(lockObj)); // busy wait
}

// Unlock is always the same and easy
function unlock(*lockObj){
	*lockObj = false;
}

compare_and_swap(ptr, expect_value, new){
	if *ptr != expect_value {
		return false;
	} else {
		*ptr = new;
		return true;
	}
}
// Implementation with compare_and_swap
function lock(*lockObj) {
	while(!compare_and_swap(lockObj, 0, 1));
}
```
