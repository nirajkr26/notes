# Operating Systems (OS) — Super Detailed Notes for Exams & Interviews

---

## 1) What is an Operating System?

An **Operating System** is system software that works as an interface between users/applications and hardware.

### Core goals
- **Convenience:** Make computer usage easier.
- **Efficiency:** Use CPU, memory, I/O devices effectively.
- **Ability to evolve:** Support new hardware and features.

### Main responsibilities
- Process and thread management
- CPU scheduling and dispatching
- Memory and virtual memory management
- File system and storage management
- Device and I/O management
- Protection, security, and access control
- Networking support

---

## 2) OS Structures and System Calls

### 2.1 OS design models
- **Monolithic kernel:** All OS services in kernel space (fast, less modular)
- **Layered OS:** OS divided into layers (clean design)
- **Microkernel:** Minimal kernel, services in user space (more reliable, IPC overhead)
- **Modular kernel:** Core + loadable modules (Linux style)
- **Hybrid kernel:** Combination approach (Windows, macOS)

### 2.2 User mode vs Kernel mode
- **User mode:** Restricted; cannot directly access hardware.
- **Kernel mode:** Full privilege; executes critical OS services.

### 2.3 System calls (API to OS)
Common categories:
- Process control (`fork`, `exec`, `wait`, `exit`)
- File management (`open`, `read`, `write`, `close`)
- Device management (`ioctl`)
- Information maintenance (`getpid`, `time`)
- Communication (`pipe`, `socket`, `send`, `recv`)

---

## 3) Process Management

### 3.1 Program vs Process vs Thread
| Aspect | Program | Process | Thread |
|---|---|---|---|
| Nature | Static file | Running program | Lightweight execution unit |
| Memory | On disk | Own address space | Shares process address space |
| Overhead | None at runtime | Higher | Lower |

### 3.2 Process states
- New
- Ready
- Running
- Waiting/Blocked
- Terminated

### 3.3 PCB (Process Control Block)
Contains:
- PID, process state
- Program counter, CPU registers
- Scheduling info (priority, queue pointers)
- Memory info (base/limit, page table pointers)
- I/O info, accounting info

### 3.4 Context switch
Saving current process state and restoring next process state.
- Pure overhead
- Triggered by interrupt/timer/I/O/system call

---

## 4) Threads and Multithreading

### 4.1 User-level threads vs Kernel-level threads
| Feature | User-level | Kernel-level |
|---|---|---|
| Managed by | User library | OS kernel |
| Context switch | Faster | Slower |
| Blocking call impact | May block all threads | Blocks only calling thread |
| Parallelism on multicore | Limited | True parallelism |

### 4.2 Thread models
- Many-to-one
- One-to-one
- Many-to-many

### 4.3 Threading issues
- Race conditions
- Thread cancellation
- Thread-local storage
- Signal handling in multithreaded programs

---

## 5) CPU Scheduling

### 5.1 Scheduling criteria
- CPU utilization (maximize)
- Throughput (maximize)
- Turnaround time (minimize)
- Waiting time (minimize)
- Response time (minimize)
- Fairness

### 5.2 Key formulas
- `Turnaround Time = Completion Time - Arrival Time`
- `Waiting Time = Turnaround Time - Burst Time`
- `Response Time = First Run Time - Arrival Time`

### 5.3 Algorithms
1. **FCFS**
   - Non-preemptive
   - Simple; convoy effect

2. **SJF**
   - Non-preemptive
   - Optimal average waiting time (if burst known)

3. **SRTF**
   - Preemptive SJF
   - Better response for short jobs, possible starvation

4. **Priority Scheduling**
   - Preemptive/non-preemptive
   - Starvation possible; use aging

5. **Round Robin**
   - Time quantum based
   - Good for interactive systems

6. **Multilevel Queue / MLFQ**
   - Practical for mixed workloads

### 5.4 Real interview insight
- Small quantum in RR → better responsiveness, higher context switch overhead.
- Large quantum in RR → behaves like FCFS.

---

## 6) Process Synchronization

### 6.1 Critical section problem requirements
- Mutual exclusion
- Progress
- Bounded waiting

### 6.2 Common primitives
- Atomic instructions (`test-and-set`, `compare-and-swap`)
- Mutex
- Semaphore (binary/counting)
- Spinlock
- Monitor + condition variables

### 6.3 Semaphore operations
- `wait(P)` decrements and may block
- `signal(V)` increments and may wake blocked process

### 6.4 Classical problems
- Producer–Consumer (bounded buffer)
- Readers–Writers
- Dining Philosophers
- Sleeping Barber

### 6.5 Related concepts
- **Deadlock:** blocked forever
- **Starvation:** waits indefinitely while others progress
- **Livelock:** keeps changing state but no useful progress

---

## 7) Deadlocks

### 7.1 Necessary conditions (Coffman)
1. Mutual exclusion
2. Hold and wait
3. No preemption
4. Circular wait

### 7.2 Handling approaches
1. **Prevention:** break one Coffman condition
2. **Avoidance:** Banker's algorithm (safe state check)
3. **Detection and recovery:** detect cycle, then recover
4. **Ignore (ostrich approach):** common in practical systems for rare cases

### 7.3 Banker's algorithm (core idea)
Grant request only if system remains in safe state.

---

## 8) Memory Management

### 8.1 Address binding
- Compile-time
- Load-time
- Execution-time

### 8.2 Logical vs physical address
- Logical (virtual): generated by CPU
- Physical: actual RAM address
- Mapped by MMU

### 8.3 Contiguous allocation
- Fixed partitioning (internal fragmentation)
- Variable partitioning (external fragmentation)
- Placement: first fit, best fit, worst fit

### 8.4 Paging
- Logical memory split into pages
- Physical memory split into frames
- Page table stores mapping
- Eliminates external fragmentation

### 8.5 Segmentation
- Variable-size logical units (code/data/stack)
- Programmer-friendly view
- Can cause external fragmentation

### 8.6 Paged segmentation
Combination of segmentation + paging (conceptually important for exams)

### 8.7 TLB
- Cache of page table entries
- Improves effective memory access time

---

## 9) Virtual Memory and Page Replacement

### 9.1 Demand paging
Load page only when required.

### 9.2 Page fault handling
1. Check validity
2. Find free frame / victim frame
3. Swap in page from disk
4. Update tables
5. Restart instruction

### 9.3 Page replacement algorithms
- FIFO (Belady anomaly possible)
- LRU
- Optimal (theoretical baseline)
- LFU/MFU
- Second chance / Clock

### 9.4 Working set and thrashing
- **Working set:** pages actively used in recent interval
- **Thrashing:** too many page faults, CPU utilization drops
- Control via working-set model and reduced degree of multiprogramming

---

## 10) File Systems

### 10.1 File concept
Attributes: name, type, size, permissions, timestamps, owner.

### 10.2 Directory structures
- Single-level
- Two-level
- Tree-structured
- Acyclic/general graph

### 10.3 File allocation methods
| Method | Pros | Cons |
|---|---|---|
| Contiguous | Fast sequential/random access | External fragmentation |
| Linked | Easy growth, no external fragmentation | Poor random access |
| Indexed | Supports random access | Index overhead |

### 10.4 Free space management
- Bit map
- Linked list
- Grouping
- Counting

### 10.5 Journaling
File system logs metadata/data changes to improve crash recovery.

---

## 11) Disk Management and Scheduling

### 11.1 Disk scheduling algorithms
- FCFS
- SSTF
- SCAN
- C-SCAN
- LOOK
- C-LOOK

### 11.2 Comparison
- SSTF: good average seek, starvation possible
- SCAN family: fairer for heavy load

### 11.3 RAID basics
- RAID 0: striping (performance)
- RAID 1: mirroring (fault tolerance)
- RAID 5: striping + parity
- RAID 10: mirror + stripe

---

## 12) I/O Systems

- Programmed I/O (polling)
- Interrupt-driven I/O
- DMA (Direct Memory Access)
- Buffering, caching, spooling

---

## 13) Protection and Security

### 13.1 Protection concepts
- Access matrix
- Access control lists (ACL)
- Capability lists
- Domains of protection

### 13.2 Security concepts
- Authentication vs authorization
- Principle of least privilege
- Malware types (virus, worm, trojan, ransomware)
- Secure boot and kernel hardening

---

## 14) Virtualization and Containers

- **Virtual Machine:** hardware virtualization using hypervisor
- **Container:** OS-level isolation, shared kernel
- VM is heavier but stronger isolation; containers are lightweight and fast startup

---

## 15) Important Numericals and Exam Focus

### Common numericals
- CPU scheduling (WT, TAT, RT)
- Disk scheduling total head movement
- Page replacement page fault count
- Effective access time with TLB
- Semaphore execution ordering

### Must-prepare theory answers
- Process vs thread
- Mutex vs semaphore
- Paging vs segmentation
- Deadlock prevention vs avoidance
- FCFS vs RR vs SJF comparisons

---

## 16) Interview Q&A (High Frequency)

1. Why is context switching expensive?
2. Why do we need virtual memory?
3. How does a process differ from a thread in practical systems?
4. What is starvation and how do you fix it?
5. Explain deadlock with a real-world analogy.
6. Why is LRU preferred over FIFO in many cases?
7. What happens during a system call?
8. What are zombie and orphan processes?
9. Why do we need kernel mode and user mode?
10. What is the difference between preemptive and non-preemptive scheduling?

---

## 17) Common Mistakes in Exams/Interviews

- Mixing up **response time** and **turnaround time**
- Saying semaphore and mutex are exactly same
- Forgetting all 4 Coffman conditions
- Assuming FIFO never beats LRU (depends on reference string)
- Ignoring context switch overhead in scheduling explanations

---

## 18) Last-Minute Revision Checklist

- [ ] OS goals and services
- [ ] OS structures (monolithic/microkernel/layered)
- [ ] System call categories
- [ ] Process lifecycle + PCB
- [ ] Scheduling formulas and algorithm trade-offs
- [ ] Synchronization primitives and classical problems
- [ ] Coffman conditions + handling strategies
- [ ] Paging, segmentation, TLB
- [ ] Page replacement + thrashing
- [ ] File allocation + directory structures
- [ ] Disk scheduling algorithms
- [ ] Protection vs security
- [ ] VM vs containers

---

*Last updated: 2026 | Target: university exams, GATE, placements, and software interviews*
