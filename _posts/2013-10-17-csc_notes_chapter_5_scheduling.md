---
layout: post
title: CSC Notes Chapter 5 CPU Scheduling
category: booknote
---

#CSC Notes Chapter 5 CPU Scheduling 

>##Chapter Objective
* To introduce CPU scheduling, which is the basis for multiprogrammed operating systems.
* To describe various CPU-scheduling algorithms.
* To discuss evaluation criteria for selecting a CPU-scheduling algorithm for a particular system.

##Basic Concepts
* An I/O-bound program typically has many short CPU bursts;
* A CPU-bound program might have a few long CPU bursts.
CPU Scheduler: short-term scheduler

###Preemptive Scheduling
CPU-scheduling decisions may take place under the following four circumstance:

* When a process switches from the running state to the waiting state;
* When a process switches from the running state to the ready state;
* When a process switches from the waiting state to the ready state;
* When a process terminates.

__When scheduling takes place only under circumstances 1 and 4, we say that the scheduling scheme is nonpreemptive or cooperative; otherwise, it is preemptive.__

Preemption will cost data inconsistence problem.

###Dispatcher
The dispatcher is the module that gives control of the CPU to the process selected by the short-term scheduler. This function involves the following:

* Switching context
* Switching to user mode
* Jumping to the proper location in the user program to restart that program


dispatch latency: the time it takes for the dispatcher to stop one process and start another running.

##Scheduling Criteria
* CPU utilization: to keep the CPU as busy as possible;
* Throughput: the number of processes that are completed per time unit;
* Turnaround time: The interval from the time of submission of a process to the time of completion is turnaround time;
* Waiting time: the sum of the periods spent waiting in the ready queue;
* Response time: the time from the submission of a request until the first response is produced.

###Goal
* Maximize: CPU utilization and throughput;
* Minimize: turnaround time, waiting time, response time

##Scheduling Algorithms

###First-Come-First-Serve(FCFS)
Pros: 

* easy to implement and understand

Cons: 

* waiting time is long: convoy effect
* nonpreemptive

###Shortest-Job-First(SJF)

Pros: 

* gives optimal waiting time for a given set of processes

Cons:

* hard to know the length of the next CPU request
* unable to be implemented at the level of short-term scheduling.

We can predict the length of next CPU burst. With this predicted value, we then can pick up the shortest one.

SJF can be either preemptive of nonpreemptive. Preemptive SJF is sometimes called shortest-remaining-time-first-scheduling.

###Priority Scheduling

Higher priority task goes first. Equal-priority processes are scheduled in FCFS order.

Pros: 

Cons: starvation

Solution: ageing. Increase the priority of processes that wait in the system for a long time.

###Round-Robin Scheduling

Share processor time among tasks. If the quantum time is exhausted, the task is switched out. If the CPU-burst is shorter than quantum time, then the context-switching will come as early as the task finishes.

###Multilevel Queue Scheduling

Allocate different processes to different queues with different priorities such that:

* When the queues with higher priority is not empty, the tasks in queues with lower priority will not be executed. 
* When a task goes into the queue with higher priority, the running task in lower priority queue will be preempted.


###Multilevel Feedback Queue Scheduling
Allow a process to move between queues. If a process uses too much CPU time, it will be moved to a lower-priority queue otherwise it will be moved to a higher-priority queue. In general, a multilevel feedback queue scheduler is defined by the following parameters:  

* The number queues;  
* The scheduling algorithm for each queue; 
* The method used to determine when to upgrade a process to a higher-priority queue; 
* The method used to determine when to demote a process to a lower-priority queue;
* The method used to determine which queue a process will enter when that process needs service.

##Thread Scheduling

>It is kernel-level threads—not processes—that are being scheduled by the operating system. User-level threads are managed by a thread library, and the kernel is unaware of them. To run on a CPU, user-level threads must ultimately be mapped to an associated kernel-level thread, although this mapping may be indirect and may use a lightweight process (LWP).


###Contention Scope

There are 2 kins of contention scope in system:

* In many-to-one and many-to-many models, user thread need to be scheduled to available LWP(redirected from kernel thread). This scheme is called __process-contention scope(PCS)__.

* To decide which kernel thread on CPU, there is a system-contention scope(SCS). Systems using one-to-one model, such as Windows XP, Solaris, and Linux, schedule threads using only SCS.

>PCS is done according to priority. User-level thread priorities are set by the programmer and are not adjusted by the thread library, although some thread libraries may allow the programmer to change the priority of a thread.

##Multiple-Processor Scheduling

###Approach to Multiple-Processor Scheduling
* Asymmetric multiprocessing: all scheduling decisions, I/O processing, and other system activities are handled by a single processor -- the master server. The other processors execute only user code.
* Symmetric multiprocessing(SMP): each processor is self-scheduling. 

###Processor Affinity

Definition:  
In order to avoid cache memory invalidating and repopulating, SMP systems try to avoid migration of processes from one processor to another and instead attempt to keep a process running on the same processor.

* soft affinity: try to keep a process running on the same processor but not guarantee that;
* hard affinity: definitely not allow a process to migrate.
* processor sets: limits process might be able to migrate on limited sets of processors.

The main-memory architecture of a system can affect processor affinity issues.


###Load Balancing

Load balancing is necessary on systems where each processor has its own private queue of eligible processes to execute.

There two approach to load balancing:

* push migration: a specific task periodically checks the load on each processor and balance if an unbalance is found.
* pull migration: an idle processor pulls a waiting task from a busy processor.

Linux runs its load-balancing algorithm every 200 milliseconds (push migration) or whenever the run queue for a processor is empty (pull migration).

Load balancing often counteracts the benefits of processor affinity.

###Multicore Processors

__Memory stall:__ when a processor accesses memory, it spends a significant amount of time waiting for the data to become available.

We can map multiple thread to one core such that when one thread is in memory stall, the core can switch in another thread for computing.

Notice that a multithreaded multicore processor actually requires two different levels of scheduling. 

* On one level are the scheduling decisions that must be made by the operating system as it chooses which software thread to run on each hardware thread (logical processor). For this level of scheduling, the operating system may choose any scheduling algorithm.

* A second level of scheduling specifies how each core decides which hardware thread to run. There are several strategies to adopt in this situation.

###Virtualization and Scheduling

A system with virtualization, even a single-CPU system, frequently acts like a multiprocessor system.

Any guest operating-system scheduling algorithm that assumes a certain amount of progress in a given amount of time will be negatively affected by virtualization. 
The net effect of such scheduling layering is that individual virtualized operating systems receive only a portion of the available CPU cycles
Virtualization can thus undo the good scheduling-algorithm efforts of the operating systems in virtual machines.

##Operating System Examples
* Solaris: multilevel priority scheduling, many-to-many in old version and one-to-one since Solaris 9;
* Windows XP: one-to-one model, preemptive priority-based; 

* Linux: preemptive, priority-based and multilevel scheduling. Higher-priority tasks will have longer time quanta and lower-priority tasks shorter time quanta. 


##Java Scheduling
A runnable thread executes until one of the following events occurs:

* Its time quantum expires;
* It blocks for I/O;
* It exits its run() method.

###Thread Priorities

###Java Thread Scheduling on Solaris

##Algorithm Evaluation
Criteria:

* Maximizing CPU utilization
* Maximizing throughput

###Deterministic Modeling

Deterministic modeling is simple and fast. It gives us exact numbers, allowing us to compare the algorithms. However, it requires exact numbers for input, and its answers apply only to those cases.

###Queueing Models

>The computer system is described as a network of servers. Each server has a queue of waiting processes. The CPU is a server with its ready queue, as is the I/O system with its device queues. Knowing arrival rates and service rates, we can compute utilization, average queue length, average wait time, and so on. This area of study is called queueing-network analysis.

###Simulations






















