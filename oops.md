# Object-Oriented Programming (OOP) — Super Detailed Notes for Exams & Interviews

---

## 1) OOP Introduction

Object-Oriented Programming models software using **objects** (state + behavior). It improves maintainability, reusability, and scalability for large systems.

### Why OOP?
- Better organization around domain entities
- Reusability via inheritance/composition
- Extensibility using polymorphism
- Encapsulation for safer APIs

### Languages
C++, Java, C#, Python (multi-paradigm), Swift, Kotlin.

---

## 2) Core Terminology

| Term | Meaning |
|---|---|
| Class | Blueprint for creating objects |
| Object | Instance of class at runtime |
| Attribute/Field | Data members |
| Method | Member function/behavior |
| Constructor | Initializes new object |
| Destructor/Finalizer | Cleanup hook (language dependent) |

---

## 3) Four Pillars of OOP

### 3.1 Encapsulation
Bundling data with methods and restricting direct access.

**Benefits:** data hiding, validation, controlled mutation.

### 3.2 Abstraction
Expose essential behavior; hide implementation details.

**Mechanisms:** abstract classes, interfaces, protocols.

### 3.3 Inheritance
Create derived classes from base classes for reuse.

### 3.4 Polymorphism
Same interface, different runtime behavior.

---

## 4) Encapsulation in Depth

### Access control
- `private`, `protected`, `public` (language-dependent variants)

### Best practices
- Keep fields private
- Expose minimal methods
- Validate all external inputs in setters/constructors
- Prefer immutable objects where possible

---

## 5) Abstraction in Depth

### Abstract class vs Interface
| Feature | Abstract Class | Interface |
|---|---|---|
| State (fields) | Yes | Limited/constant-style |
| Method implementations | Yes | Yes in modern languages (default/static in Java) |
| Multiple inheritance | Usually no | Usually yes |
| Use case | Shared base behavior | Contract/API definition |

---

## 6) Inheritance in Depth

### Types
- Single
- Multilevel
- Hierarchical
- Multiple (direct in C++, via interfaces in Java)
- Hybrid

### Key ideas
- IS-A relationship
- Method overriding
- Constructor chaining
- `super` / base class reference

### Risks
- Fragile base class problem
- Tight coupling
- Misuse where composition would be better

---

## 7) Polymorphism in Depth

### Compile-time polymorphism
- Method/function overloading
- Operator overloading (C++)

### Runtime polymorphism
- Method overriding with dynamic dispatch (virtual methods)

### Overloading vs Overriding
| Aspect | Overloading | Overriding |
|---|---|---|
| Signature | Different | Same |
| Resolution | Compile-time | Runtime |
| Class relation | Same class enough | Base/derived relation required |

---

## 8) Association, Aggregation, Composition

- **Association:** general relationship
- **Aggregation:** weak ownership (`has-a`, independent lifecycle)
- **Composition:** strong ownership (dependent lifecycle)

Interview line:
- Car has an Engine (composition in many designs)
- Department has Employees (often aggregation)

---

## 9) SOLID Principles (Must-Know)

1. **S**ingle Responsibility Principle
2. **O**pen/Closed Principle
3. **L**iskov Substitution Principle
4. **I**nterface Segregation Principle
5. **D**ependency Inversion Principle

### Typical interview expectations
- Identify SRP violation quickly
- Suggest dependency inversion using interfaces
- Explain why fat interfaces break ISP

---

## 10) Coupling and Cohesion

- **Low coupling** + **high cohesion** is ideal.
- Prefer dependency injection and abstractions to reduce coupling.

---

## 11) Static, Final/Const, This, Super

### Static
- Class-level members
- Shared state across objects

### Final/Const
- Prevent reassignment/overriding/inheritance (language specific)

### `this`
- Current object reference

### `super` / base
- Access parent behavior

---

## 12) Constructors and Object Lifecycle

### Constructor types
- Default
- Parameterized
- Copy constructor (C++)
- Move constructor (C++11+)

### Lifecycle differences
- C++: deterministic destruction (RAII)
- Java/C#: GC-managed memory
- Python: reference counting + GC

---

## 13) OOP and Memory

- Stack: local variables/call frames
- Heap: objects with dynamic lifetime
- Memory leaks in manual memory systems
- Dangling pointers in unmanaged languages

---

## 14) OOP Design Patterns (Exam + Interview)

### Creational
- Singleton
- Factory Method
- Abstract Factory
- Builder
- Prototype

### Structural
- Adapter
- Facade
- Decorator
- Composite
- Proxy

### Behavioral
- Strategy
- Observer
- Command
- Template Method
- Iterator

### Pattern interview quick picks
- Strategy for interchangeable algorithms
- Observer for event-driven updates
- Factory to hide object creation complexity

---

## 15) UML Essentials

Class diagram relationships:
- Association
- Aggregation (hollow diamond)
- Composition (filled diamond)
- Inheritance (hollow triangle)
- Realization (dashed line to interface)

---

## 16) OOP in Practice

### Good class design checklist
- Single responsibility?
- Clear invariants?
- Encapsulated internal state?
- Minimal public API?
- Testable with mocks/stubs?

### Common anti-patterns
- God class
- Deep inheritance hierarchy
- Feature envy
- Primitive obsession

---

## 17) OOP Interview Questions (High Frequency)

1. Explain OOP pillars with examples.
2. Abstraction vs encapsulation?
3. Why composition over inheritance?
4. LSP violation example?
5. Interface vs abstract class in Java?
6. What is dynamic dispatch?
7. Overloading vs overriding?
8. Can constructors be inherited?
9. Why is `equals`/`hashCode` contract important?
10. Design a parking lot using OOP.

---

## 18) Exam-Oriented Theory Points

- Definition + example for each pillar
- Diagram-based relation questions
- SOLID full forms + one-line meaning
- Inheritance types with examples
- Composition vs aggregation comparison
- Pattern categorization questions

---

## 19) Quick Revision Sheet

- [ ] Class/object basics
- [ ] 4 pillars with examples
- [ ] Overloading vs overriding
- [ ] Abstract class vs interface
- [ ] Association vs aggregation vs composition
- [ ] SOLID principles
- [ ] Coupling/cohesion
- [ ] Major design patterns
- [ ] Common interview design trade-offs

---

*Last updated: 2026 | Target: exams, viva, placements, and software interviews*
