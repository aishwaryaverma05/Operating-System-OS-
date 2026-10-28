# Phase 6: Deadlocks

## 1. What Is a Deadlock?

A **deadlock** occurs when a group of processes gets stuck because each process is waiting for a resource held by another process in the group.

### Real-Life Example

Imagine two people:

- Person A has a pen and needs a notebook.
- Person B has a notebook and needs a pen.
- Neither person releases their resource.

Both wait forever. This is similar to a deadlock.

### OS Example

- Process P1 holds Resource R1 and waits for R2.
- Process P2 holds Resource R2 and waits for R1.

Neither process can continue until it gets the resource it needs.

```text
P1 holds R1 → waits for R2
P2 holds R2 → waits for R1
```

This creates a circular wait.

---

## 2. Four Necessary Conditions for Deadlock

A deadlock can occur only when all four conditions hold simultaneously.

### 1. Mutual Exclusion

At least one resource can be used by only one process at a time.

**Example:** Only one process can use a particular non-shareable resource at a time.

### 2. Hold and Wait

A process holds one resource while waiting for another.

**Example:** P1 holds R1 and waits for R2.

### 3. No Preemption

A resource cannot be forcibly taken away; it must be released voluntarily or when the process finishes using it.

**Example:** The OS cannot safely take a particular resource away while a process is using it.

### 4. Circular Wait

A circular chain of processes exists, where each process waits for a resource held by the next process.

**Example:** P1 waits for P2, P2 waits for P3, and P3 waits for P1.

### Memory Trick

**M-H-N-C**

- Mutual Exclusion
- Hold and Wait
- No Preemption
- Circular Wait

**Interview question:** How can deadlock be prevented?

Deadlock prevention works by ensuring that at least one of these four necessary conditions cannot hold.

---

## 3. Resource Allocation Graph (RAG)

A Resource Allocation Graph represents processes, resources, and their relationships.

### Symbols

- **Circle:** Process, such as P1.
- **Square:** Resource, such as R1.
- **Process → Resource:** The process is requesting the resource.
- **Resource → Process:** The resource is allocated to the process.

### Example

```text
R1 → P1 → R2 → P2 → R1
```

Interpretation:

- R1 is allocated to P1.
- P1 requests R2.
- R2 is allocated to P2.
- P2 requests R1.

This forms a cycle.

### Important Rule

- If every resource type has only **one instance**, a cycle in the resource allocation graph implies deadlock.&#x20;
- If resource types can have **multiple instances**, a cycle indicates the possibility of deadlock, but does not always prove it.

This distinction is commonly asked in interviews.

---

## 4. Deadlock Handling Methods

Operating systems can handle deadlocks in four broad ways.

| Method                 | Meaning                                                           |
| ---------------------- | ----------------------------------------------------------------- |
| Prevention             | Design the system so at least one necessary condition cannot hold |
| Avoidance              | Grant requests only when the system remains in a safe state       |
| Detection and Recovery | Allow deadlocks, detect them, then recover                        |
| Ignore                 | Do not actively handle every possible deadlock                    |

### Prevention Example

To prevent circular wait, require processes to request resources in a fixed global order.

For example, every process must request R1 before R2. This prevents one process from holding R2 while waiting for R1.

### Avoidance Example

Before granting a request, check whether doing so would leave the system in a safe state.

**Key difference:** Prevention breaks a necessary condition; avoidance checks whether granting a request keeps the system safe.

---

## 5. Safe State vs Unsafe State

### Safe State

A system is in a safe state if there is at least one **safe sequence** in which every process can finish using the currently available resources and resources released by earlier processes.

### Unsafe State

An unsafe state has no guaranteed safe sequence under the current allocation and maximum claims.

**Important:** An unsafe state does not mean the system is already deadlocked. It means deadlock cannot be ruled out based on the safe-state test.

### Example

Suppose processes can finish in this order:

`P2 → P1 → P3`

If each process can obtain its remaining required resources, finish, and release resources for the next process, this is a safe sequence.

---

## 6. Banker’s Algorithm

The **Banker’s Algorithm** is a deadlock-avoidance algorithm. It checks whether granting a resource request leaves the system in a safe state.

### Important Terms

- **Available:** Number of currently available instances of each resource.
- **Max:** Maximum resources each process may need.
- **Allocation:** Resources currently assigned to each process.
- **Need:** Additional resources each process may still require.

Formula:

`Need = Max - Allocation`

### Safety Algorithm: Basic Steps

1. Set `Work = Available`.
2. Mark every process as unfinished.
3. Find an unfinished process whose `Need <= Work`.
4. Assume that process finishes and releases its allocated resources: `Work = Work + Allocation`.
5. Mark it finished and repeat.
6. If all processes can finish, the state is safe. Otherwise, no safe sequence was found.

**Note:** The comparison `Need <= Work` is checked for every resource type.

### Small Example

Assume one resource type, with 3 instances in total.

| Process | Allocation | Max | Need |
| ------- | ---------: | --: | ---: |
| P1      |          1 |   2 |    1 |
| P2      |          1 |   3 |    2 |
| P3      |          1 |   1 |    0 |

Available = 0.

- P3 needs 0, so it can finish first.
- P3 releases 1 resource. Available becomes 1.
- P1 needs 1, so it can finish. Available becomes 2.
- P2 needs 2, so it can finish.

Safe sequence: `P3 → P1 → P2`

Therefore, the state is safe.

---

## 7. Deadlock Detection and Recovery

Some systems allow deadlocks to occur and then detect and recover from them.

### Detection

The OS examines resource allocations and outstanding requests to identify deadlocks. The exact method depends on whether resources have single or multiple instances.

### Recovery Methods

- **Terminate processes:** Abort one or more processes to break the deadlock.
- **Resource preemption:** Take a resource away if it can safely be reclaimed.
- **Rollback:** Return a process to an earlier checkpoint and retry.

Recovery decisions may consider lost work, process priority, and recovery cost.

---

## 8. Deadlock vs Starvation vs Livelock

| Concept    | Meaning                                                                                         |
| ---------- | ----------------------------------------------------------------------------------------------- |
| Deadlock   | Processes are blocked, waiting on resources or events that cannot progress without intervention |
| Starvation | A process waits indefinitely because it repeatedly fails to get the resource or access it needs |
| Livelock   | Processes keep changing state or responding to one another, but useful progress does not happen |

### Example

- **Deadlock:** P1 waits for P2's resource, and P2 waits for P1's resource.
- **Starvation:** A low-priority process never gets CPU time because higher-priority processes keep running.
- **Livelock:** Two processes repeatedly give way to each other, but neither moves forward.

---

## 9. Deadlock Prevention vs Avoidance

| Prevention                                                      | Avoidance                                            |
| --------------------------------------------------------------- | ---------------------------------------------------- |
| Ensures at least one necessary condition cannot hold            | Checks whether each allocation keeps the system safe |
| Does not require the same safe-sequence check for every request | Uses resource-need information to assess safety      |
| Example: Fixed resource ordering                                | Example: Banker’s Algorithm                          |

**Interview trap:** Banker’s Algorithm is used for deadlock avoidance, not deadlock prevention.

---

## 10. Important Interview Questions

**Q1. What is deadlock?**
A situation where processes wait indefinitely for resources or events and cannot make progress without intervention.

**Q2. What are the four necessary conditions?**
Mutual exclusion, hold and wait, no preemption, and circular wait.

**Q3. Does a cycle always mean deadlock?**
For a resource allocation graph with one instance per resource type, a cycle implies deadlock. With multiple instances, a cycle alone does not prove it.

**Q4. What is a safe state?**
A state with at least one sequence in which all processes can finish.

**Q5. Can an unsafe state exist without deadlock?**
Yes. Unsafe means no safe sequence is guaranteed; the system may not yet be deadlocked.

**Q6. What is Banker’s Algorithm?**
A deadlock-avoidance algorithm that checks whether a resource allocation leaves the system in a safe state.

**Q7. How can circular wait be prevented?**
Impose a fixed ordering on resource acquisition.

**Q8. What is the difference between deadlock and starvation?**
Deadlock prevents a group of processes from progressing; starvation is the indefinite postponement of an individual process.

**Q9. How can an OS recover from deadlock?**
It may terminate processes, preempt resources when possible, or roll processes back to checkpoints.

**Q10. What is a safe sequence?**
An ordering in which each process can obtain its remaining required resources, finish, and release resources for subsequent processes.

---

## 11. Placement Revision Checklist

- [ ] Explain deadlock with an example.
- [ ] Memorize the four necessary conditions.
- [ ] Read a Resource Allocation Graph.
- [ ] Understand the single-instance vs multiple-instance cycle rule.
- [ ] Differentiate prevention, avoidance, detection and recovery.
- [ ] Understand safe and unsafe states.
- [ ] Calculate `Need = Max - Allocation`.
- [ ] Trace a safe sequence using Banker’s Algorithm.
- [ ] Differentiate deadlock, starvation and livelock.

**Placement priority:** The four conditions, prevention vs avoidance, and Banker’s Algorithm are the most important topics to revise first.
