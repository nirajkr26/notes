# C++ — Super Detailed Interview & Exam Notes

---

## 1. Introduction to C++

C++ is a **general-purpose, statically typed, compiled, multi-paradigm** programming language created by **Bjarne Stroustrup** in 1979 as an extension of C. It supports procedural, object-oriented, and generic programming.

**Key Features:**
- High performance (close to hardware)
- Object-Oriented Programming support
- Templates & Generic Programming
- Standard Template Library (STL)
- Manual memory management + smart pointers
- Deterministic destructors (RAII)

**Compilation pipeline:**
```
Source (.cpp) → Preprocessor → Compiler → Assembler → Linker → Executable
```

**C++ Standards Timeline:**

| Standard | Year | Notable Features |
|----------|------|-----------------|
| C++98/03 | 1998/2003 | Core language, STL |
| C++11    | 2011 | auto, lambda, move semantics, threads |
| C++14    | 2014 | Generic lambdas, relaxed constexpr |
| C++17    | 2017 | Structured bindings, std::optional, if constexpr |
| C++20    | 2020 | Concepts, Ranges, Coroutines, Modules |
| C++23    | 2023 | std::expected, std::print, deducing this |

---

## 2. Basic Syntax and Structure

```cpp
#include <iostream>   // Preprocessor directive
using namespace std;  // Bring std into scope

int main() {          // Entry point; returns int
    cout << "Hello, World!" << endl;
    return 0;         // 0 = success
}
```

**Preprocessor Directives:**
- `#include` — include header files
- `#define` — macro definition
- `#ifdef / #ifndef / #endif` — conditional compilation
- `#pragma once` — include guard (modern alternative)

---

## 3. Data Types and Variables

### 3.1 Fundamental Types

| Type | Size (typical) | Range |
|------|---------------|-------|
| `bool` | 1 byte | true / false |
| `char` | 1 byte | -128 to 127 |
| `int` | 4 bytes | -2^31 to 2^31-1 |
| `long long` | 8 bytes | -2^63 to 2^63-1 |
| `float` | 4 bytes | ~7 decimal digits precision |
| `double` | 8 bytes | ~15 decimal digits precision |
| `void` | — | No value |

**Modifiers:** `signed`, `unsigned`, `short`, `long`

### 3.2 Type Qualifiers

| Qualifier | Meaning |
|-----------|---------|
| `const` | Value cannot be changed after initialization |
| `volatile` | Value may change externally (no optimization) |
| `mutable` | Can be modified inside `const` member function |
| `constexpr` (C++11) | Evaluated at compile time |

### 3.3 Variable Scope

- **Local** — inside a function/block; stack-allocated
- **Global** — outside all functions; lives for program duration
- **Static local** — retains value across function calls; initialized once
- **Register** — hint to compiler to use CPU register (ignored in modern C++)

```cpp
int globalVar = 10;           // global

void func() {
    int localVar = 5;          // local
    static int count = 0;      // static local
    count++;
}
```

---

## 4. Operators

| Category | Operators |
|----------|-----------|
| Arithmetic | `+  -  *  /  %` |
| Relational | `==  !=  <  >  <=  >=` |
| Logical | `&&  \|\|  !` |
| Bitwise | `&  \|  ^  ~  <<  >>` |
| Assignment | `=  +=  -=  *=  /=  %=  &=  \|=  ^=  <<=  >>=` |
| Increment/Decrement | `++  --` (pre and post) |
| Ternary | `condition ? true_val : false_val` |
| Comma | `,` |
| Sizeof | `sizeof(type)` |
| Scope resolution | `::` |
| Member access | `.  ->` |
| Pointer | `*  &` |

**Operator Precedence (high → low, partial):**
```
::  >  ()[]->  >  ++-- (post)  >  ++-- (pre) * & ! ~  >  * / %  >  + -  >  << >>  >  < > <= >=  >  == !=  >  & > ^ > | > && > || > ?: > = += -= ...
```

---

## 5. Control Flow

### 5.1 Conditionals
```cpp
if (x > 0) { /* ... */ }
else if (x == 0) { /* ... */ }
else { /* ... */ }

switch (x) {
    case 1: cout << "one"; break;
    case 2: cout << "two"; break;
    default: cout << "other";
}
```

### 5.2 Loops
```cpp
for (int i = 0; i < 10; i++) { /* ... */ }

while (condition) { /* ... */ }

do { /* ... */ } while (condition);

// Range-based for (C++11)
for (auto& elem : container) { /* ... */ }
```

### 5.3 Jump Statements
- `break` — exit loop/switch
- `continue` — skip to next iteration
- `return` — exit function
- `goto` — unconditional jump (avoid!)

---

## 6. Functions

### 6.1 Function Basics
```cpp
returnType functionName(paramType param1, paramType param2) {
    // body
    return value;
}
```

### 6.2 Default Arguments
```cpp
void print(int x, int y = 10) { cout << x << " " << y; }
print(5);       // 5 10
print(5, 20);   // 5 20
```

### 6.3 Function Overloading
```cpp
int add(int a, int b) { return a + b; }
double add(double a, double b) { return a + b; }
// Resolved at compile time based on argument types
```

### 6.4 Inline Functions
```cpp
inline int square(int x) { return x * x; }
// Compiler may substitute function body at call site (avoids call overhead)
```

### 6.5 Pass by Value vs Reference vs Pointer
```cpp
void byValue(int x) { x = 100; }         // original unchanged
void byRef(int& x) { x = 100; }          // original changed
void byPtr(int* x) { *x = 100; }         // original changed via pointer

int n = 5;
byValue(n);   // n = 5
byRef(n);     // n = 100
byPtr(&n);    // n = 100
```

### 6.6 Recursion
```cpp
int factorial(int n) {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}
```

### 6.7 Lambda Functions (C++11)
```cpp
// [capture](params) -> return_type { body }
auto add = [](int a, int b) -> int { return a + b; };
cout << add(3, 4);  // 7

int x = 10;
auto addX = [x](int a) { return a + x; };  // capture by value
auto addXRef = [&x](int a) { return a + x; };  // capture by reference
```

---

## 7. Pointers and References

### 7.1 Pointers
A pointer stores the **memory address** of another variable.

```cpp
int x = 42;
int* ptr = &x;   // ptr holds address of x

cout << ptr;    // address (e.g., 0x7ffd...)
cout << *ptr;   // 42 (dereference)

*ptr = 100;     // x is now 100
```

**Pointer Arithmetic:**
```cpp
int arr[] = {10, 20, 30};
int* p = arr;         // points to arr[0]
p++;                  // now points to arr[1]
cout << *(p + 1);     // arr[2] = 30
```

**Null Pointer:**
```cpp
int* p = nullptr;    // C++11 (preferred over NULL or 0)
if (p == nullptr) { /* safe check */ }
```

**Pointer to Pointer:**
```cpp
int x = 5;
int* p = &x;
int** pp = &p;        // pointer to pointer
cout << **pp;         // 5
```

**`const` with Pointers:**
```cpp
const int* p = &x;    // pointer to const int (cannot change *p)
int* const p = &x;    // const pointer to int (cannot change p itself)
const int* const p = &x;  // both const
```

### 7.2 References
A reference is an **alias** for an existing variable. Must be initialized at declaration; cannot be re-bound.

```cpp
int x = 10;
int& ref = x;   // ref is alias for x
ref = 20;       // x is now 20
```

**Reference vs Pointer:**

| Feature | Reference | Pointer |
|---------|-----------|---------|
| Must be initialized | ✅ Yes | ❌ No |
| Can be null | ❌ No | ✅ Yes |
| Can be re-assigned | ❌ No | ✅ Yes |
| Syntax to access | Direct (no `*`) | Dereference with `*` |
| Use case | Safer alternative, pass params | Dynamic memory, arrays |

---

## 8. Arrays and Strings

### 8.1 Arrays
```cpp
int arr[5] = {1, 2, 3, 4, 5};
int matrix[3][3] = {{1,2,3},{4,5,6},{7,8,9}};

// Dynamic array
int* dynArr = new int[10];
delete[] dynArr;  // must free!
```

### 8.2 C-style Strings
```cpp
char str[] = "Hello";       // null-terminated char array
char* s = "World";          // pointer to string literal (read-only!)
strlen(str);                // length without null
strcpy(dest, src);          // copy
strcat(dest, src);          // concatenate
strcmp(s1, s2);             // compare (0 if equal)
```

### 8.3 std::string (C++)
```cpp
#include <string>
string s = "Hello";
s.length();          // 5
s.substr(1, 3);      // "ell"
s.find("ll");        // 2
s += " World";       // concatenation
s.empty();           // false
s[0];                // 'H'
stoi("42");          // string to int
to_string(42);       // int to string
```

---

## 9. Memory Management

### 9.1 Stack vs Heap

| Aspect | Stack | Heap |
|--------|-------|------|
| Allocation | Automatic (compiler) | Manual (`new`/`delete`) |
| Speed | Fast (just move SP) | Slower (allocator overhead) |
| Size | Limited (typically 1–8 MB) | Large (available RAM) |
| Lifetime | Function scope | Until explicitly freed |
| Fragmentation | No | Possible |

### 9.2 Dynamic Memory with `new`/`delete`
```cpp
int* p = new int(42);          // allocate single int
delete p;                       // free

int* arr = new int[10];        // allocate array
delete[] arr;                   // free array (use delete[], not delete)

// Always set to nullptr after delete to avoid dangling pointer
p = nullptr;
```

**Common Memory Bugs:**
- **Memory leak** — allocated memory never freed
- **Dangling pointer** — pointer to freed memory
- **Double free** — freeing same memory twice
- **Buffer overflow** — writing beyond array bounds
- **Wild pointer** — uninitialized pointer

### 9.3 Smart Pointers (C++11) — `<memory>`

> Smart pointers automate memory management using RAII (Resource Acquisition Is Initialization).

#### `unique_ptr` — sole ownership
```cpp
unique_ptr<int> p = make_unique<int>(42);
// Automatically deleted when p goes out of scope
// Cannot be copied; can be moved
unique_ptr<int> p2 = move(p);  // ownership transferred
```

#### `shared_ptr` — shared ownership (reference-counted)
```cpp
shared_ptr<int> p1 = make_shared<int>(42);
shared_ptr<int> p2 = p1;   // ref count = 2
// Deleted when last shared_ptr is destroyed (ref count = 0)
p1.use_count();             // 2
```

#### `weak_ptr` — non-owning reference (breaks circular references)
```cpp
weak_ptr<int> wp = p1;
if (auto sp = wp.lock()) {  // lock() returns shared_ptr or nullptr
    cout << *sp;
}
```

**When to use:**
- `unique_ptr` — default choice; single owner (e.g., factory-returned objects)
- `shared_ptr` — multiple owners (e.g., shared cache entries)
- `weak_ptr` — observer/cache; avoids circular ownership

---

## 10. Object-Oriented Programming in C++

### 10.1 Classes and Objects
```cpp
class Rectangle {
private:
    int width, height;     // data members

public:
    // Constructor
    Rectangle(int w, int h) : width(w), height(h) {}

    // Member function
    int area() const { return width * height; }

    // Destructor
    ~Rectangle() { cout << "Destroyed\n"; }
};

Rectangle r(4, 5);
cout << r.area();   // 20
```

### 10.2 Constructors and Destructors
```cpp
class MyClass {
    int x;
public:
    MyClass() : x(0) {}                     // Default constructor
    MyClass(int val) : x(val) {}            // Parameterized constructor
    MyClass(const MyClass& other) : x(other.x) {}  // Copy constructor
    MyClass(MyClass&& other) noexcept : x(other.x) { other.x = 0; }  // Move constructor (C++11)
    ~MyClass() {}                            // Destructor
};
```

**Rule of Three / Five / Zero:**
- **Rule of Three:** If you define destructor, copy constructor, or copy assignment — define all three.
- **Rule of Five (C++11):** Add move constructor and move assignment operator.
- **Rule of Zero:** Prefer smart pointers/containers so you don't need any.

### 10.3 Inheritance
```cpp
class Animal {
public:
    string name;
    Animal(string n) : name(n) {}
    virtual void speak() { cout << "..."; }  // virtual for polymorphism
    virtual ~Animal() {}                      // virtual destructor (important!)
};

class Dog : public Animal {
public:
    Dog(string n) : Animal(n) {}
    void speak() override { cout << "Woof!"; }  // override keyword (C++11)
};

Animal* a = new Dog("Rex");
a->speak();     // "Woof!" — dynamic dispatch
delete a;       // calls Dog's destructor (because virtual destructor)
```

**Inheritance Access Specifiers:**

| Base member | `public` inherit | `protected` inherit | `private` inherit |
|-------------|-----------------|---------------------|-------------------|
| `public`    | `public`        | `protected`         | `private`         |
| `protected` | `protected`     | `protected`         | `private`         |
| `private`   | inaccessible    | inaccessible        | inaccessible      |

### 10.4 Virtual Functions and Polymorphism
```cpp
class Shape {
public:
    virtual double area() = 0;    // pure virtual function → abstract class
    virtual ~Shape() {}
};

class Circle : public Shape {
    double r;
public:
    Circle(double r) : r(r) {}
    double area() override { return 3.14159 * r * r; }
};
```

**vtable (Virtual Table):**
- Each class with virtual functions has a hidden `vtable` (array of function pointers)
- Each object has a `vptr` (pointer to its class's vtable)
- Virtual dispatch: `obj->method()` → lookup vtable → call correct function
- Cost: one extra pointer per object + one indirect call per virtual call

### 10.5 Multiple Inheritance and Diamond Problem
```cpp
class A { public: virtual void show() { cout << "A"; } };
class B : virtual public A {};   // virtual inheritance
class C : virtual public A {};
class D : public B, public C {}; // D has only one copy of A

D d;
d.show();  // works — no ambiguity
```

### 10.6 Operator Overloading
```cpp
class Vector {
public:
    int x, y;
    Vector(int x, int y) : x(x), y(y) {}

    // Member operator overload
    Vector operator+(const Vector& other) const {
        return Vector(x + other.x, y + other.y);
    }

    // Friend function for << overloading
    friend ostream& operator<<(ostream& os, const Vector& v) {
        os << "(" << v.x << ", " << v.y << ")";
        return os;
    }
};

Vector v1(1, 2), v2(3, 4);
cout << v1 + v2;   // (4, 6)
```

**Non-overloadable operators:** `::`  `.`  `.*`  `?:`  `sizeof`

### 10.7 Friend Functions and Classes
```cpp
class MyClass {
    int secret = 42;
    friend void reveal(MyClass& obj);   // friend function
    friend class Inspector;              // friend class
};

void reveal(MyClass& obj) { cout << obj.secret; }  // can access private
```

### 10.8 `static` Members
```cpp
class Counter {
    static int count;       // shared across all instances
public:
    Counter() { count++; }
    static int getCount() { return count; }
};
int Counter::count = 0;    // definition outside class

Counter c1, c2, c3;
cout << Counter::getCount();  // 3
```

### 10.9 `const` Member Functions
```cpp
class Circle {
    double radius;
public:
    double getRadius() const { return radius; }  // promises not to modify object
    // Cannot call non-const methods inside const method
};
```

---

## 11. Templates

Templates allow writing **generic** code that works with any type.

### 11.1 Function Templates
```cpp
template <typename T>
T max(T a, T b) { return (a > b) ? a : b; }

cout << max(3, 5);      // int version
cout << max(3.1, 5.2);  // double version
```

### 11.2 Class Templates
```cpp
template <typename T>
class Stack {
    vector<T> data;
public:
    void push(T val) { data.push_back(val); }
    T pop() {
        T top = data.back();
        data.pop_back();
        return top;
    }
};

Stack<int> s;
s.push(1); s.push(2);
cout << s.pop();  // 2
```

### 11.3 Template Specialization
```cpp
template <typename T>
T stringify(T val) { return val; }

template <>
string stringify<bool>(bool val) { return val ? "true" : "false"; }
```

### 11.4 Variadic Templates (C++11)
```cpp
template <typename... Args>
void print(Args... args) {
    (cout << ... << args);  // fold expression (C++17)
}
print(1, " hello ", 3.14);  // 1 hello 3.14
```

---

## 12. Standard Template Library (STL)

### 12.1 Containers

| Container | Header | Description | Access | Insert | Delete |
|-----------|--------|-------------|--------|--------|--------|
| `vector` | `<vector>` | Dynamic array | O(1) | O(1) amortized | O(n) |
| `list` | `<list>` | Doubly linked list | O(n) | O(1) | O(1) |
| `deque` | `<deque>` | Double-ended queue | O(1) | O(1) both ends | O(n) |
| `stack` | `<stack>` | LIFO (adaptor) | top O(1) | push O(1) | pop O(1) |
| `queue` | `<queue>` | FIFO (adaptor) | front O(1) | push O(1) | pop O(1) |
| `priority_queue` | `<queue>` | Max-heap by default | top O(1) | O(log n) | O(log n) |
| `set` | `<set>` | Sorted unique keys (BST) | O(log n) | O(log n) | O(log n) |
| `multiset` | `<set>` | Like set, allows duplicates | O(log n) | O(log n) | O(log n) |
| `map` | `<map>` | Sorted key-value pairs | O(log n) | O(log n) | O(log n) |
| `unordered_set` | `<unordered_set>` | Hash-based unique keys | O(1) avg | O(1) avg | O(1) avg |
| `unordered_map` | `<unordered_map>` | Hash-based key-value | O(1) avg | O(1) avg | O(1) avg |
| `array` | `<array>` | Fixed-size array | O(1) | N/A | N/A |
| `bitset` | `<bitset>` | Fixed-size bit array | O(1) | N/A | N/A |

**vector — most commonly used:**
```cpp
#include <vector>
vector<int> v = {1, 2, 3};
v.push_back(4);       // [1,2,3,4]
v.pop_back();         // [1,2,3]
v.size();             // 3
v.empty();            // false
v[1];                 // 2 (no bounds check)
v.at(1);              // 2 (bounds check, throws out_of_range)
v.front(); v.back();  // 1, 3
v.insert(v.begin()+1, 10);  // [1,10,2,3]
v.erase(v.begin());         // [10,2,3]
sort(v.begin(), v.end());   // sort
```

**map:**
```cpp
#include <map>
map<string, int> m;
m["apple"] = 5;
m["banana"] = 3;
m.count("apple");      // 1 (exists)
m.find("apple");       // iterator
for (auto& [k, v] : m) { cout << k << ": " << v; }  // C++17 structured bindings
```

### 12.2 Iterators

```cpp
vector<int> v = {1, 2, 3, 4, 5};
auto it = v.begin();     // points to first element
auto end = v.end();      // one past last

for (auto it = v.begin(); it != v.end(); ++it) {
    cout << *it << " ";
}

// Reverse iterator
for (auto rit = v.rbegin(); rit != v.rend(); ++rit) {
    cout << *rit << " ";   // 5 4 3 2 1
}
```

**Iterator Types:**

| Type | Can do |
|------|--------|
| Input | Read, single pass forward |
| Output | Write, single pass forward |
| Forward | Read/Write, multiple pass forward |
| Bidirectional | Forward + backward (`list`, `set`) |
| Random Access | ±n, [], compare (`vector`, `deque`) |

### 12.3 Algorithms (`<algorithm>`)

```cpp
#include <algorithm>
vector<int> v = {3,1,4,1,5,9,2,6};

sort(v.begin(), v.end());              // ascending
sort(v.begin(), v.end(), greater<int>());  // descending
reverse(v.begin(), v.end());
auto it = find(v.begin(), v.end(), 5); // returns iterator
int cnt = count(v.begin(), v.end(), 1);
int mx = *max_element(v.begin(), v.end());
binary_search(v.begin(), v.end(), 4); // true/false (sorted array)
auto pos = lower_bound(v.begin(), v.end(), 4);  // first >= 4
auto pos2 = upper_bound(v.begin(), v.end(), 4); // first > 4
accumulate(v.begin(), v.end(), 0);    // sum (in <numeric>)
```

### 12.4 `<utility>` and Pairs/Tuples
```cpp
pair<int, string> p = {1, "hello"};
p.first;   // 1
p.second;  // "hello"
auto tp = make_tuple(1, "hi", 3.14);
get<0>(tp); get<1>(tp); get<2>(tp);
```

---

## 13. Exception Handling

```cpp
try {
    if (x < 0) throw invalid_argument("Negative number");
    if (x == 0) throw runtime_error("Division by zero");
    cout << 100 / x;
}
catch (const invalid_argument& e) {
    cerr << "Invalid arg: " << e.what();
}
catch (const runtime_error& e) {
    cerr << "Runtime error: " << e.what();
}
catch (...) {           // catch all
    cerr << "Unknown exception";
}
finally {              // NOTE: C++ has no finally! Use RAII instead.
}
```

**Standard Exception Hierarchy:**
```
std::exception
├── std::logic_error
│   ├── invalid_argument
│   ├── out_of_range
│   └── domain_error
└── std::runtime_error
    ├── overflow_error
    ├── underflow_error
    └── range_error
```

**Custom Exceptions:**
```cpp
class MyException : public exception {
    string msg;
public:
    MyException(const string& m) : msg(m) {}
    const char* what() const noexcept override { return msg.c_str(); }
};
```

**`noexcept` specifier (C++11):**
```cpp
void safe() noexcept { /* guaranteed not to throw */ }
// If it does throw, std::terminate() is called
```

---

## 14. Move Semantics and Rvalue References (C++11)

### 14.1 Lvalue vs Rvalue
- **Lvalue** — has a name/address; persists beyond expression (`int x = 5; x` is lvalue)
- **Rvalue** — temporary; no address (`5`, `x + y`, `string("hello")` are rvalues)

### 14.2 Move Constructor & Move Assignment
```cpp
class Buffer {
    int* data;
    size_t size;
public:
    // Move constructor — steals resources
    Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr;   // leave source in valid-but-empty state
        other.size = 0;
    }
    // Move assignment
    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data;
            data = other.data; size = other.size;
            other.data = nullptr; other.size = 0;
        }
        return *this;
    }
};
```

### 14.3 `std::move` and `std::forward`
```cpp
// std::move — cast to rvalue reference (enables move semantics)
Buffer a(100);
Buffer b = move(a);   // moves, doesn't copy

// std::forward — perfect forwarding in templates
template <typename T>
void wrapper(T&& arg) {
    process(forward<T>(arg));  // forwards as lvalue or rvalue as appropriate
}
```

---

## 15. File I/O

```cpp
#include <fstream>

// Writing
ofstream outFile("data.txt");
outFile << "Hello, File!\n";
outFile.close();

// Reading
ifstream inFile("data.txt");
string line;
while (getline(inFile, line)) {
    cout << line << "\n";
}
inFile.close();

// Binary files
ofstream bin("data.bin", ios::binary);
int x = 42;
bin.write(reinterpret_cast<char*>(&x), sizeof(x));
```

---

## 16. Multithreading (C++11 `<thread>`)

```cpp
#include <thread>
#include <mutex>

mutex mtx;

void task(int id) {
    lock_guard<mutex> lock(mtx);   // RAII lock
    cout << "Thread " << id << "\n";
}

int main() {
    thread t1(task, 1);
    thread t2(task, 2);
    t1.join();   // wait for t1 to finish
    t2.join();
}
```

**Synchronization Tools:**

| Tool | Use |
|------|-----|
| `mutex` | Basic mutual exclusion |
| `lock_guard` | RAII mutex lock (no unlock needed) |
| `unique_lock` | Flexible lock (can unlock/re-lock) |
| `condition_variable` | Wait/notify between threads |
| `atomic<T>` | Lock-free operations on simple types |
| `future` / `promise` | Async task result passing |

---

## 17. C++11/14/17 Key Features

### C++11
- `auto` keyword — type deduction
- `nullptr` — type-safe null pointer
- Range-based `for` loop
- Lambda expressions
- Smart pointers (`unique_ptr`, `shared_ptr`, `weak_ptr`)
- Move semantics (`&&`, `std::move`)
- `constexpr`
- `static_assert`
- Initializer lists `{}`
- `override` and `final`
- `std::thread`

### C++14
- Generic lambdas: `[](auto x) { ... }`
- `make_unique`
- Binary literals: `0b1010`
- Digit separators: `1'000'000`

### C++17
- Structured bindings: `auto [key, val] = pair;`
- `if constexpr`
- `std::optional<T>` — may or may not hold a value
- `std::variant<T1,T2>` — type-safe union
- `std::string_view` — non-owning string reference
- Parallel algorithms: `std::sort(std::execution::par, ...)`

### C++20
- **Concepts** — constrain templates
- **Ranges** — composable algorithms
- **Coroutines** — co_await, co_yield
- **Modules** — replace headers
- `std::span` — non-owning view over array

---

## 18. Frequently Asked Questions (FAQs)

**Q1. What is the difference between `struct` and `class` in C++?**
> Only difference: default access is `public` in `struct`, `private` in `class`. Both support all OOP features. Convention: use `struct` for plain data, `class` for data + behavior.

**Q2. What is RAII?**
> Resource Acquisition Is Initialization. Resources (memory, file handles) are acquired in constructor and released in destructor. Guarantees cleanup even with exceptions. Smart pointers and `lock_guard` are examples.

**Q3. What is a virtual destructor and why is it needed?**
> When deleting a derived class object via a base class pointer, if the base destructor is not virtual, only the base destructor is called → resource leak. Always make base class destructors `virtual`.

**Q4. What is the difference between `new`/`delete` and `malloc`/`free`?**
> `new`/`delete` call constructors/destructors and are type-safe. `malloc`/`free` don't call constructors and return `void*`. Never mix them.

**Q5. What is a copy constructor vs assignment operator?**
> Copy constructor: creates a new object as a copy (`MyClass b = a;`). Assignment operator: copies into an existing object (`b = a;` where b already exists). Deep copy must be implemented for pointer members.

**Q6. What is object slicing?**
> When a derived class object is assigned to a base class variable by value, the derived-specific data is "sliced off". Avoided by using pointers/references.

**Q7. What is the difference between `delete` and `delete[]`?**
> `delete` frees a single object; `delete[]` frees an array. Mismatching them is **undefined behavior**.

**Q8. What is a pure virtual function and abstract class?**
> A pure virtual function (`virtual void f() = 0;`) has no implementation in the base class. A class with at least one pure virtual function is **abstract** — cannot be instantiated.

**Q9. Explain the difference between stack and heap memory.**
> Stack: automatic, fast, limited size, LIFO. Heap: manual (or smart ptr), slower, large, persistent until freed. Local variables are on stack; `new` allocates on heap.

**Q10. What is `std::move`?**
> It's a cast that converts an lvalue to an rvalue reference, enabling the move constructor/assignment to be invoked instead of copy. No data is actually moved by `std::move` itself.

**Q11. What is a template and why use it?**
> Templates enable generic programming — write code once that works for multiple types. Resolved at compile time, so no runtime overhead. Used in STL containers, algorithms, etc.

**Q12. What is the difference between `vector` and `array`?**
> `vector` is dynamic (resizable), heap-allocated. `std::array` is fixed-size, stack-allocated. `vector` is preferred when size varies; `std::array` for fixed-size performance-critical code.

**Q13. What is a `friend` function?**
> A function (not a member) that is granted access to private and protected members of a class. Useful for operator overloading (e.g., `operator<<`).

**Q14. What are the differences between `++i` and `i++`?**
> `++i` (pre-increment): increments and returns new value. `i++` (post-increment): returns old value, then increments. For iterators/objects, `++i` is more efficient (no temporary created).

**Q15. What is `constexpr`?**
> A `constexpr` value or function can be evaluated at compile time. Enables compile-time computation, useful for templates and array sizes.

---

## 19. Common Misconceptions

- ❌ *"C++ always does garbage collection"* → No. C++ requires manual memory management (or smart pointers). No built-in GC.
- ❌ *"References are just pointers under the hood"* → Semantically different; a reference is an alias. Compiler may use pointer internally, but you cannot do pointer arithmetic on a reference.
- ❌ *"`struct` and `class` are completely different"* → Only default access modifiers differ.
- ❌ *"Calling `delete` on a nullptr crashes"* → `delete nullptr` is safe and does nothing.
- ❌ *"`virtual` functions have huge overhead"* → One indirect function call via vtable; usually negligible.
- ❌ *"C++11 `auto` is like JavaScript `var`"* → `auto` in C++ is compile-time type deduction — still statically typed.

---

## 20. Quick Revision Checklist

- [ ] Stack vs Heap; `new`/`delete` vs smart pointers
- [ ] Rule of Three/Five/Zero
- [ ] `const`, `constexpr`, `mutable`
- [ ] Virtual functions, vtable, pure virtual, abstract class
- [ ] Virtual destructor
- [ ] Multiple inheritance, diamond problem, `virtual` base
- [ ] Templates: function, class, specialization
- [ ] STL containers: vector, map, set, unordered_map — complexities
- [ ] STL algorithms: sort, find, binary_search, accumulate
- [ ] Lambda syntax and captures
- [ ] Move semantics, `std::move`, rvalue references
- [ ] RAII and smart pointers
- [ ] Exception handling, `noexcept`
- [ ] Copy constructor vs assignment operator
- [ ] Operator overloading
- [ ] `friend` functions/classes
- [ ] Multithreading basics, mutex, lock_guard

---

*Last updated: 2026 | Suitable for: GATE, university exams, software engineering interviews*
