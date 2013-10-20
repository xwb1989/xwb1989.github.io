---
layout: post
title: CSC Notes Chapter 6 Process Synchronization
category: booknote
---

#CSC Notes Chapter 6 Process Synchronization

>##Chapter Objective  
* To introduce the critical-section problem, whose solutions can be used to ensure the consistency of shared data.
* To present both software and hardware solutions to the critical-section problem.
* To introduce the concept of an atomic transaction and describe mechanisms to ensure atomicity.

##The Critical-Section Problem

Critical section: the part of codes in which process may be changing common variables, updating a table, writing a file, and so on.
Each process must request permission to enter its critical section. The section of code implementing this request is the __entry section__. The critical section may be followed by an __exit section__. The remaining code is the __remainder section__. 
Requirements:

* Mutual exclusion
* Progress
* Bounded waiting

Two general approaches are used to handle critical sections in operating systems: 

* Preemptive kernels: allow a process to be preempted while it is running in kernel mode.
* nonpreemptive kernels: only allows one process is alive in the kernel at a time such that this is obviously race-condition-free.

##Peterson's Solution
Peterson’s solution is restricted to two processes that alternate execution between their critical sections and remainder sections.

##Synchronization Hardware


##Semaphores

###Implementation

The main disadvantage of the semaphore definition just described is that it requires __busy waiting__. A semaphore that produces this result is also called a spinlock, because the process “spins” while waiting for the lock. 


__The critical aspect of semaphores is that they be executed atomically, which can be solved in one of two ways:__

* In single-processor environment: inhibit interrupts during the time the acquire() and release() operations are executing
* In multiprocessor environment: employ any of the correct software solutions for the critical-section problem

###Deadlocks and Starvation

two or more processes are waiting indefinitely for an event that can be caused only by one of the waiting processes.


###Priority Inversion

Low priority process interfere high priority process because the high priority is waiting a lower priority process to finish and it has been preempted by the low priority process.

Solution:

* Only use 2 priorities
* Priority-inheritance protocol: all processes that are accessing resources needed by a higher-priority process inherit the higher priority until they are finished with the resources in question


##Classic Problems of Synchronization
###The Bounded-Buffer Problem

###The Readers-Writers Problem

###The Dining-Philosophers Problem

##Monitors

##Java Synchronization

###Multiple Notifications

###Block Synchronization

###Synchronization Rules

1. A thread that owns the lock for an object can enter another synchronized method (or block) for the same object. This is known as a recursive or reentrant lock.
2. A thread can nest synchronized method invocations for different objects. Thus, a thread can simultaneously own the lock for several different objects.
3. If a method is not declared synchronized, then it can be invoked regardless of lock ownership, even while another synchronized method for the same object is executing.
4. If the wait set for an object is empty, then a call to notify() or notifyAll() has no effect.
5. wait(), notify(), and notifyAll() may only be invoked from synchronized methods or blocks; otherwise, an IllegalMonitorStateException is thrown.

>It is also possible to declare static methods as synchronized. This is because, along with the locks that are associated with object instances, there is a single class lock associated with each class.

###Handling InterruptedException

###Concurrency Features in Java

* Reentrant Locks: provides several additional features, such as setting a fairness parameter, which favours granting the lock to the longest-waiting thread.
* Semaphores
* Condition Variables

##Atomic Transactions

###System Model

* A transaction is a sequence of read and write operations terminated by either a commit operation or an abort operation.
* If a terminated transaction has completed its execution successfully, it is committed; otherwise, it is aborted.
* Roll back: restore changed states if the transaction has been aborted to keep the execution of transaction be atomic.

###Transactional Memory
>A memory transaction is a sequence of memory read–write operations that are atomic. If all operations in a transaction are completed, the memory transaction is committed; otherwise, the operations must be aborted and rolled back. The benefits of transactional memory can be obtained through features added to a programming language.

###Log-Based Recovery


###Checkpoints


###Concurrent Atomic Transactions











