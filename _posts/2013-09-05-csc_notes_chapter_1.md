---
layout: post
title: CSC Notes Chapter 1 Overview
category: booknote
---
#CSC Notes Chapter 1 Overview
Page 1-49

##What is Operating System?

A computer system is roughly composed by 4 parts:  

* Hardware
    * CPU
    * memory
    * I/O devices
* Operating System  
  _Controls the hardware and coordinates its use among the various application programs for various users._
* Application Programs
* Users

___An operating system is a program that manages the computer hardware. It also provides a basis for application programs and acts as an intermediary between user and computer hardware.___

We can split this definition into two parts, according to _Liuba_'s word on class:

* Resource allocator: mostly, resource is the hardware, and OS is to allocate(manage) them.
* Hardware abstraction: as an intermediary above hardware and under application programs and users, OS provides abstraction of hardware.

##Computer-System Organization

###Computer-System Operation

* Power up or reboot:  
    starts from a program stored in ROM(or other non-volatile memory) named ___Bootstrap Loader___. Since it's in the ROM(hard to change), we also call it ___firmware___. The Bootstrap Loader locate and load the OS into the main memory, then the OS starts to work. We have the pseudo-code like this:  

        start_point = Memory.start;
        loaded = 0;
        OS_length = OS.Length;
        while(loaded < OS_length)
            move content from disk[start_point + loaded] to Memory;
            loaded++;
            

* On working:  
    After executing the first process, init, the OS waits for events to occur. The occurrence of an event is signalled by an ___interrupt___. It's from either hardware of software.
    * Hardware trigger an interrupt by sending a signal to CUP usually by way of the ___system bus___.
    * Software may trigger an interrupt by executing a ___system call___(aka monitor call).   
    
    The interrupts will be handled specifically for high-speed requirement via special handler and information storage mechanism.
  
###Storage Structure

* Instruction Register in CPU: instructions loaded to here, after execution, stored back to:
* Main memory: small, expensive. Might not big enough to hold the whole program.

Both of above are volatile, small and expensive.

* Non-volatile memory: read-only memory, etc. To store the bootstrap loader.

* Secondary storage: magnetic disk, magnetic tape, flash storage, etc. 

###I/O Structure

I/O device<-->device driver<-->device local buffer<-->OS

##Computer-System Architecture

* Single-Processor Systems
* Multiprocessor Systems
* Clustered Systems

##Operating-System Structure

* Single-programming
* Multiprogramming:   
    Via time sharing to handle multiple jobs to utilise cpu

    * Unable to keep all jobs on RAM. So
    * Keep jobs on the disk in the job pool
    * While cup is idle(e.g. waiting for an input):
    * Job scheduling: to decide load which job into RAM;
    * CPU scheduling: to decide execute which job when multiple jobs are ready.
    * Swapping: processes are swapped in and out of RAM from/to the disk, which usually implemented as:
    * Virtual memory: Separate part of the disk as logical memory as an extension of the physical memory to handle the the memory-storage limitations.

##Operating-System Operations

###Interrupt
___OS is interrupt driven. If nothing happens, it stays quietly.___ Events are mostly signalled by the occurrence of an interrupt of a trap.
>A __trap__(or an __exception__) is a software-generated interrupt caused by an error or by a specific request from a user program that an OS service be performed.

As an error might cause problem to other programs, a robust OS must ensure that an incorrect program cannot cause other programs to behave incorrectly.

###Dual-Mode Operation

To ensure the proper execution of OS, we have different modes to execute OS code and user-defined code respectively. At least, we have 2:

* Kernel mode: aka supervisor mode, system mode, privileged mode.   
    Privileged instructions are only allowed to be executed in kernel mode thus it can stop the damage from errant users.
* User mode: when the OS is waiting from an interrupt or error.  

When a trap or interrupt occurs, hardware switches from user mode to kernel. To simplify, when users are controlling the OS, it's user mode. When the control finishes and OS take the job, it becomes kernel mode.

###Timer

To prevent a suer program from running too long.

##Process Management

* Scheduling processes and threads on the CPUs
* Creating and deleting both user and system processes
* Suspending and resuming processes
* Providing mechanisms for process synchronisation
* Providing mechanisms for process communication

##Memory Management

* Keeping track of which parts of memory are currently being used and by whom
* Deciding which processes(or parts thereof) and date to move into and out of memory
* Allocating and del allocating memory space as needed

##Storage Management

###File-System Management
* Creating and deleting files
* Creating and deleting directories to organise files
* Supporting primitives for manipulating files and directories
* Mapping files onto secondary storage
* Backing up files on stable(nonvolatile) storage media

###Mass-Storage Management

* Free-space management
* Storage allocation
* Disk scheduling

###Caching

A special, faster storage where assumed-frequently-used data is stored. It has limited size thus _cache management_ is very important.

###I/O Systems
* A memory-management component that includes buffering, caching, and spooling
* A general device-driver interface
* Drivers for specific hardware devices

##Protection and security
While OS allows multiple users and allows concurrent execution of multiple processes, then the access to data should be well regulated.

* Protection: A mechanism for controlling the access of processes of users to the resources defined by a computer system.
* Security: To defend a system from external and internal attacks.

##Distributed Systems


>A distributed system is a collection of physically separate, possibly heterogeneous computer systems that are networked to provide the uses with access to the various resources that the system maintains.

The benefit of access to a shred resource:

Increase in:  

* computation speed
* functionality
* data availability
* and reliability 

###Network Operating System
>An operating system that provides features such as file sharing across the network and that includes a communication scheme that allows different processes on different computers to exchange messages.

##Special-Purpose Systems

###Real-time Embedded Systems
The embedded systems are designed to run on specific devices with limited but specific functions.

###Multimedia Systems

###Handheld Systems

##Computing Environment  

###Traditional Computing
###Client-Server Computing
* compute-serve system: computations are executed in the server and the result then will be send back to client.
* file-server system: provides a file-system interface where clients can create, update, read, and delete files.

###Peer-to-peer Computing
###Web-Based Computing

##Open-Source Operating Systems


















































