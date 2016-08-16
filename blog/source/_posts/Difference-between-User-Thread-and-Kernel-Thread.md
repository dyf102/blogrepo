title: Difference between User Thread and Kernel Thread
date: 2016-08-04 12:21:17
tags: OS
---
Process is an executing instance of computer program. It includes following sections:
1.	Text section: code and logic of program
2. Data section: global and static variable
3. Heap: Dynamic allocated memory 
4. Stack: local variable and function  return value

###Memory Layout
Process = [code + data + Heap + stack + PC + PCB + CPU registers]
Thread = [Parant Code, Data, Heap + stack, PC, CPU registers] 

Thread is much more loghtweight than process.

###User Level Thread
The user level thread is managed by a user level library however it still requires the kernel system Call to execute the program. But the kernel has little knowledge about the user level thread. 
Advantage:
1. Fast to create, switching between threads
2. Good for no-blocking task, otherwise the whole process will be blocked.

###Kernel Level Thread
The kernel Level Thread is managed by the kernel, which implemented in kernal code. Therefore it is not portable. They can also utilize the multi-core system by putting different thread to different processors.
1. Slower to create because it required more management overhead.
2. Not portable
3. Good for blocking task because if one thread is blocking, it dose not case the entire the process blocking

### Thread model

Many-to-one: Many user level threads are managed by a library in user space but the kernel only aware of a single thread. Therefore if the current thread blocks then other threads will block as well. The kernel cannot schedule other thread to other cpu core. 

One-to-one: Each user level threads are managed by a kernel thread. Since the kernel awares of all thread, no thread will affact other thread. But the management overhead is significant as well.

Many-to-many: It multiplexing many user threads to many kernel threads to avoid the downside of other two models.

{Reference}