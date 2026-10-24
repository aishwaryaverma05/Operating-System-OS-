# Phase 2: Process Management

## 1. Program vs Process

### Program

A **program** is a set of instructions stored on a computer, usually in a file.

### Process

A **process** is a program that is currently executing, along with its execution state and allocated resources.

### Example

Suppose you have Google Chrome installed on your laptop.

- Chrome installed on disk = Program
- Chrome running on your laptop = Process
- Multiple Chrome processes may exist for different browser tasks.

### Comparison

| Program | Process |
|---|---|
| Passive set of instructions | Program in execution |
| Stored as a file on disk | Has execution state and allocated resources |
| Does not need an active execution state | Has a process state, such as Ready or Running |
| One program can be used to create multiple processes | Each process has its own identity and execution context |

**Interview Question: Can multiple processes run the same program?**

Yes. For example, multiple instances of a text editor can run as separate processes.

---

## 2. Process States

A process changes states during its lifetime depending on whether it is executing, waiting for the CPU or waiting for an event.

### Main Process States

| State | Meaning |
|---|---|
| New | The process is being created. |
| Ready | The process is ready to execute but waiting for CPU time. |
| Running | The CPU is executing the process. |
| Waiting/Blocked | The process is waiting for an event, such as I/O completion. |
| Terminated | The process has finished or been stopped. |

### Process State Diagram

```text
              Admitted
      New --------------> Ready
                             |
                             | CPU assigned
                             v
                         Running ---------> Terminated
                             |                 Exit
                             |
                 I/O request or event wait
                             v
                          Waiting
                             |
                             | I/O/event completed
                             v
                           Ready

Running -----> Ready
   Preemption / time slice expires
```

### How Does It Work?

Consider opening a file in an application:

1. The OS creates the process: **New**.
2. It becomes eligible to execute: **Ready**.
3. The scheduler assigns CPU time: **Running**.
4. The process requests data from the disk: **Waiting**.
5. The disk operation completes: **Ready**.
6. The scheduler assigns the CPU again: **Running**.
7. The process finishes: **Terminated**.

### Important Transitions

- **Ready → Running:** The CPU scheduler dispatches the process.
- **Running → Ready:** The process is preempted, for example when its time slice expires.
- **Running → Waiting:** The process waits for an event or I/O operation.
- **Waiting → Ready:** The required event or I/O operation completes.

**Interview Question: Can a Waiting process directly become Running?**

In the basic process-state model, no. It normally becomes Ready first and must then be selected by the scheduler.

---

## 3. Process Control Block (PCB)

### Definition

A **Process Control Block (PCB)** is an OS data structure that stores the information needed to manage a process.

### Why Is It Needed?

The OS must track each process's execution state and resources. It uses the PCB to manage processes and resume their execution after a context switch.

### What Does a PCB Contain?

- **Process ID (PID):** Unique identifier of the process.
- **Process State:** Ready, Running, Waiting, etc.
- **Program Counter:** Address of the next instruction to execute.
- **CPU Registers:** Current register values.
- **Scheduling Information:** Priority and scheduling-related data.
- **Memory Information:** Information about the process's address space.
- **I/O and Resource Information:** Relevant open files and allocated resources.

### Example

Suppose Process A is running and the OS switches the CPU to Process B.

The OS saves Process A's relevant execution information, including its program counter and registers, so Process A can resume later.

**Interview Question: Why is the PCB important during context switching?**

It stores the process's execution information so the OS can pause it and resume it correctly later.

---

## 4. Process Creation and Termination

### Process Creation

A process may be created when:

- A user opens an application.
- A running process creates another process.
- The OS starts a background service.
- A scheduled task needs to execute.

On Unix-like systems, `fork()` can create a child process, while `exec()` replaces a process's program with another program.

### Process Termination

A process may terminate when:

- It completes its task.
- It encounters an unrecoverable error.
- It is terminated by the OS or an authorized process.
- The user closes an application and the application exits.

**Interview Question: Does closing an application always immediately terminate every related process?**

Not necessarily. Background processes, child processes or services may continue running depending on how the application and OS are designed.

---

## 5. Context Switching

### Definition

**Context switching** is the process of saving the execution state of one process or thread and restoring the state of another so the CPU can switch execution.

### Why Is It Needed?

Multiple processes share CPU resources. The OS must switch between them to support multitasking and respond to events.

### How Does It Work?

Suppose Process A is executing and its time slice expires.

1. The OS saves Process A's execution state.
2. The saved state is maintained in its process or thread context.
3. The scheduler selects Process B.
4. The OS restores Process B's execution state.
5. Process B continues executing.

### Diagram

```text
Process A Running
       |
       v
Save A's Context
       |
       v
Select Process B
       |
       v
Restore B's Context
       |
       v
Process B Running
```

### Why Does Context Switching Have a Cost?

Saving and restoring state takes time. During this work, the CPU may not execute useful application instructions.

Frequent context switches can reduce performance because of switching overhead and effects on CPU caches.

**Interview Question: Is context switching useful work?**

It is necessary OS management work, but the switching itself generally does not advance the application tasks.

**Important:** A context switch does not necessarily mean a switch between two different processes. The CPU can also switch between threads.

---

## 6. CPU-Bound vs I/O-Bound Processes

### CPU-Bound Process

A CPU-bound process spends most of its time performing computations.

**Examples:** Large mathematical calculations, video encoding and scientific simulations.

### I/O-Bound Process

An I/O-bound process spends a significant amount of time waiting for input/output operations.

**Examples:** Reading files, waiting for database responses or fetching network data.

### Comparison

| CPU-Bound | I/O-Bound |
|---|---|
| Mostly performs computations | Frequently waits for I/O |
| Needs substantial CPU execution time | Often alternates between short CPU bursts and I/O waits |
| Example: Mathematical simulation | Example: File download |
| Performance often depends on CPU capability | Performance may depend on disk, network or other I/O speed |

### Scenario-Based Question

**Why can multiprogramming improve CPU utilization when a process is waiting for I/O?**

Because the OS can schedule another Ready process while the first process waits for its I/O operation to complete.

---

## 7. Zombie and Orphan Processes

These terms are commonly discussed in Unix-like operating systems.

### Zombie Process

A **zombie process** has finished executing, but its parent has not yet collected its termination status.

The OS retains a small process-table entry until the status is collected.

**Example:** A child process finishes, but its parent does not call an appropriate wait operation.

**Key point:** A zombie is already terminated. It is not actively executing or consuming CPU time.

### Orphan Process

An **orphan process** is a child process whose parent terminates while the child is still running.

On Unix-like systems, the orphan is generally adopted by an appropriate system process or subreaper.

**Example:** A parent process exits while its child continues executing.

### Comparison

| Zombie Process | Orphan Process |
|---|---|
| The process has finished executing. | The process is still executing or otherwise alive. |
| Termination status has not yet been collected. | Its original parent has terminated. |
| Retains a small process-table entry. | Continues with a new parent or designated adopter. |
| Usually resolved when its status is collected. | Does not necessarily require termination. |

**Interview Trap:** Zombie and orphan are not the same. A zombie has finished; an orphan is still alive after its parent exits.

---

## 8. Inter-Process Communication (IPC)

### Definition

**Inter-Process Communication (IPC)** refers to mechanisms that allow processes to exchange data and coordinate their activities.

### Why Is It Needed?

Processes generally have separate address spaces. They need controlled ways to share information.

**Example:** A browser process communicates with another process responsible for a browser task.

### Common IPC Mechanisms

| Mechanism | How It Works | Example |
|---|---|---|
| Pipes | Transfer data through a communication channel | Output of one command becomes input to another |
| Message Queues | Processes exchange messages through a queue | Producer sends messages for a consumer |
| Shared Memory | Processes access a shared memory region | Two processes work with shared data |
| Sockets | Processes communicate through endpoints | Client and server communication |
| Signals | Notify a process that an event occurred | A process receives a termination signal |

### Shared Memory vs Message Passing

| Shared Memory | Message Passing |
|---|---|
| Processes communicate through a shared memory region. | Processes exchange messages through an IPC mechanism. |
| Can be efficient for large amounts of data. | Can be easier to structure as message exchanges. |
| Requires synchronization when data is accessed concurrently. | Synchronization and coordination depend on the messaging mechanism. |

**Interview Question: Why does shared memory need synchronization?**

If two processes access and modify shared data concurrently, they may interfere with each other and produce incorrect results. Synchronization helps coordinate access.

---

## 9. Important Interview Questions

### Q1. What is the difference between a program and a process?

A program is a set of instructions stored on disk. A process is an executing instance of a program with its own execution state and resources.

### Q2. What are the main process states?

New, Ready, Running, Waiting/Blocked and Terminated.

### Q3. What is a PCB?

A Process Control Block stores information the OS needs to manage and resume a process.

### Q4. What is context switching?

It is saving one execution context and restoring another to switch CPU execution.

### Q5. What is the difference between Ready and Waiting?

Ready means the process can execute but is waiting for CPU time. Waiting means it is waiting for an event, such as I/O completion.

### Q6. What happens when a process's time slice expires?

In preemptive time-sharing, the OS can move it from Running to Ready and schedule another process.

### Q7. Can a process have multiple threads?

Yes. A process can contain multiple threads that share its resources, such as its address space and open files, while maintaining their own execution contexts.

### Q8. What is the difference between a zombie and an orphan process?

A zombie has terminated but its status has not been collected. An orphan is a living child process whose original parent has terminated.

### Q9. What is IPC?

IPC allows processes to communicate and coordinate using mechanisms such as pipes, sockets, message queues and shared memory.

### Q10. Why are context switches expensive?

They require saving and restoring execution state and may cause cache-related performance costs.

### Q11. What is the difference between a CPU-bound and an I/O-bound process?

A CPU-bound process spends most of its time computing. An I/O-bound process frequently waits for input/output operations.

### Q12. Does a context switch always require a change from user mode to kernel mode?

No. A context switch requires privileged OS involvement, but a transition into kernel mode can occur without a process switch, and not every kernel-mode transition causes a context switch.
