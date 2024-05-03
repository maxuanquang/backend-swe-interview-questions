# Topic: OPERATING SYSTEM

[Preference](https://tildesites.bowdoin.edu/~sbarker/teaching/courses/os/14spring/lectures.html)

# 1. Process and Thread

## What is `process`?

- Process is like a program in execution.
- The OS has a data structure called Process Controll Block (PCB) to store information and manage process. A PCB contains: Process ID, Process state, Program counters, CPU Registers, List of open files, etc.
- A process in memory includes:
  - Text segment contains the instructions of the program.
  - Stack segment stores temporary data used during program execution.
  - Data contains global variables.
  - Heap is the memory that is dynamically allocated during run time.

## What is `thread`?

- Thread is an execution stream within a process. It comprises a thread ID, program counter, register set, and stack. It shares with other threads belonging to the same process its code section, data section, and other operating-system resources, such as open ﬁles and signals.

## What are the differences between `process` and `thread`?

| Process     | Thread        |
| -----------   | -----------   |
| <div style="width:290px">A process is like a program in execution. Each process has its own memory address space and is isolated.</div>     |      <div style="width:290px">Thread is an execution stream within a process. Multiple threads can exist within a process, sharing the same memory space, can easily communicate with each other.</div>    |

## Why do they say that Thread is a lightweight process?

Thread is a lightweight process because it does not cost much CPU to switch between threads as between processes.

## How CPU switch (context switch) between processes/threads? How data is to ensure safety? (in case single CPU core and multiple CPU cores)

- When process context switching happens, the state of the current process will be stored in Process Control Block (PCB), so this process can be resumed later. It includes the value of the CPU registers, the process state, and memory-management information. Data pages in memory of the current process are typically kept safe because the information about these pages have been saved in PCB. However, depending on the memory-management method of OS and the availability of memory, it can lead to the replacement of these data pages (data pages replacement have nothing to do with context switching).

- When thread context switching happens, the state of the current thread will be stored in Thread Control Block (TCB), so the thread can be resumed later. It includes the value of the CPU registers, the thread state, a program counter, a stack pointer, and a pointer to the PCB of the process to which the thread belongs. There is one major difference in state becomes running.
- At the running state, the processor executes the process instructions. If the process need something like I/O, it moves to the Waiting state. If process completes its execution, it moves to Terminated state. If the process's time slice e thread context switching compared to processes: the address space remains the same (there is no page replacement).

## What is multi-process and multi-thread? When should we use which?

Multi-processing is when more than 2 processes are running simultaneously on a computer. This is probable in a multi-processor computer, where each processor handles a process.

Multi-threading is when more than 2 threads created by the same process are running simultaneously.

## Process has how many states? How does it change between each state?

Basically, a process has 5 states: new, ready, running, wait, and terminate.

- New is the state of the process when it is created (it is still in the process of setting up). After the process is intialized, it transitions to Ready state.
- At the ready state, the process waits to be assigned to a processor. When the process is assigned to a processor, its state becomes running.
- At the running state, the processor executes the process instructions. If the process need something like I/O, it moves to the Waiting state. If process completes its execution, it moves to Terminated state. If the process's time slice expires, or a higher priority process comes, it moves to waiting state.
- At waiting state, the state is waiting for some external event (like I/O operation done) occurs, it then moves back to Ready state.
- At terminated state, the process has finished its execution and is waiting for the OS to reclaim resoures. After the OS done reclaiming, it is removed from main memoey.

## Scheduling algorithm

There are many types of scheduling algorithms like:

- First come first serve: Processes are executed in the order they arrive in the ready queue.
- Shortest job first: Executes the process with shortest estimated run time first.
- Round-robin: Each process gets a small fixed amount of CPU time. After this time is up, the process is preempted and added to the end of ready queue.

## What will happen if a process is waiting? Or a thread is sleeping?

When a process is waiting, OS can run another process.

When a thread is sleeping, other threads might be running.

## How CPU detects that a thread is sleeping? Or detect when it wants to run?

OS has an OS scheduler that keeps track of the list of processes and their status. When the thread goes to sleep, it tells the scheduler that it gives up its time slide, and it shouldn't be wake up until a certain time has elapsed. The next time the scheduler looks at this process, if the time has elapsed, it will change this thread status as ready to run.

## What is thread-pool? How to use it? Describe how to create a thread-pool in your programming language

A thread pool is a design pattern used in concurrent programming to efficiently manage multiple threads used by a program (it's also called as worker model). Instead of creating and destroying threads each time a task needs to be executed, a thread pool maintains a pool of pre-instantiated, idle threads that can be reused for multiple tasks.

## Can 2 different processes access or change data of each other address space? (this question may make you confuse with your knowledge about virtual memory)

Cause memory can be used by a process at this time and later by another process, yes an address space in general does not belongs to any process and can be changed by many processes, but in a certain time, an address space belongs to a process only and can only be modified by this process.

## Can 2 processes use the same library (for eg: libc is required to every process)? How?

It is possible if the library is a shared library, not a static one. Each process has its own virtual memory address that refers to the same physical memory address that stores the shared library.

## How does debugger work? How it can attach to a running process and change data of that process?

To be defined.

## How 2 processes can communicate with each other? (There are a lot of ways but focus on the OS's way)

It can use a shared resource or pass messages to other processes.

## What is child-process? How to create a child-process?

Child process is a process created by another process. The parent process can call `fork()` to create a new child process.

## What data a child-process have when we create it?

The parent defines (or donates) resources and privileges to children. Children can receive all parent's resources or subset of parent's resources. The resources can be inherited includes: environment settings, process directory, open files, memory space, etc. But the child process must have different process id.

## Can it read/write data on it's parent process?

No, it cannot. If it wants to communicate with its parents, it has to use a separate shared memory segment or use a socket or message queue.

## What is copy on write (COW), Dirty COW? **(this concept is important to understand OS)**

On process creation, when parent process calls `fork()`, the memory space (code segment, data segment, stack segment, heap segment) will be shared between parent process and child process. After that, child needs to operate independently, so COW will be using on data segment, stack segment (and maybe code segment if the child runs new program using `exec()`) .

Copy on write is when multiple callers ask for a copy of a resource, OS can return the pointer to the old resource and only make the private copy for a caller when necessary, like when the caller wants to modify this resource. The advantage is if the caller makes no modification, no private copy needs to be created.

## What will happen when child-process changes a variable of parent process?

Child process cannot change variables of its parent.

## If file descriptor also be inherited by the child process. What if 2 processes can handle a same file descriptor or even a same socket?

2 processes can handle the same file descriptor/socket. To prevent other processes does further things with the socket that the current process wants to close, we can use shutdown function instead of close function to stop other processes from using this socket.

# 2. Concurrency and Parrallel

## Concurrency vs Parallels? (in case single CPU core and multiple CPU cores)

- Concurrency: Many tasks interleave on a CPU core.
- Parallelism: Many tasks run on different CPU cores.

## What is critical zone?

Critical zone or critical region is the segment of code where different processes can access but only one process can execute at a time.

## What is race condition and how to handle this case?

Race condition is when two threads access a variable at the same time. It can lead to data inconsistency if the operations are executed in a certain order. To prevent this we can take advantage of some locking mechanisms.

## What is locking mechanism? mutex? semaphore? spinlock? read lock vs write lock?

- Atomic process: Process that is not interfered by interrupts.
- Synchronization: Use of atomic operations to ensure cooperation between threads.
- Mutual Exclusion (mutex): Ensures that only one thread does an activity at a time and excludes other threads from doing at that time.
- Lock: A mechanism that a process use to prevent other process from doing something:
  - Lock before entering a critical section (or accessing shared data).
  - Unlock when leaving a critical section.
  - Wait if locked.
- Semaphore is a generalized lock, to control the number of available resources.
  - Binary Semaphore is same as lock.
  - Counting Semaphore is used when multiple units of a resource are available. The initial value of counting semaphore is usually the number of resources. A process can acquire access as long as semphore > 0 (at least one unit of the resource is available).
- Spinlock is a mechanism in which a process keeps asking (busy-wait) for the availability of the resource via polling (instead of sleeping while waiting for the availability of resource).

## What is deadlock and how to avoid deadlock?

Deadlock is when there is a circle where some processes lock something and wait for something that other processes have already locked.

Deadlock happens when all of these conditions happen:

- Mutual exclusion: It means processes want to access the same resource.
- Hold and wait: When processes need more than one resource and it already has some, it will hold them and wait for the rest.
- No preemption: A process can not take away resource that is being held by other processes.
- Circular wait.

This can be avoided by making processes release their locked variable when they cannot get the other necessary resource, or granting some preemption permission for some processes.

# 3. Memory Management

## How does memory is managed in the OS?

To be defined.

## What is virtual memory? Why do we need it? How does it work?

Virtual memory is a memory management technique used by operating systems to provide an "illusion" to processes that each one has its own dedicated piece of the computer's physical memory, known as RAM (Random Access Memory). In reality, the physical memory is shared among multiple processes, and each process is given the illusion that it has a contiguous block of addressable memory, known as virtual memory.

#### Why do we need it?

- Isolation: Each process has its own memory space, preventing other process to access.
- Efficient Use of Physical Memory
- Ease of Programming

#### How does it work?

Read more [here](https://tildesites.bowdoin.edu/~sbarker/teaching/courses/os/14spring/lectures.html) at Leture 11, 12, 13.

## How large virtual memory is?

The size of virtual memory is unlimited. Actually, it is limited by the disk space but the disk space is quite large so we can consider it unlimited.

## What is paging?

Paging is a memory management scheme used by computer operating systems to allow processes to use memory addresses that are independent of the actual physical layout of memory. In a paging system, both physical memory and virtual memory are divided into fixed-size blocks called pages. The primary goal of paging is to provide a more flexible and efficient way to manage memory compared to contiguous memory allocation.

## Can 2 processes map to the same physical address? How and in which case?

Yes. Each process has its virtual memory address and can be mapped to the same physical memory address when it is processed by CPU. The condition is they need to take turns to be processed.

## The structure of a process in memory?

- Stack: Contains temporary data (such as function parameters, return address, local variables).
- Heap: Memory that is dynamically allocated during process run time.
- Data: Contains global variables.
- Text: Program code.

## What will happen with memory when you open an application?

The application will be loaded to RAM when we open an application.

## What will happen when you call another function (with parameters) or return from a function?

When we call another function, local variables of the new function will be put to stack, and the stack register will be moved to the appropriate position. If the function takes parameters, it will create a duplicate version of those parameters (If the parameter is a pointer, it still creates a new pointer that points to the same address).

When a function returns, local variables will be pop out of stack and the stack pointers will also be moved to the appropriate position.

## What will happen with stack? (why we do not use heap here?)?

As above.

## What will happen with registers?

As above.

## What causes stack overflow?

Stack overflow happens when the stack memory runs out. The most common cause of stack overflow is infinite recursion.

## What is dynamic allocating? How does it work?

Dynamic allocation is the act of asking for a specific segment of memory from the OS.

## How does deallocation work?

Deallocation announces for the OS that this segment of memory is free now and can be used by other processes.

## What happens when your computer is full of memory?

To be defined. (It will throw error?)

## Why you do not need to "deallocate" local variable?

Cause local variable is allocated in stack and will be destroyed automatically after the function returns.

## How does Garbage Collection work? When it will be triggered?

Garbage Collector (GC) will detect which object in heap memory has no reference. It means this object cannot be used anymore and it's safe to sweep this object away. GC will run periodically for languages that have GC. Besides, languages like Java or Python have a mechanism to invoke GC manually.

## What is a pointer? What difference between pass by value and pass by reference?

Pointer is the address of a variable. Pass by value means passing only the value of the variable, while pass by reference means passing the address of this variable in the memory.

## Where global variable will be saved?

Process memory is divided into four sections:

- Text section: when the code is stored.
- Data section: when static and global variables are stored.
- Stack section: where local variables are stored.
- Heap section: where dynamically allocated variables are stored.

# 4. File System

## Why in Linux everything is "file"?

It will create a uniform interface for operations on Linux systems. To be more exact, everything in Linux is a stream of bytes that is represented as a file descriptor.

## How does mouse/keyboard/monitor... communicate with your computer?

It streams a stream of bytes into our computer.

## What is file descriptor?

File descriptor is a unique identifier for a file or input/output resources.

## What is buffer? Why do we need buffer?

Buffer is a segment of memory that is reserved to store necessary data being processed. Buffer helps reduce the processing time.

## What will happen if 2 processes read/write to the same file?

If 2 processes read/write to the same file, it means they use the same file descriptor to access this file, also the file offset. So the changes that one process does with the shared file are visible to the other process. ([More](https://walkerlala.github.io/archive/what-if-write-to-the-same-file.html))

# 5. System Call

## What is system call (syscal)?

A syscall is a programmatic way that allows a computer program to request a service from the kernel of the operating system, on which the program will be executed.

## How to do a syscal?

Some libraries provide functions that allow us to interact with the kernel.

## What happens with CPU when we execute a syscal?

CPU will execute the system call handler, which will perform some action to fulfill the syscall. ([More](https://www.ibm.com/docs/en/aix/7.2?topic=calls-understanding-system-call-execution))

## What is user space and kernel space?

Memory is divided into kernel space and user space:

- Kernel space is where the code of the kernel is stored, and executed.
- User space is where the code of everything other than the kernel is stored and executed.

# 7. Caching

## What is in-memory cache? (memcached/redis)

In-memory cache is the data storage layer between the application and the database. It stores data in memory which has a much fast data access speed so it is used to speed up the application.

## LRU? implement LRU in your program language! (How about multi-thread?)

Least recently used is a strategy to invalidate cache. As the name already said, it will remove the least recently used data when the cache is full. We can implement LRU using a combination of hashmap and a doubly linked list.

## How to migrate Cache stampede?

Cache stampede happens when a lot of data need to be queried at the same time but most of them haven't been cached yet, so the number of queries that need to be executed on database is huge. We can mitigate cache stampede by warming cache, cache locking, or predicting when the cache is expired so we can update cache.

## Quicksort vs Merge sort. Which is faster? Why? How they use these 2 sorting algorithms real life?

To be defined.
