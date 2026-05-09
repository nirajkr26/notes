# Python — Super Detailed Interview & Exam Notes

> 📌 **GitHub:** [nirajkr26](https://github.com/nirajkr26) &nbsp;|&nbsp; **LinkedIn:** [nirajkr26](https://www.linkedin.com/in/nirajkr26)

---

## 1. Introduction to Python

Python is a **high-level, interpreted, dynamically typed, multi-paradigm** programming language created by **Guido van Rossum** (1991). It emphasizes **readability**, **simplicity**, and a rich standard library.

**Key Features:**
- Clean, readable syntax (indentation-based)
- Multi-paradigm: procedural, OOP, functional
- Huge standard library ("batteries included")
- Cross-platform and open source
- Massive ecosystem (web, data, ML, automation)

**Popular Implementations:**
- **CPython** (reference, most common)
- **PyPy** (JIT, faster for some workloads)
- **Jython** (runs on JVM)
- **IronPython** (runs on .NET)

---

## 2. Installation & Running Python

**Run a script:**
```bash
python3 app.py
```

**Interactive REPL:**
```bash
python3
```

**Virtual environments:**
```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

**Package manager:** `pip` (PyPI)

---

## 3. Syntax Basics

### 3.1 Indentation & Blocks
Python uses **indentation** instead of braces.
```python
if x > 0:
    print("positive")
else:
    print("non-positive")
```

### 3.2 Comments
```python
# single line comment
"""multi-line
comment or docstring"""
```

### 3.3 Input / Output
```python
name = input("Enter name: ")
print(f"Hello, {name}")
```

---

## 4. Data Types

### 4.1 Built-in Types
| Type | Example | Mutable? |
|------|---------|----------|
| `int` | `42` | ❌ |
| `float` | `3.14` | ❌ |
| `bool` | `True` | ❌ |
| `str` | `"hello"` | ❌ |
| `NoneType` | `None` | ❌ |
| `list` | `[1,2,3]` | ✅ |
| `tuple` | `(1,2)` | ❌ |
| `dict` | `{ "a": 1 }` | ✅ |
| `set` | `{1,2,3}` | ✅ |
| `bytes` | `b"abc"` | ❌ |

### 4.2 Type Conversion
```python
int("10")    # 10
float("3.5") # 3.5
str(123)     # "123"
bool(0)      # False
```

---

## 5. Operators

| Category | Operators |
|----------|-----------|
| Arithmetic | `+ - * / // % **` |
| Comparison | `== != < > <= >=` |
| Logical | `and or not` |
| Bitwise | `& | ^ ~ << >>` |
| Membership | `in`, `not in` |
| Identity | `is`, `is not` |

```python
5 / 2   # 2.5
5 // 2  # 2
2 ** 3  # 8
```

---

## 6. Control Flow

### 6.1 if / elif / else
```python
if score >= 90:
    grade = "A"
elif score >= 80:
    grade = "B"
else:
    grade = "C"
```

### 6.2 Loops
```python
for i in range(3):
    print(i)

while n > 0:
    n -= 1
```

**Loop else:** runs if loop completes without `break`.
```python
for x in items:
    if x == target:
        break
else:
    print("not found")
```

### 6.3 `match` (Python 3.10+)
```python
match status:
    case 200:
        print("OK")
    case 404:
        print("Not Found")
    case _:
        print("Other")
```

---

## 7. Functions

### 7.1 Basics
```python
def add(a, b=0):
    return a + b
```

### 7.2 *args and **kwargs
```python
def f(*args, **kwargs):
    print(args, kwargs)

f(1, 2, x=3)  # (1,2) {'x':3}
```

### 7.3 Keyword-only Arguments
```python
def connect(host, *, timeout=10):
    pass

connect("localhost", timeout=5)
```

### 7.4 Annotations & Docstrings
```python
def area(r: float) -> float:
    """Return area of a circle."""
    return 3.1416 * r * r
```

---

## 8. Scope & Namespaces (LEGB)

- **L**ocal
- **E**nclosing
- **G**lobal
- **B**uilt-in

```python
x = "global"

def outer():
    x = "enclosing"
    def inner():
        nonlocal x
        x = "inner"
    inner()
    return x
```

Use `global` to rebind module-level variables.

---

## 9. Collections Deep Dive

### 9.1 Lists
```python
nums = [1, 2, 3]
nums.append(4)
nums[1:3]  # slicing
```

### 9.2 Tuples
```python
point = (3, 4)
```

### 9.3 Dictionaries
```python
user = {"id": 1, "name": "Ada"}
user["name"] = "Ada Lovelace"
```

### 9.4 Sets
```python
s = {1, 2, 3}
s.add(2)   # no duplicates
```

### 9.5 Comprehensions
```python
squares = [x * x for x in range(5)]
unique = {x for x in [1,2,2,3]}
lookup = {x: x * x for x in range(3)}
```

### 9.6 Useful `collections`
- `deque` (O(1) append/pop both ends)
- `Counter` (frequency map)
- `defaultdict` (default values)
- `namedtuple` / `dataclass`

---

## 10. Strings & Formatting

```python
s = "hello"
s.upper()            # "HELLO"
"-".join(["a", "b"]) # "a-b"
```

**f-strings (Python 3.6+):**
```python
name = "Neo"
age = 30
print(f"{name} is {age} years old")
```

**Encoding:**
```python
"café".encode("utf-8")
b"caf\xc3\xa9".decode("utf-8")
```

---

## 11. File I/O & Serialization

```python
with open("data.txt", "r", encoding="utf-8") as f:
    text = f.read()
```

**Write:**
```python
with open("out.txt", "w") as f:
    f.write("hello")
```

**JSON:**
```python
import json
json.dumps({"x": 1})
json.loads('{"x": 1}')
```

**CSV:**
```python
import csv
with open("data.csv") as f:
    reader = csv.DictReader(f)
```

---

## 12. Exceptions & Error Handling

```python
try:
    x = 1 / 0
except ZeroDivisionError as e:
    print(e)
else:
    print("no error")
finally:
    print("cleanup")
```

**Custom exceptions:**
```python
class InvalidAge(ValueError):
    pass

raise InvalidAge("age must be >= 0")
```

---

## 13. Object-Oriented Programming

```python
class User:
    role = "member"  # class attribute
    def __init__(self, name):
        self.name = name  # instance attribute

    def greet(self):
        return f"Hi {self.name}"
```

### 13.1 Inheritance & MRO
```python
class Admin(User):
    role = "admin"
```

**MRO:** `Admin -> User -> object`

### 13.2 Properties
```python
class Circle:
    def __init__(self, r):
        self._r = r

    @property
    def area(self):
        return 3.1416 * self._r ** 2
```

### 13.3 `@dataclass`
```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int
```

### 13.4 Dunder Methods
- `__str__`, `__repr__`
- `__len__`, `__iter__`, `__getitem__`
- `__eq__`, `__lt__`
- `__enter__`, `__exit__` (context managers)

---

## 14. Iterators & Generators

```python
it = iter([1, 2, 3])
next(it)  # 1
```

**Generator function:**
```python
def countdown(n):
    while n > 0:
        yield n
        n -= 1
```

**Generator expression:**
```python
gen = (x * x for x in range(5))
```

---

## 15. Decorators

```python
from functools import wraps

def log_call(fn):
    @wraps(fn)
    def wrapper(*args, **kwargs):
        print(f"Calling {fn.__name__}")
        return fn(*args, **kwargs)
    return wrapper

@log_call
def add(a, b):
    return a + b
```

**Parameterized decorator:**
```python
def retry(times):
    def deco(fn):
        def wrapped(*args, **kwargs):
            for _ in range(times):
                try:
                    return fn(*args, **kwargs)
                except Exception:
                    pass
            raise RuntimeError("failed")
        return wrapped
    return deco
```

---

## 16. Modules & Packages

```python
import math
from datetime import datetime
```

**Packages** are folders with `__init__.py` (can be empty).

**Import styles:**
- Absolute: `from app.utils import slugify`
- Relative: `from .utils import slugify`

---

## 17. Concurrency & Async

### 17.1 Threading (I/O-bound)
```python
import threading

def worker():
    pass

thread = threading.Thread(target=worker)
thread.start()
```

### 17.2 Multiprocessing (CPU-bound)
```python
from multiprocessing import Pool

with Pool(4) as p:
    p.map(sum, [[1,2,3], [4,5,6]])
```

### 17.3 asyncio (single-threaded concurrency)
```python
import asyncio

async def fetch():
    await asyncio.sleep(1)

asyncio.run(fetch())
```

**GIL:** CPython allows only one thread to execute Python bytecode at a time.

---

## 18. Memory Management & Performance

- **Reference counting** + **cyclic GC**
- Use `__slots__` to reduce per-instance memory
- Prefer list comprehensions and built-in functions (`sum`, `map`)
- `list` append is amortized O(1); `dict` lookup is average O(1)

---

## 19. Testing, Typing & Tooling

- **unittest** (built-in)
- **pytest** (popular external)
- **type hints** with `typing` module
- **mypy** for static type checks
- **black/ruff/isort** for formatting & linting

```python
from typing import List

def total(nums: List[int]) -> int:
    return sum(nums)
```

---

## 20. Frequently Asked Questions (FAQs)

**Q1. What is the difference between a list and a tuple?**
> Lists are mutable, tuples are immutable. Tuples are hashable (if their elements are), so they can be dict keys.

**Q2. What is the Global Interpreter Lock (GIL)?**
> A CPython mechanism that allows only one thread to execute Python bytecode at a time. It simplifies memory management but limits CPU-bound threading.

**Q3. What is the difference between `is` and `==`?**
> `is` checks object identity (same object in memory). `==` checks value equality (via `__eq__`).

**Q4. Explain shallow vs deep copy.**
> Shallow copy duplicates the outer container but reuses inner object references; deep copy recursively copies all nested objects.

**Q5. What are decorators used for?**
> They wrap functions to add behavior (logging, auth, caching, retries) without modifying function code.

**Q6. Why are default mutable arguments dangerous?**
> Default values are evaluated once at function definition, so a list default is shared across calls.

**Q7. What is a context manager?**
> An object with `__enter__` and `__exit__` used with `with` to ensure cleanup (files, locks, DB connections).

**Q8. What is the difference between `*args` and `**kwargs`?**
> `*args` collects positional args into a tuple; `**kwargs` collects keyword args into a dict.

**Q9. How does `async`/`await` work?**
> It builds on coroutines and an event loop; `await` yields control until the awaited task completes.

**Q10. What is a generator and why use it?**
> Generators lazily produce values, saving memory for large or infinite sequences.

---

## 21. Common Misconceptions

- ❌ *"Python is slow for everything"* → It is slower than C for CPU-bound tasks, but great for I/O, scripting, and with optimized libraries.
- ❌ *"`list` lookup is O(1)"* → List lookup by index is O(1), but searching by value is O(n).
- ❌ *"`is` and `==` are the same"* → `is` compares identity; `==` compares values.
- ❌ *"Default arguments are recalculated each call"* → They are evaluated once at definition time.
- ❌ *"The GIL prevents all concurrency"* → It limits CPU-bound threading but not I/O concurrency or multiprocessing.

---

## 22. Quick Revision Checklist

- [ ] Python philosophy: readability, simplicity, batteries included
- [ ] CPython vs PyPy; virtualenv + pip
- [ ] Data types: mutability, list vs tuple vs dict vs set
- [ ] `is` vs `==`, `in` operator, truthy/falsy
- [ ] Control flow and `match` statement
- [ ] Functions: defaults, `*args`, `**kwargs`, keyword-only
- [ ] LEGB scope, `global`, `nonlocal`
- [ ] Comprehensions & slicing
- [ ] Exceptions: try/except/else/finally
- [ ] OOP: class vs instance, inheritance, MRO, `@property`
- [ ] Iterators, generators, `yield`
- [ ] Decorators and `functools.wraps`
- [ ] Concurrency: threading vs multiprocessing vs asyncio
- [ ] GIL and performance basics

---

*Last updated: 2026 | Suitable for: university exams, software engineering interviews, backend & data roles*
