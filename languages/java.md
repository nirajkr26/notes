# Java — Super Detailed Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction to Java

Java is a **class-based, object-oriented, platform-independent, compiled-and-interpreted** programming language created by **James Gosling** at Sun Microsystems in 1995.

**Core Philosophy:** *"Write Once, Run Anywhere" (WORA)* — Java source is compiled to **bytecode**, which runs on any device with a JVM.

**Java Editions:**
- **Java SE** (Standard Edition) — Core language, APIs
- **Java EE / Jakarta EE** — Enterprise applications (Servlets, JSP, EJB)
- **Java ME** — Mobile/embedded devices

**Key Features:**
- Platform independent (via JVM)
- Strongly typed, statically typed
- Automatic memory management (Garbage Collection)
- Multi-threaded
- Rich standard library
- Security features built-in

---

## 2. JVM, JDK, JRE

```
JDK (Java Development Kit)
├── JRE (Java Runtime Environment)
│   ├── JVM (Java Virtual Machine)
│   └── Core Libraries (java.lang, java.util, etc.)
└── Development Tools (javac, javadoc, jar, jdb, jshell)
```

| Component | Role |
|-----------|------|
| **JDK** | Full toolkit for developing Java apps (includes JRE + compiler) |
| **JRE** | Runtime environment to run Java apps (JVM + libraries) |
| **JVM** | Executes bytecode; platform-specific; handles memory & GC |
| **javac** | Java compiler: `.java` → `.class` (bytecode) |
| **JIT** | Just-In-Time compiler inside JVM: converts hot bytecode to native machine code |

**Execution flow:**
```
MyProgram.java → javac → MyProgram.class (bytecode) → JVM/JIT → Machine Code → Output
```

---

## 3. Basic Syntax and Program Structure

```java
// File: HelloWorld.java
public class HelloWorld {           // class name must match file name
    public static void main(String[] args) {  // entry point
        System.out.println("Hello, World!");
    }
}
```

**Naming Conventions:**

| Item | Convention | Example |
|------|-----------|---------|
| Class | PascalCase | `BankAccount` |
| Interface | PascalCase | `Serializable` |
| Method | camelCase | `getBalance()` |
| Variable | camelCase | `accountNumber` |
| Constant | UPPER_SNAKE_CASE | `MAX_SIZE` |
| Package | lowercase with dots | `com.example.project` |

---

## 4. Data Types

### 4.1 Primitive Types

| Type | Size | Default | Range |
|------|------|---------|-------|
| `byte` | 1 byte | 0 | -128 to 127 |
| `short` | 2 bytes | 0 | -32,768 to 32,767 |
| `int` | 4 bytes | 0 | -2^31 to 2^31-1 |
| `long` | 8 bytes | 0L | -2^63 to 2^63-1 |
| `float` | 4 bytes | 0.0f | ~7 decimal digits |
| `double` | 8 bytes | 0.0 | ~15 decimal digits |
| `char` | 2 bytes | '\u0000' | 0 to 65,535 (Unicode) |
| `boolean` | 1 bit (JVM-specific) | false | true / false |

### 4.2 Wrapper Classes (Object counterparts)

| Primitive | Wrapper |
|-----------|---------|
| `int` | `Integer` |
| `long` | `Long` |
| `double` | `Double` |
| `char` | `Character` |
| `boolean` | `Boolean` |

**Autoboxing / Unboxing:**
```java
Integer obj = 42;        // autoboxing: int → Integer
int prim = obj;          // unboxing: Integer → int
// Beware: Integer a = 200; Integer b = 200; a == b is FALSE (different objects)
// Use .equals() for Integer comparison above 127
```

### 4.3 Type Casting
```java
// Widening (implicit, no data loss)
int i = 100;
long l = i;       // int → long

// Narrowing (explicit, possible data loss)
double d = 9.99;
int x = (int) d;  // 9 (truncated)
```

---

## 5. Variables and Scope

```java
public class Example {
    static int classVar = 10;       // class/static variable
    int instanceVar = 20;           // instance variable (default value if not initialized)

    void method() {
        int localVar = 30;          // local variable (must be initialized before use)
        final int CONST = 40;       // effectively final
    }
}
```

- **Local variables** — no default value; must be initialized before use
- **Instance variables** — default-initialized (0, false, null)
- **Static variables** — one per class; shared

---

## 6. Operators

Same as standard operators; Java-specific notes:
- `==` for primitives compares **value**; for objects compares **reference**
- `.equals()` for object **value** comparison (can be overridden)
- `instanceof` — type-check: `obj instanceof String`
- Ternary: `(condition) ? val1 : val2`
- `>>>` — unsigned right shift (fills with 0)

---

## 7. Control Flow

```java
// if-else-if ladder
if (score >= 90) grade = 'A';
else if (score >= 80) grade = 'B';
else grade = 'F';

// switch (Java 14+ allows switch expressions)
String result = switch (day) {
    case "MON", "TUE" -> "Weekday";
    case "SAT", "SUN" -> "Weekend";
    default -> "Unknown";
};

// Loops
for (int i = 0; i < 10; i++) { }
while (condition) { }
do { } while (condition);
for (String s : list) { }  // enhanced for

// Labeled break/continue
outer:
for (int i = 0; i < 5; i++) {
    for (int j = 0; j < 5; j++) {
        if (j == 2) break outer;  // exits outer loop
    }
}
```

---

## 8. Arrays

```java
int[] arr = new int[5];             // declaration + allocation
int[] arr2 = {1, 2, 3, 4, 5};     // declaration + initialization

arr[0] = 10;
arr.length;   // 5 (field, not method)

// 2D array
int[][] matrix = new int[3][4];
int[][] m2 = {{1,2},{3,4},{5,6}};

// Arrays utility class
Arrays.sort(arr);
Arrays.fill(arr, 0);
Arrays.copyOf(arr, 3);
Arrays.toString(arr);
Arrays.binarySearch(arr, 3);
```

---

## 9. Object-Oriented Programming in Java

### 9.1 Class and Object
```java
public class Car {
    // Fields
    private String brand;
    private int speed;

    // Constructor
    public Car(String brand, int speed) {
        this.brand = brand;
        this.speed = speed;
    }

    // Getters/Setters
    public String getBrand() { return brand; }
    public void setSpeed(int speed) { this.speed = speed; }

    // Method
    public void accelerate() { speed += 10; }

    // toString override
    @Override
    public String toString() { return brand + " at " + speed + " km/h"; }
}

Car c = new Car("Tesla", 0);
c.accelerate();
System.out.println(c);  // Tesla at 10 km/h
```

### 9.2 Constructors
```java
class Person {
    String name;
    int age;

    Person() { this("Unknown", 0); }                   // default → delegates to parameterized
    Person(String name, int age) { this.name = name; this.age = age; }
    Person(Person other) { this(other.name, other.age); }  // copy-like constructor
}
```

- `this()` — calls another constructor in same class (constructor chaining)
- `super()` — calls parent constructor (must be first statement)
- If no constructor defined, compiler adds a default no-arg constructor
- Constructors are not inherited

### 9.3 Inheritance
```java
class Animal {
    String name;
    Animal(String n) { name = n; }
    void speak() { System.out.println("..."); }
}

class Dog extends Animal {
    Dog(String n) { super(n); }       // must call super constructor

    @Override
    void speak() { System.out.println("Woof!"); }

    // Method specific to Dog
    void fetch() { System.out.println(name + " fetches!"); }
}

Animal a = new Dog("Rex");
a.speak();   // "Woof!" — dynamic dispatch
// a.fetch(); // ❌ compile error — Animal ref doesn't know fetch()
((Dog) a).fetch();  // ✅ after casting
```

**Key points:**
- Java supports **single class inheritance** only (use interfaces for multiple)
- `extends` for classes, `implements` for interfaces
- All classes implicitly extend `Object`

### 9.4 Access Modifiers

| Modifier | Same Class | Same Package | Subclass | Everywhere |
|----------|-----------|--------------|----------|------------|
| `private` | ✅ | ❌ | ❌ | ❌ |
| (default) | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

### 9.5 Polymorphism
```java
// Compile-time: method overloading
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
}

// Runtime: method overriding
Animal[] animals = { new Dog("Rex"), new Cat("Whiskers") };
for (Animal a : animals) {
    a.speak();  // calls Dog.speak() or Cat.speak() at runtime
}
```

### 9.6 Abstract Classes and Interfaces
```java
// Abstract class
abstract class Shape {
    String color;
    Shape(String c) { color = c; }
    abstract double area();               // subclasses MUST implement
    void display() { System.out.println("Color: " + color); }
}

// Interface (Java 8+)
interface Drawable {
    void draw();                          // abstract by default
    default void print() {                // default method (Java 8)
        System.out.println("Drawing...");
    }
    static Drawable create() { return () -> System.out.println("..."); }  // static method
}

// Functional Interface (Java 8) — exactly one abstract method
@FunctionalInterface
interface Transformer {
    int transform(int x);
}
Transformer doubler = x -> x * 2;        // lambda
```

**Abstract class vs Interface:**

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Constructor | ✅ Yes | ❌ No |
| Instance fields | ✅ Yes | ❌ No (only static final) |
| Multiple inherit | ❌ No | ✅ Yes (implements many) |
| Access modifiers | Any | `public` by default |
| `default` methods | ✅ Yes | ✅ Java 8+ |
| When to use | Common base + shared code | Define contract/API |

### 9.7 `final`, `static`, `this`, `super`

```java
// final
final int MAX = 100;              // constant variable
final class NoExtend {}           // cannot be subclassed
final void noOverride() {}        // cannot be overridden

// static
class Counter {
    static int count = 0;
    static { count = 1; }         // static initializer block
    Counter() { count++; }
    static int getCount() { return count; }
}

// this
class Person {
    String name;
    Person(String name) { this.name = name; }  // disambiguate
    Person copy() { return this; }              // return current object
}

// super
class Child extends Parent {
    Child() { super(args); }       // call parent constructor
    void method() { super.method(); }  // call parent method
}
```

---

## 10. String Handling

```java
String s = "Hello";           // String literal (String pool)
String s2 = new String("Hi"); // heap object (avoid this form)

// Immutable — every operation returns new String
s.length();           // 5
s.charAt(1);          // 'e'
s.substring(1, 3);    // "el"
s.toLowerCase();      // "hello"
s.toUpperCase();      // "HELLO"
s.trim();             // removes leading/trailing whitespace
s.replace("l","L");   // "HeLLo"
s.contains("ell");    // true
s.startsWith("He");   // true
s.split(",");         // split by delimiter
s.indexOf("l");       // 2
String.valueOf(42);   // "42"
s.equals("Hello");    // true (content)
s.equalsIgnoreCase("hello"); // true
s.compareTo("Iello"); // negative (H < I)
String.format("Name: %s, Age: %d", name, age);

// StringBuilder — mutable, not thread-safe (prefer over StringBuffer)
StringBuilder sb = new StringBuilder();
sb.append("Hello").append(" ").append("World");
sb.insert(5, ",");
sb.delete(0, 5);
sb.reverse();
sb.toString();

// StringBuffer — mutable, thread-safe (synchronized)
```

**String Pool:**
- String literals are stored in the **String Pool** (Heap's PermGen/Metaspace)
- `"hello" == "hello"` → `true` (same pool reference)
- `new String("hello") == new String("hello")` → `false` (different heap objects)
- Use `intern()` to add to pool: `s.intern()`

---

## 11. Java Collections Framework

### 11.1 Hierarchy

```
Iterable
└── Collection
    ├── List (ordered, allows duplicates)
    │   ├── ArrayList
    │   ├── LinkedList
    │   └── Vector → Stack
    ├── Set (unique elements)
    │   ├── HashSet (unordered)
    │   ├── LinkedHashSet (insertion order)
    │   └── TreeSet (sorted)
    └── Queue
        ├── LinkedList
        ├── PriorityQueue
        └── ArrayDeque

Map (key-value pairs, not extending Collection)
├── HashMap (unordered)
├── LinkedHashMap (insertion order)
├── TreeMap (sorted by key)
└── Hashtable (legacy, synchronized)
```

### 11.2 List
```java
List<String> list = new ArrayList<>();
list.add("Apple");
list.add(0, "Banana");     // insert at index
list.get(0);               // "Banana"
list.set(0, "Cherry");     // replace
list.remove(0);            // remove by index
list.remove("Apple");      // remove by value
list.size();               // size
list.contains("Apple");    // true/false
list.indexOf("Apple");     // index or -1
Collections.sort(list);    // sort
Collections.reverse(list);
```

**ArrayList vs LinkedList:**

| Operation | ArrayList | LinkedList |
|-----------|-----------|------------|
| Random access | O(1) | O(n) |
| Insert/delete at end | O(1) amortized | O(1) |
| Insert/delete in middle | O(n) | O(1) (with iterator) |
| Memory | Less (array) | More (node pointers) |

### 11.3 Set
```java
Set<Integer> set = new HashSet<>();
set.add(1); set.add(2); set.add(1);  // {1, 2} — no duplicate
set.contains(1);          // true
set.remove(1);
set.size();

// TreeSet — sorted
TreeSet<Integer> ts = new TreeSet<>();
ts.first(); ts.last();
ts.headSet(5);   // elements < 5
ts.tailSet(5);   // elements >= 5
```

### 11.4 Map
```java
Map<String, Integer> map = new HashMap<>();
map.put("a", 1);
map.get("a");              // 1
map.getOrDefault("b", 0); // 0
map.containsKey("a");      // true
map.remove("a");
map.putIfAbsent("c", 3);
map.size();

// Iterate
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    entry.getKey(); entry.getValue();
}
map.forEach((k, v) -> System.out.println(k + "=" + v));
```

### 11.5 Queue and Deque
```java
Queue<Integer> q = new LinkedList<>();
q.offer(1); q.offer(2);
q.peek();    // view front (null if empty)
q.poll();    // remove front (null if empty)
q.isEmpty();

Deque<Integer> deque = new ArrayDeque<>();
deque.offerFirst(1); deque.offerLast(2);
deque.peekFirst(); deque.peekLast();
deque.pollFirst(); deque.pollLast();

// Use as stack: push() / pop() / peek()
```

### 11.6 Comparator and Comparable
```java
// Comparable — natural ordering (implement in the class)
class Student implements Comparable<Student> {
    int marks;
    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.marks, other.marks);
    }
}

// Comparator — external ordering (passed to sort)
Comparator<Student> byName = (s1, s2) -> s1.name.compareTo(s2.name);
Collections.sort(students, byName);

// Method reference
students.sort(Comparator.comparing(s -> s.name));
students.sort(Comparator.comparingInt((Student s) -> s.marks).reversed());
```

---

## 12. Exception Handling

```java
try {
    int result = 10 / 0;  // throws ArithmeticException
}
catch (ArithmeticException e) {
    System.err.println("Error: " + e.getMessage());
}
catch (Exception e) {    // parent catch must come after child
    e.printStackTrace();
}
finally {
    System.out.println("Always executes (cleanup here)");
}

// Multi-catch (Java 7)
catch (IOException | SQLException e) { ... }

// Try-with-resources (Java 7) — auto-closes Closeable/AutoCloseable
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line = br.readLine();
}
// br.close() called automatically
```

**Exception Hierarchy:**
```
Throwable
├── Error (don't catch these — JVM errors)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── VirtualMachineError
└── Exception
    ├── Checked (must handle or declare with throws)
    │   ├── IOException
    │   ├── SQLException
    │   └── ClassNotFoundException
    └── RuntimeException (Unchecked — don't need to handle)
        ├── NullPointerException
        ├── ArrayIndexOutOfBoundsException
        ├── ClassCastException
        ├── NumberFormatException
        └── IllegalArgumentException
```

**Custom Exception:**
```java
class InsufficientFundsException extends Exception {
    double amount;
    InsufficientFundsException(double amount) {
        super("Insufficient funds: need " + amount + " more");
        this.amount = amount;
    }
}

// Usage
void withdraw(double amount) throws InsufficientFundsException {
    if (amount > balance) throw new InsufficientFundsException(amount - balance);
    balance -= amount;
}
```

---

## 13. Java 8 Features

### 13.1 Lambda Expressions
```java
// Before Java 8
Runnable r = new Runnable() {
    @Override
    public void run() { System.out.println("Run!"); }
};

// Lambda
Runnable r = () -> System.out.println("Run!");
Comparator<String> c = (s1, s2) -> s1.compareTo(s2);
```

### 13.2 Functional Interfaces (`java.util.function`)
```java
Function<String, Integer> len = s -> s.length();       // T → R
Consumer<String> print = s -> System.out.println(s);   // T → void
Supplier<String> greet = () -> "Hello";                // () → T
Predicate<Integer> isPositive = n -> n > 0;            // T → boolean
BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;

len.apply("Hello");      // 5
print.accept("Hi");
greet.get();             // "Hello"
isPositive.test(5);      // true
isPositive.negate().test(5);  // false
isPositive.and(n -> n < 10).test(5);  // true
```

### 13.3 Method References
```java
// Static method
Function<String, Integer> parse = Integer::parseInt;

// Instance method on instance
Consumer<String> printer = System.out::println;

// Instance method on type
Function<String, String> upper = String::toUpperCase;

// Constructor reference
Supplier<ArrayList> listFactory = ArrayList::new;
```

### 13.4 Stream API
```java
import java.util.stream.*;

List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5, 6);

// Intermediate (lazy) operations: filter, map, sorted, distinct, limit, skip, flatMap
// Terminal (eager) operations: collect, forEach, count, reduce, min, max, findFirst, anyMatch

int sum = nums.stream()
    .filter(n -> n % 2 == 0)         // [2, 4, 6]
    .map(n -> n * n)                  // [4, 16, 36]
    .reduce(0, Integer::sum);         // 56

List<String> names = employees.stream()
    .filter(e -> e.salary > 50000)
    .map(Employee::getName)
    .sorted()
    .collect(Collectors.toList());

// Collectors
Collectors.toList()
Collectors.toSet()
Collectors.joining(", ")
Collectors.groupingBy(Employee::getDept)
Collectors.counting()
Collectors.toMap(e -> e.id, e -> e.name)

// Parallel stream
nums.parallelStream().map(n -> n * 2).collect(Collectors.toList());
```

### 13.5 Optional
```java
Optional<String> opt = Optional.of("Hello");
Optional<String> empty = Optional.empty();
Optional<String> nullable = Optional.ofNullable(null);

opt.isPresent();          // true
opt.get();                // "Hello"
opt.orElse("default");    // "Hello"
empty.orElse("default");  // "default"
opt.map(String::toUpperCase);     // Optional["HELLO"]
opt.filter(s -> s.length() > 3);  // Optional["Hello"]
opt.ifPresent(System.out::println);
```

### 13.6 Default and Static Interface Methods
```java
interface Vehicle {
    void start();
    default void stop() { System.out.println("Stopping"); }   // default
    static Vehicle of(String type) { /* factory */ }          // static
}
```

---

## 14. Generics

```java
// Generic class
class Pair<A, B> {
    A first;  B second;
    Pair(A first, B second) { this.first = first; this.second = second; }
}
Pair<String, Integer> p = new Pair<>("hello", 42);

// Generic method
public <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) > 0 ? a : b;
}

// Wildcards
void printList(List<?> list) { ... }                 // unknown type
void addNumbers(List<? extends Number> list) { ... } // upper bounded (read)
void addObjects(List<? super Integer> list) { ... }  // lower bounded (write)
```

**Type Erasure:** Generics are compile-time only. At runtime, `List<String>` and `List<Integer>` are both just `List`. Cannot do `new T()` or `instanceof T<String>`.

---

## 15. Multithreading and Concurrency

### 15.1 Creating Threads
```java
// Option 1: extend Thread
class MyThread extends Thread {
    public void run() { System.out.println("Running"); }
}
new MyThread().start();

// Option 2: implement Runnable (preferred)
Runnable r = () -> System.out.println("Running");
Thread t = new Thread(r);
t.start();

// Option 3: Callable + Future (returns value)
Callable<Integer> task = () -> 42;
ExecutorService exec = Executors.newSingleThreadExecutor();
Future<Integer> future = exec.submit(task);
int result = future.get();  // blocks until done
exec.shutdown();
```

### 15.2 Thread Lifecycle

```
NEW → RUNNABLE → (BLOCKED/WAITING/TIMED_WAITING) → TERMINATED
```

### 15.3 Synchronization
```java
// synchronized method
synchronized void increment() { count++; }

// synchronized block (finer granularity)
void increment() {
    synchronized(this) { count++; }
}

// volatile — visibility guarantee (no caching in register)
volatile boolean running = true;

// Lock (java.util.concurrent.locks)
ReentrantLock lock = new ReentrantLock();
lock.lock();
try { count++; }
finally { lock.unlock(); }   // always unlock in finally
```

### 15.4 ExecutorService and Thread Pools
```java
ExecutorService pool = Executors.newFixedThreadPool(4);  // 4 worker threads
pool.submit(() -> doWork());
pool.shutdown();     // graceful shutdown (waits for tasks)
pool.shutdownNow();  // forceful shutdown

// ScheduledExecutorService
ScheduledExecutorService sched = Executors.newScheduledThreadPool(1);
sched.scheduleAtFixedRate(task, 0, 5, TimeUnit.SECONDS);
```

### 15.5 java.util.concurrent Collections
```java
ConcurrentHashMap<K,V>    // thread-safe HashMap (lock striping)
CopyOnWriteArrayList<E>   // thread-safe List (copy on write)
BlockingQueue<E>           // thread-safe queue with blocking ops
  LinkedBlockingQueue
  ArrayBlockingQueue
AtomicInteger              // lock-free thread-safe int
AtomicInteger ai = new AtomicInteger(0);
ai.incrementAndGet();  ai.compareAndSet(0, 1);
```

---

## 16. Java Memory Model

### 16.1 JVM Memory Areas

| Area | Description |
|------|-------------|
| **Heap** | Objects and class instances; GC managed |
| **Stack** | Each thread has its own; stores frames (local vars, operand stack) |
| **Metaspace** | Class metadata (replaced PermGen in Java 8) |
| **Method Area** | Class-level data (within Metaspace) |
| **PC Register** | Current instruction pointer per thread |
| **Native Method Stack** | For native (C/C++) method calls |

### 16.2 Garbage Collection

**GC Algorithms:**
- **Serial GC** — single-threaded; for small heaps
- **Parallel GC** — multi-threaded; throughput-focused
- **G1 GC** (default since Java 9) — balanced latency/throughput; region-based
- **ZGC / Shenandoah** — low-latency; concurrent GC (< 10ms pauses)

**Heap Generations:**
```
Young Generation
├── Eden Space        (new objects)
├── Survivor Space S0
└── Survivor Space S1

Old (Tenured) Generation  (long-lived objects)
```

**GC Process:**
1. Minor GC: Eden → Survivor → promotes to Old Gen
2. Major/Full GC: cleans Old Gen (expensive)

**GC Eligibility — object is eligible when:**
- No live references point to it
- Island of isolation (circular references with no external ref)

---

## 17. Design Patterns in Java

### Creational
```java
// Singleton
public class Singleton {
    private static volatile Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                if (instance == null) instance = new Singleton();  // double-checked locking
            }
        }
        return instance;
    }
}

// Factory Method
interface Shape { void draw(); }
class Circle implements Shape { public void draw() { System.out.println("Circle"); } }
class ShapeFactory {
    public static Shape create(String type) {
        return switch (type) {
            case "circle" -> new Circle();
            default -> throw new IllegalArgumentException();
        };
    }
}

// Builder
class Pizza {
    private String size; private boolean cheese; private boolean pepperoni;
    private Pizza(Builder b) { size=b.size; cheese=b.cheese; pepperoni=b.pepperoni; }
    static class Builder {
        String size;
        boolean cheese, pepperoni;
        Builder size(String s) { size=s; return this; }
        Builder cheese() { cheese=true; return this; }
        Pizza build() { return new Pizza(this); }
    }
}
Pizza p = new Pizza.Builder().size("large").cheese().build();
```

### Structural
```java
// Decorator
interface Coffee { double cost(); }
class SimpleCoffee implements Coffee { public double cost() { return 1.0; } }
class MilkDecorator implements Coffee {
    Coffee coffee;
    MilkDecorator(Coffee c) { coffee = c; }
    public double cost() { return coffee.cost() + 0.5; }
}

// Adapter
interface USPlug { void connect110V(); }
class EuropeanDevice { void connect220V() {} }
class Adapter implements USPlug {
    EuropeanDevice device;
    Adapter(EuropeanDevice d) { device = d; }
    public void connect110V() { device.connect220V(); }
}
```

### Behavioral
```java
// Observer
interface Observer { void update(String event); }
class EventSystem {
    List<Observer> observers = new ArrayList<>();
    void subscribe(Observer o) { observers.add(o); }
    void emit(String event) { observers.forEach(o -> o.update(event)); }
}

// Strategy
interface SortStrategy { void sort(int[] arr); }
class BubbleSort implements SortStrategy { public void sort(int[] arr) { /*...*/ } }
class Sorter {
    SortStrategy strategy;
    Sorter(SortStrategy s) { strategy = s; }
    void sort(int[] arr) { strategy.sort(arr); }
}
```

---

## 18. Java I/O and NIO

```java
// Reading file (try-with-resources)
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = br.readLine()) != null) System.out.println(line);
}

// Writing file
try (PrintWriter pw = new PrintWriter(new FileWriter("out.txt"))) {
    pw.println("Hello, File!");
}

// NIO (Java 7+) — java.nio.file
Path path = Paths.get("file.txt");
List<String> lines = Files.readAllLines(path);
Files.write(path, content.getBytes());
Files.exists(path);
Files.copy(src, dest);
```

---

## 19. Frequently Asked Questions (FAQs)

**Q1. What is the difference between `==` and `.equals()`?**
> `==` compares **references** (memory addresses) for objects. `.equals()` compares **content** (logical equality). Always use `.equals()` for String and object comparisons.

**Q2. Why is `String` immutable in Java?**
> Security (used in class loading, network connections), thread safety (no synchronization needed), String Pool caching (same literal can be shared), and hashCode caching.

**Q3. What is the difference between `ArrayList` and `LinkedList`?**
> `ArrayList` uses a dynamic array — O(1) random access, O(n) insert/delete in middle. `LinkedList` uses doubly-linked list — O(1) insert/delete at ends, O(n) random access.

**Q4. What is the difference between `HashMap` and `Hashtable`?**
> `Hashtable` is synchronized (thread-safe) but slower; doesn't allow null keys/values. `HashMap` is not synchronized; allows one null key and multiple null values. Use `ConcurrentHashMap` for thread-safe maps.

**Q5. What is method overloading vs method overriding?**
> Overloading: same method name, different parameters, same class, resolved at compile time. Overriding: same signature, in subclass, resolved at runtime via dynamic dispatch.

**Q6. What is the difference between `abstract class` and `interface`?**
> Abstract class can have constructors, instance fields, concrete methods, and any access modifier. Interface can only have constants, abstract methods, default/static methods (Java 8+). A class can implement multiple interfaces but extend only one class.

**Q7. What is autoboxing and unboxing? What are its pitfalls?**
> Auto-conversion between primitive and wrapper types. Pitfall: `Integer a = 1000; Integer b = 1000; a == b` is `false` (different objects; cached only for -128 to 127).

**Q8. What is the difference between `throw` and `throws`?**
> `throw` actually throws an exception: `throw new RuntimeException("msg");`. `throws` in method signature declares what checked exceptions may be thrown: `void read() throws IOException`.

**Q9. What is a functional interface?**
> An interface with exactly one abstract method. Used as lambda targets. Examples: `Runnable`, `Callable`, `Comparator`, `Function`, `Predicate`.

**Q10. What is the Java Memory Model?**
> Defines how threads interact through memory. Guarantees: visibility (volatile, synchronized), atomicity, ordering. `happens-before` relationship ensures a write is visible to subsequent reads.

**Q11. What is garbage collection in Java?**
> Automatic memory management that reclaims heap memory from unreachable objects. Uses generational approach (Young/Old gen). GC algorithms: Serial, Parallel, G1 (default), ZGC.

**Q12. What is the difference between `final`, `finally`, and `finalize()`?**
> `final`: keyword for constants, preventing inheritance/override. `finally`: block in try-catch that always executes. `finalize()`: deprecated method called by GC before object is collected (unreliable, avoid).

**Q13. What is a `volatile` variable?**
> A variable whose reads/writes are always flushed to main memory, not cached in CPU registers. Guarantees visibility across threads but NOT atomicity (use `AtomicInteger` for atomic operations).

**Q14. What is the difference between `Comparable` and `Comparator`?**
> `Comparable` is implemented by the class itself to define natural ordering (`compareTo`). `Comparator` is a separate object defining custom ordering (`compare`). Multiple `Comparator`s can be defined for a class.

**Q15. What are Java 8 Stream intermediate vs terminal operations?**
> Intermediate (lazy, return Stream): `filter`, `map`, `flatMap`, `sorted`, `distinct`, `limit`, `skip`. Terminal (eager, trigger execution): `collect`, `forEach`, `count`, `reduce`, `min`, `max`, `anyMatch`, `findFirst`.

---

## 20. Common Misconceptions

- ❌ *"Java is fully object-oriented"* → Java has primitives (`int`, `double`, etc.) which are not objects.
- ❌ *"Java is slow"* → Modern JVMs with JIT compilation achieve near-native performance for many workloads.
- ❌ *"Strings can be compared with `==`"* → Always use `.equals()` for content comparison.
- ❌ *"`finally` always runs"* → Not if `System.exit()` is called or JVM crashes.
- ❌ *"Checked exceptions are better than unchecked"* → Debated; Java community often prefers unchecked exceptions (see Spring, modern libraries).
- ❌ *"Synchronizing every method makes code thread-safe"* → Over-synchronization causes deadlocks and poor performance. Thread safety is more nuanced.

---

## 21. Quick Revision Checklist

- [ ] JVM / JDK / JRE difference
- [ ] Primitive types and wrapper classes; autoboxing
- [ ] Access modifiers (private, default, protected, public)
- [ ] Inheritance, overriding, `@Override`
- [ ] `final`, `static`, `this`, `super`
- [ ] Abstract class vs Interface
- [ ] String immutability, String pool, `equals()` vs `==`
- [ ] Collections: ArrayList vs LinkedList, HashMap vs TreeMap
- [ ] Generics and type erasure
- [ ] Exception hierarchy: checked vs unchecked; custom exceptions
- [ ] Java 8: lambdas, streams, Optional, functional interfaces
- [ ] Multithreading: Thread, Runnable, synchronized, volatile
- [ ] ExecutorService and thread pools
- [ ] Design patterns: Singleton, Factory, Builder, Observer, Strategy
- [ ] GC: Young/Old generation, GC algorithms
- [ ] Memory areas: Heap, Stack, Metaspace

---

*Last updated: 2026 | Suitable for: GATE, university exams, software engineering interviews*
