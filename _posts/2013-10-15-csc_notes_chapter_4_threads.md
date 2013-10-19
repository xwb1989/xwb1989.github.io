---
layout: post
title: CSC Notes Chapter 4 Threads
category: booknote
---
#CSC Notes Chapter 4 Threads    

>##Chapter Objective
* To introduce the notion of a thread
* To discuss the APIs for the Pthreads, Win32, and Java thread libraries
* To examine issues related to multithreaded programming

##Overview
A thread is a basic unit of CPU utilisation; it comprises:
    
* a thread ID
* a program counter
* a register set
* a stack  

It shares with other threads belonging to the same process its 

* code section
* data section
* other OS resources such as open files and signals

###Uses

###Benefits

* Responsiveness
* Resource sharing
* Economy
* Scalability

###Multicore Programming

Multithread programming provides a mechanism for more efficient use of multiple cores and improved concurrency.

Five areas present challenges in programming for multicore systems:

* Dividing activities;
* Balance. Tasks should perform equal work of equal value;
* Data splitting. As applications are divided into separate tasks, the data accessed and manipulated by the asks must be divided to run on separate cores;
* Data dependency;
* Testing and debugging

##Multithreading Models

>User threads are supported above the kernel and are managed without kernel support, whereas kernel threads are supported and managed directly by the operating system. 

###Many-to-One Model
Maps user-level threads to one kernel thread.

Thread management is done by the thread library in user space, so it is efficient; but the entire process will block if a thread makes a blocking system call. Also, because only one thread can access the kernel at a time, multiple threads are unable to run in parallel on multiprocessors. 

###One-to-One Model

>The only drawback to this model is that creating a user thread requires creating the corresponding kernel thread. Because the overhead of creating kernel threads can burden the performance of an application, most implementations of this model restrict the number of threads supported by the system.

###Many-to-Many Model

>developers can create as many user threads as necessary, and the corresponding kernel threads can run in parallel on a multiprocessor. Also, when a thread performs a blocking system call, the kernel can schedule another thread for execution.

##Thread Libraries

###The JVM and the Host Operating System

A Java thread may be in one of six possible states in the JVM:

* New
* Runnable
* Blocked
* Waiting
* Timed waiting
* Terminated

###The fork() and exec() System Calls

###Cancellation
Thread cancellation is the task of terminating a thread before it has completed.

Cancellation may occur in 2 different scenarios:

* Asynchronous cancellation: cancel immediately
* Deferred cancellation: periodically checks whether it should be terminated. This allows the thread to be cancelled safely.


###Signal Handling

>A signal is used in UNIX systems to notify a process that a particular event has occurred.

* A signal is generated by the occurrence of a particular event
* A generated signal is delivered to a process
* Once delivered, the signal must be handled. Every signal may be handled by one of two possible handlers:
    * a default signal handler
    * a user-defined signal handler

###Thread Pools

The mechanism that handle every new request by a new thread has at least two downsides:

* the overhead of creating threads; the threads will be discarded once it completed its work.
* too much threads could exhaust system resources.

One solution: __Thread Pool__:

* Create a bunch of threads and make them wait for request;
* Awake one idle thread to handle request;
* Once completed the task, the thread returns to waiting status.
* If no thread is available, the server waits.

Pros:

* Fast to use existing thread than creating new one;
* Limit the number of threads.

>he number of threads in the pool can be set heuristically based on factors


###Thread-Specific Data

###Scheduler Activations

Concerns the communications between kernel and the thread library.

Many systems use a data structure named light weight process, to handle this job.

* LWP appears as a virtual processor on which the application can schedule a user thread to run. 
* Each LWP is attached to a kernel thread, and it is kernel threads that the operating system schedules to run on physical processors.
* If a kernel thread blocks, the LWP blocks as well, then the user-level thread attached to the LWP is also blocks.

scheduler activation mechanism: 

* The kernel provides a set of LWPs and the application can scheduler user threads onto an available LWP;
* When there is a event the kernel need to inform the application, the kernel fires a __upcall__ and the upcall must be handled by upcall handler running on another LWP.
* One case: an application thread is gonna be blocked.
    * kernel fires an upcall;
    * kernel allocates the application a new LWP on which the upcall handlers runs;
    * upcall handler saves the state of the blocking thread and relinquishes the LWP on which the blocking thread is running;
    * upcall handler then schedules another thread that is eligible to run on the new LWP;
    
* Another case: an event that the blocking thread is waiting for occurs:
    * kernel fires an upcall;
    * an upcall handlers runs on a LWP;
    * the upcall handler marks the unblocked thread runnable;
    * the application schedules an eligible thread to run on an available LWP.


















