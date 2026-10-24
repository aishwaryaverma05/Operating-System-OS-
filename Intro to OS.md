# Phase 1: Introduction to Operating Systems

## 1. What is an Operating System?

An **Operating System (OS)** is system software that manages computer hardware and software resources and provides services to applications.

It acts as an interface between the user, applications and computer hardware.

**Examples:** Windows, Linux, macOS, Android and iOS.

### Why is it needed?

Without an OS, applications would need to manage hardware resources directly, making the computer difficult to use and manage.

### How does it work?

When you open a browser:

1. The OS loads the required program into memory.
2. It allocates CPU time to execute instructions.
3. It manages memory and access to files.
4. It coordinates input/output operations, such as network communication.

### Simple Diagram

```text
+---------------------------+
|          User             |
+---------------------------+
|       Applications        |
|    Browser, VS Code       |
+---------------------------+
|    Operating System       |
|  Process, Memory, Files   |
+---------------------------+
|         Hardware          |
|   CPU, RAM, Disk, I/O      |
+---------------------------+
```

**Interview Question: What is an operating system?**

An OS is system software that manages hardware resources and provides an environment for applications to run.0/

---

## 2. Functions of an Operating System

An OS performs the following important functions:

| Function                | Purpose                                    | Example                        |
| ----------------------- | ------------------------------------------ | ------------------------------ |
| Process Management      | Manages running programs                   | Running Chrome and VS Code     |
| Memory Management       | Allocates and frees RAM                    | Giving memory to a browser     |
| File Management         | Organizes files and directories            | Creating and deleting files    |
| Device Management       | Coordinates hardware devices               | Managing a printer             |
| CPU Scheduling          | Decides which process gets CPU time        | Sharing CPU time between apps  |
| Security and Protection | Controls access to resources               | Passwords and file permissions |
| User Interface          | Allows users to interact with the computer | Desktop or command line        |
| Resource Allocation     | Shares resources among programs            | Allocating CPU and memory      |

**Interview Question: What are the main functions of an OS?**

Process management, memory management, file management, device management, CPU scheduling and security.

---

## 3. Types of Operating Systems

### 3.1 Batch Operating System

A batch OS collects jobs and executes them in batches without requiring continuous user interaction.

**Example:** Traditional payroll processing systems.

**Use:** Processing large numbers of similar jobs automatically.

### 3.2 Multiprogramming Operating System

Multiple programs are kept in memory so that the CPU can execute another program when the current one is waiting for I/O.

**Example:** When one program waits for a disk operation, the CPU executes another ready program.

**Main goal:** Improve CPU utilization.

### 3.3 Multitasking Operating System

A multitasking OS allows multiple tasks to make progress by sharing CPU time.

**Example:** Listening to music while browsing the internet.

On a single CPU core, tasks typically take turns. On multiple cores, some tasks can also execute simultaneously.

### 3.4 Multiprocessing Operating System

A multiprocessing OS uses two or more CPU cores or processors to execute processes.

**Example:** Running calculations on multiple CPU cores simultaneously.

**Main goal:** Support parallel execution and improve performance.

### 3.5 Time-Sharing Operating System

A time-sharing OS gives processes or users small time slices of CPU time, providing interactive access to computing resources.

**Example:** Multiple users interacting with a shared computer system.

**Main goal:** Provide responsive, fair access to resources.

### 3.6 Real-Time Operating System (RTOS)

An RTOS is designed to respond to events within specified timing constraints.

- **Hard real-time:** Missing a deadline is considered a system failure.
- **Soft real-time:** Missing a deadline occasionally is undesirable but may be tolerated.

**Examples:** Flight-control systems for hard real-time requirements; multimedia streaming can have soft real-time requirements.

### 3.7 Distributed Operating System

A distributed OS coordinates resources across multiple connected computers and may present them as a unified system.

**Use:** Coordinating computation and resource sharing across machines.

**Important:** A distributed system is not necessarily managed by a single distributed OS.

### Quick Comparison

| Type             | Main Goal                               |
| ---------------- | --------------------------------------- |
| Batch            | Execute jobs in batches                 |
| Multiprogramming | Keep the CPU busy                       |
| Multitasking     | Run multiple tasks interactively        |
| Multiprocessing  | Use multiple processors or cores        |
| Time-sharing     | Share CPU time among users or processes |
| Real-time        | Meet timing constraints                 |
| Distributed      | Coordinate resources across computers   |

**Common Interview Trap:** Multitasking and multiprocessing are not the same. Multitasking concerns multiple tasks making progress; multiprocessing uses multiple processors or cores.

---

## 4. Kernel and Its Functions

### Definition

The **kernel** is the core part of an operating system. It manages hardware resources and provides essential services to programs.

### Why is it needed?

Applications should not be allowed to access every hardware resource freely. The kernel provides controlled access and manages shared resources.

### Main Functions

- Process and thread management
- Memory management
- Hardware and device management
- Interrupt handling
- Resource allocation
- Enforcing access and protection rules

### Types of Kernels

| Monolithic Kernel                                          | Microkernel                                                                             |
| ---------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Many OS services run in kernel space.                      | Only essential services run in kernel space.                                            |
| Can provide efficient communication between services.      | Often improves isolation between services.                                              |
| A fault in a kernel component can affect the whole system. | Isolated services may fail without crashing the entire kernel, depending on the design. |
| Example: Linux kernel.                                     | Example: MINIX 3.                                                                       |

**Interview Question: Is the kernel the same as the operating system?**

No. The kernel is the core of the OS. The complete OS also includes other system software and utilities.

---

## 5. System Calls

### Definition

A **system call** is a controlled way for a user-space program to request a service from the operating system kernel.

### Why is it needed?

Applications cannot directly perform every privileged operation. System calls allow them to request services such as file access, process creation and communication with devices.

### How does it work?

1. An application requests an OS service.
2. The system-call mechanism transfers control to the kernel.
3. The kernel checks permissions and performs the requested operation.
4. The result is returned to the application.

### Types of System Calls

| Category                | Purpose                                           | Examples                                            |
| ----------------------- | ------------------------------------------------- | --------------------------------------------------- |
| Process Control         | Create or manage processes                        | `fork()`, `execve()`, `wait()` on Unix-like systems |
| File Management         | Open, read, write or close files                  | `open()`, `read()`, `write()`, `close()`            |
| Device Management       | Access or control devices                         | Device-specific operations through OS interfaces    |
| Information Maintenance | Get system or process information                 | `getpid()` on Unix-like systems                     |
| Communication           | Exchange information between processes            | `pipe()`, socket operations                         |
| Protection              | Manage permissions or identity-related operations | `chmod()`, `setuid()` on Unix-like systems          |

*Note: These are Unix-like system-call examples. Exact interfaces vary by operating system.*

### System Call vs Function Call

| System Call                                                 | Function Call                                      |
| ----------------------------------------------------------- | -------------------------------------------------- |
| Requests a service from the OS kernel.                      | Calls a function in a program or library.          |
| May require a switch to kernel mode.                        | A normal function call usually stays in user mode. |
| Examples: `read()`, `write()` at the system-call interface. | Examples: `sqrt()`, a user-defined function.       |

**Important:** A library function may internally use a system call, but not every function call is a system call.

---

## 6. User Mode vs Kernel Mode

Modern operating systems use privilege levels to protect the system.

### User Mode

Applications normally run in user mode, with restricted access to hardware and privileged instructions.

**Example:** A browser processing a webpage.

### Kernel Mode

The kernel runs with privileges required to manage hardware and perform protected operations.

**Example:** The kernel handling a file-read request or a hardware interrupt.

### Comparison

| User Mode                                                  | Kernel Mode                               |
| ---------------------------------------------------------- | ----------------------------------------- |
| Restricted privileges                                      | High privileges                           |
| Used by most applications                                  | Used by the kernel and privileged code    |
| Direct execution of privileged instructions is not allowed | Privileged instructions can be executed   |
| Faults are generally limited to the affected process       | Kernel faults can affect the whole system |

### Example: Reading a File

1. A program requests data from a file.
2. It invokes a system-call interface.
3. Execution enters the kernel through a controlled transition.
4. The kernel checks access and performs the required operation.
5. The result is returned to the program.

**Interview Question: Why can't applications directly execute privileged instructions?**

To protect the OS, hardware and other programs from unauthorized or unsafe operations.

---

## 7. OS Architecture: User Space and Kernel Space

### User Space

The memory area and execution environment in which ordinary applications run with restricted privileges.

**Examples:** Browsers, text editors and many application processes.

### Kernel Space

The protected environment used by the kernel to execute privileged OS code and manage system resources.

### Comparison

| User Space                                         | Kernel Space                               |
| -------------------------------------------------- | ------------------------------------------ |
| Runs ordinary applications                         | Runs core kernel code                      |
| Has restricted privileges                          | Has privileged access                      |
| Applications use OS interfaces to request services | Kernel provides and manages those services |

**Remember:** User space vs kernel space describes execution and memory protection boundaries. User mode vs kernel mode describes CPU privilege levels. They are closely related, but the terms are not identical.

---

## 8. Important Interview Questions

### Q1. What is the main purpose of an OS?

To manage computer resources and provide services that allow applications to run.

### Q2. What is the difference between the OS and the kernel?

The kernel is the core component of the OS. The OS also includes other system software and utilities.

### Q3. What happens when you open an application?

The OS loads the program as needed, allocates resources, creates or manages its process, and schedules its execution.

### Q4. What is the difference between multiprogramming and multitasking?

Multiprogramming keeps multiple programs in memory to improve CPU utilization. Multitasking enables multiple tasks to make progress by sharing CPU time, often for interactive use.

### Q5. What is the difference between multitasking and multiprocessing?

Multitasking allows multiple tasks to make progress. Multiprocessing uses multiple processors or CPU cores to execute work.

### Q6. What is a system call?

A system call lets an application request a service from the OS kernel.

### Q7. Why do we need user mode and kernel mode?

They protect hardware and system resources by restricting what ordinary applications can do.

### Q8. What is a real-time OS?

An OS designed to meet specified timing constraints when responding to events.

### Q9. What is context switching?

Context switching is saving the execution state of one process or thread and restoring the state of another so the CPU can switch execution.

### Q10. Does every system call create a new process?

No. System calls perform many kinds of operations, including file access, communication and process management.

### Q11. Is Linux an operating system or a kernel?

Strictly speaking, Linux is the kernel. A Linux-based operating system distribution combines it with system software and utilities.

### Q12. Can two applications run at exactly the same time?

On separate CPU cores, they can execute simultaneously. On a single core, the OS typically switches between tasks to give the appearance of concurrent execution.
