---
layout: post
title: CSC Notes Chapter 2 Operating-System Structures
category: booknote
---

#CSC Notes Chapter 2 Operating-System Structures

>##Chapter Objectives
* To describe the services and operating system provides to users, processes, and other systems.
* To discuss the various ways of structuring an OS.
* To ex explain how operating systems are installed and customised and how they boot.

##Operating-System Services

###Services for Users:

* User interface
* Program execution
* I/O operations
* File-system manipulation
* Communications  
    * Communications between processes in the same computer.  
    * Communications between different computers.
    
    It can be implemented via _shared memory_ or through _message passing_.
* Error detection

###Services for System

* Resource allocation
* Accounting  
>Keep track of which users use how much and what kinds of computer resources.
* Protection and security
    * Control the privileges of data in the computer
    * While several processes execute concurrently, it should be able to prevent one process interfere others or the OS itself.
    * Ensure all access to system resources is controlled
    * Prevent/Authenticate users to access the system.
    
##User Operating-System Interface

###Command Interpreter
* Command interpreter itself contains the code to execute the command.
* Command interpreter implements most commands through system programs.

###GUI
* Windows
* X-window 
* …


##System Calls
>System calls provide an interface to the services made available by an operating system.

Three of most common APIs for applications to use system calls:

* Win32 API for Windows systems
* POSIX API for POSIX-based systems(which include virtually all versions of UNIX, Linux, and Mac OS X)
* Java API for Java virtual machine

Benefits from using API calls instead of direct system calls:

* portability
* system calls are often more detailed and difficult to work with

###Handle parameters passed to the OS:
* Pass the parameters in _registers_

When parameters are more than registers, we have 2 ways:

* store parameters in a _block_ or table in memory and pass the address of the block in a register. This method is used by Linux and Solaris.
* parameters are placed, or _pushed_, onto the stack by the program and popped off the stack by the OS

###_native_ in Java
Though Java is designed to run on platform-neutral systems, it is still able to make system call instead of being limited by JVM. This mechanism is called Java Native Interface(JNI). It, via declared _native_ methods, can invoke C/C++ codes to invoke system calls. When the _native_ method is used, the Java program is no longer considered as portable.

##Types of System Calls

6 categories(roughly)

* Process control
    * end, abort
    * load, execute
    * create process ,terminate process
    * get process attributes, set process attributes
    * wait for time
    * wait event, signal event
    * allocate and free memory
* File manipulation
    * create file, delete file
    * open, close
    * read, write, reposition
    * get file attributes, set file attributes
* Device manipulation
    * request device, release device
    * read, write, reposition
    * get device attributes, set device attributes
    * logically attach or detach devices
* Information maintenance 
    * get time or date, set time or date
    * get system data, set system data
    * get process, file, or device attributes
    * set process, file, or device attributes
* Communications 
    * create, delete communication connection
    * send, receive messages
    * transfer status information
    * attach or detach remote devices
* Protection

###Process Control

* A process invokes another process  
    >This question is related to the problem of whether the existing program is lost, saved, or allowed to continue execution concurrently with the new program.   
  
    * Before the invocation  
        * If control returns to the existing program when the new program terminates, we must save the memory image of the existing program before creating the new program.
        * If both program continue concurrently, the newly created job must be multiprogrammed.
        
    * When new processes are created  
    We should able to control the newly-created process(es):
        * get attributes
        * set attributes


    * Waiting the processes finish
        * wait time
        * wait event
    
    * When multiple processes share data  
      we need to lock shared data preventing another process from accessing the data until the lock is removed
        * acquire lock
        * release lock

###File Management

* create file
* delete file
* get file attribute 
* set file attribute

### Device Management

### Information Maintenance

### Communication
>There are two common models of interprocess communication: the message-passing model and the shared-memory model.

* message-passing model  
    the communicating processes exchange messages with one another to transfer information
    _Before communication can take place, a connection must be opened._
    * open connection: should have _process id_ first
    * read message and write message
    * close connection
    
* shared-memory model  
    * create and gain access to regions of memory owned by other processes  
    * allow maximum speed and convenience of communication, but cause protection and synchronisation problems.

* Protection
    * set permission
    * get permission
    * allow user
    * deny user

##System Programs

Hardware->OS->___System Programs___->Application Programs  

* File management
* Status information
* File modification
* Programming-language support
* Program loading and execution
* Communications


##Operating-System Design and Implementation

###Design Goals

* user goals
* system goals

###Mechanisms and Plicies
* Mechanism: how to do something
* Policies: What will be done

###Implementation
>As is true in other systems, major performance improvements in operating systems are more likely to be the result of better data structures and algorithms than of excellent assembly-language code. In addition, although operating systems are large, only a small amount of the code is critical to high performance; the memory manager and the CPU scheduler are probably the most critical routines. After the system is written and is working correctly, _bottleneck routines_ can be identified and can be replaced with assembly-language equivalents. (Bottlenecks are discussed further later in this chapter.)


##Operating-System Structure


###Simple Structure
* MS-DOS: not properly divided into modules and layers. e.g. application programs can access basic I/O routines directly.
* Original UNIX: Layered, but not being divided into modules. Everything between the system-call and hardware is the kernel. Thus, enormous amount of functionality to be combined into one level.

###Layered Approach
>A system can be made modular in many ways. One method is the layered approach, in which the operating system is broken into a number of layers (levels). The bottom layer (layer 0) is the hardware; the highest (layer N) is the user interface. 

Advantages:

* simplicity of construction and 
* debugging  
    Debug by layers
    
Difficulty 

* Appropriately defining the various layers
* a function could be required in different layers  
    e.g. 
     
    >The backing-store driver would normally be above the CPU scheduler, because the driver may need to wait for I/O and the CPU can be rescheduled during this time. However, on a large system, the CPU scheduler may have more information about all the active processes than can fit in memory. Therefore, this information may need to be swapped in and out of memory, requiring the backing-store driver routine to be below the CPU scheduler.

* layered implementations tends to be less efficient than other types

###Microkernels
Modularize the massive kernel into smaller one by moving nonessential components into user-level programs.
>The main function of the microkernel is to provide a communication facility between the client program and the various services that are also running in user space.

* Pros: ease of extending the OS. New services are added to user space and do not need the modification of the kernel.

* Cons: Need to invoke message passing to communicate between kernel and user-level programs. Suffer from performance decreases due to increased system function overhead.

###Modules
>Perhaps the best current methodology for operating-system design involves using object-oriented programming techniques to create a modular kernel.

Structure:  
Kernel with additional, dynamically loadable modules.

* Pros: allows the kernel to provide core services yet also allows certain features to be implemented dynamically.  
    * >The overall result resembles a layered system in that each kernel section has defined, protected interfaces; but it is more flexible than a layered system in that any module can call any other module.
    * >The approach is like the microkernel approach in that the primary module has only core functions and knowledge of how to load and communicate with other modules; but it is more efficient, because modules do not need to invoke message passing in order to communicate.

##Virtual Machines

Abstracted layered structure that produce the illusion that the virtual machines have their own processors and memory. 

###Pros:
 
* Share hardware
* The host system is protected from the virtual machines.
* For operating-system research and development.
* Running multiple OSs concurrently.
* Consolidation, which involves taking two or more separate systems and running them in virtual machines on one system.

###Implementation

The virtual machine runs in user mode on the physical machine. In the mean while, the virtual machine itself must have (virtual) kernel mode and (virtual) user mode.

##Java

>We refer to it as a technology rather than just a programming language because it provides more than a conventional programming language. Java technology consists of two essential components:  

>* Programming-language specification
 * Virtual-machine specification

###The Java Programming Language

###The Java Virtual Machine

###The Java Development Kit

###Java Operating Systems

##Operating-System Debugging

###Failure Analysis
###Performance Tuning
###DTrace































