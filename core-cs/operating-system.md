# Operating Systems — Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction / Overview

An **Operating System (OS)** is system software that manages computer hardware and software resources and provides common services for programs.

**Functions of OS:**
- Process management
- Memory management
- File system management
- I/O device management
- Security and access control
- Networking support

**Types of OS:**

| Type                  | Description                                    | Examples               |
|-----------------------|------------------------------------------------|------------------------|
| Batch OS              | Jobs queued and executed without user interaction | IBM OS/360            |
| Time-Sharing (Multi-user) | Multiple users share CPU time          | UNIX                   |
| Real-Time OS (RTOS)   | Guarantees response within strict time bounds  | VxWorks, FreeRTOS      |
| Distributed OS        | Manages resources across multiple machines     | Amoeba, Plan 9         |
| Network OS            | Provides networking capabilities               | Novell NetWare         |
| Mobile OS             | Designed for mobile devices                    | Android, iOS           |

---

## 2. Process Management

### 2.1 Process vs Program vs Thread

| Feature      | Program          | Process                        | Thread                           |
|--------------|------------------|--------------------------------|----------------------------------|
| Definition   | Static code on disk | Program in execution       | Lightweight unit within a process|
| Memory       | Disk             | Own address space (PCB)        | Shares process memory            |
| State        | Passive          | Active                         | Active                           |
| Creation     | Compiled/stored  | `fork()` / OS scheduler        | `pthread_create()`, `Thread()`   |

### 2.2 Process States

```
        ┌──────────────────────────────────────────────────┐
        │                                                  │
  New ──►  Ready  ◄────────── Running ──► Terminated      │
           │   ▲               │                          │
           │   └───────────────┘   (I/O complete)         │
           │         (dispatch)                           │
           ▼                                              │
         Waiting/Blocked ◄──────────── (I/O request) ────┘
```

**States:**
1. **New** — Process being created  
2. **Ready** — Waiting for CPU  
3. **Running** — Currently executing  
4. **Waiting/Blocked** — Waiting for I/O or event  
5. **Terminated** — Execution finished

### 2.3 Process Control Block (PCB)

Information stored per process:
- Process ID (PID)
- Process state
- Program counter (PC)
- CPU registers
- CPU scheduling info (priority, queue pointers)
- Memory management info
- I/O status info
- Accounting info

### 2.4 Context Switching

> Saving the state (PCB) of the running process and loading the state of the next process.

- **Overhead:** purely overhead; no useful work done during a context switch  
- Triggered by: timer interrupt, I/O request, system call

---

## 3. Threads

### 3.1 Thread vs Process (detailed)

| Feature             | Process                        | Thread                         |
|---------------------|--------------------------------|--------------------------------|
| Address space       | Independent                    | Shared with parent process     |
| Communication       | IPC (pipes, sockets, shared mem)| Direct (shared memory)        |
| Context switch cost | High                           | Low                            |
| Crash isolation     | Crash doesn't affect others    | Crash can affect whole process |
| Creation time       | Slow                           | Fast                           |

### 3.2 Types of Threads

- **User-Level Threads (ULT):** Managed in user space by thread library; kernel unaware; faster creation but one block blocks all  
- **Kernel-Level Threads (KLT):** Managed by OS; true concurrency on multi-core; higher overhead  
- **Hybrid:** Mix of ULT and KLT (M:N model)

### 3.3 Multithreading Models

| Model   | Description                              |
|---------|------------------------------------------|
| Many-to-One | Multiple ULTs → one KLT (no true parallelism) |
| One-to-One  | Each ULT → one KLT (true parallelism, high overhead) |
| Many-to-Many| M ULTs ↔ N KLTs (flexible, best of both) |

---

## 4. CPU Scheduling

### 4.1 Scheduling Criteria

- **CPU Utilization** — keep CPU as busy as possible (↑)
- **Throughput** — processes completed per unit time (↑)
- **Turnaround Time** — total time from submission to completion (↓)
- **Waiting Time** — time spent in ready queue (↓)
- **Response Time** — time from submission to first response (↓)

**Key Formulas:**
```
Turnaround Time (TAT) = Completion Time − Arrival Time
Waiting Time (WT)     = Turnaround Time − Burst Time
Response Time         = Time of First CPU allocation − Arrival Time
```

### 4.2 Scheduling Algorithms

#### First Come First Serve (FCFS)
- Non-preemptive; processes served in arrival order  
- **Convoy effect** — short processes wait behind long ones  
- Simple but poor average waiting time

#### Shortest Job First (SJF)
- Non-preemptive; selects process with smallest burst time  
- **Optimal** for minimizing average waiting time  
- Problem: cannot know burst time in advance

#### Shortest Remaining Time First (SRTF)
- Preemptive version of SJF  
- Can cause **starvation** of longer processes

#### Round Robin (RR)
- Time quantum `q`; each process gets CPU for `q` units, then preempted  
- If `q` large → FCFS; if `q` → 0 → processor sharing  
- Fair; good for time-sharing systems

#### Priority Scheduling
- Each process has a priority; highest priority runs first  
- Can be preemptive or non-preemptive  
- Problem: **Starvation** → Solution: **Aging** (gradually increase priority of waiting processes)

#### Multilevel Queue Scheduling
- Ready queue split into multiple queues (foreground/background)  
- Each queue has its own algorithm

#### Multilevel Feedback Queue (MLFQ)
- Processes can move between queues based on behavior  
- Most flexible and complex; used in practice (Linux CFS)

### 4.3 Algorithm Comparison

| Algorithm | Preemptive | Starvation | Overhead | Notes          |
|-----------|-----------|------------|----------|----------------|
| FCFS      | No        | No         | Low      | Convoy effect  |
| SJF       | No        | Yes        | Medium   | Optimal avg WT |
| SRTF      | Yes       | Yes        | High     | Preemptive SJF |
| RR        | Yes       | No         | Medium   | Fair, quantum  |
| Priority  | Both      | Yes        | Medium   | Aging fixes it |
| MLFQ      | Yes       | No         | High     | Most practical |

---

## 5. Process Synchronization

### 5.1 Race Condition & Critical Section

- **Race Condition:** Multiple processes access shared data concurrently; outcome depends on execution order  
- **Critical Section:** Code segment that accesses shared resources

**Requirements for a valid solution:**
1. **Mutual Exclusion** — only one process in CS at a time  
2. **Progress** — if no process is in CS, selection of entering process can't be postponed indefinitely  
3. **Bounded Waiting** — limit on number of times others can enter CS while a process is waiting

### 5.2 Synchronization Mechanisms

#### Peterson's Solution (2 processes)
- Software solution; uses `flag[]` and `turn` variables  
- Satisfies all 3 requirements

#### Semaphores
```
wait(S):   while (S <= 0); S--;   // P operation
signal(S): S++;                    // V operation
```
- **Binary Semaphore (Mutex):** S ∈ {0,1}; mutual exclusion  
- **Counting Semaphore:** S ≥ 0; resource counting

#### Mutex vs Semaphore

| Feature          | Mutex                          | Semaphore                      |
|------------------|--------------------------------|--------------------------------|
| Value            | Binary (locked/unlocked)       | Integer (0 to N)               |
| Ownership        | Yes (only owner can unlock)    | No ownership                   |
| Use case         | Mutual exclusion                | Signaling + resource counting  |

#### Monitors
- High-level synchronization construct  
- Only one process active inside monitor at a time  
- Use **condition variables**: `wait()`, `signal()`

### 5.3 Classical Problems

| Problem                    | Solution                              |
|----------------------------|---------------------------------------|
| Producer-Consumer          | Semaphores: `empty`, `full`, `mutex`  |
| Readers-Writers            | Priority rules + semaphores           |
| Dining Philosophers        | Semaphore per fork; or ordering       |
| Sleeping Barber            | Semaphores for chairs + barber        |

---

## 6. Deadlocks

### 6.1 Definition & Conditions

> A **deadlock** is a state where a set of processes are blocked, each waiting for a resource held by another.

**Coffman Conditions (all 4 must hold simultaneously):**
1. **Mutual Exclusion** — resource held non-shareably  
2. **Hold and Wait** — process holds resource while waiting for more  
3. **No Preemption** — resources cannot be forcibly taken  
4. **Circular Wait** — P1 waits for P2, P2 waits for P3, ... Pn waits for P1

### 6.2 Resource Allocation Graph (RAG)

```
Process → Resource  (request edge)
Resource → Process  (assignment edge)

No cycle    → No deadlock
Cycle exists:
  - Single instance per resource type → DEADLOCK
  - Multiple instances → POSSIBLE deadlock (check further)
```

### 6.3 Deadlock Handling Strategies

#### Prevention (break one of 4 conditions)
- No mutual exclusion (not always possible)
- No hold-and-wait: request all resources at once
- Allow preemption: force release resources
- No circular wait: impose ordering on resource types

#### Avoidance — Banker's Algorithm
- Before granting a resource, check if the system remains in a **safe state**  
- **Safe state:** there exists a sequence in which all processes can finish  
- Requires knowing maximum resource needs in advance

```
Safety Algorithm:
1. Find an unfinished process whose need ≤ available
2. Pretend it finishes, release its resources
3. Repeat until all finish (safe) or none qualifies (unsafe)
```

#### Detection & Recovery
- Periodically check for deadlocks (RAG reduction)  
- Recovery: terminate processes, or preempt resources

#### Ignorance (Ostrich Algorithm)
- Pretend deadlock never occurs (used in most OS, including Linux/Windows for rare deadlocks)

---

## 7. Memory Management

### 7.1 Memory Hierarchy

```
Registers (fastest, smallest)
    ↓
Cache (L1, L2, L3)
    ↓
Main Memory (RAM)
    ↓
Secondary Storage (HDD/SSD)
    ↓
Tertiary (Tape — slowest, largest)
```

### 7.2 Contiguous Memory Allocation

- **Fixed (Static) Partitioning:** memory divided into fixed-size partitions → **internal fragmentation**  
- **Dynamic (Variable) Partitioning:** partitions sized to process needs → **external fragmentation**

**Allocation strategies:**
- **First Fit** — allocate first hole large enough (fast)
- **Best Fit** — smallest hole that fits (minimizes wasted space, slow, small fragments)
- **Worst Fit** — largest hole (maximizes remaining fragment, usually worst performance)

**Fragmentation:**
- **Internal** — allocated block larger than needed (wasted inside block)  
- **External** — total free memory sufficient but not contiguous  
- **Compaction** — shuffle processes to consolidate free space (expensive)

### 7.3 Paging

> Divide logical memory into **pages** (fixed-size), physical memory into **frames**.

- **Page size** = **Frame size** (power of 2, typically 4KB)  
- **Page Table** maps page number → frame number  
- **No external fragmentation**; internal fragmentation ≤ page size

```
Logical Address = [Page Number | Page Offset]
Physical Address = Frame Base Address + Offset
```

**TLB (Translation Lookaside Buffer):**
- Cache for page table entries  
- **Effective Access Time (EAT)** = α(cache hit time) + (1−α)(memory access time)  
  where α = TLB hit ratio

### 7.4 Segmentation

> Divide logical memory into **variable-size segments** (code, data, stack)

- **Segment Table:** base address + limit for each segment  
- External fragmentation possible; matches programmer's view  
- Protection per segment

### 7.5 Virtual Memory & Demand Paging

> Allow processes to execute even if not fully in memory; load pages **on demand**.

- **Page Fault** — referenced page not in memory → OS loads it from disk  
- **Swap Space** — reserved disk area for swapped-out pages

**Page Fault Handling:**
1. Check if reference is valid
2. Find free frame (or use replacement)
3. Load page from disk into frame
4. Update page table
5. Restart instruction

### 7.6 Page Replacement Algorithms

| Algorithm | Description                               | Belady's Anomaly? |
|-----------|-------------------------------------------|-------------------|
| FIFO      | Replace oldest page                       | Yes ✅             |
| LRU       | Replace least recently used               | No ❌              |
| Optimal (OPT) | Replace page not used for longest time | No ❌           |
| LFU       | Replace least frequently used             | No ❌              |
| Clock (Second Chance) | FIFO with use bit                | No ❌         |

**Belady's Anomaly:** More frames → more page faults (occurs only in FIFO).

**Thrashing:** Process spends more time paging than executing (too many processes, insufficient frames).

---

## 8. File Systems

### 8.1 File Concepts

- **File:** named collection of related information stored on secondary storage  
- **File attributes:** name, identifier, type, location, size, protection, timestamps

### 8.2 Directory Structure

| Structure       | Description                                |
|-----------------|--------------------------------------------|
| Single-level    | All files in one directory                 |
| Two-level       | Separate directory per user                |
| Tree-structured | Hierarchical; absolute/relative paths      |
| Acyclic Graph   | Allows shared files (links)                |
| General Graph   | Allows cycles; requires cycle detection    |

### 8.3 File Allocation Methods

| Method       | Description                          | Pros                  | Cons                           |
|--------------|--------------------------------------|-----------------------|--------------------------------|
| Contiguous   | Files stored in contiguous blocks    | Fast sequential access| External fragmentation         |
| Linked       | Blocks linked via pointers           | No fragmentation      | No random access; pointer overhead |
| Indexed      | Index block holds all block pointers | Random access         | Overhead for small files       |

### 8.4 Disk Scheduling Algorithms

| Algorithm | Strategy                                   | Notes                         |
|-----------|--------------------------------------------|-------------------------------|
| FCFS      | Service in order of arrival                | Simple; poor seek time        |
| SSTF      | Shortest seek time first                   | Good avg; starvation possible |
| SCAN      | Elevator; service in one direction then reverse | Good throughput       |
| C-SCAN    | SCAN but only one direction; jump to start | More uniform wait times       |
| LOOK/C-LOOK | SCAN/C-SCAN but only go as far as last request | Better than SCAN/C-SCAN |

---

## 9. I/O Systems

- **Programmed I/O (Polling):** CPU continuously checks device status (wastes CPU cycles)  
- **Interrupt-driven I/O:** Device interrupts CPU when ready (efficient)  
- **DMA (Direct Memory Access):** Device controller transfers data directly to memory without CPU  
- **Spooling:** Simultaneous Peripheral Operations OnLine; e.g., print queue

---

## 10. Real-world Use Cases

| Concept              | Example                                       |
|----------------------|-----------------------------------------------|
| Scheduling (RR)      | Time-sharing on UNIX/Linux terminals          |
| Virtual Memory       | Running large applications on limited RAM     |
| Semaphores           | Database connection pools                     |
| Deadlock avoidance   | Banker's algorithm in resource managers       |
| File systems         | ext4 (Linux), NTFS (Windows), APFS (macOS)    |
| Paging               | Every modern OS (Windows, Linux, macOS)       |

---

## 11. Frequently Asked Questions (FAQs)

**Q1. What is the difference between process and thread?**  
> A process is an independent program in execution with its own address space. A thread is a unit of execution within a process, sharing the process's memory.

**Q2. What is a context switch?**  
> Saving the state of the current process and loading the state of the next process. It's pure overhead but necessary for multitasking.

**Q3. What is a race condition? How do you prevent it?**  
> When multiple processes access shared data concurrently and the outcome depends on execution order. Prevented using synchronization (mutex, semaphores, monitors).

**Q4. What are the Coffman conditions for deadlock?**  
> Mutual exclusion, Hold & Wait, No preemption, Circular wait — all four must hold simultaneously.

**Q5. What is Belady's Anomaly?**  
> In FIFO page replacement, increasing the number of frames sometimes increases the number of page faults.

**Q6. What is thrashing?**  
> When a process spends more time in paging than executing due to insufficient frames, leading to very low CPU utilization.

**Q7. Difference between paging and segmentation?**  
> Paging: fixed-size units (pages/frames), no external fragmentation, hardware-driven. Segmentation: variable-size logical units, programmer's view, can cause external fragmentation.

**Q8. What is the difference between a mutex and a semaphore?**  
> Mutex has ownership (only the locking thread can unlock); binary. Semaphore has no ownership; can be binary or counting.

**Q9. What is the purpose of TLB?**  
> TLB (Translation Lookaside Buffer) is a fast cache for page table entries that speeds up virtual-to-physical address translation.

**Q10. What is a zombie process?**  
> A process that has completed execution but still has an entry in the process table because its parent hasn't read its exit status using `wait()`.

---

## 12. Common Misconceptions

- ❌ *"Threads don't share anything"* → Threads share heap, global variables, code; only stack and registers are private.  
- ❌ *"Deadlock and livelock are the same"* → Livelock: processes keep changing state in response to each other but make no progress.  
- ❌ *"Optimal page replacement is practical"* → It's theoretical; requires knowing the future.  
- ❌ *"More RAM always prevents thrashing"* → Thrashing is also caused by too many processes; working-set model helps.  
- ❌ *"Starvation and deadlock are the same"* → Deadlock: processes blocked forever; Starvation: process waits indefinitely but others make progress.

---

## 13. Quick Revision Checklist

- [ ] Process states (5 states and transitions)  
- [ ] PCB contents  
- [ ] Scheduling algorithms + formulas (TAT, WT)  
- [ ] Coffman conditions for deadlock  
- [ ] Banker's algorithm steps  
- [ ] Paging: address translation, TLB, EAT formula  
- [ ] Page replacement algorithms + Belady's anomaly  
- [ ] Semaphore operations (wait/signal)  
- [ ] Classical synchronization problems  
- [ ] File allocation methods  
- [ ] Disk scheduling algorithms  
- [ ] Thrashing and working-set model  

---

*Last updated: 2026 | Suitable for: GATE, university exams, software engineering interviews*
