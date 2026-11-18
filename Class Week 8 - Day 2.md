---
date: 2026-11-12
---
Continuing with memory management

# Old Time Memory Management
Requirements: 
- All continuous in memory
- All loaded into memory, not in sections
### Fixed partition
Takes the memory and divides it into sections (partitions) of different sizes from small to large and your program goes into the one that's available that best fits the program. 

### Variable Partition
As new processes arrive the OS assigns to each process the exact amount it needs. You can end up with "holes" of unused space (or external fragmentation). This way, we can end up with a lot of unused memory. 

To manage variable partitions we used either linked lists or bitmap. Linux uses a **buddy system**, in which the memory keeps getting divided by 2

### Insufficient Memory Issue
What if no hole (free space) in memory is not large enough to fit a process? We still assume that the memory allocation needs to be continuous, which means we would have a problem. 
Possible solutions:
- Memory compaction: try moving the process blocks in memory to one end of the memory to compact the holes. 
- Swapping: Swap processes. Create new space by selecting one of the processes in memory and temporarily evicting it to secondary storage. 
- Overlay: A process space is reduced by establishing how much it needs, but it's puts the burden of memory management on the programmer. 

# Modern Memory Management
Another solution to the memory management is to use non-continuous memory space. 

### Segmentation
A solution to use non-continuous space. When a problem is compiled and broken down into different sections, such as functions, stack, main program, symbol table, etc. 
Each segment has: 
1. Name
2. Length
We need to convert segment number and offset -> into physical address (2D logical address -> 1D physical address). 

Problem with segmentation: 

### Paging
Paging and Swapping is consider virtual memory. 

#### Logical Space
The program deals with the **logical space**, it looks to the developer as a continuous piece of memory. The size of the logical space is determined by the address bits, for example 3 bit addresses can work with 8 addresses (2^3). 

Say we have one page of 4 sections of 1K bytes. Then `4K = 4 * 2^10 = 2^2 * 2^10 = 2^12` - which means we need 12 bits to select the byte within the one page to be able to select one specific byte. 

A logical address can be 32 bits, we break the address into two parts: the page number and the offset. In the case of 4K pages, we know the offset has to be 12 bits. So, the logical page number has to use the remaining 20 bits. 

Total number of pages: `2^20 = 2^10 * 2^10 = 1024 * 1024 = 1M` (1 million entries). The pages are numbered from page 0 to page 1M, each of them are 4K in size. 

Absolute size of the logical space using 32 bit addresses: `1M * 4K = 4G`. **Therefore, each process can have a logical space up to 4GB**.  

#### Physical Space
Now, moving on to the physical space to see how the logical maps to physical address. 
The physical space is also broken down into physical pages, we use the same size of 4K. The physical memory space is also numbered from page 0 to the last page (based on the number of bits, for example, in RISC-V we use 34 bits), which is 4M. The last page is numbered 4 Million.  

In a physical address, we break down the address into two parts: the physical page number (22 bits) and the offset (12 bits). 
- Num of physical pages: `2^22 = 4 * 2^20 = 4M`
- Size of each page: `4K`
- Size of physical space = `4M * 4K = 16GB`. 

\*Even though the whole physical space is 16GB, we cannot use the whole space since we need some allocations for devices. 

We use a **page table** to map a page from logical space into a page of physical space. In this table we have rows with the number of the logical page and the corresponding number of the physical page. Each entry in the table is 4 bytes (32 bits) and, in this case, the paging table has 1M entries, going from entry 0 to entry 1M. The break down for each entry is: physical page num (22 bits) and the options (10 bits), while the index of the entry corresponds to the logical page number. 

The options bits in the page table entries are: 
- V - Valid bit - is this page valid (allocated) or not?
- R - Read bit 
- W - Write bit
- X - Executable bit 
- U
- G
- A - Access bit 
- D - Dirty bit - is the page "dirty" meaning that we need to update it because it's modified