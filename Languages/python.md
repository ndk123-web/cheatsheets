# Python Cheatsheet

Comprehensive end-to-end reference for Python from CPython runtime internals, memory layout, and the GIL to advanced metaprogramming, asyncio, Pydantic data modeling, FastAPI backend architecture, and production tooling.

---

## Table of Contents
- [High Priority Topics](#high-priority-topics)
- [1 Python Architecture, CPython Internals, and Memory Model](#1-python-architecture-cpython-internals-and-memory-model)
- [2 Environment, Tooling, and Package Management](#2-environment-tooling-and-package-management)
- [3 Variables, Scopes, and LEGB Rule](#3-variables-scopes-and-legb-rule)
- [4 Data Types, Mutability, and Memory Representation](#4-data-types-mutability-and-memory-representation)
- [5 Strings, Slicing, and Formatting](#5-strings-slicing-and-formatting)
- [6 Control Flow and Pattern Matching](#6-control-flow-and-pattern-matching)
- [7 Functions, Arguments, and Closures](#7-functions-arguments-and-closures)
- [8 Decorators and Metaprogramming](#8-decorators-and-metaprogramming)
- [9 Data Structures and Collections Module](#9-data-structures-and-collections-module)
- [10 Comprehensions and Iteration Protocols](#10-comprehensions-and-iteration-protocols)
- [11 Generators, Yield, and Memory-Efficient Pipelines](#11-generators-yield-and-memory-efficient-pipelines)
- [12 Object-Oriented Programming (OOP) and Dunder Methods](#12-object-oriented-programming-oop-and-dunder-methods)
- [13 Dataclasses and Pydantic (Modern Data Modeling)](#13-dataclasses-and-pydantic-modern-data-modeling)
- [14 Error and Exception Handling](#14-error-and-exception-handling)
- [15 Context Managers and the with Statement](#15-context-managers-and-the-with-statement)
- [16 Static Typing and Type Hints (PEP 484 - PEP 695)](#16-static-typing-and-type-hints-pep-484---pep-695)
- [17 Concurrency: Multi-Threading, Multi-Processing, and Asyncio](#17-concurrency-multi-threading-multi-processing-and-asyncio)
- [18 Modern Web Backends (FastAPI & Async Architecture)](#18-modern-web-backends-fastapi--async-architecture)
- [19 File I/O, Serialization, and Network Requests](#19-file-io-serialization-and-network-requests)
- [20 Testing, Benchmarking, and Profiling](#20-testing-benchmarking-and-profiling)
- [21 High-Yield Interview Questions and Reality Check](#21-high-yield-interview-questions-and-reality-check)

---

## High Priority Topics

Most asked in Python interviews and core systems engineering:
1. **The Global Interpreter Lock (GIL) Mechanics, Multi-threading vs Multi-processing**
2. **Memory Management (Reference Counting, Cyclic Generational GC, `sys.getrefcount`)**
3. **Mutable Default Argument Trap (`def fn(a=[])`)**
4. **`is` vs `==` & Small Integer Caching (-5 to 256) / String Interning**
5. **Scopes & LEGB Rule (`global` vs `nonlocal`)**
6. **Closures, Decorators (`@functools.wraps`), and Parameterized Decorators**
7. **Iterators (`__iter__`, `__next__`) vs Generators (`yield`, `yield from`)**
8. **Dunder Methods (`__new__` vs `__init__`, `__str__` vs `__repr__`, `__call__`, `__slots__`)**
9. **Method Resolution Order (MRO & C3 Linearization)**
10. **Asyncio Event Loop, Coroutines, and Structured Concurrency (`asyncio.TaskGroup`)**

---

## 1 Python Architecture, CPython Internals, and Memory Model

### CPython Execution Pipeline
```
Python Source Code (.py)
       │
       ▼ [Tokenizer & AST Parser]
Abstract Syntax Tree (AST)
       │
       ▼ [Bytecode Compiler]
Bytecode (.pyc cached in __pycache__)
       │
       ▼
Python Virtual Machine (PVM - Stack-based interpreter loop)
```

### Memory Management in CPython
CPython combines two memory management layers:
1. **Reference Counting (Primary)**: Every object header contains `ob_refcnt`. When `refcnt == 0`, memory is deallocated **immediately**.
2. **Cyclic Generational Garbage Collector (Secondary)**: Handles cyclic references (e.g. `a.ref = b; b.ref = a`) using 3 generations (`Gen 0`, `Gen 1`, `Gen 2`):
   - `Gen 0`: Newly allocated objects (scanned most frequently).
   - `Gen 1`: Survived 1 GC cycle.
   - `Gen 2`: Long-lived objects (scanned least frequently).

```python
import sys
import gc

a = []
print(sys.getrefcount(a)) # 2 (variable 'a' + argument passed into getrefcount)
b = a
print(sys.getrefcount(a)) # 3
del b
print(sys.getrefcount(a)) # 2

# Force cyclic garbage collection
gc.collect()
```

### The Global Interpreter Lock (GIL)
- The GIL is a mutex that prevents multiple native OS threads from executing Python bytecodes simultaneously.
- **Why it exists**: Protects CPython's reference counting mechanism from race conditions without complex per-object locks.
- **I/O-Bound Work**: Threads release the GIL during I/O operations (network, disk, `time.sleep`). Multi-threading is effective here.
- **CPU-Bound Work**: Threads compete for the single GIL lock, suffering context-switch overhead. Use `multiprocessing` instead.
- **Python 3.13+ Free-Threaded Build**: Experimental support for disabling the GIL entirely (`--disable-gil`).

---

## 2 Environment, Tooling, and Package Management

### Package Managers Comparison
| Tool | Speed | Description | Command |
| :--- | :--- | :--- | :--- |
| **uv** | Ultra-Fast (Rust) | Modern drop-in replacement for pip/pip-tools/virtualenv | `uv venv`, `uv pip install fastapi` |
| **poetry** | Fast | Dependency resolver, virtualenv, and build tool | `poetry add fastapi`, `poetry run` |
| **pip + venv** | Standard | Default built-in toolchain | `python -m venv .venv`, `pip install -r requirements.txt` |

### Modern `pyproject.toml` Standard (PEP 621)
```toml
[project]
name = "my-app"
version = "0.1.0"
description = "High-performance Python backend"
readme = "README.md"
requires-python = ">=3.11"
dependencies = [
    "fastapi>=0.110.0",
    "uvicorn[standard]>=0.28.0",
    "pydantic>=2.6.0",
    "sqlalchemy[asyncio]>=2.0.0",
    "asyncpg>=0.29.0",
    "httpx>=0.27.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0.0",
    "pytest-asyncio>=0.23.0",
    "ruff>=0.3.0",
    "mypy>=1.9.0",
]

[tool.ruff]
line-length = 88
target-version = "py311"
```

---

## 3 Variables, Scopes, and LEGB Rule

### The LEGB Lookup Order
When a variable is accessed, Python searches scopes in this exact order:
1. **L - Local**: Inside the current function/lambda.
2. **E - Enclosing**: Inside enclosing functions (outer closures).
3. **G - Global**: Top-level module variables.
4. **B - Built-in**: Pre-loaded Python symbols (`len`, `range`, `ValueError`).

### `global` vs `nonlocal` Keywords
```python
x = "GLOBAL"

def outer():
    x = "ENCLOSING"

    def inner_modify_global():
        global x
        x = "MUTATED_GLOBAL"

    def inner_modify_enclosing():
        nonlocal x
        x = "MUTATED_ENCLOSING"

    inner_modify_enclosing()
    print("outer x:", x) # "MUTATED_ENCLOSING"

outer()
```

---

## 4 Data Types, Mutability, and Memory Representation

### Data Types Classification
| Category | Mutability | Types | Description |
| :--- | :--- | :--- | :--- |
| **Numeric** | Immutable | `int`, `float`, `complex`, `bool` | Unlimited precision `int`, IEEE-754 `float` |
| **Sequence** | Immutable | `str`, `tuple`, `bytes` | Fixed-size, hashable |
| **Sequence** | **Mutable** | `list`, `bytearray` | Dynamic arrays, over-allocates on growth |
| **Set** | **Mutable** / Immutable | `set` (mutable), `frozenset` (immutable) | Hash-table based unique collections |
| **Mapping** | **Mutable** | `dict` | Compact hash table (insertion-ordered since 3.7) |

### `is` vs `==` & Small Integer Caching
- `==` checks for **value equality** (`__eq__`).
- `is` checks for **identity / same memory address** (`id(a) == id(b)`).

```python
# CPython pre-allocates integers from -5 to 256 in memory pool:
a = 256
b = 256
print(a is b) # True (Same memory address from pool)

c = 257
d = 257
print(c == d) # True (Same value)
print(c is d) # False (Different heap objects in interactive shell)
```

### Shallow vs Deep Copy
```python
import copy

original = [1, [2, 3], 4]

# Shallow copy: Copies outer list, inner list is a shared reference
shallow = list(original) # or original.copy()
shallow[1].append(99)
print(original) # [1, [2, 3, 99], 4] -> Mutated!

# Deep copy: Recursively duplicates all nested objects
deep = copy.deepcopy(original)
deep[1].append(100)
print(original) # [1, [2, 3, 99], 4] -> Untouched!
```

---

## 5 Strings, Slicing, and Formatting

### String Slicing: `[start:stop:step]`
```python
text = "PythonProgramming"

print(text[0:6])     # "Python"
print(text[:6])      # "Python"
print(text[6:])      # "Programming"
print(text[::2])     # "PtoPormig" (Every 2nd char)
print(text[::-1])    # "gnimmargorPnohtyP" (Reverse string)
```

### Modern f-Strings (Formatting & Debugging)
```python
name = "Navnath"
score = 98.4567
price = 1000000

# 1. Debugging output with '=' (Python 3.8+)
print(f"{name=}") # name='Navnath'

# 2. Number formatting
print(f"{score:.2f}")       # '98.46' (2 decimal places)
print(f"{price:,}")         # '1,000,000' (Thousands comma)
print(f"{score:>10.2f}")    # '     98.46' (Right-aligned, width 10)
print(f"{0.25:.1%}")        # '25.0%' (Percentage)
```

---

## 6 Control Flow and Pattern Matching

### The `for...else` and `while...else` Construct
The `else` block executes **ONLY if the loop finishes naturally without encountering a `break`**.

```python
def find_prime(n):
    for i in range(2, int(n**0.5) + 1):
        if n % i == 0:
            print(f"{n} is divisible by {i}")
            break
    else:
        print(f"{n} is PRIME!")

find_prime(17) # "17 is PRIME!"
```

### Structural Pattern Matching (`match / case` - Python 3.10+)
```python
def handle_command(command: dict):
    match command:
        case {"action": "move", "direction": ("up" | "down" | "left" | "right") as dir, "steps": int(s)} if s > 0:
            print(f"Moving {dir} by {s} steps")
        case {"action": "attack", "target": str(t)}:
            print(f"Attacking {t}")
        case _:
            print("Unknown command")
```

### The Walrus Operator `:=` (Assignment Expression)
```python
# Assign and test condition simultaneously
data = [1, 2, 3, 4, 5]
if (n := len(data)) > 3:
    print(f"List is long with {n} elements")

# In while loops for reading streams
# while (chunk := file.read(1024)): process(chunk)
```

---

## 7 Functions, Arguments, and Closures

### Positional-Only (`/`) and Keyword-Only (`*`) Parameters
```python
# Parameters before '/' MUST be passed positionally.
# Parameters after '*' MUST be passed by keyword name.
def configure_server(host: str, port: int, /, *, ssl: bool = False, timeout: int = 30):
    return f"{host}:{port} (SSL: {ssl}, Timeout: {timeout})"

# configure_server("127.0.0.1", 8080, ssl=True)      # OK
# configure_server(host="127.0.0.1", port=8080)       # ERROR: host, port are positional-only
# configure_server("127.0.0.1", 8080, True)           # ERROR: ssl is keyword-only
```

### The Mutable Default Argument Gotcha
Default arguments are evaluated **ONCE when the function definition is executed**, not at each call!

```python
# BAD: Shared mutable list across all calls
def append_bad(item, target=[]):
    target.append(item)
    return target

print(append_bad(1)) # [1]
print(append_bad(2)) # [1, 2] -> BUG!

# GOOD / IDIOMATIC: Use None as default sentinel
def append_good(item, target=None):
    if target is None:
        target = []
    target.append(item)
    return target
```

---

## 8 Decorators and Metaprogramming

### Standard Function Decorators & `@functools.wraps`
```python
import functools
import time

def timing_decorator(fn):
    @functools.wraps(fn) # Preserves original function __name__, __doc__, and signature
    def wrapper(*args, **kwargs):
        start = time.perf_counter()
        result = fn(*args, **kwargs)
        duration = time.perf_counter() - start
        print(f"[{fn.__name__}] executed in {duration:.4f}s")
        return result
    return wrapper

@timing_decorator
def process_data(count: int):
    """Processes computational batch."""
    return sum(i * i for i in range(count))

process_data(1_000_000)
print(process_data.__name__) # "process_data" (preserves metadata due to wraps)
```

### Decorators with Arguments (Parameterized Decorators)
```python
def retry(max_attempts=3, delay_sec=1):
    def decorator(fn):
        @functools.wraps(fn)
        def wrapper(*args, **kwargs):
            attempts = 0
            while attempts < max_attempts:
                try:
                    return fn(*args, **kwargs)
                except Exception as e:
                    attempts += 1
                    if attempts >= max_attempts:
                        raise e
                    time.sleep(delay_sec)
        return wrapper
    return decorator

@retry(max_attempts=3, delay_sec=0.5)
def call_flaky_api():
    pass
```

---

## 9 Data Structures and Collections Module

### Specialized Collections (`collections`)
```python
from collections import defaultdict, Counter, deque, namedtuple

# 1. defaultdict: Default factory for missing keys
grouped = defaultdict(list)
grouped["admins"].append("Alex")

# 2. Counter: Frequency multiset
counts = Counter("abracadabra")
print(counts.most_common(2)) # [('a', 5), ('b', 2)]

# 3. deque: Double-ended queue with O(1) appends and pops on both ends
queue = deque(maxlen=3)
queue.append(1)
queue.append(2)
queue.append(3)
queue.append(4) # Automatically evicts 1 from left!

# 4. namedtuple: Lightweight tuple with named fields
Point = namedtuple("Point", ["x", "y"])
p = Point(10, 20)
print(p.x, p.y)
```

### Priority Queues with `heapq`
```python
import heapq

# Min-heap by default
heap = []
heapq.heappush(heap, (3, "low priority task"))
heapq.heappush(heap, (1, "CRITICAL task"))
heapq.heappush(heap, (2, "medium task"))

priority, task = heapq.heappop(heap)
print(f"Executing: {task}") # "CRITICAL task"
```

---

## 10 Comprehensions and Iteration Protocols

```python
# 1. List Comprehension
squares = [x * x for x in range(10) if x % 2 == 0]

# 2. Dict Comprehension
user_map = {u["id"]: u["name"] for u in [{"id": 1, "name": "Aman"}, {"id": 2, "name": "Alex"}]}

# 3. Set Comprehension
unique_lengths = {len(w) for w in ["apple", "banana", "apple", "pie"]}
```

### Iteration Protocol (`__iter__` and `__next__`)
```python
class CountDown:
    def __init__(self, start: int):
        self.current = start

    def __iter__(self):
        return self

    def __next__(self):
        if self.current <= 0:
            raise StopIteration
        val = self.current
        self.current -= 1
        return val

for n in CountDown(3):
    print(n) # 3, 2, 1
```

---

## 11 Generators, Yield, and Memory-Efficient Pipelines

Generators produce items **lazily on-demand (O(1) memory footprint)**.

```python
# Generator function
def read_large_file(file_path: str):
    with open(file_path, "r", encoding="utf-8") as f:
        for line in f:
            yield line.strip()

# Generator Pipeline: Streams lines without loading entire 5GB file into RAM
lines = read_large_file("access.log")
error_lines = (line for line in lines if "ERROR 500" in line)
ip_addresses = (line.split()[0] for line in error_lines)

# Process 1 by 1
# for ip in ip_addresses: process(ip)
```

### `yield from` (Delegating to Sub-generators)
```python
def flatten(nested):
    for sub in nested:
        if isinstance(sub, list):
            yield from flatten(sub)
        else:
            yield sub

print(list(flatten([1, [2, [3, 4], 5], 6]))) # [1, 2, 3, 4, 5, 6]
```

---

## 12 Object-Oriented Programming (OOP) and Dunder Methods

### Essential Dunder Methods
```python
class Vector:
    # Memory optimization: Disables per-instance __dict__ and allocates fixed array
    __slots__ = ("x", "y")

    def __init__(self, x: float, y: float):
        self.x = x
        self.y = y

    # String for developers / debugging
    def __repr__(self) -> str:
        return f"Vector({self.x}, {self.y})"

    # String for users
    def __str__(self) -> str:
        return f"({self.x}, {self.y})"

    # Operator overloading: v1 + v2
    def __add__(self, other: "Vector") -> "Vector":
        return Vector(self.x + other.x, self.y + other.y)

    # Equality check: v1 == v2
    def __eq__(self, other: object) -> bool:
        if not isinstance(other, Vector):
            return False
        return self.x == other.x and self.y == other.y

    # Callable instance: v()
    def __call__(self) -> float:
        return (self.x**2 + self.y**2) ** 0.5
```

### Inheritance and Method Resolution Order (MRO)
Python uses the **C3 Linearization** algorithm to compute MRO for multiple inheritance.

```python
class A:
    def ping(self): print("A")

class B(A):
    def ping(self): print("B"); super().ping()

class C(A):
    def ping(self): print("C"); super().ping()

class D(B, C):
    def ping(self): print("D"); super().ping()

d = D()
d.ping() # Prints: D -> B -> C -> A (Cooperative multiple inheritance via MRO)
print(D.mro()) # [D, B, C, A, object]
```

---

## 13 Dataclasses and Pydantic (Modern Data Modeling)

### Python Standard `@dataclass` (PEP 557)
```python
from dataclasses import dataclass, field
from datetime import datetime

@dataclass(frozen=True, slots=True) # Immutable + memory efficient
class UserAccount:
    id: str
    username: str
    is_active: bool = True
    tags: list[str] = field(default_factory=list) # Mutable default factory
    created_at: datetime = field(default_factory=datetime.utcnow)
```

### Pydantic v2 (Runtime Validation & Serialization)
```python
from pydantic import BaseModel, EmailStr, Field, field_validator

class CreateUserSchema(BaseModel):
    id: str
    username: str = Field(min_length=3, max_length=20)
    email: EmailStr
    age: int | None = Field(default=None, ge=18)
    role: str = "viewer"

    @field_validator("username")
    @classmethod
    def validate_username(cls, val: str) -> str:
        if " " in val:
            raise ValueError("Username cannot contain spaces")
        return val.lower()

# Runtime validation and automatic JSON parsing
user = CreateUserSchema.model_validate({
    "id": "u-123",
    "username": "Navnath_Dev",
    "email": "dev@example.com",
    "age": 25
})

print(user.model_dump_json()) # Serialized JSON string
```

---

## 14 Error and Exception Handling

### Exception Flow Architecture
```python
class DomainError(Exception):
    """Base domain exception."""

class ResourceNotFoundError(DomainError):
    def __init__(self, resource: str, id: str):
        super().__init__(f"{resource} with id '{id}' was not found.")

def fetch_user(user_id: str):
    try:
        if user_id == "0":
            raise ValueError("Invalid user ID")
        # simulate DB error
    except ValueError as e:
        # Exception Chaining: Preserve original traceback with 'from'
        raise ResourceNotFoundError("User", user_id) from e
    else:
        print("Runs ONLY if NO exception was raised")
    finally:
        print("Runs ALWAYS (cleanup)")
```

### Exception Groups (Python 3.11+)
```python
# Handling concurrent/multiple errors simultaneously
try:
    raise ExceptionGroup("Batch errors", [
        ValueError("Invalid format"),
        TypeError("Expected string"),
    ])
except* ValueError as eg:
    print("Handled ValueErrors:", eg.exceptions)
except* TypeError as eg:
    print("Handled TypeErrors:", eg.exceptions)
```

---

## 15 Context Managers and the with Statement

### Class-Based Context Manager
```python
class DatabaseTransaction:
    def __init__(self, connection):
        self.connection = connection

    def __enter__(self):
        print("BEGIN TRANSACTION")
        return self

    def __exit__(self, exc_type, exc_val, exc_tb):
        if exc_type is not None:
            print(f"ROLLBACK due to {exc_val}")
            return False # Re-raise exception
        print("COMMIT TRANSACTION")
        return True
```

### Generator-Based Context Manager (`contextlib`)
```python
from contextlib import contextmanager

@contextmanager
def temporary_lock(resource_name: str):
    print(f"Acquired lock on {resource_name}")
    try:
        yield resource_name
    finally:
        print(f"Released lock on {resource_name}")

with temporary_lock("PAYMENT_SERVICE") as lock:
    print(f"Processing inside {lock}")
```

---

## 16 Static Typing and Type Hints (PEP 484 - PEP 695)

### Core Type Annotations
```python
from typing import Callable, Literal, Protocol, TypeVar, Any

# 1. Type Aliases (Python 3.12+ syntax)
type ID = int | str
type Handler = Callable[[dict[str, Any]], bool]

# 2. Literal Types & TypedDict
type Status = Literal["pending", "approved", "rejected"]

# 3. Structural Subtyping / Duck Typing via Protocol (like TS Interfaces)
class Renderable(Protocol):
    def render(self) -> str: ...

def display(item: Renderable) -> None:
    print(item.render()) # Validates statically that item implements render()
```

---

## 17 Concurrency: Multi-Threading, Multi-Processing, and Asyncio

### Concurrency Matrix
| Model | Mechanism | Best For | GIL Impact | Memory Overhead |
| :--- | :--- | :--- | :--- | :--- |
| **`asyncio`** | Single-threaded cooperative multitasking | I/O-bound with 10k+ concurrent connections | No impact (Single thread) | **Lowest** (Lightweight coroutines) |
| **`threading`** | OS-level threads | I/O-bound (blocking libraries, file writes) | Blocked by GIL for CPU code | Low |
| **`multiprocessing`** | Separate OS processes | **CPU-bound** (ML inference, crypto, image processing) | **Bypasses GIL completely** | High (Copies process memory) |

### Modern Asyncio with `TaskGroup` (Python 3.11+)
```python
import asyncio

async def fetch_user(user_id: int) -> dict:
    await asyncio.sleep(0.1) # Non-blocking I/O
    return {"id": user_id, "name": f"User_{user_id}"}

async def main():
    # TaskGroup provides structured concurrency: If one fails, all are cancelled!
    async with asyncio.TaskGroup() as tg:
        task1 = tg.create_task(fetch_user(1))
        task2 = tg.create_task(fetch_user(2))

    print(task1.result(), task2.result())

asyncio.run(main())
```

---

## 18 Modern Web Backends (FastAPI & Async Architecture)

### Layered Production FastAPI Architecture
```
app/
├── main.py            # FastAPI app & lifespan setup
├── api/
│   ├── routes.py      # Route definitions
│   └── deps.py        # Dependency injection
├── core/
│   └── config.py      # Pydantic Settings
├── schemas/           # Pydantic request/response models
├── services/          # Business logic
└── db/
    └── session.py     # Async SQLAlchemy engine
```

### Complete Async Endpoint with Dependency Injection
```python
from fastapi import FastAPI, Depends, HTTPException, status
from pydantic import BaseModel
from contextlib import asynccontextmanager

@asynccontextmanager
async def lifespan(app: FastAPI):
    # Startup: Connect DB pools
    print("Database pool connected")
    yield
    # Shutdown: Close DB pools
    print("Database pool closed")

app = FastAPI(title="Production API", lifespan=lifespan)

class ItemDto(BaseModel):
    title: str
    price: float

async def get_db_session():
    # Yields async DB session
    session = {"connected": True}
    try:
        yield session
    finally:
        pass

@app.post("/items", status_code=status.HTTP_201_CREATED)
async def create_item(item: ItemDto, db = Depends(get_db_session)):
    if item.price < 0:
        raise HTTPException(status_code=400, detail="Price cannot be negative")
    return {"status": "success", "data": item}
```

---

## 19 File I/O, Serialization, and Network Requests

### Modern File Paths with `pathlib`
```python
from pathlib import Path

base_dir = Path("./data")
base_dir.mkdir(parents=True, exist_ok=True)
config_file = base_dir / "config.json"

# Quick read / write without open() boilerplate
config_file.write_text('{"env": "prod"}', encoding="utf-8")
content = config_file.read_text(encoding="utf-8")
```

### Async HTTP Requests with `httpx`
```python
import httpx

async def fetch_remote_data(url: str):
    async with httpx.AsyncClient(timeout=10.0) as client:
        response = await client.get(url)
        response.raise_for_status()
        return response.json()
```

---

## 20 Testing, Benchmarking, and Profiling

### Testing with `pytest`
```python
import pytest

def divide(a: float, b: float) -> float:
    if b == 0:
        raise ZeroDivisionError("Cannot divide by zero")
    return a / b

@pytest.mark.parametrize("a, b, expected", [
    (10, 2, 5.0),
    (9, 3, 3.0),
    (-4, 2, -2.0),
])
def test_divide_valid(a, b, expected):
    assert divide(a, b) == expected

def test_divide_by_zero():
    with pytest.raises(ZeroDivisionError, match="Cannot divide by zero"):
        divide(10, 0)
```

---

## 21 High-Yield Interview Questions and Reality Check

### 1. What happens when you do `def func(x=[])`? How to fix it?
> **Answer**: Default arguments are instantiated **once** when the function is defined, not when it is called. Every subsequent call that relies on the default value shares the same mutated list instance in memory. Fix: Use `def func(x=None): if x is None: x = []`.

### 2. How does CPython's Garbage Collector handle cyclic references?
> **Answer**: While standard reference counting frees objects immediately when `refcount == 0`, it cannot free circular references (`A ➔ B ➔ A`). CPython's cyclic GC periodically tracks container objects (`list`, `dict`, `custom instances`) across 3 generations (`Gen 0`, `Gen 1`, `Gen 2`), detecting isolated reference clusters unreachable from root pointers and destroying them.

### 3. What is the difference between `__new__` and `__init__`?
> **Answer**: `__new__` is the static method responsible for **creating and returning a new instance** in memory. `__init__` is the instance method responsible for **initializing the newly created instance**. `__new__` is used when subclassing immutable types (`int`, `str`, `tuple`) or implementing Singletons/Metaclasses.

### 4. What is `__slots__` and why should it be used in large data structures?
> **Answer**: By default, every Python instance stores its attributes in a dynamic dictionary `__dict__`, which consumes significant memory overhead. `__slots__` tells Python to allocate a fixed-size array for listed attributes, saving 40-50% RAM per instance and preventing arbitrary attribute creation.

### 5. Why doesn't multi-threading speed up CPU-bound tasks in Python?
> **Answer**: Due to the **Global Interpreter Lock (GIL)**, only one native thread can execute Python bytecode at any given moment. CPU-bound multi-threading creates thread contention overhead and slowdowns. For CPU-bound acceleration, you must use `multiprocessing` (which spawns separate processes with independent Python interpreters and GILs) or C/Rust extensions.

---

### Reality Check & Best Practices
- Never use mutable defaults (`[]`, `{}`) in function arguments.
- Prefer `pathlib.Path` over legacy `os.path` functions.
- Use `is` only for singleton checks (`is None`, `is True`, `is False`); use `==` for value comparison.
- Use Pydantic models for API validation and `@dataclass(slots=True)` for internal memory-critical domain classes.
- Use `asyncio.TaskGroup` instead of `asyncio.gather` in Python 3.11+ for fail-fast structured concurrency.
