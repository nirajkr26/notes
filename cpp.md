# C++ — Super Detailed Notes for Exams & Technical Interviews

---

## 1) C++ Overview

C++ is a high-performance, statically typed, compiled language supporting procedural, object-oriented, and generic programming.

### Why C++ in interviews?
- Fine-grained memory control
- STL and algorithmic performance
- System-level and performance-critical development

---

## 2) Compilation Pipeline

1. Preprocessing
2. Compilation
3. Assembly
4. Linking

### Header/source model
- Declarations in `.h/.hpp`
- Definitions in `.cpp`

---

## 3) Core Language Basics

- Data types, signed/unsigned
- `const`, `constexpr`
- References (`&`) vs pointers (`*`)
- Lvalue/rvalue fundamentals
- Scope, storage duration, linkage

---

## 4) OOP in C++

- Class and object
- Access specifiers
- Constructors/destructor
- Copy constructor and assignment
- Inheritance and polymorphism
- Virtual functions and runtime dispatch

### Rule of 3 / 5 / 0
- Rule of 3: destructor, copy ctor, copy assignment
- Rule of 5 adds move ctor and move assignment
- Rule of 0: rely on RAII-managed members

---

## 5) Memory Management

### Stack vs heap
- Stack: automatic storage
- Heap: dynamic storage via `new/delete`

### Smart pointers (must know)
- `std::unique_ptr`
- `std::shared_ptr`
- `std::weak_ptr`

### RAII
Acquire resources in constructors and release in destructors.

---

## 6) Templates and Generic Programming

- Function templates
- Class templates
- Template specialization
- Variadic templates
- Concepts (C++20)

---

## 7) STL Deep Dive

### Containers
- Sequence: `vector`, `deque`, `list`, `array`
- Associative: `set`, `map`, `multiset`, `multimap`
- Unordered: `unordered_set`, `unordered_map`
- Adaptors: `stack`, `queue`, `priority_queue`

### Iterators
- Input, output, forward, bidirectional, random access

### Algorithms
- `sort`, `binary_search`, `lower_bound`, `upper_bound`
- `find`, `count`, `accumulate`, `for_each`

### Complexity examples
- `vector` push_back amortized O(1)
- `map` insert/find O(log n)
- `unordered_map` average O(1)

---

## 8) Modern C++ (C++11 and later)

- `auto`, `decltype`
- Range-based for loop
- Lambda expressions
- Move semantics
- `emplace_back`
- `nullptr`
- `enum class`
- Structured bindings (C++17)
- `std::optional`, `std::variant`, `std::any`
- Coroutines/modules (advanced, modern compilers)

---

## 9) Exception Handling

- `try`, `catch`, `throw`
- Stack unwinding
- Prefer exception-safe RAII design
- Guarantee levels: basic, strong, no-throw

---

## 10) Concurrency in C++

- `std::thread`
- `std::mutex`, `std::lock_guard`
- `std::unique_lock`
- `std::condition_variable`
- `std::atomic`
- Deadlock prevention by lock ordering

---

## 11) C++ Object Model (Interview Core)

- Vtable/vptr basics
- Object slicing
- Virtual destructor importance for base pointers
- Multiple inheritance and virtual inheritance

---

## 12) Common Pitfalls

- Dangling pointers/references
- Double delete
- Memory leaks
- Undefined behavior (UB)
- Iterator invalidation
- Returning references to local variables

---

## 13) Frequently Asked Interview Questions

1. `struct` vs `class`?
2. Deep copy vs shallow copy?
3. Why virtual destructor?
4. `new/delete` vs smart pointers?
5. `map` vs `unordered_map`?
6. `emplace_back` vs `push_back`?
7. Lvalue vs rvalue?
8. Move semantics explanation?
9. `const` correctness?
10. What is undefined behavior?

---

## 14) Exam-Focused Theory

- Difference between compile-time and runtime polymorphism
- Inline functions and macros
- Friend function/class
- Operator overloading rules
- Namespace and scope resolution
- Static member behavior

---

## 15) C++ Quick Revision Checklist

- [ ] Rule of 3/5/0
- [ ] RAII and smart pointers
- [ ] Templates and STL categories
- [ ] OOP and virtual dispatch
- [ ] Move semantics
- [ ] Exception safety basics
- [ ] Threading primitives
- [ ] Common UB cases

---

*Last updated: 2026 | Target: C++ interviews, DSA rounds, and systems programming exams*
