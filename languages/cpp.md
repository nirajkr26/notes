# C++ — Detailed Interview, Exam & Practical Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

> **Focus:** modern C++, object model, memory, RAII, STL, templates, move semantics, concurrency, performance, exceptions, C++20/23 concepts, and interview preparation.

---

## 1. C++ Overview

C++ is a compiled, statically typed, multi-paradigm language supporting procedural programming, object-oriented programming, generic programming, and low-level systems programming.

Modern C++ emphasizes **resource safety through RAII**, value semantics, generic algorithms, and zero/low-overhead abstractions when used appropriately.

### Standard status

- **C++23** is the current published ISO C++ standard.
- **C++26** is the next standard generation and remains a moving target during the standardization process.
- Compiler support for individual features can lag the standard.

When writing portable code, distinguish “standardized” from “implemented by my compiler/version”.

---

## 2. Compilation Pipeline

A typical C++ build can be viewed as:

```text
source.cpp
   |
   v
Preprocessor
   |
   v
translation unit
   |
   v
Compiler
   |
   v
object file
   |
   v
Linker + libraries
   |
   v
executable / shared library
```

### Preprocessor

Handles directives such as:

```cpp
#include <vector>
#define SIZE 100
#if defined(DEBUG)
#endif
```

Modern C++ generally prefers language constructs, templates, `constexpr`, and modules where available over excessive macro usage.

### Translation unit

A source file after preprocessing, including the relevant included headers, forms a translation unit that the compiler processes.

### Linking

The linker resolves references across object files and libraries.

Common errors:

- **Compile error:** syntax/type/template issue.
- **Linker error:** declaration exists but a required definition/symbol cannot be resolved.
- **Runtime error/undefined behavior:** program builds but execution violates requirements.

---

## 3. Basic Types

Common fundamental types include:

```cpp
bool
char
signed char / unsigned char
short
int
long
long long
float
double
long double
void
```

Exact sizes are implementation-defined; do not blindly assume `int` is always 32 bits on every platform. `<cstdint>` provides fixed-width types when available:

```cpp
std::int32_t
std::uint64_t
```

### Signed vs unsigned

Unsigned arithmetic is modulo `2^N` for the corresponding unsigned type. Mixing signed and unsigned values can produce surprising comparisons and conversions.

```cpp
int x = -1;
unsigned int y = 1;
// x < y may not behave as beginners expect because of conversions.
```

Avoid unnecessary signed/unsigned mixing.

---

## 4. Variables, Initialization and `auto`

Prefer initialization forms that make intent clear.

```cpp
int a = 10;
int b{20};
std::vector<int> v{1, 2, 3};
```

Brace initialization helps prevent some narrowing conversions:

```cpp
// int x{3.14}; // compile error: narrowing
```

### `auto`

```cpp
auto count = 42;          // int
const auto name = "Bob";  // const char[4] expression rules apply to deduction
```

Use `auto` when the type is obvious from the initializer or would be verbose, but avoid hiding important semantic information in public interfaces.

### `decltype`

```cpp
int x = 0;
decltype(x) y = 1; // int
```

`decltype` preserves more type/reference information than ordinary `auto` deduction and is heavily used in templates.

---

## 5. References and Pointers

### Reference

```cpp
int x = 10;
int& ref = x;
ref = 20; // x becomes 20
```

A reference is an alias-like language construct and must normally be initialized.

### Pointer

```cpp
int x = 10;
int* p = &x;
*p = 20;
```

A pointer stores an address-like value and can be null.

### Pointer vs reference

| Pointer | Reference |
|---|---|
| Can be null | Normally must bind to an object |
| Can be reseated | Cannot be reseated |
| Explicit dereference | Usually implicit access |
| Pointer arithmetic possible | No pointer arithmetic |
| Useful for optional/low-level ownership/non-ownership | Useful for aliasing/function parameters |

Do not use raw pointers as an ownership signal when a smart pointer expresses ownership more safely.

---

## 6. Stack, Heap and Storage Duration

“Stack vs heap” is useful intuition but not the complete C++ object model.

Objects have storage duration such as:

- Automatic
- Static
- Thread
- Dynamic

```cpp
void f() {
  int local = 10; // automatic storage duration
}

static int counter = 0; // static storage duration

int* p = new int(42); // dynamic storage duration; prefer RAII wrappers
```

Modern C++ should rarely use naked `new`/`delete` in application code. Prefer containers and smart pointers.

---

## 7. RAII — Core Modern C++ Concept

**RAII (Resource Acquisition Is Initialization)** ties resource lifetime to object lifetime.

```cpp
class File {
public:
  explicit File(const char* path) {
    // acquire resource
  }

  ~File() {
    // release resource
  }
};
```

The resource is released automatically when the object leaves scope, including during exception unwinding.

RAII applies to:

- Memory
- Files
- Mutex locks
- Sockets
- Database connections
- OS handles

### Scope-based cleanup

```cpp
{
  std::lock_guard<std::mutex> lock(mutex);
  // critical section
} // mutex unlocked automatically
```

This is one of the most important differences between modern C++ and manual-resource programming styles.

---

## 8. `const`, `constexpr`, `consteval`

### `const`

Prevents modification through that particular object/reference binding.

```cpp
const int x = 10;
```

### `constexpr`

Declares that an expression/function can participate in constant evaluation when its arguments and context allow it.

```cpp
constexpr int square(int x) {
  return x * x;
}

constexpr int n = square(5);
```

### `consteval`

A `consteval` function must be evaluated at compile time when called in contexts where it is invoked.

These tools enable compile-time computation without resorting to macros.

---

## 9. Functions

```cpp
int add(int a, int b) {
  return a + b;
}
```

### Pass by value

```cpp
void process(User user);
```

Copies/moves the argument into the parameter.

### Pass by reference

```cpp
void process(User& user);
```

Allows modification of the caller's object.

### Pass by const reference

```cpp
void print(const User& user);
```

Avoids copying while preventing modification through that reference.

### Return by value

Modern C++ efficiently returns objects by value using copy elision and move semantics. Do not automatically return raw pointers/references just to “avoid copying”.

---

## 10. Function Overloading

Multiple functions can share a name if their parameter lists differ sufficiently.

```cpp
int add(int a, int b);
double add(double a, double b);
```

Return type alone cannot distinguish overloads.

---

## 11. Classes and Objects

```cpp
class User {
private:
  std::string name;

public:
  explicit User(std::string name)
      : name(std::move(name)) {}

  const std::string& getName() const {
    return name;
  }
};
```

### Constructor initializer list

Prefer member initialization lists. Members are initialized before the constructor body, in **declaration order**, not the order written in the initializer list.

### `explicit`

Prevents unwanted implicit conversions from single-argument constructors.

```cpp
explicit User(std::string name);
```

---

## 12. `struct` vs `class`

The main language difference is default access:

```cpp
struct Data {
  int value; // public by default
};

class User {
  int id; // private by default
};
```

Both can have methods, constructors, inheritance, templates, etc.

Use `struct` commonly for simple data-oriented types and `class` where encapsulation/invariants are central, but this is a style convention rather than a language requirement.

---

## 13. Encapsulation and Invariants

Good classes protect invariants.

```cpp
class BankAccount {
  double balance_ = 0;

public:
  void withdraw(double amount) {
    if (amount > balance_) throw std::runtime_error("insufficient funds");
    balance_ -= amount;
  }
};
```

Do not expose mutable state merely because getters/setters are easy to write. Design the interface around valid operations.

---

## 14. Inheritance and Polymorphism

```cpp
class Animal {
public:
  virtual ~Animal() = default;
  virtual void speak() const = 0;
};

class Dog : public Animal {
public:
  void speak() const override {
    std::cout << "Woof\n";
  }
};
```

### Runtime polymorphism

A base pointer/reference can refer to a derived object, and a virtual call dispatches to the appropriate override.

```cpp
std::unique_ptr<Animal> a = std::make_unique<Dog>();
a->speak();
```

### Why virtual destructor?

If a polymorphic base is destroyed through a base pointer, the base destructor should generally be virtual so the derived destructor runs correctly.

---

## 15. Virtual Functions and Vtable Intuition

A common implementation uses a **vtable** and a hidden pointer to support dynamic dispatch, although the C++ standard specifies behavior, not a required implementation.

Typical intuition:

```text
Base pointer
    |
    v
object -> vptr -> virtual function table
                    |
                    v
              Derived::speak
```

Do not claim that every C++ implementation must use exactly this layout.

---

## 16. Multiple Inheritance and Virtual Inheritance

C++ supports multiple base classes.

```cpp
class A {};
class B {};
class C : public A, public B {};
```

### Diamond problem

```text
      A
     / \
    B   C
     \ /
      D
```

If B and C each inherit A normally, D can contain two A subobjects.

Virtual inheritance can share a single A subobject:

```cpp
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {};
```

Use multiple inheritance carefully; interfaces/mixins can be safer than complex stateful inheritance graphs.

---

## 17. Rule of Three, Five and Zero

### Rule of Three

If a class manually manages a resource and needs one of:

- Destructor
- Copy constructor
- Copy assignment operator

it often needs all three.

### Rule of Five

Modern C++ adds:

- Move constructor
- Move assignment operator

### Rule of Zero

Prefer composing RAII types such as `std::string`, containers, and smart pointers so the class needs none of these special functions explicitly.

```cpp
class Buffer {
  std::vector<std::byte> data_;
};
```

This is generally safer than manually owning `new[]` memory.

---

## 18. Copy vs Move Semantics

### Copy

Creates an independent logical value.

```cpp
std::string a = "hello";
std::string b = a;
```

### Move

Transfers/moves resources from an object that is about to be discarded.

```cpp
std::string a = "hello";
std::string b = std::move(a);
```

After moving, the source remains valid but its value is generally unspecified unless the type documents more.

### Important

`std::move()` does not itself move anything. It is essentially a cast that permits move construction/assignment to be selected.

---

## 19. Value Categories

Modern C++ distinguishes expressions including:

- lvalue
- xvalue
- prvalue

and groups them into:

```text
glvalue = lvalue + xvalue
rvalue  = xvalue + prvalue
```

This machinery supports move semantics and perfect forwarding.

### Example

```cpp
std::string s = "hello";
std::string a = s;            // copy from lvalue
std::string b = std::move(s); // move from xvalue
```

---

## 20. Smart Pointers

### `unique_ptr`

Exclusive ownership.

```cpp
auto p = std::make_unique<User>("Alice");
```

Cannot be copied, but can be moved.

### `shared_ptr`

Shared ownership through reference counting.

```cpp
auto p = std::make_shared<User>("Alice");
auto q = p;
```

### `weak_ptr`

Non-owning reference to an object managed by `shared_ptr`.

Useful for breaking ownership cycles.

```text
A shared_ptr → B shared_ptr
B weak_ptr  → A
```

### Why prefer `make_unique` / `make_shared`?

They make ownership explicit and generally simplify allocation/exception safety.

### Smart pointer warning

Do not use `shared_ptr` everywhere. Shared ownership has cost and can obscure lifetime design. Prefer `unique_ptr` by default when ownership is exclusive.

---

## 21. STL Overview

The Standard Library provides containers, iterators, algorithms, utilities, strings, ranges, concurrency primitives, and more.

### Major containers

| Container | Typical strength |
|---|---|
| `vector` | Contiguous dynamic array; excellent general-purpose sequence |
| `deque` | Efficient insertion/removal at both ends |
| `list` | Stable node-based iterators; specialized use cases |
| `array` | Fixed-size contiguous array |
| `map` | Ordered key/value tree structure |
| `set` | Ordered unique keys |
| `unordered_map` | Hash table; average O(1) lookup |
| `unordered_set` | Hash set; average O(1) lookup |
| `stack` | LIFO adapter |
| `queue` | FIFO adapter |
| `priority_queue` | Heap-based priority structure |

---

## 22. `vector` Deep Dive

`std::vector` stores elements contiguously.

```cpp
std::vector<int> v;
v.push_back(10);
v.emplace_back(20);
```

Typical complexity:

- Random access: O(1)
- `push_back`: amortized O(1)
- Insert/erase in middle: O(n)

### Size vs capacity

```cpp
v.size();
v.capacity();
v.reserve(1000);
```

`reserve()` increases capacity without changing size. `resize()` changes the number of elements.

### Iterator invalidation

Reallocation can invalidate pointers, references, and iterators to vector elements. Insert/erase can also invalidate elements depending on position and whether reallocation occurs.

---

## 23. Associative Containers

### `map`

Typically implemented using a balanced tree, giving O(log n) search/insert/erase.

### `unordered_map`

Hash-based; average O(1), but worst-case behavior can be worse and depends on hashing/bucket distribution.

### Choosing

Use ordered containers when sorted traversal/order or tree semantics matter. Use unordered containers when hash lookup is appropriate and ordering is unnecessary.

---

## 24. Iterators

Iterators generalize traversal.

```cpp
for (auto it = v.begin(); it != v.end(); ++it) {
  std::cout << *it;
}
```

Modern range-based loops are usually clearer:

```cpp
for (const auto& value : v) {
  std::cout << value;
}
```

Iterator categories include input, output, forward, bidirectional, and random-access; modern C++ also has the contiguous iterator concept.

---

## 25. Algorithms

Prefer standard algorithms over hand-written loops when they express intent clearly.

```cpp
std::sort(v.begin(), v.end());
auto it = std::find(v.begin(), v.end(), target);
int total = std::accumulate(v.begin(), v.end(), 0);
```

C++20 ranges provide cleaner composition:

```cpp
auto result = values
  | std::views::filter([](int x) { return x % 2 == 0; })
  | std::views::transform([](int x) { return x * x; });
```

Ranges are often lazy: the view describes computation rather than immediately creating a new container.

---

## 26. Lambdas

```cpp
auto square = [](int x) { return x * x; };
```

### Captures

```cpp
int factor = 2;

auto byValue = [factor](int x) { return x * factor; };
auto byRef = [&factor](int x) { return x * factor; };
```

Be careful when returning/storing lambdas that capture references to objects whose lifetime may end.

### Generic lambdas

```cpp
auto equal = [](const auto& a, const auto& b) {
  return a == b;
};
```

---

## 27. Templates

Templates enable generic programming.

```cpp
template <typename T>
T maximum(T a, T b) {
  return a > b ? a : b;
}
```

Templates are instantiated for concrete types, subject to language rules and constraints.

### Function vs class templates

```cpp
template <typename T>
class Box {
  T value;
};
```

### Non-type template parameters

```cpp
template <std::size_t N>
struct Buffer {
  std::array<int, N> data;
};
```

---

## 28. Concepts

C++20 concepts constrain templates more explicitly.

```cpp
template <typename T>
requires std::integral<T>
T add(T a, T b) {
  return a + b;
}
```

Or:

```cpp
template <std::integral T>
T add(T a, T b) {
  return a + b;
}
```

Benefits:

- Clearer intent.
- Better diagnostics than many unconstrained template failures.
- More expressive generic interfaces.

---

## 29. Exceptions

```cpp
try {
  process();
} catch (const std::exception& e) {
  std::cerr << e.what();
}
```

Throw by value:

```cpp
throw std::runtime_error("invalid state");
```

Catch polymorphic exceptions by `const` reference.

### RAII + exceptions

During stack unwinding, automatic objects are destroyed. RAII therefore provides deterministic cleanup even when exceptions occur.

Avoid using exceptions for ordinary expected control flow when a simpler return/error type is more appropriate for the system design.

---

## 30. `noexcept`

```cpp
void close() noexcept;
```

`noexcept` communicates that a function does not throw under its contract. It can affect generic code and move operations.

For example, standard containers may prefer a `noexcept` move constructor when deciding whether moving elements is safe during reallocation.

---

## 31. Namespaces and ODR

Namespaces prevent name collisions.

```cpp
namespace math {
  int add(int a, int b) { return a + b; }
}
```

Avoid `using namespace std;` in headers because it pollutes users' namespaces.

### One Definition Rule

C++ has rules governing how declarations and definitions may appear across translation units. Header design, `inline`, templates, and linkage all interact with the ODR.

A practical rule: declarations belong in headers, non-inline ordinary definitions usually belong in one source file, and templates generally need definitions visible where instantiated.

---

## 32. Header Guards and `#pragma once`

Classic:

```cpp
#ifndef USER_H
#define USER_H
// declarations
#endif
```

Many compilers support:

```cpp
#pragma once
```

It is widely used but technically compiler-supported rather than historically part of the ISO C++ standard.

---

## 33. `std::optional`, `variant`, `any`

### Optional

Represents a value that may be absent.

```cpp
std::optional<int> findId();
```

### Variant

Type-safe discriminated union.

```cpp
std::variant<int, std::string> value;
```

### Any

Can contain a value of arbitrary type that satisfies its runtime storage requirements.

Use `variant` when the set of alternatives is known; `any` when genuinely open-ended type erasure is needed.

---

## 34. `string_view` and `span`

`std::string_view` is a non-owning view of character data.

```cpp
void print(std::string_view text);
```

It avoids copying but requires the underlying characters to outlive the view.

`std::span<T>` is a non-owning view over contiguous objects.

```cpp
void process(std::span<const int> values);
```

These are useful for flexible APIs without transferring ownership.

---

## 35. Concurrency

C++ provides standard threading primitives.

```cpp
std::thread t([] {
  // work
});
t.join();
```

Modern code often prefers `std::jthread`, which supports cooperative stopping and automatically joins when destroyed.

### Mutex

```cpp
std::mutex m;
std::lock_guard lock(m);
```

### `unique_lock`

Provides more flexible lock management, including deferred locking and manual unlock/relock.

### Condition variable

```cpp
std::condition_variable cv;
std::mutex m;
std::queue<int> q;
```

Use a predicate with `wait` to guard against spurious wakeups.

---

## 36. Atomics and Memory Ordering

```cpp
std::atomic<int> counter{0};
counter.fetch_add(1);
```

Atomics provide operations that participate in the C++ memory model.

Memory orders include:

- `relaxed`
- `acquire`
- `release`
- `acq_rel`
- `seq_cst`

### Intuition

`relaxed` provides atomicity without the stronger synchronization ordering of acquire/release.

A release operation can publish prior writes, and an acquire operation that reads the corresponding value can establish synchronization.

Use `seq_cst` when simple global ordering is more valuable than advanced optimization, and use weaker orderings only when you understand the memory model and have evidence they matter.

---

## 37. Data Race vs Race Condition

A **data race** in C++ is a specific memory-model violation involving conflicting unsynchronized accesses to the same memory location where at least one is a write; it results in undefined behavior.

A **race condition** is broader: correctness depends on timing/order and may exist even without a C++ data race.

This distinction is important in interviews.

---

## 38. Futures, Promises and Async

```cpp
auto future = std::async(std::launch::async, [] {
  return expensive_work();
});

auto result = future.get();
```

`std::future` represents a result that can become ready later. `std::promise` can be used by one execution context to provide a value/error to a future.

Do not assume `std::async` always creates a dedicated OS thread unless the launch policy explicitly requires asynchronous execution.

---

## 39. Smart Ownership Design

A useful ownership hierarchy:

```text
Does this object own the resource?
        |
        +-- no --> reference / pointer / view
        |
        +-- yes --> exclusive? --> unique_ptr
                    |
                    +--> shared ownership? --> shared_ptr
```

Use `weak_ptr` for observing shared ownership without extending lifetime.

Prefer values and RAII where possible.

---

## 40. Undefined Behavior

Undefined behavior (UB) means the C++ standard imposes no requirements on the program's behavior after the violation.

Examples:

```cpp
int* p = nullptr;
*p = 1; // UB
```

Other examples include out-of-bounds access, signed integer overflow, invalid lifetime use, and data races.

UB is not the same as “the program crashes”. It may appear to work, be optimized into unexpected behavior, or change between builds.

---

## 41. Common Performance Concepts

### Cache locality

Contiguous structures such as `vector` often perform well because neighboring elements can exploit spatial locality.

### Reserve capacity

```cpp
v.reserve(expected_size);
```

Can reduce repeated reallocations when growth is predictable.

### Avoid premature optimization

First choose the right algorithm/data structure. Measure before changing code for speculative micro-optimizations.

### Move vs copy

Moves can avoid expensive resource duplication, but small types may already be cheap and modern compilers often eliminate unnecessary copies.

---

## 42. Common Interview Questions

### Q1. Why is C++ called statically typed?

Types are checked as part of compilation and expressions have compile-time type information, although runtime polymorphism and dynamic allocation still exist.

### Q2. Pointer vs reference?

Pointers can be null and reseated; references are alias-like bindings that normally must be initialized and cannot be reseated.

### Q3. Stack vs heap?

A simplified distinction between automatic and dynamically allocated storage. The language defines storage duration and lifetime more precisely than the simplistic “stack/heap” terminology.

### Q4. What is RAII?

Tie resource lifetime to object lifetime so destruction performs cleanup automatically and safely during scope exit/exception unwinding.

### Q5. Why prefer smart pointers?

They make ownership explicit and automate resource lifetime, reducing leaks and double deletes.

### Q6. `unique_ptr` vs `shared_ptr`?

`unique_ptr` represents exclusive ownership. `shared_ptr` represents shared ownership through reference counting and has extra overhead/complexity.

### Q7. Why use `weak_ptr`?

To observe a `shared_ptr`-managed object without owning it, especially to break reference cycles.

### Q8. What does `std::move` do?

It casts an expression to an xvalue so move-enabled overloads can be selected. It does not itself perform the move.

### Q9. What is a moved-from object?

A valid object whose value is generally unspecified unless its type documents a stronger post-move state.

### Q10. Rule of three/five/zero?

Resource-owning types may need special copy/move/destructor operations; RAII composition enables the preferred rule of zero.

### Q11. Why should a polymorphic base destructor be virtual?

So deleting/destroying through a base interface performs the correct derived destruction.

### Q12. What is virtual dispatch?

Runtime selection of an overridden virtual function based on the dynamic type of the object.

### Q13. `struct` vs `class`?

Same capabilities; `struct` defaults to public members/inheritance while `class` defaults to private.

### Q14. `vector` vs `list`?

`vector` provides contiguous storage and excellent cache locality; `list` has node allocation overhead and is useful only for specialized stable-node operations.

### Q15. `map` vs `unordered_map`?

Ordered tree-based lookup is typically O(log n); hash-based lookup is average O(1) but unordered and sensitive to hashing/buckets.

### Q16. What is iterator invalidation?

Operations on containers can invalidate iterators/references/pointers, making previously obtained handles unsafe to use. Rules depend on the container and operation.

### Q17. What is a data race?

A C++ memory-model violation caused by conflicting unsynchronized accesses to the same memory location with at least one write.

### Q18. Why use `atomic`?

To perform synchronization-safe atomic operations on shared state without a mutex for suitable problems.

### Q19. What is `constexpr`?

It enables constant evaluation when the expression/function and context satisfy compile-time evaluation rules.

### Q20. `constexpr` vs `consteval`?

`constexpr` permits compile-time evaluation but can also be used at runtime; `consteval` requires compile-time evaluation for calls that use it.

### Q21. Why use `noexcept` on move operations?

It can allow generic/container code to prefer moving because a throwing move can otherwise make strong exception guarantees difficult.

### Q22. What is object slicing?

Copying a derived object into a base object by value discards the derived portion.

```cpp
Derived d;
Base b = d; // slicing
```

Use references/pointers for polymorphic behavior.

### Q23. What is the diamond problem?

Multiple inheritance can create duplicate base subobjects. Virtual inheritance can represent one shared virtual base subobject.

### Q24. Why is `new/delete` discouraged in modern C++?

Manual ownership is error-prone. Containers and smart pointers provide safer lifetime management.

### Q25. What is undefined behavior?

A standard violation for which the C++ standard imposes no requirements. It must not be treated as a predictable runtime error.

---

## 43. Quick Revision Checklist

- [ ] Compilation/linking
- [ ] Fundamental types and conversions
- [ ] `const`, `constexpr`, `consteval`
- [ ] Pointers/references
- [ ] Storage duration/lifetime
- [ ] RAII
- [ ] Classes/constructors/destructors
- [ ] Inheritance/polymorphism
- [ ] Virtual functions/destructors
- [ ] Rule of 3/5/0
- [ ] Copy/move semantics
- [ ] Value categories
- [ ] Smart pointers
- [ ] STL containers
- [ ] Iterator invalidation
- [ ] Algorithms/ranges
- [ ] Lambdas
- [ ] Templates/concepts
- [ ] Exceptions/`noexcept`
- [ ] ODR/linkage/headers
- [ ] `optional`/`variant`/`any`
- [ ] `string_view`/`span`
- [ ] Threads/mutex/condition variables
- [ ] Atomics/memory ordering
- [ ] Data races/UB
- [ ] Performance/cache locality
- [ ] Modern C++ interview questions
