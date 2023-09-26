## Introduction

One of the most basic operating system abstractions is a process. A program can be thought of as a set of instructions waiting to be run. Once the OS runs the program, it becomes a process.
In running your computer, it may seem like it is running a process for each application at the same time. You do not have to worry about how many processes the computer can handle; everything just works as expected.
The operating system appears to be using virtualization to create several virtual CPUs. However, your computer only has one CPU which runs a single process at a time.
A modern CPU has time-sharing, which permits you to run your application concurrently, though you may see a decline in performance.
TIP: Time (and Space) Sharing
Time-sharing allows an operating system to share a resource. Different items use the resource for a bit, and then let another item use the resource etc.
Space sharing is similar to time-sharing. The space of a resource is divided up among those who wish to use it. The most common example is disk space. The OS assigns a block to a file, and it does not usually assign the same block until the original file is deleted.

CPU virtualization requires low-level hardware and high-level intelligence to be done properly.
Mechanisms are low-level protocols or techniques that complete the required tasks.
Policies are algorithms that decide how to handle the numerous requests and limited resources.
For example, the scheduling policy will often decide which program to run and how to run them.

## What is a Process?
A *process* is another name for a running application. Processes are described by listing the components it works with while being executed.
To understand a process, we must understand its machine state: what a software can read or update.

### Which parts of a machine are required for running a program?
- Memory* encompasses the data and instructions read and written by the executing program. The *address space* is the memory that the process can access.

- *Registers* are another part of a process’s machine state. They fetch, decode, and execute instructions.
Some registers of note are:
- A *Program Counter* contains the memory address of the next instruction.
- A *stack pointer* and a *frame pointer* are used to manage the stack in relation to parameters, variables, and return addresses.
*Persistent storage devices* are used to store information such as all of the open files for a process.

## Process Creation
Now that we have a general idea about what a process is, let’s talk about how the operating system starts a program and creates a process.
Computer store programs on a disk in a format that can be executed. Before the OS can run the program, it has to do a few things first.
1. Read the bytes of the program.
2. Load these bytes into memory, which is the process’s address space.
3. Set aside memory for the run-time stack of the program.
4. Get ready for any I/O requests.
Stack
Programs written in C store local variables, function arguments, and return addresses on the stack.
The OS will also initialize the stack with the argc and argv arrays from the main method.
The OS uses the heap to set aside memory for the program. C programs, dynamically store data. Programs can request more memory for the heap by using the malloc() API.
Once all of this is done, the OS can finally initiate the program at its entry point, the main() function. Control of the CPU is passed to the new process and execution begins!

## Process States
Once a process is created, it can be in one of three different states:
Running: A process is executing instructions on the CPU.
Ready: A process is ready to run, but it is not yet being executed on the CPU.
Blocked: A running process is stopped until another event happens.
Processes can be shifted from ready to running at will. When a process moves from ready to running, it is said to be scheduled. A descheduled process moves from running to ready.
I/O operations block a process until the reading or writing to disk is complete. The process then returns to the ready state or may be run if need be.
The tables below illustrate how two different processes move between the different stats as each one shares time on the CPU. The first example does not have any blocking.
Time	Process0	Process1	Notes
1	Ready	Running	
2	Ready	Running	
3	Ready	Running	
4	Ready	Running	Process1 finishes
5	Running	-	
6	Running	-	
7	Running	-	
8	Running	-	Process0 finishes
This next example illustrates how I/O operations would block a process.
Process1 initiates an I/O and becomes blocked while waiting for it to complete. The OS see Process1 is idle and launches Process0. The I/O completes, returning Process1 to ready. Process0 finishes, and Process 1 runs and finishes.
Time	Process0	Process1	Notes
1	Ready	Running	
2	Ready	Running	
3	Ready	Running	Process1 initiates I/O
4	Running	Blocked	Process1 blocked, Process0 runs
5	Running	Blocked	
6	Running	Blocked	
7	Running	Ready	I/O done
8	Running	Ready	Process0 finishes
9	-	Running	
10	-	Running	Process1 finishes
The OS is making several decisions:
The system decided to execute Process0 while Process1 performed an I/O to improve resource usage.
The system decided to keep running Process0 after Process1 finished its I/O.

## Data Structures
An operating system is similar to any other computer program. It uses data structures to keep track of important information. A process list is used to store the states of all of the processes.
Some the information the process list monitors includes:
- The processes that are ready
- The processes that are running
- The processes that are blocked
- When I/O events finish.
The code sample on the left represents the kind of information an OS must track about each process in the xv6 kernel. You will see similar process structures in other operating systems like Linux, Mac OS X, and Windows.
When the OS stops a process, its registers are stored in the register context. If the OS wants to continue the process, it uses the register context to restore the information back into physical registers.
The code sample also shows additional states that a process can have.
A process can have an initial state when it is built. The operating system can terminate a process, but not clean it up. The end state of a process allows other processes to verify the OS has properly terminated the process. UNIX-based systems usually use 0 to represent successful completion of a task and non-zero numbers to represent an unsuccessful completion.
A parent process will make a wait() to wait for the child process to complete. It then tells the OS to clear away any data structures related to the now-extinct process.
Important Data Structures
The process list (sometimes referred to as the task list) has already been discussed. It’s one of the simplest data structures, but it is not required. An OS can run applications without this list.
The Process Control Block (PCB) is another data structure used to hold information about each process. This data structure is also known as the process descriptor.

## Summary
A process is an OS abstraction for a running program. It can be described by:
The state
Which includes Running, Ready, and Blocked
The contents of its address space
The contents of its CPU registers
Which includes the program counter and the stack pointer
The I/O information
Which includes files that are open and files that can be read or written
The process list includes all of the processes in the system. The process control block (PCB), is a data structure that stores information about a particular process.