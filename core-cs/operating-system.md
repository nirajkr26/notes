# Operating Systems — Detailed Interview & Exam Notes

> **Focus:** processes, threads, scheduling, synchronization, deadlocks, memory, virtual memory, filesystems, I/O, IPC, security, numericals, and interview preparation.

## 1. OS Fundamentals

An **Operating System** manages hardware resources and provides abstractions/services to applications.

```text
Applications
   ↓ system calls
Kernel
   ├─ Processes / threads
   ├─ Memory / virtual memory
   ├─ Filesystems / storage
   ├─ I/O / drivers
   ├─ Networking
   └─ Security / protection
   ↓
Hardware
```

### Kernel and user mode

- **User mode:** restricted privileges; applications run here.
- **Kernel mode:** privileged execution; kernel can access protected resources.
- **System call:** controlled transition from user mode to kernel mode.

### OS responsibilities

| Area | Responsibilities |
|---|---|
| Process | Creation, scheduling, synchronization, termination |
| Memory | Allocation, address translation, paging, protection |
| File system | Files, directories, metadata, permissions |
| I/O | Drivers, interrupts, DMA, buffering |
| Storage | Blocks, caching, disk/SSD access |
| Security | Authentication, authorization, isolation |
| Networking | Sockets and network buffers |

### OS types

- Batch
- Time-sharing
- Real-time
- Embedded
- Distributed
- Network
- Mobile

**Hard real-time** systems have strict deadlines; **soft real-time** systems tolerate occasional deadline misses.

## 2. Kernel Architecture

### Monolithic kernel

Most core services run in kernel space. High performance, but a faulty kernel component can affect the whole kernel.

### Microkernel

Keeps the kernel minimal and moves more services to user space. Improves isolation/modularity but increases IPC complexity and can add overhead.

### Modular/hybrid designs

Practical systems combine ideas. Linux is generally described as a monolithic kernel with loadable modules, not a pure microkernel.

## 3. System Calls

Common system-call families:

| Category | Examples |
|---|---|
| Process | `fork`, `exec`, `wait`, `exit` |
| Files | `open`, `read`, `write`, `close` |
| IPC | `pipe`, shared-memory/semaphore APIs |
| Network | `socket`, `bind`, `listen`, `accept`, `connect` |
| Information | `getpid`, time APIs |

A normal function call can remain in user space; a system call crosses a protection boundary and may execute kernel code.

## 4. Processes

A **process** is an executing program plus its runtime state and resources.

### Process memory

```text
High
+----------------+
| Stack          | ↓
+----------------+
| Shared libs /  |
| mapped files   |
+----------------+
| Heap           | ↑
+----------------+
| BSS / Data     |
+----------------+
| Text / Code    |
+----------------+
Low
```

Actual layout varies by architecture, OS, ABI, ASLR, and runtime.

### Process states

```text
New → Ready → Running → Terminated
             ↓   ↑
           Blocked ── I/O complete ──→ Ready
             ↑
        I/O / event wait
```

A scheduler dispatches a ready task. A running task can block, terminate, or be preempted.

### PCB

The Process Control Block can contain PID, state, program counter, registers, scheduling information, address-space information, open files, accounting data and security credentials.

## 5. Context Switching

A context switch saves the current execution context and restores another.

```text
A running → save A → load B → B running
```

Cost includes register state changes and possible loss of cache/TLB locality. Context switching is overhead, not useful application work.

## 6. `fork`, `exec`, and Copy-on-Write

On UNIX-like systems:

- `fork()` creates a child process.
- `exec()` replaces the current process image.
- `wait()` collects child termination status.
- `exit()` terminates a process.

### Copy-on-write

After `fork`, parent and child can initially share physical pages. A write causes a private copy. This avoids eagerly copying the entire address space.

## 7. Threads

Threads share a process's code, heap and many resources, but each thread has its own stack, registers and program counter.

| Feature | Process | Thread |
|---|---|---|
| Address space | Separate | Shared within process |
| Creation | Usually more expensive | Usually cheaper |
| Communication | IPC | Shared memory/direct |
| Isolation | Stronger | Weaker |
| Parallel execution | Yes | Yes on multi-core |

User-level threading is managed by a runtime/library; kernel-visible threads can be independently scheduled on CPU cores.

## 8. CPU Scheduling

### Metrics

```text
Turnaround = Completion − Arrival
Waiting    = Turnaround − CPU Burst
Response   = First CPU allocation − Arrival
```

Goals: maximize utilization/throughput; minimize waiting, response and turnaround; maintain fairness.

### FCFS

Non-preemptive, arrival order. Simple, but can suffer from the **convoy effect** when a long job precedes many short jobs.

### SJF

Select the shortest next CPU burst. Under standard assumptions it minimizes average waiting time among non-preemptive schedules. Exact future burst time must be estimated.

### SRTF

Preemptive SJF: a newly arrived shorter remaining job can preempt the current task. Long jobs can starve.

### Round Robin

Each task receives a time quantum.

- Tiny quantum → high context-switch overhead.
- Huge quantum → approaches FCFS.
- Good for interactive time-sharing.

### Priority scheduling

Highest-priority runnable task is selected according to policy. Can be preemptive/non-preemptive. **Starvation** can be reduced by **aging**.

### Priority inversion

A high-priority task waits for a resource held by a low-priority task while medium-priority work runs. **Priority inheritance** is a common mitigation.

### MLFQ

Tasks can move between priority queues based on CPU behavior and waiting. Do not equate textbook MLFQ directly with Linux's modern scheduler; Linux uses more nuanced scheduling mechanisms.

## 9. Synchronization

A **race condition** occurs when correctness depends on concurrent timing/interleaving.

A **critical section** accesses shared state.

Correct mutual exclusion requires:

1. Mutual exclusion
2. Progress
3. Bounded waiting

### Mutex

Protects a critical section with ownership-style lock/unlock semantics.

### Semaphore

Counter-based primitive used for signaling or controlling a number of resources.

- Binary semaphore: 0/1.
- Counting semaphore: 0..N.

### Condition variable

Allows a thread to sleep until a condition changes. Recheck the condition in a loop because wakeups can be spurious.

```c
lock(m);
while (!ready) wait(cv, m);
use_data();
unlock(m);
```

### Classical problems

- Producer-consumer → bounded buffer + synchronization.
- Readers-writers → multiple readers, exclusive writers; fairness policy matters.
- Dining philosophers → demonstrates deadlock/starvation; resource ordering can prevent cycles.

## 10. Deadlocks

Deadlock is a set of tasks waiting forever because each depends on resources held/waited for by others.

### Coffman conditions

All four are necessary for classic resource deadlock:

1. Mutual exclusion
2. Hold and wait
3. No preemption
4. Circular wait

### Prevention

Break one condition, e.g. impose global resource ordering to prevent circular wait.

### Avoidance

Grant a request only if the resulting state remains safe. **Banker's algorithm** uses available, allocated and maximum/remaining resource needs.

### Detection/recovery

Detect cycles or unsafe resource relationships, then terminate processes, preempt recoverable resources, or roll back when supported.

## 11. Memory Management

- Virtual/logical address: generated by CPU/program.
- Physical address: RAM location.
- MMU: translates virtual to physical addresses.

### Fragmentation

- **Internal:** unused space inside an allocated block.
- **External:** free memory exists but is fragmented into separate holes.
- **Compaction:** moves allocations to consolidate holes; expensive.

## 12. Paging

Virtual memory is divided into fixed-size **pages**; physical memory into same-size **frames**.

```text
Virtual address = [ page number | offset ]
Page table: page number → frame number
Physical address = [ frame number | offset ]
```

If page size is `2^n`, offset is `n` bits.

Example: 32-bit virtual address + 4 KiB pages (`2^12`) → 12 offset bits + 20 page-number bits.

Paging removes external fragmentation but can create internal fragmentation.

## 13. Page Tables and TLB

A page-table entry can contain frame number plus present, permission, accessed/reference and dirty bits.

The **TLB** caches recent virtual→physical translations.

```text
CPU virtual address → TLB
                    ├─ hit → frame
                    └─ miss → page table → frame
```

**TLB miss ≠ page fault.** A TLB miss can still find a resident page in the page table.

### Multi-level page tables

Large virtual address spaces use hierarchical page tables so unused regions do not require fully allocated lower-level tables.

## 14. Segmentation

Segmentation divides an address space into variable-size logical units such as code, data and stack.

| Paging | Segmentation |
|---|---|
| Fixed-size units | Variable-size units |
| No external fragmentation | External fragmentation possible |
| Mostly transparent | Matches logical program structure |
| Page-level protection | Segment-level protection |

## 15. Virtual Memory and Page Faults

Virtual memory provides large, isolated address spaces while only part of each process needs to reside in RAM.

A **page fault** occurs when a referenced page requires OS handling because it is not currently resident or the access is otherwise not immediately satisfiable.

Typical flow:

1. CPU references page.
2. Hardware detects non-resident page.
3. Trap to kernel.
4. Validate access.
5. Find free frame or victim.
6. Read page from backing storage if needed.
7. Update page table/TLB.
8. Restart instruction.

Page faults are normal in demand paging; not every page fault indicates a bug.

## 16. Page Replacement

| Algorithm | Idea | Key point |
|---|---|---|
| FIFO | Oldest page out | Can show Belady anomaly |
| OPT | Farthest future use | Theoretical optimum |
| LRU | Least recently used | Uses temporal locality |
| Clock | Reference-bit approximation | Practical LRU approximation |
| LFU | Least frequently used | Can retain stale popular pages |

### Belady's anomaly

Increasing frames can increase faults under FIFO. True LRU is a stack algorithm and does not have this anomaly.

### Thrashing

The system spends excessive time paging and too little time executing. Mitigations include reducing multiprogramming or increasing effective frame allocation.

### Locality

- Temporal locality: recent items likely reused.
- Spatial locality: nearby addresses likely used soon.

Caches, TLBs and virtual memory exploit locality.

## 17. File Systems

A file system manages persistent data, metadata and namespaces.

Typical metadata includes name, size, ownership, permissions, timestamps and block/extents information.

### Inodes

In UNIX-like systems, a directory maps a filename to an inode. The inode stores metadata and references/extents for the file's data. Filename and inode are different concepts.

### Allocation

| Method | Advantage | Disadvantage |
|---|---|---|
| Contiguous | Fast sequential/random access | Fragmentation/growth problem |
| Linked | Easy growth | Poor random access |
| Indexed | Random access | Metadata overhead |
| Extents | Compact representation of contiguous ranges | More complex allocation |

## 18. Disk Scheduling

Classical seek algorithms target magnetic disks: FCFS, SSTF, SCAN, C-SCAN, LOOK and C-LOOK.

SSDs do not have mechanical seek heads, so classical seek optimization is less directly relevant to them.

## 19. I/O

### Polling

CPU repeatedly checks device state. Simple but can waste CPU.

### Interrupt-driven I/O

Device notifies CPU when service/completion needs attention.

### DMA

A device/DMA controller transfers blocks between device and memory with minimal CPU copying.

```text
Device ↔ DMA/controller ↔ RAM
              ↑
       CPU configures transfer
```

### Buffering vs caching vs spooling

- **Buffering:** absorbs rate/burst mismatch.
- **Caching:** stores likely-reused data.
- **Spooling:** queues work for a sequential device such as a printer.

## 20. IPC

Common inter-process communication mechanisms:

- Pipes/FIFOs
- Message queues
- Shared memory
- Semaphores
- Signals
- Sockets

Shared memory is fast but requires synchronization. Sockets can communicate between processes on the same machine or across a network.

## 21. Signals and Process Control

UNIX-like systems use signals for asynchronous notifications.

- `SIGTERM`: request graceful termination.
- `SIGKILL`: unconditional termination; cannot be caught or ignored.
- `SIGINT`: terminal interrupt, commonly Ctrl+C.
- `SIGHUP`: terminal hangup; commonly also used by daemons as a reload signal.

## 22. Security and Protection

- Authentication: who are you?
- Authorization: what can you do?
- Least privilege: grant only necessary permissions.
- Isolation: prevent one process from corrupting another's state.
- Memory permissions: read/write/execute controls.

## 23. OS Numericals

### CPU scheduling

Given arrival/burst times:

1. Draw the Gantt chart.
2. Find completion time.
3. `TAT = CT - AT`.
4. `WT = TAT - BT`.
5. `RT = first start - AT`.
6. Average the metrics.

### Page replacement

Process a reference string left-to-right, mark hits/faults, apply the selected policy and count page faults.

### Address translation

For page size `2^n`, the lower `n` address bits are the offset; remaining bits identify the virtual page.

## 24. Interview Questions & Answers

### Q1. Process vs program?

A program is passive code/data; a process is a running instance with execution state and resources.

### Q2. Process vs thread?

A process owns an address space/resource context; threads are execution units within that process and normally share its address space.

### Q3. Why are threads usually cheaper?

They share address-space resources, so less setup and isolation work is required. They still incur scheduling and synchronization overhead.

### Q4. What is context switching?

Saving one execution context and restoring another. It is overhead and can hurt cache/TLB locality.

### Q5. What is a race condition?

A correctness problem whose result depends on the timing/interleaving of concurrent operations.

### Q6. Mutex vs semaphore?

A mutex primarily provides ownership-based mutual exclusion; a semaphore is a counter used for signaling or controlling a resource count.

### Q7. Coffman conditions?

Mutual exclusion, hold-and-wait, no preemption and circular wait.

### Q8. Deadlock prevention vs avoidance?

Prevention breaks a necessary condition structurally. Avoidance checks whether granting a request keeps the system in a safe state.

### Q9. Starvation vs deadlock?

Starvation means a task may wait indefinitely because policy repeatedly favors others. Deadlock is a cyclic dependency where involved tasks cannot proceed.

### Q10. What is aging?

Increasing the priority of long-waiting tasks to reduce starvation.

### Q11. What is a page fault?

A trap/exception requiring OS handling because the referenced virtual page is not immediately resident or accessible.

### Q12. Page fault vs TLB miss?

TLB miss means the translation cache missed. Page fault means the memory access requires page-fault handling; a TLB miss can resolve without a page fault.

### Q13. Why is TLB needed?

It caches recent address translations, avoiding a page-table walk for every memory access.

### Q14. What is virtual memory?

An address-space abstraction providing process isolation and allowing only portions of an address space to be resident in RAM.

### Q15. What is thrashing?

Excessive paging that leaves little CPU time for useful application work.

### Q16. Why use copy-on-write after `fork`?

Because parent and child often do not modify all pages; copying only modified pages saves memory and time.

### Q17. What is DMA?

Direct Memory Access lets a device controller move data to/from RAM with minimal CPU involvement.

### Q18. Kernel mode vs user mode?

Kernel mode has privileged access; user mode is restricted and must use controlled OS interfaces.

### Q19. What is priority inversion?

A high-priority task is blocked indirectly by a low-priority task holding a needed resource, often worsened by medium-priority work.

### Q20. Why does page size matter?

Larger pages reduce page-table overhead but can increase internal fragmentation and the amount of data moved on a fault.

### Q21. Is every page fault an error?

No. Demand paging intentionally generates page faults when pages are first accessed.

### Q22. Is Linux a microkernel?

No. Linux is generally described as a monolithic kernel with modular/loadable components.

### Q23. Is virtual memory the same as swap?

No. Virtual memory is the address-space abstraction; swap is one possible backing-storage mechanism.

### Q24. Why can a blocked process improve CPU utilization?

While it waits for I/O, the scheduler can run another ready task instead of leaving the CPU idle.

### Q25. Why are classical disk algorithms less important for SSDs?

SSDs lack mechanical seek heads, so dominant costs and optimization strategies differ from magnetic disks.

## 25. Quick Revision Checklist

- [ ] Kernel/user mode and system calls
- [ ] Process states and PCB
- [ ] `fork`, `exec`, `wait`, COW
- [ ] Threads and context switches
- [ ] FCFS/SJF/SRTF/RR/Priority/MLFQ
- [ ] Scheduling numericals
- [ ] Race conditions and critical sections
- [ ] Mutex/semaphore/condition variable
- [ ] Classical synchronization problems
- [ ] Deadlock conditions and Banker's algorithm
- [ ] Paging/segmentation
- [ ] Page tables and TLB
- [ ] Virtual memory/page faults
- [ ] FIFO/LRU/OPT/Clock and thrashing
- [ ] File systems/inodes/extents
- [ ] Disk scheduling
- [ ] Interrupts/DMA
- [ ] IPC/signals
- [ ] Security/protection
