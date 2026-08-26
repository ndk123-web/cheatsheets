# C++ Cheatsheet

Comprehensive end-to-end reference for Modern C++ (C++11 to C++20/C++23) from low-level memory layout, pointers, and the compilation pipeline (CMake) to RAII, Smart Pointers, Move Semantics, OOP/Vtables, Templates/Concepts, STL Internals, Concurrency, and systems architecture.

---

## Table of Contents
- [High Priority Topics](#high-priority-topics)
- [1 Compilation Pipeline, Toolchains, and CMake](#1-compilation-pipeline-toolchains-and-cmake)
- [2 Memory Layout, Pointers, and References](#2-memory-layout-pointers-and-references)
- [3 Modern Language Basics and Type System](#3-modern-language-basics-and-type-system)
- [4 Strings, String Views, and Slices](#4-strings-string-views-and-slices)
- [5 Control Flow and Modern Pattern Syntax](#5-control-flow-and-modern-pattern-syntax)
- [6 Functions, Lambdas, and Functional Utilities](#6-functions-lambdas-and-functional-utilities)
- [7 RAII and Smart Pointers](#7-raii-and-smart-pointers)
- [8 Move Semantics, Rvalues, and Rule of 0/3/5](#8-move-semantics-rvalues-and-rule-of-035)
- [9 Object-Oriented Programming, Vtables, and Polymorphism](#9-object-oriented-programming-vtables-and-polymorphism)
- [10 Templates, SFINAE, and C++20 Concepts](#10-templates-sfinae-and-c20-concepts)
- [11 Standard Template Library (STL) Containers Deep Dive](#11-standard-template-library-stl-containers-deep-dive)
- [12 STL Algorithms and C++20 Ranges](#12-stl-algorithms-and-c20-ranges)
- [13 Error Handling, Exceptions, and std::expected](#13-error-handling-exceptions-and-stdexpected)
- [14 Multithreading, Mutexes, and Atomics](#14-multithreading-mutexes-and-atomics)
- [15 C++20 Modules and Code Architecture](#15-c20-modules-and-code-architecture)
- [16 File I/O and Modern Filesystem API](#16-file-io-and-modern-filesystem-api)
- [17 Testing, Sanitizers, and Tooling](#17-testing-sanitizers-and-tooling)
- [18 High-Yield Interview Questions and Reality Check](#18-high-yield-interview-questions-and-reality-check)

---

## High Priority Topics

Most asked in C++ interviews and core systems architecture:
1. **Pointers, Pointer Arithmetic, Dangling References & Stack vs Heap Layout**
2. **RAII & Smart Pointers (`std::unique_ptr`, `std::shared_ptr`, `std::weak_ptr`)**
3. **Move Semantics (`std::move`), Rvalue References (`T&&`), and Perfect Forwarding (`std::forward`)**
4. **Rule of 0 / 3 / 5 (Destructor, Copy Ctor/Assign, Move Ctor/Assign)**
5. **Virtual Functions, Vtable & Vptr Mechanics, Virtual Destructors, Object Slicing**
6. **Diamond Problem & Virtual Inheritance**
7. **STL Internals (`std::vector` capacity doubling, `std::unordered_map` bucket hashing & rehash)**
8. **Templates, Template Specialization, and C++20 Concepts (`requires`)**
9. **C++ Memory Model, `std::atomic<T>`, `std::lock_guard`, `std::unique_lock`**
10. **Modern CMake Project Structure & Compilation Pipeline**

---

## 1 Compilation Pipeline, Toolchains, and CMake

### The 4 Compilation Stages
```
Source (.cpp) + Headers (.hpp)
       │
       ▼ [1. Preprocessor (g++ -E)]
Expands #include, evaluates #define, strips comments ──► Translation Unit (.i)
       │
       ▼ [2. Compiler (g++ -S)]
Translates code into target assembly ──► Assembly (.s)
       │
       ▼ [3. Assembler (g++ -c)]
Converts assembly into machine bytecode ──► Object File (.o / .obj)
       │
       ▼ [4. Linker (g++ / ld)]
Resolves external symbols, combines object files & libraries ──► Executable / Dynamic Lib (.so / .dll)
```

### Modern CMake (`CMakeLists.txt`)
```cmake
cmake_minimum_required(VERSION 3.20)
project(EngineApp VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Source and Include directories
add_executable(EngineApp
    src/main.cpp
    src/core/memory.cpp
    src/network/client.cpp
)

target_include_directories(EngineApp PRIVATE
    ${PROJECT_SOURCE_DIR}/include
)

# Compiler Warnings & Sanitizers in Debug
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_options(EngineApp PRIVATE -Wall -Wextra -Wpedantic -fsanitize=address,undefined)
    target_link_options(EngineApp PRIVATE -fsanitize=address,undefined)
endif()

# Find and link third-party libraries (e.g. Threads, fmt)
find_package(Threads REQUIRED)
target_link_libraries(EngineApp PRIVATE Threads::Threads)
```

### Essential CLI Commands
```bash
# Build with CMake
cmake -B build -DCMAKE_BUILD_TYPE=Release
cmake --build build -j 8

# Compile single file directly with g++ / clang++
g++ -std=c++20 -O3 -Wall -Wextra main.cpp -o app
./app
```

---

## 2 Memory Layout, Pointers, and References

### C++ Process Memory Layout
```
┌────────────────────────────────────────────────────────┐
│ High Memory (Kernel Space)                             │
├────────────────────────────────────────────────────────┤
│ Stack Frame (Local vars, parameters, returns) [Down ──►│
│                          │                             │
│                          ▼                             │
│                          ▲                             │
│                          │                             │
│ Heap Memory (new/malloc, vector buffers) [Up ─────────]│
├────────────────────────────────────────────────────────┤
│ BSS Segment (Uninitialized global / static variables)  │
├────────────────────────────────────────────────────────┤
│ Data Segment (Initialized global / static variables)   │
├────────────────────────────────────────────────────────┤
│ Text Segment (Read-only machine instructions / Code)   │
└────────────────────────────────────────────────────────┘
```

### Pointer vs Reference Comparison
| Feature | Pointer (`T*`) | Reference (`T&`) |
| :--- | :--- | :--- |
| **Reassignment** | Can point to different objects | **Immutable binding** (bound at creation) |
| **Nullability** | Can be `nullptr` | **Cannot be null** (Must reference valid object) |
| **Memory Address** | Has its own distinct memory address | Alias sharing the referenced object's address |
| **Arithmetic** | Pointer arithmetic allowed (`ptr++`) | Arithmetic applies directly to the target value |

```cpp
#include <iostream>

void pointerExample() {
    int value = 42;
    int* ptr = &value; // Pointer holds address of value
    int& ref = value;  // Reference is an alias for value

    *ptr = 100;        // Dereference to modify value
    ref = 200;         // Modifies value directly

    // Pointer arithmetic
    int arr[] = {10, 20, 30};
    int* pArr = arr;
    std::cout << *(pArr + 1) << "\n"; // Prints 20 (advances by sizeof(int) bytes)
}
```

---

## 3 Modern Language Basics and Type System

### `auto`, `decltype`, and Type Deduction
```cpp
auto x = 10;          // int
const auto& rx = x;   // const int& (reference preserved)
auto y = rx;          // int (drops const and reference!)
decltype(rx) z = x;   // const int& (exact type preserved)
```

### `constexpr` vs `consteval` vs `const`
| Keyword | Evaluation Time | Purpose |
| :--- | :--- | :--- |
| `const` | Runtime or Compile-time | Read-only value after initialization |
| `constexpr` | Compile-time **if possible**, otherwise runtime | Enables constant expressions and optimizations |
| `consteval` | **Strictly Compile-time** (C++20) | Immediate function; error if not evaluated at compile-time |

```cpp
constexpr int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}

consteval int square(int n) {
    return n * n; // Must be evaluated at compile time
}

constexpr int f5 = factorial(5); // Computed by compiler at compile time!
constexpr int s4 = square(4);
```

---

## 4 Strings, String Views, and Slices

### `std::string` vs `std::string_view` (C++17)
- `std::string`: Owns heap-allocated character buffer (Small String Optimization - SSO typically holds up to 15-22 chars on stack).
- `std::string_view`: Non-owning view `(const char* data, size_t size)` with zero-allocation slicing.

```cpp
#include <string>
#include <string_view>
#include <iostream>

// Zero-copy string parameter (accepts std::string, "literal", or substrings)
void printPrefix(std::string_view sv, size_t n) {
    if (n <= sv.size()) {
        std::cout << sv.substr(0, n) << "\n"; // Zero heap allocations!
    }
}

int main() {
    std::string text = "Modern Systems Programming";
    printPrefix(text, 6); // Prints "Modern"
}
```

---

## 5 Control Flow and Modern Pattern Syntax

### `if` with Initializer (C++17)
Scopes variables directly inside the conditional statement.

```cpp
#include <map>
#include <string>

std::map<std::string, int> scores = {{"Alice", 95}, {"Bob", 80}};

if (auto it = scores.find("Alice"); it != scores.end()) {
    // 'it' exists ONLY inside this if/else block
    std::cout << "Score: " << it->second << "\n";
}
```

### Structured Bindings (C++17)
```cpp
#include <tuple>

auto getMetadata() -> std::tuple<int, double, std::string> {
    return {1, 99.5, "NodeA"};
}

auto [id, score, name] = getMetadata(); // Unpacks tuple into variables
```

---

## 6 Functions, Lambdas, and Functional Utilities

### Lambda Expressions Anatomy
```
[capture_clause](parameters) mutable noexcept -> return_type { body }
```
- `[]`: No capture
- `[&]`: Capture all outer variables by **reference**
- `[=]`: Capture all outer variables by **value (copy)**
- `[this]`: Capture current class instance pointer
- `[val = std::move(ptr)]`: Generalized init-capture (Move into lambda)

```cpp
#include <vector>
#include <algorithm>
#include <iostream>

int main() {
    std::vector<int> nums = {5, 2, 8, 1, 9};
    int threshold = 4;

    // Filter and count using lambda
    auto count = std::count_if(nums.begin(), nums.end(), [threshold](int n) {
        return n > threshold;
    });

    // Generic Lambda (C++14)
    auto print = [](const auto& item) { std::cout << item << " "; };
    std::for_each(nums.begin(), nums.end(), print);
}
```

---

## 7 RAII and Smart Pointers

### The RAII Principle (Resource Acquisition Is Initialization)
Resources (memory, file handles, mutex locks) must be bound to object lifetimes so that destructors automatically clean up upon leaving scope (even during exceptions).

### Smart Pointers Reference Guide (`<memory>`)
| Smart Pointer | Ownership | Overhead | Thread-Safe RefCount | Use Case |
| :--- | :--- | :--- | :--- | :--- |
| `std::unique_ptr<T>` | **Exclusive** (Cannot copy, only move) | **Zero overhead** (Same size as raw pointer) | N/A | Default choice for heap objects, factories |
| `std::shared_ptr<T>` | **Shared** (Reference-counted) | 2 words (ptr + control block pointer) | Yes (Atomic ref count) | Shared ownership across multiple components |
| `std::weak_ptr<T>` | **Non-owning observer** | Tracks shared_ptr without preventing deletion | Yes | Breaking circular dependencies, caching |

```cpp
#include <memory>
#include <iostream>

struct Resource {
    Resource() { std::cout << "Resource Acquired\n"; }
    ~Resource() { std::cout << "Resource Destroyed\n"; }
    void process() { std::cout << "Processing\n"; }
};

void smartPointerDemo() {
    // 1. std::unique_ptr (Always use std::make_unique)
    auto uPtr = std::make_unique<Resource>();
    uPtr->process();
    // auto copy = uPtr; // COMPILE ERROR: Cannot copy unique_ptr
    auto movedPtr = std::move(uPtr); // Ownership transferred; uPtr is now nullptr

    // 2. std::shared_ptr and std::weak_ptr
    auto sPtr1 = std::make_shared<Resource>();
    std::weak_ptr<Resource> wPtr = sPtr1; // Non-owning reference

    if (auto locked = wPtr.lock()) { // Lock returns valid shared_ptr if object alive
        locked->process();
    }
} // All resources automatically freed here!
```

---

## 8 Move Semantics, Rvalues, and Rule of 0/3/5

### Lvalues vs Rvalues
- **Lvalue**: An expression with an identifiable memory address (can appear on left side of `=`).
- **Rvalue**: A temporary value / literal without a persistent memory address (expires at end of expression).

```cpp
int a = 10;     // 'a' is lvalue, '10' is rvalue
int& lref = a;   // Lvalue reference
int&& rref = 20; // Rvalue reference (binds to temporary)
```

### The Rule of 0 / 3 / 5
1. **Rule of 0**: If your class uses standard RAII types (`std::string`, `std::vector`, smart pointers), do not declare custom destructors or copy/move operations.
2. **Rule of 3**: If you define a custom Destructor, Copy Constructor, or Copy Assignment, you must define all 3.
3. **Rule of 5**: In Modern C++, add Move Constructor and Move Assignment.

```cpp
#include <utility>
#include <cstring>

class Buffer {
private:
    size_t size;
    char* data;

public:
    // 1. Constructor
    Buffer(size_t s) : size(s), data(new char[s]) {}

    // 2. Destructor
    ~Buffer() { delete[] data; }

    // 3. Copy Constructor (Deep Copy)
    Buffer(const Buffer& other) : size(other.size), data(new char[other.size]) {
        std::memcpy(data, other.data, size);
    }

    // 4. Copy Assignment Operator
    Buffer& operator=(const Buffer& other) {
        if (this != &other) {
            delete[] data;
            size = other.size;
            data = new char[other.size];
            std::memcpy(data, other.data, size);
        }
        return *this;
    }

    // 5. Move Constructor (Pilfer resources - Zero copy!)
    Buffer(Buffer&& other) noexcept : size(other.size), data(other.data) {
        other.size = 0;
        other.data = nullptr; // Invalidate source
    }

    // 6. Move Assignment Operator
    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data;
            size = other.size;
            data = other.data;
            other.size = 0;
            other.data = nullptr;
        }
        return *this;
    }
};
```

---

## 9 Object-Oriented Programming, Vtables, and Polymorphism

### Virtual Table (Vtable) and Vptr Mechanics
- Any class with at least one `virtual` function gets a hidden **`vptr` (Virtual Table Pointer)** inserted into its memory layout.
- The `vptr` points to the class's static `vtable` array containing function pointers to the virtual methods.
- **Dynamic Dispatch**: `obj->virtualMethod()` executes `vptr->vtable[index]()` at runtime.

```cpp
#include <iostream>

class Base {
public:
    virtual void speak() { std::cout << "Base speak\n"; }
    // Virtual destructor is MANDATORY for base classes to prevent memory leaks on deletion!
    virtual ~Base() { std::cout << "Base destructor\n"; }
};

class Derived : public Base {
public:
    void speak() override { std::cout << "Derived speak\n"; }
    ~Derived() override { std::cout << "Derived destructor\n"; }
};

int main() {
    Base* poly = new Derived();
    poly->speak(); // Calls Derived::speak via vtable lookup
    delete poly;   // Correctly calls Derived destructor then Base destructor!
}
```

### The Diamond Problem and Virtual Inheritance
```cpp
class Device { public: int id; };
class Scanner : virtual public Device {}; // Virtual base prevents duplicate Device copies
class Printer : virtual public Device {};
class Copier : public Scanner, public Printer {}; // Single shared 'id' field
```

---

## 10 Templates, SFINAE, and C++20 Concepts

### Generic Function & Class Templates
```cpp
template <typename T>
T add(T a, T b) {
    return a + b;
}

template <typename T, size_t Capacity>
class StaticStack {
    T buffer[Capacity];
    size_t topIndex = 0;
};
```

### C++20 Concepts and Constraints (`requires`)
Replaces complex template metaprogramming and SFINAE errors with readable constraints and clean compiler error messages.

```cpp
#include <concepts>
#include <iostream>

// Define a custom concept
template <typename T>
concept Numeric = std::integral<T> || std::floating_point<T>;

// Constrained generic function
template <Numeric T>
T multiply(T a, T b) {
    return a * b;
}

// Alternative syntax with auto
auto calculate(Numeric auto a, Numeric auto b) {
    return a + b;
}

int main() {
    std::cout << multiply(10, 5) << "\n";      // OK
    std::cout << multiply(3.14, 2.0) << "\n";  // OK
    // multiply("a", "b"); // COMPILE ERROR: Constraints not satisfied!
}
```

---

## 11 Standard Template Library (STL) Containers Deep Dive

### STL Containers Reference Matrix
| Container | Underlying Data Structure | Access | Insert/Delete Front | Insert/Delete Back | Insert/Delete Middle |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `std::vector<T>` | Contiguous dynamic array (Doubles capacity on realloc) | $O(1)$ | $O(N)$ | $O(1)$ amortized | $O(N)$ |
| `std::deque<T>` | Chunked fixed-size array buffers | $O(1)$ | $O(1)$ | $O(1)$ | $O(N)$ |
| `std::list<T>` | Doubly-linked list | $O(N)$ | $O(1)$ | $O(1)$ | $O(1)$ |
| `std::map<K, V>` | Red-Black Tree (Self-balancing BST) | $O(\log N)$ | $O(\log N)$ | $O(\log N)$ | $O(\log N)$ |
| `std::unordered_map<K, V>` | Hash Table (Buckets with chaining) | $O(1)$ avg | $O(1)$ avg | $O(1)$ avg | $O(1)$ avg ($O(N)$ worst) |
| `std::priority_queue<T>` | Binary Max-Heap | Top: $O(1)$ | N/A | Push/Pop: $O(\log N)$ | N/A |

### Practical STL Examples
```cpp
#include <vector>
#include <unordered_map>
#include <queue>
#include <iostream>

void stlDemo() {
    // 1. Vector with reserve to prevent reallocations
    std::vector<int> vec;
    vec.reserve(100); // Allocates capacity upfront
    vec.push_back(10);
    vec.emplace_back(20); // Constructs in-place without temporary copy

    // 2. Unordered Map
    std::unordered_map<std::string, int> scoreMap;
    scoreMap["Aman"] = 100;
    scoreMap.emplace("Alex", 90);

    // 3. Min-Heap Priority Queue
    std::priority_queue<int, std::vector<int>, std::greater<int>> minHeap;
    minHeap.push(30);
    minHeap.push(10);
    minHeap.push(20);
    std::cout << "Min element: " << minHeap.top() << "\n"; // 10
}
```

---

## 12 STL Algorithms and C++20 Ranges

```cpp
#include <vector>
#include <algorithm>
#include <numeric>
#include <ranges>
#include <iostream>

void modernAlgorithms() {
    std::vector<int> nums = {1, 2, 3, 4, 5, 6, 7, 8};

    // 1. Standard Algorithms
    std::sort(nums.begin(), nums.end(), std::greater<int>());
    auto it = std::lower_bound(nums.begin(), nums.end(), 4, std::greater<int>());

    // 2. C++20 Ranges & Views (Lazy pipeline execution)
    auto evenSquares = nums
        | std::views::filter([](int n) { return n % 2 == 0; })
        | std::views::transform([](int n) { return n * n; });

    for (int val : evenSquares) {
        std::cout << val << " "; // 64 36 16 4
    }
}
```

---

## 13 Error Handling, Exceptions, and std::expected

### Exception Safety Levels
1. **No-throw guarantee (`noexcept`)**: Never throws.
2. **Strong exception safety**: If exception occurs, program state is rolled back to before operation.
3. **Basic exception safety**: No resource leaks, invariants preserved.

### `std::expected` (C++23 - Result Type Pattern)
```cpp
#include <expected>
#include <string>
#include <iostream>

enum class ParseError { InvalidChar, Overflow };

std::expected<int, ParseError> parsePort(std::string_view str) {
    if (str.empty()) return std::unexpected(ParseError::InvalidChar);
    int port = std::stoi(std::string(str));
    if (port > 65535) return std::unexpected(ParseError::Overflow);
    return port;
}

int main() {
    auto res = parsePort("8080");
    if (res) {
        std::cout << "Port: " << *res << "\n";
    } else {
        std::cout << "Error occurred\n";
    }
}
```

---

## 14 Multithreading, Mutexes, and Atomics

### Threads, Mutexes, and Lock Guards
```cpp
#include <thread>
#include <mutex>
#include <vector>
#include <iostream>

std::mutex g_mutex;
int sharedCounter = 0;

void incrementCounter(int count) {
    for (int i = 0; i < count; ++i) {
        // std::lock_guard provides RAII-scoped mutex lock
        std::lock_guard<std::mutex> lock(g_mutex);
        sharedCounter++;
    }
}

int main() {
    std::thread t1(incrementCounter, 10000);
    std::thread t2(incrementCounter, 10000);

    t1.join();
    t2.join();
    std::cout << "Counter: " << sharedCounter << "\n"; // 20000
}
```

### Lock-Free Programming with `std::atomic<T>`
```cpp
#include <atomic>

std::atomic<int64_t> atomicHits{0};

void recordHit() {
    // Lock-free atomic increment
    atomicHits.fetch_add(1, std::memory_order_relaxed);
}
```

---

## 15 C++20 Modules and Code Architecture

### Traditional Header/Source vs C++20 Modules
- **Headers (`.hpp`/`.cpp`)**: Preprocessor literally copy-pastes header contents into each translation unit, leading to slow compilation times and macro leaks.
- **C++20 Modules (`.ixx` / `.cppm`)**: Compiled once into binary module interfaces (BMI), zero macro leakage, 5-10x faster build times.

```cpp
// math_module.cppm
export module MathModule;

export namespace Math {
    int add(int a, int b) { return a + b; }
    int subtract(int a, int b) { return a - b; }
}

// main.cpp
import MathModule;
import <iostream>;

int main() {
    std::cout << Math::add(10, 20) << "\n";
}
```

---

## 16 File I/O and Modern Filesystem API

```cpp
#include <filesystem>
#include <fstream>
#include <iostream>

namespace fs = std::filesystem;

void fileOperations() {
    fs::path dir = "data/logs";
    fs::create_directories(dir);

    fs::path logFile = dir / "app.log";

    // Write file
    std::ofstream out(logFile, std::ios::app);
    out << "Log entry: Operation succeeded\n";
    out.close();

    // Iterate over directory contents
    for (const auto& entry : fs::directory_iterator(dir)) {
        std::cout << entry.path() << " (" << fs::file_size(entry) << " bytes)\n";
    }
}
```

---

## 17 Testing, Sanitizers, and Tooling

### Sanitizers (LLVM / Clang / GCC)
Enable sanitizers in Debug mode to catch undefined behavior and memory bugs instantly:
- `-fsanitize=address`: Detects out-of-bounds, use-after-free, memory leaks.
- `-fsanitize=undefined`: Detects integer overflows, null dereferences.
- `-fsanitize=thread`: Detects data races in multi-threaded code.

### Unit Testing with GoogleTest (GTest) / Catch2
```cpp
#include <gtest/gtest.h>

int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}

TEST(FactorialTest, HandlesZeroAndPositiveNumbers) {
    EXPECT_EQ(factorial(0), 1);
    EXPECT_EQ(factorial(1), 1);
    EXPECT_EQ(factorial(5), 120);
}
```

---

## 18 High-Yield Interview Questions and Reality Check

### 1. What is the difference between `std::unique_ptr` and `std::shared_ptr`?
> **Answer**: `std::unique_ptr` enforces strict single ownership with zero runtime overhead (same size as raw pointer) and moves only. `std::shared_ptr` provides reference-counted shared ownership via an allocated control block (containing strong ref count, weak ref count, and custom deleter). `shared_ptr` ref count increments/decrements are thread-safe atomic operations, incurring a small performance and memory penalty.

### 2. Why should base class destructors always be declared `virtual`?
> **Answer**: If a derived class object is deleted through a base class pointer (`Base* ptr = new Derived(); delete ptr;`) and the base destructor is non-virtual, the compiler performs static binding and calls **only the Base destructor**. The Derived class destructor is never invoked, causing memory leaks and undefined behavior for derived resources.

### 3. What is the difference between `std::move()` and `std::forward()`?
> **Answer**: `std::move(t)` unconditionally casts its argument into an rvalue reference (`static_cast<T&&>(t)`), signaling that its resources can be pilfered. `std::forward<T>(t)` is a **conditional cast** used in template perfect forwarding: it casts to an rvalue only if the original argument passed into the function was an rvalue, preserving the exact value category.

### 4. What is Object Slicing and how is it prevented?
> **Answer**: Object slicing occurs when a derived class object is assigned to a base class object **by value** (`Base b = derivedObj;`). The derived portion of the object is "sliced off" because the base object only has memory allocated for base members. Slicing is prevented by passing objects by reference or pointer (`const Base&` or `std::unique_ptr<Base>`).

### 5. How does `std::vector` allocate memory when capacity is exceeded?
> **Answer**: When `push_back()` exceeds current `capacity()`, `std::vector` allocates a new contiguous buffer (typically $1.5\times$ or $2\times$ the old size), moves existing elements to the new buffer using move constructors (if `noexcept`), and deallocates the old buffer. This ensures an amortized $O(1)$ push time.

---

### Reality Check & Best Practices
- Never use raw `new` and `delete` in modern code; use `std::make_unique` or `std::make_shared`.
- Always mark move constructors and move assignment operators as `noexcept` so STL containers (like `std::vector`) can safely use move operations during reallocation.
- Prefer `std::string_view` and `std::span` over `const std::string&` and `const std::vector<T>&` for non-owning function parameters.
- Always compile with `-Wall -Wextra -Wpedantic` and run AddressSanitizer (`-fsanitize=address`).
- Use `emplace_back()` instead of `push_back()` to construct objects directly in container buffers without unnecessary copies.
