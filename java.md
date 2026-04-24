# Java — Super Detailed Notes for Exams & Technical Interviews

---

## 1) Java Overview

Java is a class-based, object-oriented language designed for portability via JVM.

### WORA
**Write Once, Run Anywhere** through bytecode execution on JVM.

---

## 2) Java Platform

- JDK: development kit (compiler + tools + JRE)
- JRE: runtime environment
- JVM: executes bytecode

### Execution flow
`.java` -> `javac` -> `.class` bytecode -> JVM executes

---

## 3) Core Java Syntax and Concepts

- Primitive vs reference types
- Wrapper classes and autoboxing
- Type casting (widening/narrowing)
- Operators and control flow
- Arrays and strings

---

## 4) OOP in Java

- Class/object
- Encapsulation via access modifiers
- Inheritance with `extends`
- Polymorphism with overriding
- Abstraction via abstract class/interface

### Access modifiers
- private
- default
- protected
- public

---

## 5) Important Keywords

- `this`, `super`
- `static`
- `final`
- `abstract`
- `synchronized`
- `volatile`
- `transient`

---

## 6) String and Immutability

- `String` is immutable
- `StringBuilder` mutable and efficient for frequent concatenation
- String pool concept

---

## 7) Exception Handling

- Checked exceptions
- Unchecked exceptions
- `try-catch-finally`
- `throw` vs `throws`
- Custom exceptions

---

## 8) Collections Framework

### Interfaces
- `List`, `Set`, `Queue`, `Map`

### Implementations
- `ArrayList`, `LinkedList`
- `HashSet`, `TreeSet`
- `PriorityQueue`
- `HashMap`, `LinkedHashMap`, `TreeMap`, `ConcurrentHashMap`

### Comparison highlights
- HashMap O(1) average operations
- TreeMap O(log n), sorted keys
- LinkedHashMap preserves insertion/access order

---

## 9) Generics

- Type safety at compile time
- Type erasure in runtime
- Wildcards: `?`, `? extends T`, `? super T`

---

## 10) Multithreading and Concurrency

- Thread creation (`Thread`, `Runnable`, `Callable`)
- Executor framework
- Synchronization and intrinsic locks
- Deadlock, starvation, livelock
- Volatile vs synchronized
- Concurrent collections

---

## 11) Java Memory Model and JVM Internals

### Runtime areas
- Heap
- Stack
- Method area/Metaspace
- PC register
- Native method stack

### Garbage collection
- Young/old generations
- Minor vs major GC
- GC algorithms (mark-sweep, G1, etc.)

---

## 12) Java 8+ Features

- Lambda expressions
- Functional interfaces
- Streams API
- Method references
- Optional
- Default/static methods in interfaces

---

## 13) I/O and Serialization

- Byte streams vs character streams
- NIO basics
- Serialization (`Serializable`)
- `transient` for non-serializable fields

---

## 14) JDBC Basics

- Driver, connection, statement, result set
- PreparedStatement to prevent SQL injection
- Transactions with commit/rollback

---

## 15) Frequently Asked Interview Questions

1. JDK vs JRE vs JVM?
2. Why is String immutable?
3. HashMap internal working?
4. equals vs == ?
5. final/finally/finalize?
6. volatile vs synchronized?
7. Interface vs abstract class?
8. Checked vs unchecked exceptions?
9. Fail-fast vs fail-safe iterators?
10. How does garbage collection work?

---

## 16) Exam-Focused Topics

- OOP pillars in Java terms
- Access modifiers table
- Constructor chaining and inheritance
- Collections comparison
- Exception hierarchy
- Threads and synchronization states
- Java memory model basics

---

## 17) Java Quick Revision Checklist

- [ ] JVM/JRE/JDK
- [ ] OOP + modifiers
- [ ] String immutability
- [ ] Collections and complexity
- [ ] Exception handling rules
- [ ] Multithreading primitives
- [ ] Java 8 stream/lambda concepts
- [ ] GC and memory areas

---

*Last updated: 2026 | Target: Java interviews, semester exams, and backend fundamentals*
