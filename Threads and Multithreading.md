# Phase 3: Threads and Multithreading

## 1. What Is a Thread?

A **thread** is the smallest unit of CPU execution managed by the operating system or runtime.

A process can contain one or multiple threads.

### Why Is It Needed?

A single application may need to perform multiple tasks without making the entire application wait for each task to finish.

### Example

Consider a shopping application:

- Thread 1: Loads product details.
- Thread 2: Processes user interactions.
- Thread 3: Updates notifications.

These threads can make progress independently while belonging to the same process.

### What Does a Thread Have?

Each thread has its own:
- Thread ID
- Program counter
- CPU registers
- Stack
- Execution state

Threads within the same process generally share:
- Code
- Data and heap memory
- Address space
- Process resources, such as open files

**Interview Question: Why does each thread need its own stack?**

Each thread needs its own function-call information, local variables and return addresses so that its execution can proceed independently.

---

## 2. Process vs Thread

This is one of the most important OS interview questions.

| Process | Thread |
|---|---|
| An executing program with its own process resources and address space. | An execution path within a process. |
| Processes generally have separate address spaces. | Threads within the same process share an address space. |
| Has its own process-level resources and state. | Has its own registers, program counter, stack and execution state. |
| Communication between processes often requires IPC. | Threads can communicate through shared process memory. |
| Process creation and switching can involve more overhead. | Thread creation and switching can involve less overhead, depending on the OS and workload. |
| Failure isolation is generally stronger between separate processes. | An unsafe memory operation by one thread can affect the whole process. |

### Example

Suppose you open two separate text-editor applications.

They usually run as separate processes.

Inside one text editor, different threads might handle the user interface, background file processing and other tasks.

**Interview Question: Why are threads called lightweight processes?**

Threads share many resources of their parent process, so creating and managing them can require fewer resources than creating separate processes.

---

## 3. Multithreading

### Definition

**Multithreading** means using multiple threads within a process to perform different tasks concurrently.

### Benefits

- **Responsiveness:** The application can respond to user actions while background work continues.
- **Resource sharing:** Threads can access shared process data.
- **Lower overhead:** Threads can be cheaper to create and manage than separate processes.
- **Parallel execution:** Threads can execute simultaneously on different CPU cores.

### Example: File Download Application

Without multithreading, an application might wait for a download to finish before responding to other work.

With multithreading, one thread can handle the download while another responds to user interactions.

**Important:** Multithreading does not automatically make every program faster. Synchronization overhead and contention can reduce performance.

---

## 4. User-Level vs Kernel-Level Threads

### User-Level Threads

User-level threads are managed by a user-space thread library or runtime.

The kernel may not know about each individual user-level thread; it may see only the underlying process or kernel-scheduled entity.

### Kernel-Level Threads

Kernel-level threads are known to and scheduled by the operating system kernel.

### Comparison

| User-Level Threads | Kernel-Level Threads |
|---|---|
| Managed primarily by a user-space library or runtime. | Managed and scheduled by the kernel. |
| Some thread operations can be inexpensive. | Operations may require kernel involvement. |
| In a many-to-one model, a blocking system call can block all threads in that process. | A blocked thread does not necessarily prevent other threads in the same process from running. |
| True parallel execution across multiple cores is not available in a pure many-to-one model. | Multiple kernel threads can execute in parallel on multiple cores. |

**Interview Trap:** Do not assume every user-level threading system has identical blocking behavior. It depends on the threading model and runtime.

---

## 5. Multithreading Models

These models describe how user-level threads are mapped to kernel-level threads.

### 5.1 Many-to-One

Many user-level threads map to one kernel thread.

```text
User Threads:     T1   T2   T3
                   \   |   /
Kernel Thread:         K1
```

- Simple and potentially inexpensive.
- A blocking operation may block all user threads.
- Cannot achieve true parallel execution across multiple cores through that single kernel thread.

### 5.2 One-to-One

Each user thread maps to a separate kernel thread.

```text
User Threads:     T1   T2   T3
                   |    |    |
Kernel Threads:   K1   K2   K3
```

- Allows parallel execution on multiple cores.
- A blocked thread does not necessarily block the others.
- Creating many threads can increase kernel resource and scheduling overhead.

### 5.3 Many-to-Many

Many user-level threads are mapped onto a number of kernel threads.

```text
User Threads:     T1   T2   T3   T4
                    \  /     \  /
Kernel Threads:      K1       K2
```

- Supports many user threads with a managed number of kernel threads.
- Can allow parallel execution.
- Requires more complex runtime and scheduling coordination.

**Interview Question: Which model allows multiple threads to execute in parallel on multiple CPU cores?**

One-to-one and suitable many-to-many implementations can support parallel execution. A pure many-to-one model cannot execute its user threads simultaneously on multiple cores through its single kernel thread.

---

## 6. Concurrency vs Parallelism

### Concurrency

Concurrency means multiple tasks make progress during overlapping periods. They do not necessarily execute at the exact same instant.

### Parallelism

Parallelism means multiple tasks execute at the same time, typically on different CPU cores.

### Comparison

| Concurrency | Parallelism |
|---|---|
| Tasks make progress during overlapping periods. | Tasks execute simultaneously. |
| Can occur on a single CPU core through scheduling. | Requires multiple execution resources, such as CPU cores. |
| Example: A single core switches between two threads. | Example: Two cores execute two threads simultaneously. |

### Example

Imagine one chef preparing two dishes by switching between them.

This is similar to concurrency.

Now imagine two chefs preparing two dishes simultaneously.

This is similar to parallelism.

**Interview Question: Is concurrency possible on a single-core CPU?**

Yes. The CPU can switch between tasks, allowing each to make progress.

---

## 7. Thread Pool

### Definition

A **thread pool** is a collection of reusable worker threads that execute submitted tasks.

### Why Is It Needed?

Creating a new thread for every small task can waste resources and add overhead.

A thread pool reuses existing threads to handle multiple tasks.

### How Does It Work?

1. The application creates a pool of worker threads.
2. Tasks are submitted to a queue.
3. An available worker picks up a task.
4. The worker executes the task.
5. After completion, it becomes available for another task.

### Example

A web server receives 100 requests. Instead of creating a new thread for every request, it may use a pool of worker threads to process requests.

**Interview Question: What happens if all threads in the pool are busy?**

New tasks may wait in a queue until a worker becomes available, depending on the pool's configuration.

---

## 8. Common Thread-Related Problems

### 8.1 Race Condition

A **race condition** occurs when the result depends on the timing or ordering of concurrent operations accessing shared data.

**Example:** Two threads increment the same shared counter without synchronization.

Both may read the same old value and overwrite each other's updates, causing a lost increment.

### 8.2 Deadlock

A **deadlock** occurs when threads or processes wait indefinitely for resources held by one another.

**Example:** Thread A holds Lock 1 and waits for Lock 2, while Thread B holds Lock 2 and waits for Lock 1.

Deadlocks are covered in detail in Phase 6.

### 8.3 Starvation

**Starvation** occurs when a thread waits indefinitely or for an excessively long time because other threads repeatedly get access to a required resource.

**Example:** A low-priority thread repeatedly misses CPU time because higher-priority work keeps arriving.

### How Can Race Conditions Be Prevented?

Use synchronization mechanisms such as mutex locks, semaphores or other appropriate coordination techniques.

These concepts are covered in Phase 5.

---

## 9. Important Interview Questions

### Q1. What is a thread?

A thread is an execution unit that has its own execution state and operates within a process.

### Q2. What is the difference between a process and a thread?

Processes generally have separate address spaces, while threads within the same process share an address space and many resources.

### Q3. What resources do threads share?

Threads in the same process generally share code, global data, heap memory and process resources such as open files.

### Q4. What resources are private to each thread?

Each thread has its own stack, program counter, CPU registers and execution state.

### Q5. Why is multithreading useful?

It can improve responsiveness, allow tasks to make progress concurrently and enable parallel execution on multiple cores.

### Q6. Can two threads execute simultaneously?

Yes, if the threading model and available CPU resources permit execution on separate cores.

### Q7. What is the difference between concurrency and parallelism?

Concurrency means multiple tasks make progress during overlapping periods; parallelism means they execute simultaneously.

### Q8. What is a thread pool?

A thread pool maintains reusable worker threads to execute tasks without creating a new thread for every task.

### Q9. What is a race condition?

A race condition occurs when concurrent operations access shared data and the result depends on their execution order.

### Q10. Can multithreading cause problems?

Yes. Common problems include race conditions, deadlocks, starvation and synchronization overhead.

### Q11. Why can a process be safer than threads for isolation?

Separate processes generally have separate address spaces, which limits the impact of many memory errors. Threads share process memory, so one thread can corrupt shared state or crash the process.

### Q12. Does multithreading always improve performance?

No. Performance depends on the workload, CPU resources, synchronization costs and scheduling overhead.

### Q13. What is the difference between user-level and kernel-level threads?

User-level threads are managed by a user-space library or runtime, while kernel-level threads are managed and scheduled by the operating system kernel.

### Q14. What happens when one thread modifies shared data?

Other threads in the same process may observe that change. If multiple threads modify the data concurrently, synchronization may be needed to prevent incorrect results.
