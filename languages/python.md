# Python — Detailed Interview, Exam & Practical Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

> **Focus:** Python fundamentals, object model, functions, OOP, iterators/generators, decorators, exceptions, typing, memory, concurrency, async programming, standard library, performance, testing, and interview preparation.

---

## 1. Python Overview

Python is a high-level, dynamically typed, garbage-collected, multi-paradigm programming language with a large standard library.

It supports:

- Procedural programming.
- Object-oriented programming.
- Functional programming techniques.
- Iterators and generators.
- Async programming.
- Type hints and static analysis.

Python source is commonly compiled to bytecode and executed by an implementation such as CPython, but “Python” is a language specification/ecosystem rather than one single execution engine.

### Current version note

Python 3.14 is the current feature line in 2026. Use the Python version targeted by a project when relying on newly introduced syntax or runtime features; do not assume every Python implementation supports CPython-specific behavior.

---

## 2. Python Execution Model

A simplified CPython view:

```text
.py source
   |
   v
parser/compiler
   |
   v
bytecode
   |
   v
CPython evaluation/runtime
   |
   v
objects + C/runtime APIs + OS
```

Implementation details differ across Python implementations, so do not treat bytecode or reference counting as universal Python language guarantees.

---

## 3. Variables and Names

Python variables are **names bound to objects**.

```python
x = 10
y = x
```

The name `x` does not contain an integer in the same sense as a fixed C integer variable; it refers to an object.

```python
x = [1, 2]
y = x
y.append(3)
print(x)  # [1, 2, 3]
```

Both names refer to the same list object.

### Identity vs equality

```python
a = [1, 2]
b = [1, 2]

print(a == b)  # True: values equal
print(a is b)  # False: different objects
```

Use `is` for identity checks, especially:

```python
if value is None:
    ...
```

Do not use `is` as a general replacement for `==`.

---

## 4. Built-in Data Types

| Type | Example | Mutable? |
|---|---|---|
| `int` | `42` | No |
| `float` | `3.14` | No |
| `complex` | `1+2j` | No |
| `bool` | `True` | No |
| `str` | `'hello'` | No |
| `bytes` | `b'abc'` | No |
| `bytearray` | `bytearray(b'abc')` | Yes |
| `tuple` | `(1, 2)` | No* |
| `list` | `[1, 2]` | Yes |
| `set` | `{1, 2}` | Yes |
| `frozenset` | `frozenset({1,2})` | No |
| `dict` | `{'a': 1}` | Yes |
| `NoneType` | `None` | No |

`tuple` is immutable as a container, but it can contain mutable objects.

```python
t = ([1, 2],)
t[0].append(3)  # allowed
```

---

## 5. Mutable vs Immutable

Immutable objects cannot be changed in place.

```python
s = 'hello'
s.upper()  # returns a new string
```

Lists mutate:

```python
items = [1, 2]
items.append(3)
```

### Why immutability matters

Immutable objects are easier to share safely and can be hashable when their contents support hashing. For example, strings and integers can be dictionary keys.

A tuple is hashable only when all of its elements are hashable.

---

## 6. Numbers and Arithmetic

Python integers have arbitrary precision in normal Python implementations; they are not limited to 32-bit or 64-bit ranges like typical C integer types.

```python
big = 10 ** 100
```

Division:

```python
7 / 2   # 3.5
7 // 2  # 3
7 % 2   # 1
```

Exponentiation:

```python
2 ** 10
```

Be aware of floating-point representation when comparing decimal results.

```python
0.1 + 0.2 == 0.3  # False
```

Use `decimal.Decimal` when decimal arithmetic requirements demand decimal semantics.

---

## 7. Strings

Strings are immutable Unicode text sequences.

```python
name = 'Alice'
message = f'Hello, {name}!'
```

Useful operations:

```python
s.strip()
s.lower()
s.upper()
s.split(',')
','.join(parts)
s.replace('old', 'new')
s.startswith('py')
s.endswith('.txt')
```

### `str` vs `bytes`

```text
str   → Unicode text
bytes → raw byte sequence
```

Encode/decode explicitly at boundaries:

```python
data = text.encode('utf-8')
text = data.decode('utf-8')
```

---

## 8. Lists, Tuples, Sets and Dictionaries

### List

Ordered, mutable sequence.

```python
items = [3, 1, 2]
items.append(4)
items.sort()
```

### Tuple

Ordered immutable sequence.

```python
point = (10, 20)
```

Useful for fixed records and unpacking.

### Set

Unique unordered collection with efficient average membership testing.

```python
unique = set(items)
if value in unique:
    ...
```

### Dictionary

Mapping of hashable keys to values.

```python
user = {'name': 'Alice', 'age': 25}
user['role'] = 'admin'
```

Modern Python dictionaries preserve insertion order as part of the language specification.

---

## 9. Dictionary Internals

A Python `dict` is hash-table based in CPython.

Average-case expectations:

```text
lookup   O(1)
insert   O(1)
delete   O(1)
```

Worst-case behavior can differ, and implementation details such as resizing and hashing matter.

Keys must be hashable. Mutable containers such as lists cannot normally be dictionary keys.

```python
# { [1, 2]: 'value' }  # TypeError
```

---

## 10. Slicing

```python
items[start:stop:step]
```

Examples:

```python
items[:3]
items[2:]
items[::2]
items[::-1]
```

Slicing a list creates a new list, so large slices can have O(k) time and memory cost.

---

## 11. Comprehensions

List comprehension:

```python
squares = [x * x for x in range(10)]
```

With filtering:

```python
evens = [x for x in numbers if x % 2 == 0]
```

Dictionary comprehension:

```python
lookup = {user.id: user for user in users}
```

Set comprehension:

```python
unique_lengths = {len(word) for word in words}
```

Use comprehensions when they remain readable; do not compress complex multi-step business logic into one line.

---

## 12. Functions

```python
def add(a: int, b: int) -> int:
    return a + b
```

Functions are first-class objects.

```python
def apply(fn, value):
    return fn(value)

apply(lambda x: x * 2, 5)
```

Functions can be:

- Stored in variables.
- Passed as arguments.
- Returned from functions.
- Stored in containers.

---

## 13. Default Arguments — Important Pitfall

Do not use mutable objects as defaults when you intend a fresh object per call.

Bad:

```python
def add_item(item, items=[]):
    items.append(item)
    return items
```

The default list is created once when the function is defined.

Better:

```python
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

This is one of the most common Python interview questions.

---

## 14. `*args` and `**kwargs`

```python
def log(message, *args, **kwargs):
    print(message)
    print(args)
    print(kwargs)
```

- `*args` collects extra positional arguments into a tuple.
- `**kwargs` collects extra keyword arguments into a dictionary.

Calling:

```python
log('hello', 1, 2, level='info')
```

---

## 15. Keyword-Only and Positional-Only Parameters

Keyword-only:

```python
def connect(host, *, timeout=5):
    ...
```

`timeout` must be passed by keyword.

Positional-only parameters use `/`:

```python
def power(x, /, exponent=2):
    return x ** exponent
```

These tools make APIs clearer and preserve flexibility for future parameter naming changes.

---

## 16. Scope and LEGB

Python name lookup is commonly summarized as:

```text
L → Local
E → Enclosing
G → Global
B → Builtins
```

Example:

```python
x = 'global'

def outer():
    x = 'enclosing'

    def inner():
        print(x)

    inner()
```

Use `nonlocal` to modify an enclosing binding and `global` to modify a module-level binding.

---

## 17. Closures

A nested function can retain access to variables from an enclosing scope.

```python
def make_counter():
    count = 0

    def next_value():
        nonlocal count
        count += 1
        return count

    return next_value
```

Closures are useful for factories, callbacks, decorators, and encapsulated state.

---

## 18. Lambda Functions

```python
square = lambda x: x * x
```

Lambdas contain a single expression. Use them for short callbacks, not for complex logic.

```python
users.sort(key=lambda u: u.name)
```

---

## 19. Iterables, Iterators and Generators

An **iterable** can produce an iterator. An **iterator** implements the iteration protocol and produces values one at a time.

```python
it = iter([1, 2, 3])
next(it)
```

### Generator

```python
def numbers():
    for i in range(5):
        yield i
```

Generators are lazy: values are produced as requested rather than constructing the entire sequence immediately.

### Why generators matter

They reduce memory use for large streams and are useful for pipelines.

```python
def read_lines(path):
    with open(path) as f:
        for line in f:
            yield line
```

---

## 20. Decorators

A decorator transforms or wraps a callable.

```python
from functools import wraps

def log_calls(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        print('calling', fn.__name__)
        return fn(*args, **kwargs)
    return wrapper

@log_calls
def add(a, b):
    return a + b
```

Use `functools.wraps` to preserve useful metadata such as function name and docstring.

Common uses:

- Logging.
- Authorization.
- Caching.
- Timing.
- Validation.
- Framework registration.

---

## 21. OOP in Python

```python
class User:
    def __init__(self, name):
        self.name = name

    def greet(self):
        return f'Hello {self.name}'
```

Python classes support inheritance, polymorphism, composition, descriptors, class methods, static methods, properties, and special methods.

### Instance vs class attributes

```python
class User:
    role = 'user'  # class attribute

    def __init__(self, name):
        self.name = name  # instance attribute
```

Attribute lookup can find the class attribute when no instance attribute shadows it.

---

## 22. `self`, `classmethod`, `staticmethod`

`self` is the conventional name for the instance parameter; it is not a reserved keyword.

### Class method

```python
class User:
    @classmethod
    def guest(cls):
        return cls('Guest')
```

Receives the class as `cls` and is useful for alternative constructors or class-level behavior.

### Static method

```python
class Math:
    @staticmethod
    def add(a, b):
        return a + b
```

No automatic instance/class argument.

---

## 23. Properties

```python
class User:
    def __init__(self, age):
        self._age = age

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value < 0:
            raise ValueError('invalid age')
        self._age = value
```

Properties allow attribute-style access while enforcing validation or computed behavior.

---

## 24. Inheritance and MRO

```python
class Animal:
    def speak(self):
        return 'sound'

class Dog(Animal):
    def speak(self):
        return 'woof'
```

Python uses the **Method Resolution Order (MRO)** to determine where attributes/methods are found, especially with multiple inheritance.

```python
Dog.mro()
```

Python uses C3 linearization to construct a consistent MRO.

### `super()`

`super()` follows the MRO; it does not simply mean “call my direct parent”.

---

## 25. Dunder / Special Methods

Python protocols are expressed through special methods.

Examples:

```python
__init__
__repr__
__str__
__len__
__iter__
__next__
__eq__
__lt__
__hash__
__enter__
__exit__
```

Example:

```python
class Point:
    def __init__(self, x, y):
        self.x, self.y = x, y

    def __repr__(self):
        return f'Point({self.x}, {self.y})'

    def __eq__(self, other):
        if not isinstance(other, Point):
            return NotImplemented
        return (self.x, self.y) == (other.x, other.y)
```

Return `NotImplemented` when an operation is not supported for the other type so Python can attempt reflected dispatch appropriately.

---

## 26. Dataclasses

Dataclasses reduce boilerplate for data-centric classes.

```python
from dataclasses import dataclass

@dataclass
class User:
    name: str
    age: int
```

They can generate methods such as `__init__`, `__repr__`, and comparisons depending on configuration.

### Default factory

For mutable defaults:

```python
from dataclasses import dataclass, field

@dataclass
class Cart:
    items: list[str] = field(default_factory=list)
```

Do not write `items: list[str] = []` for a shared mutable default.

---

## 27. Type Hints

Python type hints are primarily for tooling and static analysis; normal Python execution remains dynamically typed.

```python
def total(values: list[int]) -> int:
    return sum(values)
```

Modern typing includes:

- `list[int]`
- `dict[str, int]`
- `str | None`
- `Literal`
- `Protocol`
- `TypeVar`
- `Generic`
- `Callable`
- `TypedDict`

### Optional

Modern Python commonly writes:

```python
def find_user(id: int) -> User | None:
    ...
```

rather than the older `Optional[User]` spelling, when the project's supported Python version allows it.

---

## 28. Protocols and Structural Typing

`Protocol` enables structural typing: an object can satisfy an interface by having the required members without inheriting from a particular base class.

```python
from typing import Protocol

class HasName(Protocol):
    name: str
```

This is useful for decoupled APIs and dependency injection.

---

## 29. Exceptions

```python
try:
    value = int(text)
except ValueError as exc:
    print(exc)
else:
    print(value)
finally:
    cleanup()
```

Use specific exception types rather than catching everything.

Avoid:

```python
except Exception:
    pass
```

unless there is a deliberate reason and appropriate logging/handling.

### Custom exception

```python
class ValidationError(ValueError):
    pass
```

---

## 30. Context Managers

Context managers guarantee cleanup around a block.

```python
with open('data.txt') as f:
    content = f.read()
```

The file is closed when the block exits, including exception paths.

### Custom context manager

```python
from contextlib import contextmanager

@contextmanager
def managed_resource():
    resource = acquire()
    try:
        yield resource
    finally:
        release(resource)
```

This is Python's equivalent of a scope-based resource-management pattern.

---

## 31. Modules and Packages

A module is typically a Python source file. A package organizes modules into a reusable namespace.

```text
project/
  app/
    __init__.py
    models.py
    service.py
```

Use imports explicitly:

```python
from app.service import UserService
```

Avoid circular imports by designing dependency direction clearly and moving shared abstractions into lower-level modules when appropriate.

---

## 32. `if __name__ == '__main__'`

```python
def main():
    print('run')

if __name__ == '__main__':
    main()
```

When the file is executed directly, `__name__` is typically `'__main__'`. When imported, it is the module's import name.

This prevents command-line behavior from executing merely because another module imported the file.

---

## 33. Virtual Environments and Packaging

Use isolated environments so project dependencies do not conflict.

```bash
python -m venv .venv
```

Typical project tooling may include:

- `venv`
- `pip`
- `pyproject.toml`
- Build backends
- Lock/dependency tools

A modern project should declare its Python version and dependencies explicitly rather than relying on globally installed packages.

---

## 34. File Handling

Prefer context managers:

```python
with open('data.txt', 'r', encoding='utf-8') as f:
    text = f.read()
```

For large files, stream line by line rather than reading the entire file:

```python
with open(path, encoding='utf-8') as f:
    for line in f:
        process(line)
```

### JSON

```python
import json

with open('data.json', encoding='utf-8') as f:
    data = json.load(f)
```

Be cautious about deserializing untrusted formats that can execute code; JSON is preferable for ordinary data interchange.

---

## 35. Useful Standard Library Modules

| Module | Common use |
|---|---|
| `pathlib` | Filesystem paths |
| `collections` | Specialized containers |
| `itertools` | Iterator building blocks |
| `functools` | Higher-order utilities/caching |
| `dataclasses` | Data classes |
| `typing` | Type annotations |
| `contextlib` | Context manager utilities |
| `datetime` | Date/time |
| `json` | JSON serialization |
| `re` | Regular expressions |
| `logging` | Application logging |
| `subprocess` | External processes |
| `threading` | Thread-based concurrency |
| `multiprocessing` | Process-based parallelism |
| `asyncio` | Async I/O |
| `sqlite3` | SQLite database |

---

## 36. `collections` Essentials

### `Counter`

```python
from collections import Counter
Counter('banana')
```

### `defaultdict`

```python
from collections import defaultdict

groups = defaultdict(list)
groups['admin'].append(user)
```

### `deque`

Efficient append/pop from both ends.

```python
from collections import deque
q = deque()
q.append(1)
q.popleft()
```

Use `deque` rather than repeatedly `pop(0)` from a list for queue workloads.

---

## 37. Sorting

Python's sorting is stable.

```python
users.sort(key=lambda u: u.age)
```

or:

```python
sorted_users = sorted(users, key=lambda u: u.age)
```

`list.sort()` mutates the list and returns `None`; `sorted()` returns a new list and accepts any iterable.

---

## 38. Regular Expressions

```python
import re

pattern = re.compile(r'\d+')
match = pattern.search('order 123')
```

Prefer simple string methods when regex is unnecessary. Complex regular expressions can be hard to maintain and, with certain patterns, vulnerable to excessive backtracking.

---

## 39. Concurrency vs Parallelism

- **Concurrency:** multiple tasks make progress during overlapping periods.
- **Parallelism:** multiple tasks execute simultaneously on multiple compute resources.

Python provides multiple approaches:

```text
threading       → useful for many I/O-bound workloads
multiprocessing → process-based parallelism
asyncio         → cooperative asynchronous I/O
```

---

## 40. GIL — Important CPython Concept

In CPython, the **Global Interpreter Lock (GIL)** has historically limited simultaneous execution of Python bytecode by multiple threads within one interpreter.

Consequences for the common CPython build:

- Threads can be useful for I/O-bound workloads.
- Threads do not automatically make CPU-bound Python code execute in parallel across cores.
- Native extensions can release the GIL during suitable operations.
- Process-based parallelism can provide true multi-process CPU parallelism.

Modern CPython development is also adding free-threaded execution capabilities. Treat GIL behavior as **implementation/version dependent**, not as a universal Python-language rule.

---

## 41. Threading

```python
from concurrent.futures import ThreadPoolExecutor

with ThreadPoolExecutor(max_workers=8) as pool:
    results = list(pool.map(fetch_url, urls))
```

Threads share process memory, so synchronization may be required.

Useful primitives:

- `Lock`
- `RLock`
- `Semaphore`
- `Event`
- `Condition`
- `Queue`

Avoid shared mutable state when message passing or isolated tasks provide a simpler design.

---

## 42. Multiprocessing

Processes have separate address spaces and can run CPU-bound Python workloads in parallel across cores.

```python
from concurrent.futures import ProcessPoolExecutor

with ProcessPoolExecutor() as pool:
    results = list(pool.map(expensive_fn, values))
```

Process-based approaches have overhead for process creation and data serialization/IPC, so they are not automatically faster for small tasks.

---

## 43. `asyncio`

Async programming uses cooperative scheduling around awaitable operations.

```python
import asyncio

async def fetch():
    await asyncio.sleep(1)
    return 'done'

async def main():
    result = await fetch()
    print(result)

asyncio.run(main())
```

Use async I/O when operations spend significant time waiting and the libraries involved support async interfaces.

### Concurrent tasks

```python
results = await asyncio.gather(fetch_a(), fetch_b())
```

This can overlap independent waiting periods.

---

## 44. Async vs Threads

| Asyncio | Threads |
|---|---|
| Cooperative scheduling | OS/runtime-managed threads |
| Great for async I/O ecosystems | Useful when libraries are blocking |
| One event loop thread can manage many tasks | Shared-memory concurrency |
| Blocking call can stall event loop | Blocking call occupies its thread |
| Requires async-compatible APIs | Can wrap blocking APIs |

Do not call blocking CPU or blocking I/O operations directly inside an async event loop unless you intentionally offload them.

---

## 45. Memory Management

Python implementations manage object lifetime automatically.

CPython historically uses reference counting plus a cyclic garbage collector for reference cycles.

```text
references → object
              |
              +-- reference count reaches zero → often reclaimed immediately
              |
              +-- cycles → cyclic GC may detect them
```

Do not write application logic that assumes every Python implementation has identical destruction timing.

### `del`

`del x` removes a name/binding; it does not directly mean “free this object now”. The object becomes collectible when no relevant references remain and the implementation decides to reclaim it.

---

## 46. Copying

Assignment does not copy objects:

```python
b = a
```

Shallow copy:

```python
import copy
b = copy.copy(a)
```

Deep copy:

```python
b = copy.deepcopy(a)
```

Deep copy is not universally appropriate; objects with external resources, locks, file handles, or custom semantics may not be safely deep-copyable.

---

## 47. Performance and Big-O

Typical operations:

| Operation | Typical complexity |
|---|---:|
| List index | O(1) |
| List append | Amortized O(1) |
| List insert front | O(n) |
| Dict lookup | Average O(1) |
| Set membership | Average O(1) |
| Sorting | O(n log n) |
| String concatenation in a loop | Can become costly; prefer `''.join()` for many pieces |

Always consider actual workload, constants, memory, and algorithmic structure.

---

## 48. Caching

`functools.lru_cache` memoizes function results.

```python
from functools import lru_cache

@lru_cache(maxsize=1024)
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)
```

The arguments must satisfy the cache's hashing requirements.

Caching is useful only when reuse outweighs memory usage and invalidation complexity.

---

## 49. Logging

Prefer `logging` over scattered `print()` calls in production applications.

```python
import logging

logger = logging.getLogger(__name__)
logger.info('user created', extra={'user_id': user_id})
```

Benefits:

- Levels.
- Structured integration.
- Configurable handlers.
- Timestamps/context.
- Routing to files or observability systems.

Never log secrets or sensitive credentials.

---

## 50. Testing

A common Python testing stack uses `pytest` or the standard library's `unittest`.

Test behavior rather than implementation details.

```python
def test_add():
    assert add(2, 3) == 5
```

Important categories:

- Unit tests.
- Integration tests.
- End-to-end tests.
- Property-based tests.
- Regression tests.

Use fixtures and dependency injection to isolate external systems.

---

## 51. Debugging and Profiling

Useful tools/modules include:

```text
pdb          → debugger
cProfile     → CPU profiling
profile      → profiling interface
tracemalloc  → memory allocation tracing
```

Do not optimize based solely on intuition. Measure representative workloads first.

---

## 52. Security Basics

### `eval` / `exec`

Never evaluate untrusted input as Python code.

### Pickle

Python `pickle` is designed for Python object serialization, not safe untrusted data interchange. Loading malicious pickle data can execute arbitrary code.

Use JSON or another safe data format for untrusted input where appropriate.

### SQL injection

Use parameterized queries rather than string concatenation:

```python
cursor.execute(
    'SELECT * FROM users WHERE id = ?',
    (user_id,)
)
```

### Secrets

Use environment/secret-management systems rather than committing credentials into source control.

---

## 53. Common Interview Questions

### Q1. Is Python interpreted or compiled?

Python is a language with multiple implementations. CPython commonly compiles source to bytecode before executing it. “Interpreted only” is too simplistic.

### Q2. Is Python dynamically typed?

Yes. Names are bound to objects at runtime and objects carry type information.

### Q3. `is` vs `==`?

`is` checks object identity; `==` checks value equality according to the objects' comparison semantics.

### Q4. Mutable vs immutable?

Mutable objects can change in place; immutable objects cannot. Lists/dicts/sets are mutable; strings/integers/tuples are immutable containers.

### Q5. Why is a mutable default argument dangerous?

The default object is created once when the function is defined, so mutations can persist across calls.

### Q6. List vs tuple?

Lists are mutable; tuples are immutable. Tuples can be useful for fixed records and can be hashable when all elements are hashable.

### Q7. List vs set?

Lists preserve sequence semantics and support indexing; sets provide uniqueness and efficient average membership tests.

### Q8. Why are dictionary lookups fast?

Dictionaries use hash-table techniques, giving average O(1) lookup under normal hashing assumptions.

### Q9. What is a generator?

A lazy iterator-producing function using `yield`; values are produced on demand.

### Q10. Generator vs list comprehension?

A list comprehension creates the whole list; a generator expression produces values lazily and can use much less memory for large streams.

### Q11. What is a decorator?

A callable that wraps or transforms another callable, commonly used for logging, caching, authorization, and framework registration.

### Q12. What is MRO?

Method Resolution Order determines the order in which Python searches classes for attributes/methods, including multiple inheritance.

### Q13. What does `super()` do?

It provides a proxy for accessing methods according to the class's MRO; it is not simply a hard-coded parent call.

### Q14. `@staticmethod` vs `@classmethod`?

A static method receives no automatic class/instance argument; a class method receives the class as `cls`.

### Q15. What is a context manager?

An object/protocol that manages setup and cleanup around a `with` block.

### Q16. Why use `with open(...)`?

It guarantees file cleanup even when an exception occurs.

### Q17. What is the GIL?

A CPython implementation mechanism that historically prevents simultaneous execution of Python bytecode by multiple threads in one interpreter. It is not a universal property of Python itself, and CPython is evolving toward optional/free-threaded configurations.

### Q18. Threads vs processes?

Threads share memory and are useful for many I/O-bound workloads; processes provide separate address spaces and can enable CPU parallelism at higher IPC/serialization cost.

### Q19. Asyncio vs threading?

Asyncio uses cooperative tasks and requires async-compatible operations; threads can integrate blocking libraries but introduce shared-memory synchronization concerns.

### Q20. What is `deepcopy`?

It recursively copies supported nested objects, but not every object can or should be deep-copied.

### Q21. Why use `deque` for queues?

`deque` supports efficient operations at both ends; `list.pop(0)` requires shifting remaining elements.

### Q22. What are type hints?

Annotations used by tools/static analyzers and documentation; ordinary Python execution remains dynamically typed.

### Q23. What is a protocol?

A structural typing interface that describes required members without requiring explicit inheritance.

### Q24. Why is `pickle` unsafe for untrusted input?

Unpickling can execute attacker-controlled behavior; it is not a safe data interchange format for untrusted data.

### Q25. Why can an async function still block?

Because calling a blocking operation inside the event loop prevents other tasks from running until that operation returns. Offload blocking work when necessary.

---

## 54. Common Mistakes

| Mistake | Better approach |
|---|---|
| Using `is` for value comparison | Use `==` |
| Mutable default argument | Use `None`/factory |
| `list.pop(0)` as queue | Use `collections.deque` |
| Catching all exceptions and ignoring them | Catch specific errors and handle/log deliberately |
| Blocking inside `asyncio` | Use async APIs or offload blocking work |
| Assuming CPython behavior is Python-language behavior | Distinguish implementation details |
| Using `pickle` on untrusted data | Use safe interchange formats |
| Deep-copying everything | Design ownership/data flow intentionally |
| Huge one-line comprehensions | Prefer readable loops/functions |
| Logging credentials | Redact/separate secrets |

---

## 55. Quick Revision Checklist

- [ ] Names vs objects
- [ ] Identity vs equality
- [ ] Mutable/immutable types
- [ ] Lists/tuples/sets/dicts
- [ ] Dict hashing and complexity
- [ ] Slicing/comprehensions
- [ ] Functions and argument rules
- [ ] Mutable default arguments
- [ ] `*args` / `**kwargs`
- [ ] LEGB and closures
- [ ] Generators/iterators
- [ ] Decorators
- [ ] Classes/OOP
- [ ] MRO and `super()`
- [ ] Properties/dataclasses
- [ ] Dunder methods
- [ ] Type hints/Protocol
- [ ] Exceptions/context managers
- [ ] Modules/packages/venv
- [ ] File handling
- [ ] Standard library
- [ ] Threading/multiprocessing/asyncio
- [ ] CPython GIL
- [ ] Memory management
- [ ] Performance/caching
- [ ] Logging/testing/profiling
- [ ] Security/pickle/SQL injection
- [ ] Interview questions
