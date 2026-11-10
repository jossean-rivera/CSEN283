---
date: 2026-11-05
---
# Producer & Consumer Case
Going back to the consumer & producer case. The professor discusses that if in the consumer you switch the order of the calls `wait(items)` and `wait(mutex)`, then it would create another deadlock. 

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
	sleep(); // go to sleep
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


## Reader Writer Problem
Both the reader and writer are accessing a shared resource. 
- The reader only reads the resource
- The writer only writes into the resource
- Multiple readers can read at the same time (no competition with each other)
- But the reader and a writer cannot work at the same time (they have to compete)
- Problems:
	- A - first reader-writer problem - reader preference
		- Say we have 3 reader and 2 writers, in this case only the first reader needs to compete because the other two do not compete with the first reader once the first reader gets control. 
	- B - second reader-writer problem - give writer preference (more opportunity)

### Case A first reader-writer problem code

First approach:
```cpp
sem_t resource_mutex = 1;

int reader_count = 0;

void reader() {
	while (true) {
		reader_count++; 
		if (reader_count == 1) { // this current thread is the first reader
			// this first reader is responsible 
			// for competeting with other writers
			wait(&resource_mutex);
		}
		
		read();
		
		reader_count--;
		if (reader_count == 0) { // this current thread is the last reader
			signal(&resource_mutex); // signal mutext so other writer 
		}
	}
}
```

This is the general idea, but it does not fully work because we need to protect the reading of the count variable, since multiple readers are running and we can have race condition on who's the first and last. 

```cpp
sem_t resource_mutex = 1;

int reader_count = 0;

void reader() {
	while (true) {
		wait(&reader_count_mutex); // protect count
		reader_count++; 
		if (reader_count == 1) { // this current thread is the first reader
			// this first reader is responsible 
			// for competeting with other writers
			wait(&resource_mutex);
		}
		signal(&reader_count_mutex);
		
		read();
		
		wait(&reader_count_mutex); // protect count
		reader_count--;
		if (reader_count == 0) { // this current thread is the last reader
			signal(&resource_mutex); // signal mutext so other writer 
		}
		signal(&reader_count_mutex);
	}
}

void writer() { 
	while (true) {
		wait(&resouce_mutex);
		
		write();
		
		signal(&resource_mutex);
	}
}
```

### Case B - Give preference to writer
We have a writer count in this case. We also need need a reader count to identify the first reader. 

```cpp

sem_t first_door; // mutex for the first door

void reader() {
	wait(&first_door); // wait until we can enter the first door
	
	wait(&reader_count_mutex); // protect reader count
	reader_count++;
	if (reader_count == 1) { // first reader
		wait(resource_mutex);
	}
	signal(&reader_count_mutex); // release reader count
	
	read();
	
	wait(&reader_count_mutex); // protect reader count
	reader_count--;
	if (reader_count == 0) { // last reader
		signal(resource_mutex);
	}
	signal(&reader_count_mutex); // release reader count
	
	signal(&first_door); // relase for writers
}

void writer() {
	
	wait(&writer_count_mutex); // protect reader count
	writer_count++;
	if (writer_count == 1) {
		wait(&first_door);
	}
	signal(&writer_count_mutex); // release writer count
	
	wait(resource_mutex);
	write();
	signal(resource_mutex);
	
	wait(&writer_count_mutex); // protect reader count
	writer_count++;
	if (writer_count == 0) { // last writer releases the first door
		signal(&first_door);
	}
	signal(&writer_count_mutex); // release writer count
}
```