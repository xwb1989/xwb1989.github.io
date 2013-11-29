---
layout: post
title: CSC Notes Chapter 9 Virtual Memory
category: booknote
---
#CSC Notes Chapter 9 Virtual Memory

>##Chapter Objectives
* To describe the benefits of a virtual memory system.
* To explain the concepts of demand paging, page-replacement algorithms,
and allocation of page frames.
* To discuss the principles of the working-set model.

##Background
Normally, it's unnecessary to load the whole program into memory:

* Programs often have code to handle unusual error conditions. They are seldomly used.
* Arrays, lists and tables are often allocated lager than used.
* Certain options and features of a program may be used rarely.

Even the whole program is useful, partially load the program could still have following advantage:

* A program would no longer be constrained by the amount of physical memory that is available.
* More programs could be run at the same time which could increase CPU utilization and throughput but with no increase in response time or turnaround time.
* Less I/O would be needed to load or swap user programs into memory, so each user program would run faster.

Virtual memory allows files and memory to be shared by two or more processes through page sharing. This leads to the following benefits:

* System libraries can be shred by several processes through mapping of the shared object into a virtual address space.
* Virtual memory enables processes to share memory.
* Virtual memory can allow pages to be shared during process creation with the `fork()` system call, thus speeding up process creation.

##Demand Paging

Definition: Load pages only as they are needed instead of the entire program.

>A lazy swapper never swaps a page into memory unless that page will be needed. Since we are now viewing a process as a sequence of pages, rather than as one large contiguous address space, use of the term swapper is technically incorrect. A swapper manipulates entire processes, whereas a pager is concerned with the individual pages of a process. We thus use pager, rather than swapper, in connection with demand paging.

###Basic Concepts

The procedure for handling page fault:

1. Check an internal table(usually kept with the process control block) for this process to determine whether the reference was a valid or an invalid memory access
* If the reference was invalid, we terminate the process. If it was valid, but we have not yet brought in that page, we now page it in.
* We find a free frame(by taking one from the free-frame list, for example).
* We schedule a disk operation to read the desired page into the newly allocated frame.
* When the disk read is complete, we modify the internal table kept we the process and the page table to indicate that the  page is now in memory.
* We restart the instruction that was interrupted by the trap. The process can now access the page as though it had always been in memory.

Pure demand paging: never bring a page into memory until it is required.

* Theoretically, some programs might access several new pages of memory with each instruction execution(one page for the instruction and many for data), possibly causing multiple page faults per instruction. This situation would result in unacceptable system performance. But this happens rarely.

* A crucial requirement for demand paging is the ability to restart any instruction after a page fault.  
    * It can be achieved easily since we have saved the state when an interrupt happens.
    * But one difficulty might be: the swap-in might modified the origin code/data, thus we cannot simply restart the instruction.
        * Solution one: Check the ends of each blocks. If a page fault is going to occur, it will happen at this step, before anything is modified.
        * Solution two: Use temporary registers to told the values of overwritten locations. If there is a page fault, all the old values are written back into memory before the trap occurs(roll back).

###Performance of Demand Paging
Performance of demand paging heavily depends on the page-fault rate. If we can not minimize the page-fault rate, the performance will be worsen dramatically.

##Copy-on-Write
Child process reference to the same address space as its parent. When one of the process modified the memory, it creates a copy.


##Page Replacement

The solution to solve over-allocation: when we need to swap in some pages, there is no free frame in memory.

###Basic Page Replacement

The page-fault service routine with page replacement:

1. Find the location of the desired page on the disk.
* Find a free frame:
    1. If there is a free frame, use it
    * If there is no free frame, use a page-replacement algorithm to select a __victim frame__,
    * Write the victim frame to the disk; change the page and frame tables accordingly.
* Read the desired page into the newly freed frame; change the page and frame tables.
* Restart the user process.

Since this scheme may double the effective access time, we can efficient reduce this overhead by using a ***modify bit(or dirty bit)***:

1. Each page or frame has a modify bit associated with it in the hardware.
* The modify bit for a page is set by the hardware whenever any word or byte in the page is written into, indicating that the page has been modified
* When we select a page for replacement, we examine its modify bit.
* If the bit is set, we need to write the page to the disk.
* Else, we can simply discard that page.

The two major problems to implement demand paging:

1. frame-allocation algorithm
2. page-replacement algorithm

###FIFO Page Replacement


###Optimal Page Replacement
Replace the page that will not be used for the longest period of time.

###LRU Page Replacement

Use the recent past as an approximation of the near future, replace the page that _has note been used_ for the longest period of time. This is called __least-recently-used(LRU) algorithm__.

Two ways to implement:

1. Counters: associate with each page-table entry a time-of-use field and add to the CPU a logical clock or counter. The clock is incremented for every memory reference. Whenever a reference to a page is made, the contents of the clock register are copied to the time-of-use field in the page-table entry for that page.
2. Stack: keep a stack of page numbers. Whenever a page is referenced, it is removed from the stack and put on the top.


###LRU-Approximation Page Replacement

####Additional-Reference-Bits Algorithm

1. If used, flip the highest bit to 1.
2. Shift right after a time interval
3. The process with smallest reference bits is the LRU.

####Second-Chance Algorithm

1. The basic algorithm of second-chance replacement is FIFO replacement algorithm.
2. When a page has been selected, we inspect its reference bit.
3. If the value is 0, replace the page.
4. If it's 1, we give this page a second chance by clear its reference bit and move on to select the next FIFO page.
5. Thus, when a page got a second chance, it will not be replaced until all the other pages have been replaced or given second chances.

####Enhanced Second-Chance Algorithm

Combine **reference bit** and **modify bit** to reduce the I/O request:

1. (0,0) neither recently used nor modified -- best page to replace
2. (0,1) not recently used but modified -- not quite as good,because the page will need to be written out before replacement.
3. (1,0) recently used but clean -- probably will be used again soon
4. (1,1) recently used and modified -- probably will be sued again soon, and the page will be need to be written out to disk before it can be replaced.


###Counting-Based Page Replacement

* least-frequently-used(LFU) algorithm
* most-frequently-used(MFU) algorithm: the page with the smallest count was probably just brought in and has yet to be used.

###Page-Buffering Algorithms

Buffer the modified pages and write them out to disk when the I/O is idle.

###Applications and Page Replacement

Because some applications can manager I/O more efficiently than OS for some specific functionalities, some OS give special programs the ability to use a disk partition as a large sequential array of logical blocks without any file-system data structures.

##Allocation of Frames

###Minimum Number of Frames

* one reason for allocating at least a minimum number of frames involves performance.
    * as the number of frames allocated to each process decreases, the page-fault rate increases
    * when a page fault occurs before an executing instruction is complete, the instruction must be restarted

* we must have enough frames to hold all the different pages that any single instruction can reference.
    
    
###Allocation Algorithms

1. equal allocation: split m frames among n processes equally and have leftover as free-frame buffer pool.
2. proportional allocation: allocate available memory to each process according to its size.

###Global versus Local Allocation

With multiple processes competing for frames, we can classify page-replacement algorithms into two broad categories:

* global replacement: allows a process to select a replacement frame from the set of all frames
* local replacement: requires that each process select from only its own set of allocated frames.

one problem with global replacement algorithm is that a process cannot control its own page-fault rate.

Local replacement might hinder a process, however, by not making available to it other, less used pages of memory. Thus, global replacement generally results in greater system throughput and is therefore the more common method.

###Non-Uniform Memory Access

The system boards are interconnected in various ways, ranging from system buses to high-speed network connections like InfiniBand. As you might expect, the CPUs on a particular board can access the memory on that board with less delay than they can access memory on other boards in the system. Systems in which memory access times vary significantly are known collectively as non-uniform memory access (NUMA) systems, and without exception, they are slower than systems in which memory and CPUs are located on the same motherboard.

##Thrashing

* If the process does not have the number of frames it needs to support pages in active use, it will quickly page-fault. 

* At this point, it must replace some page. However, since all its pages are in active use, it must replace a page that will be needed again right away. 

* Consequently, it quickly faults again, and again, and again, replacing pages that it must bring back in immediately.

* This high paging activity is called thrashing. A process is thrashing if it is spending more time paging than executing.

###Cause of Thrashing

Low CPU utilization causes higher multiprogramming which causes more page fault and further worsen the CPU utilization.

**local replacement algorithm**(or **priority replacement algorithm**): if one process starts thrashing, it cannot steal frames from another process and cause the latter to thrash as well.

To prevent thrashing, we must provide a process with as many frames as it needs. We need the **locality model** of process execution.

**Locality model**: 

* as a process executes, it moves from locality to locality. 
* A locality is a set of pages that are actively used together. 
* A program is generally composed of several different localities, which may overlap.

How it works:

1. Suppose we allocate enough frames to a process to accommodate its current locality. 
2. It will fault for the pages in its locality until all these pages are in memory; then, it will not fault again until it changes localities. 
3. If we do not allocate enough frames to accommodate the size of the current locality, the process will thrash, since it cannot keep in memory all the pages that it is actively using.

###Working-Set Model

What is working set:

1. Working-set model is based on the assumption of locality. 
* This model uses a parameter, `delta`, to define the **working-set window**.
* the idea is examine the most recently used `delta` page references.
* The set of pages in the most recent `delta` page references is the **working set**.
    * If a page is in active use, it will be in the working set.
    * If a page is no longer being used, it will drop from the working set `delta` time units after its last reference.

Thus, the working set is an approximation of the program's locality.  
The accuracy of the working set depends on the selection of `delta`:

1. If `delta` is too small, it will not encompass the entire locality;
2. If `delta` is too large, it may overlap several localities.


How does it work:

1. OS monitors the working set of each process and allocates to that working set enough frames to provide it with its working-set size.
2. If there are enough extra frames, another process can be initiated.
3. If the sum of the working-set sizes increases, exceeding the total number of available frames, OS selected a process to suspend.
4. The process's pages are written out(swapped), and its frames are reallocated to other processes.
5. The suspended process can be restarted later.

This strategy prevents thrashing while keeping the degree of multiprogramming as high as possible. Thus it optimizes CPU utilization.

It's hard to keep track of the working set because the working-set window is a moving window.  
We can approximate the working-set model with a fixed-interval timer interrupt and a reference bit. We update the reference bit when the timer interrupt incurs and when a page fault incurs, the pages with particular reference bit(larger than 00, for example) are considered in the working set.

###Page-Fault Frequency

**Page-fault frequency(PFF)**: a more direct approach to handle thrashing:

* If the page-fault rate exceeds the upper limit, we allocate the process another frame;
    * If the page-fault rate increases and no free frames are available, we must select some process and suspend it.
* If the page-fault rate falls below the lower limit, we remove a frame from the process.

##Memory-Mapped Files

**Memory-mapping**: Instead of using standard system calls `open(), read(), write()` to access files, we can use virtual memory techniques to treat file I/O as routine memory accesses.
###Basic Mechanism

Memory-mapping a file is accomplished by mapping a disk block to a page(or pages) in memory.

1. Initial access to the file proceeds through ordinary demand paging, resulting in a page fault;
2. a page-sized portion of the file is read from the file system into a physical page;
3. subsequent reads and writes to the file are handled as routine memory accesses;
4. The practice simplifies file access and usage by allowing the system of manipulate files through memory rather than incurring the overhead of using the `read()` and `write()` system calls.

##Allocating Kernel Memory

Kernel memory is often allocated from a free-memory pool different from the list(discussed earlier) used to satisfy ordinary user-mode processes. There are two primary reasons for this:

1. The kernel requests memory for data structures of varying sizes, some of which are less than a page in size. Use paging will generate fragmentation problem.
2. Pages allocated to user-mode processes do not necessarily have to be in contiguous physical memory. However, certain hardware devices interact directly with physical memory -- without the benefit of virtual memory interface -- and consequently may require memory residing in physically contiguous pages.

###Buddy System

The buddy system allocates memory from a fixed-size segment consisting of physically contiguous pages. Memory is allocated from this segment using a power-of-2 allocator, which satisfies requests in units sized as a power of 2 (4 KB, 8 KB, 16 KB, and so forth). A request in units not appropriately sized is rounded up to the next highest power of 2.

We split a memory chunk evenly until we get a block that meets the requirement from the allocator.

Drawback: up to 50% internal fragmentation.

###Slab Allocation

* A **slab** is made up of one or more physically contiguous pages.
* A **cache** consists of one or more slabs.

How it works:

1. The slab allocator first attempt to satisfy the request with a free object in a partial slab.  
2. If none exists, a free object is assigned from an empty slab.   
3. If no empty slabs are available, a new slab is allocated from contiguous physical pages and assigned to a cache; memory for the object is allocated from this slab.

Benefits:

1. No memory is wasted due to fragmentation
2. Memory requests can be satisfied quickly because objects are created in advance and thus can be quickly allocated from the cache.

##Other Considerations for Paging Systems

###Prepaging

**Prepaging** is an attempt to prevent this high level of initial paging. The strategy is to bring into memory at one time all the pages that will be needed.

###Page Size

* Large page size: smaller page table, more fragmentation
* Small page size: larger page table, less fragmentation

###TLB Reach

###Inverted Page Tables

###Program Structure

###I/O Interlock




















