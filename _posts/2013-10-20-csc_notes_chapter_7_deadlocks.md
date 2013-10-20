---
layout: post
title: CSC Notes Chapter 7 Deadlocks
category: booknote
---
#CSC Notes Chapter 7 Deadlocks
>##Chapter Objectives
* To develop a description of deadlocks, which prevent sets of concurrent processes from completing their tasks.* To present a number of different methods for preventing or avoiding deadlocks in a computer system.
##System Model

Under the normal mode of operation, a process may utilize a resource in only the following sequence:

* Request
* Use
* Release

A set of processes is in a deadlocked state when every process in the set is waiting for an event that can be caused only by another process in the set.

##Deadlock Characterization

###Necessary Conditions

A deadlock situation can arise if the following four conditions hold simultaneously in a system:

1. Mutual exclusion
2. Hold and wait
3. No preemption
4. Circular wait

###Resource-Allocation Graph

A circle in the graph indicates there may be deadlocks.

###Method for handling deadlocks
Generally speaking, we can deal with the deadlock problem in one of three ways:

* We can use a protocol to prevent or avoid deadlocks, ensuring that the system will never enter a deadlocked state.
* We can allow the system to enter a deadlocked state, detect it,and recover.
* We can ignore the problem altogether and pretend that deadlocks never occur in the system.

###Three Major Methods

##Deadlock Prevention

* Mutual Exclusion: The mutual-exclusion condition must hold for non-sharable resources. 
* Hold and Wait: To ensure that the hold-and-wait condition never occurs in the system, we must guarantee that, whenever a process requests a resource, it does not hold any other resources. 
* No preemption: there should be no preemption of resources that have already been allocated.
* Circular wait: impose a total ordering of all resource types and to require that each process requests resources in an increasing order of enumeration.

##Deadlock Avoidance
A deadlock-avoidance algorithm dynamically examines the resource-allocation state to ensure that a circular- wait condition can never exist. The resource-allocation state is defined by the number of available and allocated resources and the maximum demands of the processes.

###Safe State
A state is safe if the system can allocate resources to each process (up to its maximum) in some order and still avoid a deadlock.

###Resource-Allocation-Graph Algorithm

Now suppose that process Pi requests resource Rj. The request can be granted only if converting the request edge Pi → Rj to an assignment edge Rj → Pi does not result in the formation of a cycle in the resource-allocation graph. We check for safety by using a cycle-detection algorithm. An algorithm for detecting a cycle in this graph requires an order of n^2 operations, where n is the number of processes in the system.

###Banker's Algorithm

##Deadlock Detection
>A deadlock exists in the system if and only if the wait-for graph contains a cycle. To detect deadlocks, the system needs to maintain the wait-for graph and periodically invoke an algorithm that searches for a cycle in the graph. An algorithm to detect a cycle in a graph requires an order of n2 operations, where n is the number of vertices in the graph.

##Recovery from Deadlock

###Process Termination
Two ways to eliminate deadlocks by aborting a process:

* Abort all deadlocked processes.
* Abort one process at a time until the deadlock cycle is eliminated

###Resource Preemption

To eliminate deadlocks using resource preemption, we successively preempt some resources from processes and give these resources to other processes until the deadlock cycle is broken.

3 issues need to be addressed:

1. Selecting a victim: which resources and which processes are to be prompted?
2. Rollback: roll back the preempted process and resource to some safe state and restart it from that state.
3. Starvation: avoid starvation.















