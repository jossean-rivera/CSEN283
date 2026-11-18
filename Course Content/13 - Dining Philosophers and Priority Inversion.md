# Dining Philosophers and Priority Inversion

> [!info] Source priority
> The following class-note excerpts record topics the professor discussed. They are retained in detail as lecture records. Warnings distinguish corrections from the original record; pseudocode is illustrative unless explicitly stated otherwise.

**Professor materials:** Week 8 Day 1 class note; no corresponding professor image or slides identified. All listed materials are in `/Users/jossean/Documents/CSEN 283/`.

> [!warning] Corrections to original examples
> In the first dining-philosophers snippet, the second release must use `chopstick[(i + 1) % size]`. In the global-order solution the second bound must be `max`, not a second `min`; each philosopher acquires the lower-numbered of its two chopsticks, then the higher, including the wraparound pair. This removes circular wait for those locks; it does not itself guarantee starvation freedom. Timeouts and retries can livelock, which is repeated activity without progress, rather than the same blocked state as deadlock. Priority inheritance boosts the lock owner when a higher-priority task waits; the ceiling example refers to an immediate-ceiling approach, and details vary by protocol. Additional reference: [Linux RT mutex design](https://docs.kernel.org/locking/rt-mutex-design.html).



## Resource-ordering correction

With distinct chopsticks numbered `0` through `N - 1`, each philosopher holds its two actual neighbors and acquires them in the same global order:

```text
left = i
right = (i + 1) % N
first = min(left, right)
second = max(left, right)
wait(chopstick[first])
wait(chopstick[second])
eat
signal(chopstick[second])
signal(chopstick[first])
```

For philosopher `N - 1`, the pair is `N - 1` and `0`, so it requests `0` first. All waiting edges go toward a higher-numbered held resource; a cycle cannot arise from these requests. This prevents that circular-wait deadlock but does not ensure every philosopher eventually eats.

---

## Lecture record: [[Class Week 8 - Day 1]] (lines 4–100)

# Continue process synchronization
Continuing with other examples of process synchronization. 
## Dining Philosopher Problem
### The problem
there a several philosophers seating around a round table (circle table), there's food in the middle and they use chopsticks to eat. There is one chopstick (a single item) on the side of each philosopher. The philosopher needs to use both chopstick on each side to be able to eat, while the other one will have to wait. 

## Solution
We'll have a function for each philosopher at index `i`, and the chopstick will have the same index. 

An intuitive solution would be this one:
```cpp
int size = 5; // assuming 5 philosophers and 5 chopsticks

void philosopher_i(int i) {
	wait(chopstick[i]);
	wait(chopstick[(i+1) % size]);
	
	// ... eat (critical resource) ...
	
	signal(chopstick[i]);
	signal(chopsitck[i+1] % size);
}
```

But this solution has a problem: Imagine each philosopher getting a chopstick on one side

| User        | P1        | P2        | P3        | P4        | P5        |
| ----------- | --------- | --------- | --------- | --------- | --------- |
| Hold        | C1        | C2        | C3        | C4        | C5        |
| Second Hold | C2 (fail) | C3 (fail) | C4 (fail) | C5 (fail) | C1 (fail) |
This is a deadlock situation. It's a **circular deadlock**. 

Similar problem: We have two threads with different locks:

| Thread | T1  | T2  |
| ------ | --- | --- |
| Lock 1 | M1  | M2  |
| Lock 2 | M2  | M1  |
This is a deadlock, and we need to solve it by calling wait in the same order. 

Solution 1 for philosopher problem: 
1. wait(left) with a 10 minute timer
2. After 10 minutes, if we did not get the lock, then signal(left) to release it to give the opportunity to someone else
3. 10 minutes later, we try again

This can still have a deadlock, called a **live lock**. The professor says it's like when two people run into each other on a hallway, and they both move to the same side of the hall and then back to the original. The problem is that they are still in the same lane of the hall which would be the shared resource in this case. 

Solution 2 for philosopher problem: number the resource and request from small for big number.

```cpp
void philosopher(int i) {
	// request for small -> big num
	a = min(i, (i+1) % size);
	b = min(i, (i+1) % size);
	wait(chopstick[a]);
	wait(chopstick[b]);
	
	// eat (critical section)
	
	signal(chopstick[a]);
	signal(chopstick[b]);
}
```

The problem with this solution (2), is that C5 will have to request C1 which is not a bigger number. So then we need to release C2, ..., C4

# Priority Scheduling
Going back to the scheduling topic. There's a related issue called **priority inversion**. 

**Normally:**
In a normal situation, a high priority preempts low priority. Now, there's an exception to that, it's not always the case because what if the high priority has a lock. 

**Exception**:
Except that high and low compete for the same lock, and the lower acquired the lock first. Then the higher priority needs to wait for the lower priority to release the lock. 

Case 1: P low starts, gets lock, and it's preempted by P high, which then will wait and yield back to P low to finish first. 

Case 2: P low starts, gets lock, and it's preempted by P high, which then waits and yields but if there's another thread P med, then this one will run and delay P low and P high. This is the main problem we need to fix, because what if P med is very long? Then P low will basically be locked for a while. 

Solution 1 for Case 2: Priority ceiling. Max priority of process that might need that same lock. 

Whenever we need to request that lock, then we raise the priority. In this example, P low will call `wait(m)` and then raise its priority to the ceiling (maximum) priority to run the critical section. In this solution, P medium will not be able to preempt the first process so there is no problem of delay. 

Solution 2 for Case 2: Priority Inheritance. In this case, P low runs at low priority until P high competes. The first steps are the same: P low calls wait, aquires lock, starts running critical section and gets preempted by P high. Then, P high calls wait and yields back, then P low inherits the priority of P high and continues in the same level. So, P low will raise to P high's priority section. 

## Process Topics Completed
Topics covered in class so far:
- Scheduling
- Competing
	- Spin lock
	- Mutex lock
- Synchronization
	- Semaphore
- IPC (inter process communication)
	- Exchange large amount of data
	- Example: sockets (between remote computers)
