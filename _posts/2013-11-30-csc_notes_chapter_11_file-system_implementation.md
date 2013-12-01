---
layout: post
title: CSC Notes Chapter 11 File-System Implementation
category: booknote
---
#CSC Notes Chapter 11 File-System Implementation

>##CHAPTER OBJECTIVES
* To describe the details of implementing local file systems and directory structures.
* To describe the implementation of remote file systems.
* To discuss block allocation and free-block algorithms and trade-offs.

##File-System Structure

2 characteristics that make disks a convenient medium for storing multiple files:

1. A disk can be rewritten in place
2. A disk can access directly any block of information it contains.

To improve I/O efficiency, I/O transfers between memory and disk are performed in units of blocks. 

File systems provide efficient and convenient access to the disk by allowing data to be stored, located, and retrieved easily. It poses two quite different design problems:

1. to define how the file system should look to the user
2. to create algorithms and data structures to map the logical file system onto the physical secondary-storage devices

The structures: list from lowest to highest levels:

1. **device**
2. **I/O control**: 
    * consists of device drivers and interrupt handlers to transfer information between the main memory and the disk system. 
    * Its input consists of high-level commands and its output consists of low-level, hardware-specific instructions that are used by the hardware controller.
3. **basic file system**: 
    * issue generic commands to the appropriate device driver to read and write physical blocks on the disk. 
    * This layer also manages the memory buffers and caches that hold various file-system, directory, and data blocks.
4. **file-organization module**: 
    * translate logical block addresses to physical block addresses for the basic file system to transfer.
    * includes the free-space manager, which tracks unallocated blocks and provides these blocks to the file-organization module when requested.  

5. **logical file system**: manages **metadata** information. Metadata includes all of the file-system structure except the actual data (or contents of the files). It maintains file structure via **file-control blocks(FCB)**.
    * Metadata includes all of the file-system structure except the actual data (or contents of the files). 
    * A file-control block (FCB) (an inode in most UNIX file systems) contains information about the file, including ownership, permissions, and location of the file contents.

##File-System Implementation

###Overview
Several on-disk and in-memory structures are used to implement a file system.

On disk, the file system may contain information about how to boot an operating system stored there, the total number of blocks, the number and location of free blocks, the directory structure, and individual files.

* a **boot control block(per volume)** can contain information needed by the system to boot an operating system from that volume. 
    * If the disk does not contain an operating system, this block can be empty. It is typically the first block of a volume. 
    * In UFS, it is called the boot block; in NTFS, it is the partition boot sector.

* a **volume control block(per volume)** contains volume (or partition) details, such as number of blocks in the partition, size of the blocks, a free-block count and free-block pointers, and a free-FCB(file control block) count and FCB pointers.

* a **directory structure(per file system)** is used to organize the files.
* a **per-file FCB** contains many details about the file.

The in-memory information is used for both file-system management and performance improvement via caching. The data are loaded at mount time, updated during file-system operations, and discarded at dismount. Several types of structures may be included.

* an **in-memory mount table** contains information about each mounted volume
* an **in-memory directory-structure cache** holds the directory information of recently accessed directories.
* the **system-wide open-file table** contains a copy of the FCB of each open file, as well as other information. This table not only stores the FCB but also tracks the number of processes that have the file open.
* the **per-process open-file table** contains a pointer to the appropriate entry in the system-wide open-file table, as well as other information.
* **Buffers** hold file-system blocks when they are being read from disk or written to disk.

**The process of creating a new file:**

1. an application program calls the logical file system.
2. the logical file system knows the format of the directory structures
    * it allocates a new FCB
    * if the file-system implementation creates all FCBs at file-system creation time, an FCB is allocated from the set of free FCBs.
3. the system then reads the appropriate directory into memory, updates it with new file name and FCB, and writes it back to the disk.

**The process of opening a file:**

1. the `open()` call passes a file name to the logical file system.
2. the `open()` system first searches the system-wide open-file table to see if the file is already in use by another process.
    * if it is, a per-process open-file table entry is created pointing to the existing system-wide open-file table.
    * if not, the directory structure is searched for the given file name. Once the file is found, the FCB is copied into a system-wide open-file table in memory.
3. an entry is made in the per-process open-file table, with a pointer to the entry in the system-wide open-file table and some other fields.
4. the `open()` call returns a pointer to the appropriate entry in the per-process file-system table. **All file operations are then performed via this pointer.**

**The process of closing a file**

1. the per-process table entry is removed and the system-wide entry's open count is decremented.
2. when all users that have opened the file close it, any updated metadata is copied back to the disk-based directory structure and the system-wide open-file table entry is removed.

###Partitions and Mounting

A disk can be sliced into multiple partitions, or a volume can span multiple partitions on multiple disks.

Each partition can be either “raw,” containing no file system, or “cooked,” containing a file system. Raw disk is used where no file system is appropriate. UNIX swap space can use a raw partition, for example, as it uses its own format on disk and does not use a file system.

###Virtual File Systems(VFSs)

The VFS layer serves two important functions:

1. It separates file-system-generic operations from their implementation by defining a clean VFS interface.
2. It provides a mechanism for uniquely representing a file throughout a network. The VFS is based on a file-representation structure, called a **vnode**, that contains a numerical designator for a network-wide unique file. 

VFS handles requests separately:

1. The VFS activates file-system-specific operations to handle local requests according to their file-system types.
2. It calls the NFS protocol procedures for remote requests.

The VFS architecture in Linux. The four main object types defined by the Linux VFS are:

* The **inode object**, which represents an individual file
* The **file object**, which represents an open file
* The **super block object**, which represents an entire file system
* The **dentry object**, which represents an individual directory entry

VFS software layer can perform an operation on one of these objects by calling the appropriate function from the object’s function table, without having to know in advance exactly what kind of object it is dealing with.

##Directory Implementation

###Linear List

Linear list: to use a linear list of file names with pointers to the data blocks

* easy to implement
* time consuming

###Hash Table

Use linear list with a hash table: the hash table has (file name, pointer to the file in the linear list) as (key, value) pair.

##Allocation Methods

###Contiguous Allocation

Contiguous allocation requires that each file occupy a set of contiguous blocks on the disk.

Pros:

* good performance: less head movement

Cons: 

* CONS: hard to find space for a new file. Suffer from external fragmentation. One solution is **compaction**:
    * it can be done off-line: with the file system unmounted
    * it can be done on-line in most modern systems: during normal system operations, but with performance compensation.

* hard to determine how much space is needed for a file. When the file is created, the total amount of space it will need must be found and allocated
    * if we allocate too little space, the file cannot be extended
    * if we allocate too much, suffer from external fragmentation

Solution: **extent**. When the initially-allocated chunk of space is note large enough during the filer's life cycle, another chunk of contiguous space is allocated as an **extent**. Still suffers from fragmentation.

###Linked Allocation

* Each file is a linked list of disk blocks; 
* The disk blocks may be scattered anywhere on the disk. 
* The directory contains a pointer to the first and last blocks of the file.
* Each block contains a pointer to the next block.

The process of creating a new file:

1. create a new entry in the directory;
2. each directory entry has a pointer to the first disk block of the file, which is initialized to `nil` to signify an empty file;
3. the size field is also set to 0;
4. a write to the file causes the free-space management system to find a free block and this new block is written to and is linked to the end of the file;
5. to read a file, we simply read blocks by following the pointers from block to block;

Pros:

* no external fragmentation
* any free block on the free-space list can be sued to satisfy a request
* the size of file need not be declared when that file is created
* a file can continue to grow as long as free blocks are available
* NEVER necessary to compact disk space

Cons:

* it can be used effectively only for sequential-access files: to find the *i*th block of a file, we must start at the beginning of the file and follow the pointers until we get to the *i*th block.
* the space required for pointers
    * this can be solved by **clusters**: allocate clusters instead of blocks, but increase internal fragmentation.
* reliability: an error in one block might result in the loss of the whole file

VARIATION: **file-allocation table(FAT)**:

* A section of disk at the beginning of each volume is set aside to contain the table. 
* The table has one entry for each disk block and is indexed by block number.
* The directory entry contains the block number of the first block of the file. 
* The table entry indexed by that block number contains the block number of the next block in the file. 
* This chain continues until it reaches the last block, which has a special end-of-file value as the table entry. 
* An unused block is indicated by a table value of 0. 
* Allocating a new block to a file is a simple matter of finding the first 0-valued table entry and replacing the previous end-of-file value with the address of the new block. 
* The 0 is then replaced with the end-of-file value.

It can improve the random-access time and  reduce the number of disk head seeks if cached.


###Indexed Allocation

**Indexed allocation** solve the random-access problem by bringing all the pointers together into one location: the **index block**.

The index block stores an array containing the addresses of the blocks of the file.

Pros:

* direct access
* no external fragmentation
* no size issue

Cons:

* the index-block overhead: this can be solved by multiple mechanism:
    * linked scheme: link several index blocks for large file
    * multilevel: A variant of linked representation uses a first-level index block to point to a set of second-level index blocks, which in turn point to the file blocks.
    * combined scheme: mixed direct block and indirect block
    
    Under this method, the number of blocks that can be allocated to a file exceeds the amount of space addressable by the four-byte file pointers used by many operating systems.

###Performance

* Contiguous allocation: good for direct access, but suffer from fragmentation;
* Linked allocation: good for large file but bad at direct access, high storage utilization;
* Indexed allocation: good for direct access, large file and storage usage, the access of the tail of the file might be slow(because it uses multilevel index);

##Free-Space Management

**General Idea:**

* The free-space list records all free disk blocks—those not allocated to some file or directory. 
* To create a file, we search the free-space list for the required amount of space and allocate that space to the new file. 
* This space is then removed from the free-space list. 
* When a file is deleted, its disk space is added to the free-space list.

###Bit Vector

**bit map** or **bit vector**: each block is represented by 1 bit:
    
* if the block is free, the bit is 1;
* else it is 0. 

Pros:

* simplicity
* efficiency in finding the first free block or n consecutive free blocks on the disks

Cons:

* the bitmap need to be in memory
* the bitmap can be very large for large disk

###Linked List

link together all the free disk blocks, keeping a pointer to the first free block in a special location on the disk and caching it in memory.

###Grouping

* stores the addresses of n free blocks in the first free block.
* the first n-1 of these blocks are actually free
* the last block contains the addresses of another n free blocks and so on

Pros:

* the addresses of a large number of free blocks can now be found quickly, unlike the situation when the standard linked-list approach is used.

###Counting

Based on the fact: several contiguous blocks may be allocated or freed simultaneously.

We keep the address of the first free block and the number(*n*) of free contiguous blocks that follow the first block. Each entry in the free-space list then consists of a disk address and a count.

Note that the following blocks can be stored in a B-tree, rather than a linked list, for efficient lookup, insertion, and deletion.

###Space Maps

ZFS uses a combination of techniques in its free-space management algorithm to control the size of data structures and minimize the I/O needed to manage those structures.

##Efficiency and Performance
* Efficiency: disk storage utilization
* Performance: time consumption


###Efficiency 

The efficient use of disk space depends heavily on the disk allocation and directory algorithms in use. 

Larger pointers support larger disk but consume more space.

###Performance

####Caching
* Some systems maintain a separate section of main memory for a **buffer cache**, where blocks are kept under the assumption that they will be used again shortly. 
* Other systems cache file data using a **page cache**. 
    * The page cache uses virtual memory techniques to cache file data as pages rather than as file-system-oriented blocks. 
    * Caching file data using virtual addresses is far more efficient than caching through physical disk blocks, as accesses interface with virtual memory rather than the file system. 
* Several systems—including Solaris, Linux, and Windows NT, 2000, and XP—use page caching to cache both process pages and file data. This is known as **unified virtual memory**.

* unified buffer cache: when a unified buffer cache is provided, both memory mapping and the read() and write() system calls use the same page cache. This has the benefit of avoiding double caching, and it allows the virtual memory system to manage file-system data.

####Synchronous or Asynchronous

* Synchronous write: the disk subsystem receives the order, and the writes are not buffered.
* Asynchronous write: the data are stored in the cache, and the control returns to the caller.

###Algorithms
Algorithms of the page cache to improve the performance: 

* Free-behind: removes a page from the buffer as soon as the next page is requested. The previous pages are not likely to be used again and waste buffer space.
* Read-ahead: a requested page and several subsequent pages are read and cached. The pages are likely to be requested after the current page is processed.

>One might think that a track cache on the controller would eliminate the need for read-ahead on a multiprogrammed system. However, because of the high latency and overhead involved in making many small transfers from the track cache to main memory, performing a read-ahead remains beneficial.

##Recovery

###Consistency Checking

a file system can record its state within the file-system metadata. At the start of any metadata change, a status bit is set to indicate that the metadata is in flux. If all updates to the metadata complete successfully, the file system can clear that bit. If, however, the status bit remains set, a consistency checker is run.

> * For instance, if linked allocation is used and there is a link from any block to its next block, then the entire file can be reconstructed from the data blocks, and the directory structure can be recreated. 
> * In contrast, the loss of a directory entry on an indexed allocation system can be disastrous, because the data blocks have no knowledge of one another. For this reason, UNIX caches directory entries for reads; but any write that results in space allocation, or other metadata changes, is done synchronously, before the corresponding data blocks are written.

###Log-Structured File Systems
**log-based transaction- oriented (or journaling) file systems**: using the idea of log-based recovery algorithms.

1. all metadata changes are written sequentially to a log;
2. each set of operations for performing a specific task is a transaction;
3. once the changes are written to this log, they are considered to be committed, and the system call can return to the user process, allowing it to continue execution(user-kernel-user mode switches); 
4. meanwhile, these log entries are replayed across the actual file-system structures;
5. as the changes are made, a pointer is updated to indicate which actions have completed and which are still incomplete;
6. when an entire committed transaction is completed, it is removed from the log file, which is actually a circular buffer;
7. if the system crashes, the log file will contain zero or more transactions;
8. any transactions it contains were not completed to the file system:
    * the transactions can be executed from the pointer until the work is complete so that the file-system structures remain consistent;
    * if a transaction was aborted — that is, was "uncommitted" before the system crashed: any changes from such a transaction that were applied to the file system must be undone;

>A side benefit of using logging on disk metadata updates is that those updates proceed much faster than when they are applied directly to the on-disk data structures. The costly synchronous random metadata writes are turned into much less costly synchronous sequential writes to the log-structured file system’s logging area. Those changes in turn are replayed asynchronously via random writes to the appropriate structures. The overall result is a significant gain in performance of metadata-oriented operations, such as file creation and deletion.

###Other Solutions

###Backup and Restore

##NFS(Network File System)



















