# Readers and Writers

> [!info] Source priority
> The following class-note excerpts record topics the professor discussed. They are retained in detail as lecture records. Warnings distinguish corrections from the original record; pseudocode is illustrative unless explicitly stated otherwise.

**Professor materials:** Week 7 Day 2 class note; no corresponding professor image or slides identified. All listed materials are in `/Users/jossean/Documents/CSEN 283/`.

> [!warning] Incomplete and incorrect original pseudocode
> The first implementation lacks protection for `reader_count`, as the note itself explains. Initialize `reader_count = 0`, `reader_count_mutex = 1`, and `resource_mutex = 1`. Correct typos before implementing. In the writer-preference sketch, the exit path must decrement `writer_count`, not increment it. The reader must release the entry gate after it has joined the active-reader group, before `read()`, so subsequent readers can join; retaining it for the entire read serializes readers and can interfere with the intended design. Initialize `first_door`, `writer_count_mutex`, and `reader_count_mutex` to 1 and both counts to 0. Fairness depends on semaphore queueing; reader preference can starve writers, and writer preference can starve readers. These are conceptual algorithms, not tested production code. Additional reference: [University of Wisconsin reader–writer lecture](https://pages.cs.wisc.edu/~oliphant/cs537-sp24/slides/15_Semaphores.pdf).



## Reconstructed writer-preference outline

This restates the intended first-door algorithm with the counter and gate errors corrected. The semaphores `first_door`, `reader_count_mutex`, `writer_count_mutex`, and `resource_mutex` start at 1; both counts start at 0. Operations below are pseudocode, and wakeup fairness depends on the semaphore implementation.

```text
reader:
    wait(first_door)
    wait(reader_count_mutex)
    reader_count += 1
    if reader_count == 1: wait(resource_mutex)
    signal(reader_count_mutex)
    signal(first_door)              # admit other readers if no writer closes gate
    read shared resource
    wait(reader_count_mutex)
    reader_count -= 1
    if reader_count == 0: signal(resource_mutex)
    signal(reader_count_mutex)

writer:
    wait(writer_count_mutex)
    writer_count += 1
    if writer_count == 1: wait(first_door)  # block new readers
    signal(writer_count_mutex)
    wait(resource_mutex)
    write shared resource
    signal(resource_mutex)
    wait(writer_count_mutex)
    writer_count -= 1
    if writer_count == 0: signal(first_door)
    signal(writer_count_mutex)
```

Once the first waiting writer closes the gate, existing readers can finish but new readers cannot enter. The last writer reopens the gate. The algorithm gives writers preference at the gate; it does not by itself guarantee fairness for either group.

---

## Lecture record: [[Class Week 7 - Day 2]] (lines 105–232)

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
