# Condition Variables and Lost Wakeups

> [!info] Source priority
> The following class-note excerpts record topics the professor discussed. They are retained in detail as lecture records. Warnings distinguish corrections from the original record; pseudocode is illustrative unless explicitly stated otherwise.

**Professor materials:** Professor Notes Lecture7-1.jpg (producer-consumer background); later discussion is in Week 7 Day 2. All listed materials are in `/Users/jossean/Documents/CSEN 283/`.

> [!warning] Corrections and answered question
> `has_space` and `has_items` are condition variables, not `bool` flags; `has_item` and `has_items` should use the same name. The `cond_wait` sketch is conceptual: releasing the mutex and becoming a waiter must be atomic with respect to signaling, and the mutex is reacquired before return. The `while` loop rechecks the shared predicate after every wakeup. A lost wakeup can happen if a thread checks the predicate, another changes it and signals before the first becomes a waiter, and the first then sleeps indefinitely. Protect the predicate with the same mutex and use an atomic condition wait. The CV itself does not retain a signal when no one waits. Additional references: [POSIX condition wait](https://man7.org/linux/man-pages/man3/pthread_cond_wait.3p.html) and [Oracle: lost wake-up](https://docs.oracle.com/cd/E19120-01/open.solaris/816-5137/sync-30/index.html).


---

## Lecture record: [[Class Week 7 - Day 2]] (lines 7–104)

## Another solution: **condition variable**
This case does not have a count. 
- We have two condition variables: `has_space` and `has_items`. 
- `has_space` (queue for producer)
- `has_items` (queue for consumer)

```cpp
int count = 0;
mutex m; // to protect whole operation
bool has_space;
bool has_items;

void producer() {
	while (true) {
		item = produce(); // simulate producing one item [impl not important]
		mutex_lock(&m); // lock the mutext
		while (count == N) { // this could be any condition
			// If count is N, the buffer's full
			// we cannot proceed
			cond_wait(&has_space, &m); // sleep & come back to check count again
			// when we wake up, we go to the condition to see if we can go
			// if we can't proceed again, then we go to sleep again & release loc
		}
		
		insert(item);
		count++;
		
		// after the producing is done, do a conditional signal
		cond_signal(&has_items);
		mutext_unlock(&m);
	}
}

void cond_wait(*cv, *m) {
	// ... first, add current cv to queue
	// something like: queue(cv);
	mutext_unlock(m); // release mutex
	block_current_thread(); // pseudocode: use the sleep function
	mutext_lock(m); // we wake up, so we need to acquire the lock again
}

void cond_signal(*cv) {
	if (empty(cv->queue) == false) { // if not empty
		thread = remove_one(cv->queue); // take one thread from the waiting queue
		make_runnable(thread); // then wakeup it
	}
}

void consumer() {
	while (true) {
		mutex_lock(&m);
		while (count == 0) { // if count is 0, there are no items to consumer
			// if empty, then we cannot proceed
			cond_wait(&has_item, &m); // sleep & come back later to check again
			// when we wakup, we check again if there are items to consume
			// if there are no items again, we sleep and wait
		}
		
		item = remove();
		count--;
		cond_signal(&has_space);
		mutex_unlock(&m);
		consume(item);
	}
}
```

The professor went over another deadlock case called loss wakeup if the order is not correct. What is that??

## Conditional Variable Example
Say we have a shared bank account and two threads. 
- Thread A withdraws
- Thread B deposits

```cpp
float balance = initial_deposit;

void threadB(float deposit_amount = 100) {
	mutext_lock(&m);
	balance += deposit_amount;
	cond_signal(&sufficient_fund);
	mutex_unlock(&m);
}

void threadA() {
	mutex_lock(&m);
	while (balance < 500) { // while we cannot withdraw the amount
		cond_wait(&sufficient_fund, &m); // release & sleep
		// wake up, get lock, and check again
	}
	
	// withdraw the amount
	balance -= 500;
	mutex_unlock(&m);
}
```
