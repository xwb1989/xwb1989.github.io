---
layout: post
title: CSC Notes Chapter 10 File-System Interface
category: booknote
---
#CSC Notes Chapter 10 File-System Interface

>##CHAPTER OBJECTIVES
* To explain the function of file systems.
* To describe the interfaces to file systems.
* To discuss file-system design tradeoffs, including access methods, file sharing, file locking, and directory structures.
* To explore file-system protection.

##File Concept

A file is a named collection of related information that is recorded on secondary storage.

* **text file**: a sequence of characters organized into lines(and possibly pages)
* **source file**: a sequence of subroutines and functions, each of which is further organized as declarations followed by executable statements
* **object file**: a sequence of bytes organized into blocks understandable by the system's linker
* **executable file**: a series of code sections that the loader can bring into memory and execute

###File Attributes

* Name
* Identifier: this is the non-human-readable name for the file
* Type
* Location
* Size
* Protection
* Time, date, and user identification

###File Operations

A file is an abstract data type. To define a file properly, we need to consider the operations that can be performed on files. 

* Creating a file
* Writing a file
* Reading a file
* Repositioning within a file
* Deleting a file
* Truncating a file

###File Types

###File Structure

###Internal File Structure

All disk I/O is performed in units of one block(physical record), and all blocks are the same size. All file systems suffer from internal fragmentation; the larger the block size, the greater the internal fragmentation.

##Access Methods

###Sequential Access

Sequential access: information in the file is processed in order, one record after the other.

Reads and writes make up the bulk of the operations on a file. A read operation—read next—reads the next portion of the file and automatically advances a file pointer, which tracks the I/O location. Similarly, the write operation—write next—appends to the end of the file and advances to the end of the newly written material (the new end of file). 

###Direct Access
Direct access(or relative access) is a file is made up of fixed- length logical records that allow programs to read and write records rapidly in no particular order. 

The direct-access method is based on a disk model of a file, since disks allow random access to any file block. For direct access, the file is viewed as a numbered sequence of blocks or records.

How does it work:

1. we have `read n`
2. we position to the block `n`, then `read next`

###Other Access Methods

Other access methods can be built on top of a direct-access method. These methods generally involve the construction of an index for the file. The index, like an index in the back of a book, contains pointers to the various blocks. To find a record in the file, we first search the index and then use the pointer to access the file directly and to find the desired record.

##Directory and Disk Structure

1. Disk -(partitioned)-> 
2. Volume -(each contains)-> 
3. file system -(stores information about the files in the system in)-> 
4. device directory(or volume table of contents)

###Storage Structure

###Directory Overview

1. Search for a file
2. Create a file
3. Delete a file
4. List a directory
5. Rename a file
6. Traverse the file system

###Single-Level Directory

All files are contained in the same directory, which is easy to support and understand.

###Two-Level Directory

###Tree-Structure Directories

###Acyclic-Graph Directories
A tree structure prohibits the sharing of files or directories. An acyclic graph —that is, a graph with no cycles—allows directories to share subdirectories and files.

A common way, exemplified by many of the UNIX systems, is to create a new directory entry called a link. A link is effectively a pointer to another file or subdirectory. For example, a link may be implemented as an absolute or a relative path name. 

###General Graph Directory
Need to avoid cycle in traversing.

##File-System Mounting

The operating system is given the name of the device and the mount point — the location within the file structure where the file system is to be attached. 


##File Sharing

###Multiple Users

###Remote File Systems

###Consistency Semantics
Consistency semantics represent an important criterion for evaluating any file system that supports file sharing. These semantics specify how multiple users of a system are to access a shared file simultaneously. In particular, they specify when modifications of data by one user will be observable by other users. These semantics are typically implemented as code with the file system.

##Protection

###Types of Access
###Access Control
###Other Protection Approaches
















