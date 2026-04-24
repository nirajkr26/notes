# Object-Oriented Programming (OOP) — Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction / Overview

Object-Oriented Programming (OOP) is a programming paradigm based on the concept of **objects** — entities that combine **data (attributes)** and **behavior (methods)**. OOP helps model real-world problems, improves code reusability, and makes large systems easier to maintain.

**Popular OOP Languages:** Java, C++, Python, C#, Ruby, Swift

---

## 2. Key Concepts at a Glance

| Concept         | One-line Definition                                                |
|-----------------|--------------------------------------------------------------------|
| Class           | Blueprint/template for creating objects                            |
| Object          | Instance of a class                                                |
| Encapsulation   | Bundling data + methods; restricting direct access                 |
| Abstraction     | Hiding implementation details, exposing only the interface         |
| Inheritance     | Child class acquires properties/methods from parent class          |
| Polymorphism    | Same interface, different behavior depending on the context        |
| Composition     | Building complex objects by combining simpler ones                 |
| Association     | Relationship between two separate classes                          |
| Aggregation     | Weak "has-a" relationship (child can exist without parent)         |
| Composition (HAS-A) | Strong "has-a" relationship (child cannot exist without parent) |

---

## 3. Core OOP Pillars

### 3.1 Class & Object

```
class Car {
    // attributes
    String brand;
    int speed;

    // method
    void accelerate() { speed += 10; }
}

Car myCar = new Car();   // myCar is an object (instance)
```

- **Class** = template (no memory allocated until instantiation)  
- **Object** = runtime instance of a class (memory allocated on heap)  
- **Constructor** = special method called when an object is created  
- **Destructor** = called when an object is destroyed (C++ `~ClassName()`, Python `__del__`)

---

### 3.2 Encapsulation

> Wrapping data and the methods that operate on that data into a single unit (class), and **restricting access** using access modifiers.

**Access Modifiers:**

| Modifier    | Same Class | Same Package | Subclass | World |
|-------------|-----------|--------------|----------|-------|
| `private`   | ✅        | ❌           | ❌       | ❌    |
| `default`   | ✅        | ✅           | ❌       | ❌    |
| `protected` | ✅        | ✅           | ✅       | ❌    |
| `public`    | ✅        | ✅           | ✅       | ✅    |

**Benefits:**
- Data hiding / security  
- Controlled access via getters/setters  
- Easier debugging and maintenance

**Example:**
```java
class BankAccount {
    private double balance;  // hidden

    public double getBalance() { return balance; }
    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }
}
```

---

### 3.3 Abstraction

> Hiding complex implementation details and showing only the **essential features** to the user.

- **Abstract Class:** can have both abstract (no body) and concrete methods; cannot be instantiated  
- **Interface:** all methods are abstract by default (Java 8+ allows `default`/`static` methods)

```java
abstract class Shape {
    abstract double area();    // must be overridden
    void display() { System.out.println("Shape"); }
}

interface Drawable {
    void draw();               // implicitly abstract
}
```

**Abstraction vs Encapsulation:**

| Aspect          | Abstraction                        | Encapsulation                      |
|-----------------|------------------------------------|------------------------------------|
| Focus           | What an object does                | How an object does it              |
| Achieved via    | Abstract classes / interfaces      | Access modifiers (private/public)  |
| Goal            | Reduce complexity                  | Protect data                       |

---

### 3.4 Inheritance

> Mechanism by which a **child (subclass)** acquires properties and behavior of a **parent (superclass)**.

**Types of Inheritance:**

```
Single:        A → B
Multi-level:   A → B → C
Hierarchical:  A → B, A → C
Multiple:      A + B → C  (supported via interfaces in Java; directly in C++)
Hybrid:        Combination of above
```

**Key Terms:**
- `extends` — Java keyword for class inheritance  
- `super` — refers to the parent class  
- `super()` — calls the parent constructor  
- **Method Overriding** — child redefines parent's method (same signature)

**IS-A relationship** (inheritance) vs **HAS-A relationship** (composition):
```
Dog IS-A Animal    → use inheritance
Car HAS-A Engine   → use composition
```

**Diamond Problem (C++):** Ambiguity when two parents share a common ancestor → solved using `virtual` inheritance.

---

### 3.5 Polymorphism

> Ability of an object to take **many forms**. Same method name → different behaviors.

#### Compile-time (Static) Polymorphism — Method Overloading
```java
class Calculator {
    int add(int a, int b)           { return a + b; }
    double add(double a, double b)  { return a + b; }
    int add(int a, int b, int c)    { return a + b + c; }
}
```
- Resolved at **compile time**
- Same method name, different **parameters** (type/number)

#### Runtime (Dynamic) Polymorphism — Method Overriding
```java
class Animal {
    void sound() { System.out.println("Some sound"); }
}
class Dog extends Animal {
    @Override
    void sound() { System.out.println("Woof"); }
}

Animal a = new Dog();
a.sound();  // prints "Woof"  ← decided at runtime
```
- Resolved at **runtime** via **dynamic dispatch**
- Requires `@Override` (Java) or `virtual`/`override` (C++)

**Overloading vs Overriding:**

| Feature           | Overloading              | Overriding                    |
|-------------------|--------------------------|-------------------------------|
| Where             | Same class               | Parent–child classes          |
| Signature         | Must differ              | Must be identical             |
| Resolution        | Compile-time             | Runtime                       |
| Return type       | Can differ               | Must be same (or covariant)   |
| `static` methods  | Can be overloaded        | Cannot be overridden          |

---

## 4. SOLID Principles

| Letter | Principle                         | Short Description                                                   |
|--------|-----------------------------------|---------------------------------------------------------------------|
| **S**  | Single Responsibility Principle   | A class should have only **one reason to change**                   |
| **O**  | Open/Closed Principle             | Open for extension, **closed for modification**                     |
| **L**  | Liskov Substitution Principle     | Subtypes must be substitutable for their base types                 |
| **I**  | Interface Segregation Principle   | Clients should not depend on interfaces they don't use              |
| **D**  | Dependency Inversion Principle    | Depend on abstractions, not on concrete implementations             |

**Memory Trick:** _"SOLID code is easier to maintain."_

---

## 5. Other Important OOP Concepts

### 5.1 Constructor Types

| Type                  | Description                                       |
|-----------------------|---------------------------------------------------|
| Default Constructor   | No parameters; provided by compiler if none exists |
| Parameterized         | Takes arguments to initialize fields               |
| Copy Constructor      | Creates a copy of another object (C++)             |
| Static (Factory)      | Static method that returns a new object            |

### 5.2 `this` and `super`

- `this` — refers to the **current** object; resolves naming conflicts between instance vars & parameters  
- `super` — refers to the **parent** class; used to call parent constructor or methods

### 5.3 `static` Keyword

- `static` variable — shared across all instances (class variable)  
- `static` method — belongs to the class, not instances; cannot access non-static members  
- `static` block — runs once when class is loaded

### 5.4 `final` Keyword (Java)

| Usage           | Effect                                            |
|-----------------|---------------------------------------------------|
| `final` class   | Cannot be subclassed                              |
| `final` method  | Cannot be overridden                              |
| `final` variable| Acts like a constant (cannot be reassigned)       |

### 5.5 Abstract Class vs Interface

| Feature             | Abstract Class               | Interface                          |
|---------------------|------------------------------|------------------------------------|
| Instantiation       | ❌ Cannot                    | ❌ Cannot                          |
| Abstract methods    | Can have (some/all)          | All (Java 7); default allowed (8+) |
| Concrete methods    | ✅ Yes                       | ✅ `default` methods (Java 8+)     |
| Constructors        | ✅ Yes                       | ❌ No                              |
| Multiple inheritance| ❌ No (single)               | ✅ Yes (implement many)            |
| Variables           | Can be non-final             | `public static final` by default   |
| Use when            | Shared code + partial impl   | Define a contract/API              |

### 5.6 Coupling and Cohesion

- **Tight Coupling** — classes are highly dependent on each other (bad)  
- **Loose Coupling** — minimal dependency between classes (good, use interfaces)  
- **High Cohesion** — class/methods do one specific thing well (good)  
- **Low Cohesion** — class handles many unrelated tasks (bad)

### 5.7 Design Patterns (Overview)

| Category    | Patterns                                         |
|-------------|--------------------------------------------------|
| Creational  | Singleton, Factory, Abstract Factory, Builder, Prototype |
| Structural  | Adapter, Bridge, Composite, Decorator, Facade, Proxy |
| Behavioral  | Observer, Strategy, Command, Iterator, Template Method |

**Singleton** — only one instance of a class allowed:
```java
class Singleton {
    private static Singleton instance;
    private Singleton() {}
    public static Singleton getInstance() {
        if (instance == null) instance = new Singleton();
        return instance;
    }
}
```

---

## 6. Memory Management

- **Stack** — stores local variables, method calls; LIFO order; faster  
- **Heap** — stores objects; managed by Garbage Collector (Java/Python) or manually (C++)  
- **Garbage Collection (GC)** — automatic memory reclamation; uses algorithms like Mark-and-Sweep, Generational GC  
- **Memory Leak** — objects no longer needed but not freed (common in C/C++)

---

## 7. Common OOP Diagrams

### UML Class Diagram Notation
```
+---------------------+
|      ClassName      |
+---------------------+
| - privateAttr: type |
| + publicAttr: type  |
+---------------------+
| + method(): return  |
+---------------------+

Relationships:
──────────►  Association (uses)
─────────◇   Aggregation (has-a, weak)
─────────◆   Composition (has-a, strong)
─────────▷   Inheritance (is-a)
- - - - -▷   Realization / Interface
```

---

## 8. Real-world Use Cases

| Concept       | Real-world Example                                              |
|---------------|-----------------------------------------------------------------|
| Encapsulation | ATM machine — user interacts via buttons, not internal circuits |
| Abstraction   | Car steering wheel — driver doesn't know gear mechanisms        |
| Inheritance   | `ElectricCar` extends `Car`                                     |
| Polymorphism  | `draw()` method works differently for Circle, Rectangle, Triangle |
| Singleton     | Logger, Configuration Manager, DB Connection Pool               |
| Observer      | Event listeners, pub-sub systems, MVC frameworks                |

---

## 9. Frequently Asked Questions (FAQs)

**Q1. What are the four pillars of OOP?**  
> Encapsulation, Abstraction, Inheritance, Polymorphism.

**Q2. What is the difference between abstraction and encapsulation?**  
> Abstraction hides **complexity** (what); encapsulation hides **data** (how). Abstraction is a design concept; encapsulation is an implementation technique.

**Q3. Can we override a static method?**  
> No. Static methods belong to the class, not the instance. They can be **hidden** (method hiding) but not overridden.

**Q4. What is the diamond problem?**  
> In multiple inheritance (C++), if two parent classes share a common ancestor, there is ambiguity about which version of a method to use. Solved using `virtual` inheritance in C++; Java avoids it by disallowing multiple class inheritance.

**Q5. Difference between `==` and `.equals()` in Java?**  
> `==` compares **references** (memory addresses); `.equals()` compares **content** (logical equality, can be overridden).

**Q6. What is method hiding vs method overriding?**  
> Overriding applies to **instance methods** (resolved at runtime). Method hiding applies to **static methods** (resolved at compile time).

**Q7. What is a marker/tag interface?**  
> An interface with no methods (e.g., `Serializable`, `Cloneable` in Java). Used to signal metadata to the JVM.

**Q8. What is covariant return type?**  
> An overriding method can return a subtype of the original return type (Java 5+).

**Q9. What is `Object` class in Java?**  
> The root of the Java class hierarchy. Every class implicitly extends `Object`. Key methods: `toString()`, `equals()`, `hashCode()`, `clone()`, `finalize()`.

**Q10. Explain early binding vs late binding.**  
> Early binding (static binding) — method call resolved at compile time (overloading, `static`, `final`, `private` methods).  
> Late binding (dynamic binding) — method call resolved at runtime (method overriding via virtual dispatch).

---

## 10. Common Misconceptions

- ❌ *"Abstraction and Encapsulation are the same"* → They're related but different (see §3.3).  
- ❌ *"Multiple inheritance causes problems in all OOP languages"* → Only if implemented incorrectly; Java avoids it, C++ handles it with `virtual`.  
- ❌ *"Constructor is inherited"* → Constructors are NOT inherited; they can be called using `super()`.  
- ❌ *"Interfaces can't have any implementation"* → Java 8+ introduced `default` and `static` methods in interfaces.  
- ❌ *"Overloading is runtime polymorphism"* → Overloading is **compile-time** polymorphism.

---

## 11. Quick Revision Checklist

- [ ] Class vs Object  
- [ ] 4 pillars: Encapsulation, Abstraction, Inheritance, Polymorphism  
- [ ] Overloading vs Overriding  
- [ ] Abstract class vs Interface  
- [ ] IS-A vs HAS-A  
- [ ] SOLID principles (S, O, L, I, D)  
- [ ] Static vs instance members  
- [ ] `final` / `this` / `super` keywords  
- [ ] Constructor types  
- [ ] Design patterns (Singleton, Factory, Observer)  
- [ ] Coupling & Cohesion  
- [ ] Diamond problem  

---

*Last updated: 2026 | Suitable for: GATE, university exams, software engineering interviews*
