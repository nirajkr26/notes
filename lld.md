# Low Level Design (LLD) — Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction / Overview

**Low Level Design (LLD)** is the process of designing the **internal workings** of a system — the classes, interfaces, data structures, algorithms, and interactions that implement a feature or component. LLD is the step *after* High Level Design (HLD) and translates architectural components into concrete, implementable code structures.

**Key Goals of LLD:**
- Define class hierarchies, attributes, and methods
- Specify relationships between objects (association, aggregation, composition, inheritance)
- Choose appropriate design patterns to solve recurring problems
- Ensure the design follows SOLID principles and is extensible, maintainable, and testable
- Produce a blueprint close enough to code that any developer can implement it

**LLD vs HLD:**

| Aspect         | LLD                                       | HLD                                         |
|----------------|-------------------------------------------|---------------------------------------------|
| Granularity    | Class/method level                        | Component/service level                     |
| Audience       | Developers                                | Architects, Tech Leads, Stakeholders        |
| Output         | Class diagrams, sequence diagrams, pseudocode | Architecture diagrams, component diagrams |
| Detail         | Data types, method signatures, algorithms | Services, databases, APIs, communication    |
| Focus          | How to implement                          | What to build and how pieces fit together   |

---

## 2. Object-Oriented Design Fundamentals

### 2.1 Classes and Objects

A **class** is the blueprint; an **object** is a runtime instance.

```
class BankAccount {
    private String accountId;
    private double balance;
    private String owner;

    public BankAccount(String accountId, String owner, double initialBalance) { ... }
    public void deposit(double amount) { ... }
    public boolean withdraw(double amount) { ... }
    public double getBalance() { ... }
}
```

**Design tip:** Keep each class focused on a single responsibility. Avoid "God classes" that know too much.

### 2.2 Access Modifiers

| Modifier     | Visibility                                  |
|--------------|---------------------------------------------|
| `private`    | Only within the same class                  |
| `protected`  | Same class + subclasses + same package (Java)|
| `package`    | Same package only (Java default)            |
| `public`     | Everywhere                                  |

### 2.3 Relationships Between Classes

| Relationship  | Meaning                                               | Code Signal                      |
|---------------|-------------------------------------------------------|----------------------------------|
| Association   | A uses B (loose coupling)                             | Method parameter or field        |
| Aggregation   | A "has-a" B; B can exist without A                    | A holds a reference to B         |
| Composition   | A "owns" B; B cannot exist without A                  | A creates and destroys B         |
| Inheritance   | B "is-a" A; B extends A                               | `extends` / `:`                  |
| Realization   | Class implements an interface                         | `implements` / `:`               |
| Dependency    | A temporarily depends on B                            | B appears in a method signature  |

---

## 3. SOLID Principles

SOLID is an acronym for five object-oriented design principles that make software more maintainable and scalable.

### 3.1 Single Responsibility Principle (SRP)

> A class should have **one, and only one, reason to change**.

**Bad:**
```
class UserService {
    void registerUser(User u) { ... }
    void sendWelcomeEmail(User u) { ... }  // email concern mixed in
    void saveToDatabase(User u) { ... }    // persistence concern mixed in
}
```

**Good:**
```
class UserService    { void registerUser(User u) { ... } }
class EmailService   { void sendWelcomeEmail(User u) { ... } }
class UserRepository { void save(User u) { ... } }
```

### 3.2 Open/Closed Principle (OCP)

> Software entities should be **open for extension but closed for modification**.

Use abstraction (interfaces/abstract classes) so new behavior can be added without touching existing code.

```
interface Discount {
    double apply(double price);
}
class SeasonalDiscount implements Discount { ... }
class LoyaltyDiscount  implements Discount { ... }
// Adding a new discount type never modifies existing code
```

### 3.3 Liskov Substitution Principle (LSP)

> Objects of a subclass must be **substitutable** for objects of the superclass without breaking the program.

**Violation example:** A `Square` extending `Rectangle` and overriding `setWidth` to also set height breaks the Rectangle contract.

**Rule of thumb:** Subclasses should not throw unexpected exceptions or restrict the behavior of inherited methods.

### 3.4 Interface Segregation Principle (ISP)

> Clients should **not be forced to depend on interfaces they do not use**.

Split fat interfaces into smaller, role-specific ones.

```
// Fat interface — BAD
interface Worker { void work(); void eat(); void sleep(); }

// Segregated — GOOD
interface Workable  { void work(); }
interface Eatable   { void eat(); }
interface Sleepable { void sleep(); }
```

### 3.5 Dependency Inversion Principle (DIP)

> High-level modules should not depend on low-level modules. **Both should depend on abstractions.**

```
// Bad — high-level depends on concrete low-level
class OrderService {
    MySQLOrderRepository repo = new MySQLOrderRepository();
}

// Good — depend on interface
class OrderService {
    private final OrderRepository repo;  // interface
    OrderService(OrderRepository repo) { this.repo = repo; }
}
```

---

## 4. Design Patterns

Design patterns are **proven, reusable solutions** to common software design problems. They are grouped into three categories.

### 4.1 Creational Patterns

#### 4.1.1 Singleton

> Ensure a class has **only one instance** and provide a global access point to it.

**Use cases:** Configuration manager, Logger, Thread pool, Connection pool.

```
class Singleton {
    private static Singleton instance;

    private Singleton() {}  // private constructor

    public static synchronized Singleton getInstance() {
        if (instance == null) instance = new Singleton();
        return instance;
    }
}
```

**Thread-safe Double-Checked Locking:**
```
private static volatile Singleton instance;

public static Singleton getInstance() {
    if (instance == null) {
        synchronized (Singleton.class) {
            if (instance == null) instance = new Singleton();
        }
    }
    return instance;
}
```

**Pitfalls:** Hard to unit test; global state; be cautious with multi-classloader environments.

---

#### 4.1.2 Factory Method

> Define an interface for creating an object, but let **subclasses decide which class to instantiate**.

```
interface Notification { void send(String msg); }
class SMSNotification   implements Notification { ... }
class EmailNotification implements Notification { ... }
class PushNotification  implements Notification { ... }

class NotificationFactory {
    public static Notification create(String type) {
        return switch (type) {
            case "SMS"   -> new SMSNotification();
            case "EMAIL" -> new EmailNotification();
            case "PUSH"  -> new PushNotification();
            default      -> throw new IllegalArgumentException("Unknown type: " + type);
        };
    }
}
```

---

#### 4.1.3 Abstract Factory

> Provide an interface for creating **families of related objects** without specifying concrete classes.

```
interface Button  { void render(); }
interface CheckBox { void render(); }

interface GUIFactory {
    Button    createButton();
    CheckBox  createCheckBox();
}
class WindowsFactory implements GUIFactory { ... }
class MacFactory     implements GUIFactory { ... }
```

---

#### 4.1.4 Builder

> Separate the **construction** of a complex object from its **representation**.

```
class Pizza {
    private String size;
    private boolean cheese, pepperoni, mushrooms;

    private Pizza(Builder b) { ... }

    static class Builder {
        private String size;
        private boolean cheese, pepperoni, mushrooms;

        Builder(String size) { this.size = size; }
        Builder cheese()     { this.cheese = true;     return this; }
        Builder pepperoni()  { this.pepperoni = true;  return this; }
        Builder mushrooms()  { this.mushrooms = true;  return this; }
        Pizza build()        { return new Pizza(this); }
    }
}

Pizza p = new Pizza.Builder("large").cheese().pepperoni().build();
```

**Use cases:** Building SQL queries, HTTP requests, complex DTOs.

---

#### 4.1.5 Prototype

> Create new objects by **cloning** an existing object.

```
class Shape implements Cloneable {
    String color;
    public Shape clone() throws CloneNotSupportedException {
        return (Shape) super.clone();
    }
}
```

Useful when object creation is expensive and objects share a common base state.

---

### 4.2 Structural Patterns

#### 4.2.1 Adapter

> Convert the interface of a class into **another interface** that clients expect.

```
// Third-party library
class LegacyLogger { public void logMessage(String msg) { ... } }

// Your interface
interface Logger { void log(String msg); }

// Adapter
class LoggerAdapter implements Logger {
    private LegacyLogger legacy = new LegacyLogger();
    public void log(String msg) { legacy.logMessage(msg); }
}
```

---

#### 4.2.2 Decorator

> Attach **additional responsibilities** to an object dynamically without modifying its class.

```
interface Coffee    { String getDescription(); double getCost(); }
class SimpleCoffee  implements Coffee { ... }

class MilkDecorator implements Coffee {
    private Coffee coffee;
    MilkDecorator(Coffee c) { this.coffee = c; }
    public String getDescription() { return coffee.getDescription() + ", Milk"; }
    public double getCost()        { return coffee.getCost() + 0.25; }
}

Coffee c = new MilkDecorator(new SimpleCoffee());
```

**Use cases:** Java I/O streams, middleware pipelines, logging wrappers.

---

#### 4.2.3 Facade

> Provide a **simplified interface** to a complex subsystem.

```
class HomeTheaterFacade {
    private Amplifier amp;
    private DVDPlayer dvd;
    private Projector projector;

    void watchMovie(String movie) {
        amp.on(); amp.setVolume(10);
        projector.on(); projector.wideScreenMode();
        dvd.on(); dvd.play(movie);
    }
}
```

---

#### 4.2.4 Proxy

> Provide a **surrogate or placeholder** for another object to control access.

**Types:**
- **Virtual Proxy:** lazy initialization of expensive objects
- **Protection Proxy:** access control
- **Remote Proxy:** represents an object in another address space
- **Caching Proxy:** caches results of expensive operations

```
interface Image { void display(); }
class RealImage   implements Image { ... /* loads from disk */ }
class ProxyImage  implements Image {
    private RealImage real;
    private String filename;
    ProxyImage(String f) { this.filename = f; }
    public void display() {
        if (real == null) real = new RealImage(filename); // lazy load
        real.display();
    }
}
```

---

#### 4.2.5 Composite

> Compose objects into **tree structures** to represent part-whole hierarchies.

```
interface FileSystemItem { void print(String indent); }
class File      implements FileSystemItem { ... }
class Directory implements FileSystemItem {
    List<FileSystemItem> children = new ArrayList<>();
    void add(FileSystemItem item) { children.add(item); }
    public void print(String indent) {
        System.out.println(indent + name);
        children.forEach(c -> c.print(indent + "  "));
    }
}
```

---

#### 4.2.6 Bridge

> Decouple an abstraction from its implementation so the two can **vary independently**.

```
interface DrawAPI { void drawCircle(int r, int x, int y); }
class RedCircle   implements DrawAPI { ... }
class GreenCircle implements DrawAPI { ... }

abstract class Shape {
    protected DrawAPI drawAPI;
    Shape(DrawAPI api) { this.drawAPI = api; }
    abstract void draw();
}
class Circle extends Shape {
    Circle(DrawAPI api) { super(api); }
    void draw() { drawAPI.drawCircle(5, 10, 10); }
}
```

---

#### 4.2.7 Flyweight

> Use **sharing** to efficiently support a large number of fine-grained objects.

```
class CharacterFlyweight {
    private final char symbol;  // intrinsic state (shared)
    CharacterFlyweight(char c) { this.symbol = c; }
    void display(int fontSize, String color) { ... } // extrinsic state (passed in)
}
```

**Use cases:** Game particles, text characters in a document editor.

---

### 4.3 Behavioral Patterns

#### 4.3.1 Observer (Pub-Sub)

> Define a one-to-many dependency so when one object changes state, **all dependents are notified automatically**.

```
interface Observer { void update(String event); }

class EventBus {
    private Map<String, List<Observer>> subscribers = new HashMap<>();

    void subscribe(String event, Observer o) {
        subscribers.computeIfAbsent(event, k -> new ArrayList<>()).add(o);
    }
    void publish(String event) {
        subscribers.getOrDefault(event, List.of()).forEach(o -> o.update(event));
    }
}
```

**Use cases:** UI event handling, stock price updates, notification systems.

---

#### 4.3.2 Strategy

> Define a family of algorithms, encapsulate each one, and make them **interchangeable**.

```
interface SortStrategy { void sort(int[] arr); }
class BubbleSort implements SortStrategy { ... }
class QuickSort  implements SortStrategy { ... }

class Sorter {
    private SortStrategy strategy;
    Sorter(SortStrategy s) { this.strategy = s; }
    void setStrategy(SortStrategy s) { this.strategy = s; }
    void sort(int[] arr) { strategy.sort(arr); }
}
```

---

#### 4.3.3 Command

> Encapsulate a request as an **object**, allowing parameterization, queuing, logging, and undo/redo.

```
interface Command { void execute(); void undo(); }

class LightOnCommand implements Command {
    private Light light;
    LightOnCommand(Light l) { this.light = l; }
    public void execute() { light.turnOn(); }
    public void undo()    { light.turnOff(); }
}

class RemoteControl {
    private Deque<Command> history = new ArrayDeque<>();
    void press(Command cmd) { cmd.execute(); history.push(cmd); }
    void undoLast()         { if (!history.isEmpty()) history.pop().undo(); }
}
```

---

#### 4.3.4 Template Method

> Define the **skeleton of an algorithm** in a base class, deferring some steps to subclasses.

```
abstract class DataProcessor {
    final void process() {   // template method
        readData();
        processData();
        writeData();
    }
    abstract void readData();
    abstract void processData();
    void writeData() { /* default implementation */ }
}
class CSVProcessor  extends DataProcessor { ... }
class JSONProcessor extends DataProcessor { ... }
```

---

#### 4.3.5 Iterator

> Provide a way to **sequentially access** elements of a collection without exposing its underlying representation.

```
interface Iterator<T> { boolean hasNext(); T next(); }

class NumberRange implements Iterable<Integer> {
    private int start, end;
    NumberRange(int s, int e) { start = s; end = e; }
    public Iterator<Integer> iterator() {
        return new Iterator<>() {
            int current = start;
            public boolean hasNext() { return current <= end; }
            public Integer next()    { return current++; }
        };
    }
}
```

---

#### 4.3.6 State

> Allow an object to **alter its behavior** when its internal state changes.

```
interface State { void handle(Context ctx); }

class TrafficLight {
    private State current = new RedState();
    void change() { current.handle(this); }
    void setState(State s) { this.current = s; }
}
class RedState    implements State { public void handle(Context c) { c.setState(new GreenState()); } }
class GreenState  implements State { public void handle(Context c) { c.setState(new YellowState()); } }
class YellowState implements State { public void handle(Context c) { c.setState(new RedState()); } }
```

---

#### 4.3.7 Chain of Responsibility

> Pass a request along a **chain of handlers** until one handles it.

```
abstract class Handler {
    protected Handler next;
    Handler setNext(Handler h) { this.next = h; return h; }
    abstract void handle(Request r);
}
class AuthHandler    extends Handler { ... }
class RateLimitHandler extends Handler { ... }
class LoggingHandler extends Handler { ... }

// Chain: auth -> rateLimit -> logging
```

---

#### 4.3.8 Mediator

> Define an object that **encapsulates how objects interact**, promoting loose coupling.

```
class ChatRoom {  // mediator
    private List<User> users = new ArrayList<>();
    void addUser(User u) { users.add(u); }
    void send(String msg, User sender) {
        users.stream().filter(u -> u != sender).forEach(u -> u.receive(msg));
    }
}
```

---

#### 4.3.9 Memento

> Capture and externalize an object's internal state so it can be **restored later**, without violating encapsulation.

```
class Editor {
    private String content;
    Memento save()                    { return new Memento(content); }
    void restore(Memento m)           { this.content = m.getContent(); }
}
class Memento {
    private final String content;
    Memento(String c)       { this.content = c; }
    String getContent()     { return content; }
}
class History {
    private Deque<Memento> stack = new ArrayDeque<>();
    void push(Memento m)  { stack.push(m); }
    Memento pop()         { return stack.pop(); }
}
```

---

## 5. UML Diagrams

### 5.1 Class Diagram Notation

```
+------------------+
|   ClassName      |  ← Class name (bold/italics for abstract)
+------------------+
| - privateField   |  ← (-) private, (+) public, (#) protected
| + publicField    |
+------------------+
| + method(): void |
| - helper(): int  |
+------------------+
```

**Relationships in UML:**

| Symbol               | Relationship   |
|----------------------|----------------|
| `──────>`            | Association    |
| `◇──────`            | Aggregation (hollow diamond) |
| `◆──────`            | Composition (filled diamond) |
| `──────▷`            | Inheritance (open arrow)    |
| `- - - -▷`           | Realization / Implementation|
| `- - - ->`           | Dependency                  |

### 5.2 Sequence Diagram

Shows **object interactions arranged in time sequence**.

```
Client          AuthService       UserRepo          DB
  |                  |                |              |
  |--login(user,pw)->|                |              |
  |                  |--findUser(u)-->|              |
  |                  |               |--SELECT------>|
  |                  |               |<--User--------|
  |                  |<--user--------|              |
  |                  |--verify(pw)   |              |
  |<--JWT token------|               |              |
```

### 5.3 Activity Diagram (Flow)

Used to model workflows and business processes with decision nodes, forks, and joins.

---

## 6. Common LLD Interview Problems

### 6.1 Design a Parking Lot

**Requirements:** Multiple floors, multiple spot types (compact, large, handicapped), entry/exit, payment.

**Key Classes:**

| Class            | Responsibility                                          |
|------------------|---------------------------------------------------------|
| `ParkingLot`     | Singleton; manages floors; entry/exit points            |
| `ParkingFloor`   | Contains spots, tracks availability                     |
| `ParkingSpot`    | Spot type, occupancy status, spot id                    |
| `Vehicle`        | Abstract; subclasses: Car, Truck, Motorcycle            |
| `Ticket`         | Issued on entry; vehicle ref, spot ref, entry time      |
| `Payment`        | Abstract; subclasses: CashPayment, CardPayment          |
| `EntryPanel`     | Issues ticket on vehicle entry                          |
| `ExitPanel`      | Calculates fee, processes payment                       |
| `ParkingFeeCalc` | Strategy for calculating fee based on vehicle type/time |

**Class Relationships:**
- `ParkingLot` (Composition) → `ParkingFloor[]`
- `ParkingFloor` (Composition) → `ParkingSpot[]`
- `Ticket` (Association) → `ParkingSpot`, `Vehicle`
- `ExitPanel` (Dependency) → `ParkingFeeCalc` (Strategy pattern)

---

### 6.2 Design an Elevator System

**Key Classes:**

| Class               | Responsibility                                     |
|---------------------|----------------------------------------------------|
| `ElevatorController`| Manages all elevators; dispatching algorithm       |
| `Elevator`          | State machine: IDLE, MOVING_UP, MOVING_DOWN, OPEN  |
| `ElevatorDoor`      | OPEN / CLOSED state                                |
| `Button`            | Inside (floor destination) or outside (call)       |
| `Floor`             | Has up/down call buttons                           |
| `Request`           | Holds source floor, direction, and target floor    |
| `DispatchAlgorithm` | Interface; implementations: FCFS, SCAN, LOOK       |

**State Transitions:**
```
IDLE ──(request)──> MOVING_UP / MOVING_DOWN
MOVING_UP ──(reached floor)──> DOOR_OPEN
DOOR_OPEN ──(timeout/button)──> DOOR_CLOSED
DOOR_CLOSED ──(more floors in queue)──> MOVING or IDLE
```

---

### 6.3 Design a Library Management System

**Key Classes:**

| Class           | Responsibility                                      |
|-----------------|-----------------------------------------------------|
| `Library`       | Singleton; manages books, members                   |
| `Book`          | ISBN, title, author, publisher, copies              |
| `BookItem`      | Physical copy; barcode, current state (issued/available) |
| `Member`        | Member ID, name, contact, borrowed books            |
| `Librarian`     | Can add/remove books, issue/return                  |
| `Reservation`   | Holds book for member for a time window             |
| `Fine`          | Calculated per day overdue                          |
| `Catalog`       | Search by title, author, subject                    |
| `Notification`  | Observer: notifies member when reserved book available |

---

### 6.4 Design a Chess Game

**Key Classes:**

| Class       | Responsibility                                           |
|-------------|----------------------------------------------------------|
| `Game`      | Manages turns, check/checkmate detection                 |
| `Board`     | 8×8 grid of `Cell` objects                               |
| `Cell`      | Row, col, optional `Piece`                               |
| `Piece`     | Abstract; color; `getValidMoves(Board)`                  |
| `King`, `Queen`, `Rook`, `Bishop`, `Knight`, `Pawn` | Concrete pieces with move logic |
| `Player`    | Human or AI; color (white/black)                         |
| `Move`      | Source cell, destination cell, captured piece            |
| `MoveValidator` | Validates move legality including check avoidance   |

**Patterns Used:** Strategy (move validation), Command (moves for undo), Composite (board).

---

### 6.5 Design a Rate Limiter

**Algorithms:**

| Algorithm         | Description                                             |
|-------------------|---------------------------------------------------------|
| Token Bucket      | Bucket fills at rate R; request consumes one token      |
| Leaky Bucket      | Requests enter bucket; leaked at constant rate          |
| Fixed Window      | Count requests per fixed time window                    |
| Sliding Window Log| Track timestamp of each request; evict old              |
| Sliding Window Counter | Hybrid of fixed window; smoother                   |

**Key Classes:**
```
interface RateLimiter {
    boolean allowRequest(String clientId);
}
class TokenBucketRateLimiter implements RateLimiter {
    private Map<String, TokenBucket> buckets;
    ...
}
class TokenBucket {
    private double tokens;
    private double capacity;
    private double refillRate;        // tokens per second
    private long lastRefillTimestamp;
    boolean consume() { refill(); if (tokens >= 1) { tokens--; return true; } return false; }
}
```

---

### 6.6 Design a URL Shortener (LLD Layer)

**Key Classes:**

| Class             | Responsibility                                          |
|-------------------|---------------------------------------------------------|
| `URLShortener`    | Facade; createShortURL, resolve                         |
| `URLRepository`   | Interface; save/find short↔long URL                    |
| `Base62Encoder`   | Encodes auto-increment ID to 6-char base62 string       |
| `CacheService`    | In-memory LRU cache for hot URLs                        |
| `AnalyticsService`| Tracks click count, geographic data                     |
| `ExpiryManager`   | TTL-based expiry of short URLs                          |

**Base62 Encoding:**
```
Characters: 0-9, a-z, A-Z (62 total)
7 chars → 62^7 = ~3.5 trillion combinations
```

---

### 6.7 Design an LRU Cache

**Target:** O(1) get and put.

**Implementation:** HashMap + Doubly Linked List

```
class LRUCache {
    private int capacity;
    private Map<Integer, DLinkedNode> cache = new HashMap<>();
    private DLinkedNode head, tail;  // dummy nodes

    int get(int key) {
        if (!cache.containsKey(key)) return -1;
        DLinkedNode node = cache.get(key);
        moveToHead(node);
        return node.value;
    }

    void put(int key, int value) {
        if (cache.containsKey(key)) {
            DLinkedNode node = cache.get(key);
            node.value = value;
            moveToHead(node);
        } else {
            DLinkedNode node = new DLinkedNode(key, value);
            cache.put(key, node);
            addToHead(node);
            if (cache.size() > capacity) {
                DLinkedNode tail = removeTail();
                cache.remove(tail.key);
            }
        }
    }
}
```

---

## 7. Data Structures for LLD

| Data Structure       | Best Use Case in LLD                                    |
|----------------------|---------------------------------------------------------|
| HashMap              | O(1) lookup by key; caches, indexes                     |
| LinkedHashMap        | Insertion-order / access-order (LRU cache)              |
| TreeMap              | Sorted order; range queries                             |
| PriorityQueue (Heap) | Scheduling, rate limiting, top-K problems               |
| Deque                | Undo/redo history, sliding window                       |
| Trie                 | Autocomplete, spell check, prefix search                |
| Graph (Adj List)     | Social networks, route finding                          |
| Segment Tree         | Range queries, interval scheduling                      |
| Bloom Filter         | Probabilistic membership; URL deduplication             |

---

## 8. Concurrency in LLD

### 8.1 Thread Safety Patterns

| Pattern               | Mechanism                                          |
|-----------------------|----------------------------------------------------|
| Immutability          | Make objects immutable; no synchronization needed  |
| Synchronized Methods  | `synchronized` keyword; one thread at a time       |
| ReadWriteLock         | Multiple readers OR one writer                     |
| ConcurrentHashMap     | Thread-safe map with segment-level locking         |
| AtomicInteger         | Lock-free counter using CAS                        |
| volatile              | Visibility guarantee; no atomicity                 |
| ThreadLocal           | Per-thread storage; no sharing                     |

### 8.2 Deadlock Prevention

**Conditions for Deadlock (all four must hold):**
1. Mutual Exclusion
2. Hold and Wait
3. No Preemption
4. Circular Wait

**Prevention strategies:**
- Always acquire locks in the **same order**
- Use **tryLock** with timeout
- Prefer **higher-level concurrency utilities** (Executor, CompletableFuture)

### 8.3 Producer-Consumer Pattern

```
class BoundedBuffer<T> {
    private Queue<T> queue;
    private int capacity;

    synchronized void produce(T item) throws InterruptedException {
        while (queue.size() == capacity) wait();
        queue.add(item);
        notifyAll();
    }

    synchronized T consume() throws InterruptedException {
        while (queue.isEmpty()) wait();
        T item = queue.poll();
        notifyAll();
        return item;
    }
}
```

---

## 9. API Design Best Practices (Internal/Module Level)

- **Minimal surface area:** expose only what's necessary; keep internals private
- **Fail fast:** validate inputs at boundaries and throw early
- **Immutable return types:** return unmodifiable collections when possible
- **Fluent interfaces:** builder pattern for complex configurations
- **Null safety:** prefer `Optional<T>` over returning `null`
- **Idempotency:** methods that don't mutate state should always produce the same result

### 9.1 Method Naming Conventions

| Prefix       | Meaning                             |
|--------------|-------------------------------------|
| `get`        | Returns a value, no side-effects    |
| `set`        | Mutates state                       |
| `is` / `has` | Returns boolean                     |
| `find`       | Returns `Optional` (may be absent)  |
| `create`     | Factory — constructs and returns    |
| `build`      | Builder terminal step               |
| `process`    | Side-effectful transformation       |

---

## 10. Error Handling in LLD

### 10.1 Exception Hierarchy Design

```
AppException (base)
├── ValidationException
│   ├── InvalidInputException
│   └── ConstraintViolationException
├── ResourceNotFoundException
├── ConflictException
└── ServiceUnavailableException
```

### 10.2 Best Practices

- Throw **specific** exceptions; never swallow exceptions silently
- Prefer **unchecked (runtime) exceptions** for programming errors
- Use **checked exceptions** for recoverable conditions the caller must handle
- Always include a meaningful message and root cause
- Log at the boundary where the exception is **first caught and handled**
- Use **Result/Either types** in functional style to avoid exceptions for control flow

---

## 11. Testing Strategies in LLD

### 11.1 Unit Testing

- Test each class in isolation using mocks for dependencies
- Follow **AAA pattern:** Arrange → Act → Assert
- Aim for high coverage on business logic; don't test framework code

### 11.2 Test Doubles

| Type    | Description                                           |
|---------|-------------------------------------------------------|
| Mock    | Verifies method calls; behavior-based testing         |
| Stub    | Returns predefined responses                          |
| Fake    | Working lightweight implementation (in-memory DB)     |
| Spy     | Real object with some methods stubbed                 |
| Dummy   | Passed around but not used                            |

### 11.3 Testability Principles

- **Dependency Injection** makes classes testable (inject mocks)
- Avoid `new` inside methods for dependencies; use DI or factory
- Keep logic in pure functions where possible (easier to test)
- Avoid static state and singletons (makes parallel testing hard)

---

## 12. Key Interview Tips

1. **Clarify requirements** before designing — ask about scale, constraints, edge cases
2. **Start with core entities** — identify nouns → classes, verbs → methods
3. **Apply SOLID** consciously — mention the principles while designing
4. **Choose patterns wisely** — don't over-engineer; justify each pattern
5. **Draw class diagrams** — even rough boxes and arrows show structured thinking
6. **Think about extensibility** — "what if we add a new payment method?"
7. **Mention concurrency** if relevant — thread safety shows senior thinking
8. **Code key methods** — interviewers often ask to implement a specific method
9. **Discuss trade-offs** — no design is perfect; acknowledge limitations

---

## 13. Quick Reference — Pattern Cheat Sheet

| Pattern            | Category   | Solves                                              | Real-World Example                  |
|--------------------|------------|-----------------------------------------------------|-------------------------------------|
| Singleton          | Creational | Ensure single instance                              | Logger, Config Manager              |
| Factory Method     | Creational | Delegate object creation to subclasses              | Notification sender                 |
| Abstract Factory   | Creational | Create families of related objects                  | Cross-platform UI widgets           |
| Builder            | Creational | Construct complex objects step by step              | SQL query builder, HTTP client      |
| Prototype          | Creational | Clone existing object                               | Game level duplication              |
| Adapter            | Structural | Make incompatible interfaces work together          | Legacy system integration           |
| Decorator          | Structural | Add behavior without changing class                 | Java I/O streams, middleware        |
| Facade             | Structural | Simplify complex subsystem                          | Home theater, payment gateway       |
| Proxy              | Structural | Control access to an object                         | Lazy loading, CDN, security         |
| Composite          | Structural | Tree of objects treated uniformly                   | File system, org chart              |
| Bridge             | Structural | Decouple abstraction from implementation            | Device drivers, rendering engines   |
| Flyweight          | Structural | Share common state among many objects               | Font rendering, game sprites        |
| Observer           | Behavioral | Notify dependents of state change                   | Event listeners, pub-sub, MVC       |
| Strategy           | Behavioral | Swap algorithms at runtime                          | Sorting, pricing, compression       |
| Command            | Behavioral | Encapsulate request as object                       | Undo/redo, task queues              |
| Template Method    | Behavioral | Define algorithm skeleton; defer steps              | Data parsers, report generators     |
| Iterator           | Behavioral | Sequential traversal                                | Collections, database cursors       |
| State              | Behavioral | Behavior changes with state                         | Vending machine, traffic light      |
| Chain of Resp.     | Behavioral | Pass request through handler chain                  | Middleware, logging pipeline        |
| Mediator           | Behavioral | Centralize communication between objects            | Chat room, air traffic control      |
| Memento            | Behavioral | Save and restore object state                       | Text editor undo, game save         |
