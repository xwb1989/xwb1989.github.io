---
layout: post
title: CSC Notes Chapter 3 Processes
category: booknote
---

#CSC Notes Chapter 3 Processes

>##Chapter Objectives
* To introduce the notion of a process - a program in execution that forms the basis of a lll computation.
* To describe the various feature of processes, including scheduling, creation and termination, and communication.
* To describe communication in client-server systems.

##Process Concept

__A process is a program in execution.__

###The process
A process (may) includes:

* text section: program code
* program counter: represents the current activity of the process
* stack: contains temporary data
* data section: contains global variables
* (maybe) heap: a dynamically allocated memory during process run time.

>A program by itself is not a process; a program is a _passive_ entity, such as a file containing a list of instructions stored on disk(often called an executable file), whereas a process is an active entity, with a _program counter_ specifying the next instruction to execute and a set of associated resources. __A program becomes a process when an executable file is loaded into memory.__

###Process State
Each process may be in one of the following states(maybe not exactly with the same name):

* New. The process is being created.
* Running. Instructions are being executed.
* Waiting. The process is waiting for some event to occur.
* Ready. The process is waiting to be assigned to a processor.
* Terminated.. The process has finished execution.

###Process Control Block

Each process is represented by a __process control block(PCB)__. A PCB contains:

* Process state.
* Program counter: indicates the address of this process's next will-be-executed instruction.
* CPU registers: include
    * accumulators
    * index registers
    * stack points 
    * general-purpose registers
    * plus any condition-code information. 
    
    Along with the program counter, this state information must be saved when an interrupt occurs, too allow the process to resume correctly afterward.
    
* CPU-scheduling information.
* Memory-management information.
* Accounting information.
* I/O status information.

###Threads

##Process Scheduling

###Scheduling Queues

* Job queue  
    As processes enter the system, they are put into a job queue that consists of all processes in the system

* Ready queue  
    Processes are residing in main memory and are ready and waiting to execute are kept on a list called the ready queue. This queue is generally stored as a __linked list__.
    
* Device queue  
    Processes wait in device queue because device may be busy

####Dispatch
> A new process is initially put in the ready queue. It waits there until it is selected for execution, or is dispatched. 

Once the process is allocated the CPU and is executing, one of several events could occur:

* The process could issue an I/O request and then be placed in an I/O queue.* The process could create a new subprocess and wait for the subprocess’stermination.* The process could be removed forcibly from the CPU, as a result of an interrupt, and be put back in the ready queue.

###Schedulers

* Job scheduler: load processes from disk to memory. This scheduler controls the __degree of multiprogramming__(the number of processes in memory).
* CPU scheduler: select processes to execute.
* Medium-term scheduler

###Context Switch
>Switching the CPU to another process requires performing a state save of the current process and a state restore of a different process. This task is known as a context switch.

##Operations on Processes

###Process Creation
* parent process: creating process  
    might need to partition its resource among children processes. When a child process is created, 2 possibilities exist in terms of execution:
    * The parent continues to execute concurrently
    * The parent waits until some or all of its children have terminated.
* children process: created process  
    children processes might use resource directly from the system or from its parent process. There are also two possibilities in terms of the address space of the new process:
    * The child process is a duplicate of the parent process (it has the same program and data as the parent).
    * The child process has a new program loaded into it.
* tree of processes
* pid: process identifier

###Process Termination

##Interprocess Communication

Reasons for process cooperation:

* Information sharing
* Computation speedup
* Modularity
* Convenience

###Shared-Memory Systems
>Normally the operating system tries to prevent one process from accessing another process’s memory. Shared memory requires that two or more processes agree to remove this restriction.

* request two or more processes to remove the restriction
* prevent writing to the same location simultaneously

####Use buffer to share memory
* Producer fills the buffer
* Consumer empties it

Two kinds of buffer

* unbounded buffer
    * consumers may need to wait for new items, but producer don't need to wait for room.
* bounded buffer
    * producer need to wait when buffer is full; consumer need to wait when buffer is empty.
    
bounded buffer can be implemented by a queue.

###Message-Passing Systems
####Naming

* Direct communication. Communicate directly between processes
    * `send(P, message)` --- Send a message to process P
    * `receive(id, message)` --- Receive a message from any process; the `id` indicates the process with which the communication is taking place.  
    
    The information is `hard-coding`.
    
* Indirect communication. Via mail ports.
    * `send(A, message)` --- Send a message to mailbox A
    * `receive(A, message)` --- Receive a message from mailbox A 
     
    Such scheme has such properties:
    * A link is established between a pair of processes only if both members of the pair have a shared mailbox.
    * A link may be associated with more than two processes.
    * Between each pair of communicating processes, there may be a number of different links, with each link corresponding to one mailbox.
    
    There 2 kinds of mailbox:
    
    * owned by OS
    * owned by processes

####Synchronization
> Communication between processes takes place through calls to `send()` and `receive()` primitives. There are different design options for implementing each primitive. Message passing may be either __blocking__ or __nonblocking__— also known as __synchronous__ and __asynchronous__.

* Blocking send. The sending process is blocked until the message is received by the receiving process or by the mailbox
* Nonblocking send.
* Blocking receive. The receiver blocks until a message is available.
* Nonblocking receive.

####Buffering
>Whether communication is direct or indirect, messages exchanged by communicating processes reside in a temporary queue. Basically, such queues can be implemented in three ways:

* Zero capacity. 
* Bounded capacity. 
* Unbounded capacity.

##Examples of IPC Systems
>IPC: inter-process communication

###An Example: Mach

All calls are based on message -- even system calls.

When a task is created, two special mailboxes are also created:

* Kernel mailbox
* Notify mailbox

3 systems calls are need for message transfer:

* `msg_send()`
* `msg_receive()`
* `msg_rpc()`: executes remote procedure calls(RPCs), sending a message and waiting for exactly one return message from the sender. 

When the mailbox is full, there are 4 strategy:

* Wait indefinitely
* Wait at most n milliseconds
* Do not wait but return immediately
* Cache the message in the OS

###An Example: Windows XP

##Communication in Client-Server Systems
###Sockets
>A socket is defined as an endpoint for communication. A socket is identified by an IP address concatenated with a port number.

Initialize a socket: assign a port with a number greater than 1024. All connections must be unique.

###Remote Procedure Calls

>In contrast to the IPC facility, the messages exchanged in RPC communication are well structured and are thus no longer just packets of data. 

* port
* stub
* transmit
* response

2 important concerns:

* differences in data representation: client converts(marshals) machine-dependent data into external data representation(XDR); server unmarshalls and converted it to the machine-dependent data.
* make sure the messages are acted on _exactly once_ or _at least once_. The latter is more frequently used but more difficult to implement.

###Remote Method Invocation






































