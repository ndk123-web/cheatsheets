# Python Cheatsheet

Quick reference for core Python concepts, tooling, and ecosystem.

---

## Table of Contents
- [Introduction](#introduction)
- [Learn the Basics](#learn-the-basics)
- [Data Structures and Algorithms](#data-structures-and-algorithms)
- [Modules](#modules)
- [Functional and Advanced Concepts](#functional-and-advanced-concepts)
- [Object Oriented Programming](#object-oriented-programming)
- [Package Managers](#package-managers)
- [Configuration and Environments](#configuration-and-environments)
- [Python Features](#python-features)
- [Frameworks](#frameworks)
- [Concurrency](#concurrency)
- [Static Typing](#static-typing)
- [Code Formatting](#code-formatting)
- [Documentation](#documentation)
- [Testing](#testing)
- [Common Packages](#common-packages)
- [DevOps](#devops)

---

## Introduction

### Python
- Python is a high-level, readable, multiparadigm language used in backend, automation, data, and AI.
- Popular because of fast development speed and huge package ecosystem.

### Related Roadmaps
- Backend Roadmap
- DevOps Roadmap
- AI and Data Scientist Roadmap

---

## Learn the Basics

### Basic Syntax
```py
print("Hello, Python")
```

### Variables and Data Types
```py
name = "Aman"          # str
age = 22                # int
height = 5.9            # float
is_active = True        # bool
items = [1, 2, 3]       # list
```

### Conditionals
```py
if age >= 18:
    print("Adult")
elif age >= 13:
    print("Teen")
else:
    print("Child")
```

### Type Casting
```py
x = int("42")
y = float("3.14")
z = str(100)
```

### Exceptions
```py
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print("Error:", e)
finally:
    print("Done")
```

### Functions
```py
def add(a, b):
    return a + b

print(add(2, 3))
```

### Builtin Functions
- Common: `len`, `sum`, `min`, `max`, `sorted`, `map`, `filter`, `zip`, `enumerate`, `any`, `all`.

```py
nums = [3, 1, 2]
print(len(nums), sum(nums), sorted(nums))
```

### Lists
```py
nums = [1, 2, 3]
nums.append(4)
print(nums[0], nums[-1])
```

### Tuples
```py
point = (10, 20)
x, y = point
```

### Sets
```py
s = {1, 2, 2, 3}
print(s)  # {1, 2, 3}
```

### Dictionaries
```py
user = {"id": 1, "name": "Riya"}
print(user["name"])
```

### Loops
```py
for i in range(3):
    print(i)

n = 0
while n < 3:
    print(n)
    n += 1
```

---

## Data Structures and Algorithms

### Arrays and Linked Lists
- Python list behaves like a dynamic array.
- Linked list is usually custom implementation.

```py
arr = [10, 20, 30]
```

### Heaps, Stacks and Queues
```py
import heapq
heap = []
heapq.heappush(heap, 3)
heapq.heappush(heap, 1)
print(heapq.heappop(heap))

stack = []
stack.append(1)
stack.pop()

from collections import deque
q = deque([1, 2])
q.append(3)
q.popleft()
```

### Hash Tables
- `dict` and `set` are hash-table-based.

### Binary Search Tree
- Usually implemented manually or via third-party libs.

### Recursion
```py
def fact(n):
    if n <= 1:
        return 1
    return n * fact(n - 1)
```

### Sorting Algorithms
- Built-in `sorted()` and `.sort()` use Timsort.

```py
nums = [4, 2, 5, 1]
print(sorted(nums))
```

---

## Modules

### Builtin
```py
import math
print(math.sqrt(25))
```

### Custom
```py
# utils.py

def greet(name):
    return f"Hello {name}"

# main.py
# from utils import greet
# print(greet("Dev"))
```

---

## Functional and Advanced Concepts

### Lambdas
```py
square = lambda x: x * x
print(square(5))
```

### Decorators
```py
def log_calls(fn):
    def wrapper(*args, **kwargs):
        print("Calling", fn.__name__)
        return fn(*args, **kwargs)
    return wrapper

@log_calls
def hello():
    print("Hi")
```

### Iterators
```py
it = iter([1, 2, 3])
print(next(it), next(it))
```

### Regular Expressions
```py
import re
m = re.search(r"\d+", "Order123")
print(m.group())
```

---

## Object Oriented Programming

### Classes
```py
class User:
    def __init__(self, name):
        self.name = name
```

### Inheritance
```py
class Admin(User):
    pass
```

### Methods
```py
class Counter:
    def __init__(self):
        self.value = 0

    def inc(self):
        self.value += 1
```

### Dunder Methods
```py
class Box:
    def __init__(self, value):
        self.value = value

    def __repr__(self):
        return f"Box({self.value})"
```

---

## Package Managers

### PyPI
- Official Python package index.

### pip
```bash
pip install requests
pip install -r requirements.txt
```

### Conda
```bash
conda create -n myenv python=3.12
conda activate myenv
```

### Poetry
```bash
poetry init
poetry add fastapi
poetry run python app.py
```

---

## Configuration and Environments

### pyproject.toml
- Unified config for build system, tools, dependencies.

```toml
[project]
name = "myapp"
version = "0.1.0"
requires-python = ">=3.11"
```

### Pipenv
```bash
pipenv install
pipenv shell
```

### virtualenv
```bash
python -m venv .venv
```

### pyenv
```bash
pyenv install 3.12.2
pyenv local 3.12.2
```

### Environments
- Use per-project isolated environments.
- Do not install project dependencies globally.

---

## Python Features

### List Comprehensions
```py
squares = [x * x for x in range(6)]
```

### Generator Expressions
```py
g = (x * x for x in range(6))
print(next(g))
```

### Paradigms
- Procedural
- Object-oriented
- Functional

### Context Manager
```py
with open("data.txt", "r", encoding="utf-8") as f:
    text = f.read()
```

---

## Frameworks

### Synchronous
- Pyramid
- Plotly Dash

### Asynchronous
- gevent
- aiohttp
- Tornado
- Sanic

### Synchronous and Asynchronous
- FastAPI
- Django
- Flask

```py
# FastAPI quick example
# from fastapi import FastAPI
# app = FastAPI()
# @app.get("/")
# def root():
#     return {"ok": True}
```

---

## Concurrency

### GIL
- Global Interpreter Lock allows one thread to execute Python bytecode at a time in CPython.

### Threading
```py
import threading

def task():
    print("thread")

t = threading.Thread(target=task)
t.start()
t.join()
```

### Multiprocessing
```py
from multiprocessing import Process

def task():
    print("process")

p = Process(target=task)
p.start()
p.join()
```

### Asynchrony
```py
import asyncio

async def main():
    await asyncio.sleep(1)
    print("async done")

asyncio.run(main())
```

---

## Static Typing

### typing
```py
from typing import List

def total(nums: List[int]) -> int:
    return sum(nums)
```

### Pydantic
- Data validation using Python type hints.

### mypy
```bash
mypy .
```

### pyright
```bash
pyright
```

### pyre
```bash
pyre check
```

---

## Code Formatting

### ruff
```bash
ruff check .
ruff format .
```

### black
```bash
black .
```

### yapf
```bash
yapf -r -i .
```

---

## Documentation

### Sphinx
```bash
pip install sphinx
sphinx-quickstart
```

---

## Testing

### doctest
```py
def add(a, b):
    """Return sum.

    >>> add(2, 3)
    5
    """
    return a + b
```

### nose
- Legacy framework (mostly replaced by pytest).

### pytest
```py
# test_math.py

def test_add():
    assert 2 + 3 == 5
```

### unittest / pyUnit
```py
import unittest

class TestMath(unittest.TestCase):
    def test_add(self):
        self.assertEqual(2 + 3, 5)
```

### tox
```bash
tox
```

---

## Common Packages
- requests
- numpy
- pandas
- matplotlib
- scipy
- scikit-learn
- pydantic
- sqlalchemy
- fastapi
- django
- flask
- pytest

---

## DevOps
- Dockerize Python apps.
- Use CI for lint, type-check, test.
- Use env vars for configuration.
- Use Gunicorn/Uvicorn for production serving.

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```
