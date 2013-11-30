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













